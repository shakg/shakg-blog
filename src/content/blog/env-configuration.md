---
title: Secret Management In Golang Binaries
author: shakg
pubDatetime: 2024-11-16T06:16:51Z
slug: secret-management-in-golang-binaries
featured: false
draft: false
tags:
  - Software Development
  - env
  - golang
  - typescript
  - Secret Management
description: "How to manage secrets in development vs production? How it looks like when you work with a language that outputs binary like golang?"
---

# Environment, what is it?

Environment is a 'pool' that you put some information on there and then you can retrieve it. On the root, generally, there is a file that you write and read such as .env file. But instead of putting them into flat text files, we often see the dot file pattern like `.env` in the software projects. This allows developers to not push the secrets to VCSs. In order to do that, we need to add that file to `.gitignore`, this is a general pattern we use while developing software. Add env file to gitignore, put your secrets to env file, maybe create an `.env.sample` file that you can specifiy what is needed to run the application, this is usefull for other developers who doesn't know much about the codebase. By following this pattern, you can use your secrets like, api tokens, urls etc. However, there is some languages like golang that outputs binary when you compile it. What happens when you compile an app to binary that uses .env file pattern?

---

### **Golang and environment variables**

As usual, you can use env files with golang and retreive the secrets from this file using some libraries, for example, `godotenv`. Its pretty straightforward. But when you compile your golang app and get the binary, it searches for .env file on the same folder where you run it. This is a problem if you are going to distribute the app or if you are going to put it on `/usr/local/bin` folder to be able to run it in system wide. Lets imagine a scenario where you develop your application that uses env, you've built the app and put it on `/usr/local/bin` folder to be able to run it system wide. And on the home folder, you've run the app, you'll get something like this;

```bash
ishakgonul@192 ~ % pokeme
2024/12/05 09:44:29 Error loading .env file: open .env: no such file or directory
```
To fix this you could create .env file in the folder you've run the command but this is not the correct way to fix this problem. 

### Correct Handling of Environment Variables in Golang  
Instead of relying solely on a `.env` file, a better approach involves implementing a flexible, robust mechanism to manage environment variables. Here’s how you can address the issues and improve your app’s environment variable management:  

1. **Use Embedded Environment Variables**  
   Golang provides the ability to embed files or data into binaries using tools like `embed`. You can include your `.env` file content during the build process. This ensures the binary contains all the necessary environment variables without requiring external files.  

   Example:  
   ```go
   package main

   import (
       "embed"
       "github.com/joho/godotenv"
       "log"
   )

   //go:embed .env
   var embeddedEnv embed.FS

   func main() {
       envFile, err := embeddedEnv.ReadFile(".env")
       if err != nil {
           log.Fatalf("Failed to load embedded .env file: %v", err)
       }
       if err := godotenv.Unmarshal(string(envFile)); err != nil {
           log.Fatalf("Failed to parse .env content: %v", err)
       }
       log.Println("Environment variables loaded successfully!")
   }
   ```  
   By embedding the `.env` file, you avoid file-not-found issues at runtime.

2. **Rely on System-Level Environment Variables**  
   Many production environments, especially cloud-native platforms like Kubernetes or Docker, rely on system-level environment variables. Instead of distributing a `.env` file, you can pass these variables directly to the runtime environment:  
   - **Docker**: Use the `-e` flag or a `docker-compose.yml` file to define variables.  
   - **Kubernetes**: Define them in ConfigMaps or Secrets and inject them into your application’s container.  

   Modify your application to check system environment variables as a fallback when the `.env` file is unavailable:  
   ```go
   import (
       "os"
       "log"
   )

   func main() {
       apiKey := os.Getenv("API_KEY")
       if apiKey == "" {
           log.Fatal("API_KEY is not set")
       }
       log.Printf("API_KEY loaded: %s", apiKey)
   }
   ```  

3. **Provide Configuration Options**  
   Allow your application to accept environment variables through multiple sources:  
   - `.env` file (local development).  
   - System environment variables (production).  
   - Command-line flags (on-demand overrides).  

   Example:  
   ```go
   import (
       "flag"
       "os"
       "log"
   )

   func main() {
       apiKey := flag.String("api-key", "", "API Key for the application")
       flag.Parse()

       if *apiKey == "" {
           *apiKey = os.Getenv("API_KEY")
       }

       if *apiKey == "" {
           log.Fatal("API Key is not provided")
       }
       log.Printf("Using API Key: %s", *apiKey)
   }
   ```  
   This approach makes your application highly configurable and flexible.  

4. **Use a Secret Management System**  
   For distributed applications or systems with sensitive data, consider using a dedicated secret management system like HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault. These tools securely store and manage secrets, providing APIs to retrieve them programmatically.  

   Example with AWS Secrets Manager:  
   ```go
   import (
       "github.com/aws/aws-sdk-go/aws/session"
       "github.com/aws/aws-sdk-go/service/secretsmanager"
       "log"
   )

   func main() {
       sess := session.Must(session.NewSession())
       svc := secretsmanager.New(sess)

       secret, err := svc.GetSecretValue(&secretsmanager.GetSecretValueInput{
           SecretId: aws.String("your-secret-id"),
       })
       if err != nil {
           log.Fatalf("Failed to retrieve secret: %v", err)
       }
       log.Printf("Retrieved secret: %s", *secret.SecretString)
   }
   ```  

### Summary  
The default `.env` pattern works well for local development but becomes impractical for compiled binaries or production environments. To address these challenges:  
- Embed `.env` files in the binary or use system-level environment variables.  
- Allow for multiple configuration sources to increase flexibility.  
- Consider integrating a secret management solution for enhanced security and scalability.  

By implementing these strategies, you ensure that your Golang applications remain secure, flexible, and production-ready.

