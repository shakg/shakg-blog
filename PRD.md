# Astro Markdown Blog - Product Requirements Document

## 1. Concept & Vision

A minimalist, content-focused blog built with Astro that transforms markdown files into a beautiful reading experience. The aesthetic is "digital paper" — clean typography, generous whitespace, and subtle animations that feel like turning pages rather than clicking links. Fast by default, delightful by design.

## 2. Design Language

### Aesthetic Direction
Inspired by premium editorial design (NYT, Medium's old design) — the content is the hero, not the chrome.

### Color Palette
- **Background**: `#fafaf9` (warm off-white)
- **Text**: `#1c1917` (warm black)
- **Accent**: `#dc2626` (editorial red — for links, highlights)
- **Secondary**: `#78716c` (muted stone for metadata)
- **Surface**: `#f5f5f4` (subtle card backgrounds)

### Typography
- **Headings**: `Playfair Display` (serif, elegant) — fallback: `Georgia, serif`
- **Body**: `Source Serif 4` (readable serif for long-form) — fallback: `Georgia, serif`
- **Meta/UI**: `Inter` (clean sans-serif) — fallback: `system-ui, sans-serif`

### Spatial System
- Base unit: 8px
- Content max-width: 680px (optimal reading width)
- Generous vertical rhythm: 1.75 line-height for body text
- Section spacing: 64px between major sections

### Motion Philosophy
- Page transitions: subtle fade (200ms ease-out)
- Link hovers: color shift with 150ms transition
- No jarring animations — everything should feel calm

### Visual Assets
- No decorative icons — typography-driven
- Code blocks: subtle syntax highlighting (dracula theme)
- Blockquotes: left border accent line

## 3. Layout & Structure

### Pages

**Homepage (`/`)**
- Hero section with blog title, tagline, and subtle animation
- Recent posts list (title + date + reading time)
- Clean, scannable vertical rhythm

**Post Page (`/blog/[slug]`)**
- Post title (Playfair Display, large)
- Metadata bar: publication date, reading time, tags
- Markdown content with proper typography
- Previous/Next post navigation at bottom

**About Page (`/about`)**
- Simple bio text
- Minimal, no images needed

**404 Page**
- Friendly message
- Link back to homepage

### Responsive Strategy
- Mobile-first, single column
- Desktop: centered content column with whitespace on sides
- No sidebar — focus on reading

## 4. Features & Interactions

### Core Features

**Markdown Rendering**
- Full GFM (GitHub Flavored Markdown) support
- Syntax highlighting for code blocks
- Blockquotes, tables, task lists
- Images with lazy loading

**Post Management**
- Posts stored as `.md` files in `/src/content/blog/`
- Frontmatter fields: `title`, `description`, `pubDate`, `tags`, `draft`
- Draft posts excluded from production build
- Slug derived from filename

**Reading Time**
- Auto-calculated from word count
- Displayed in metadata (e.g., "5 min read")

**Tags**
- Posts can have multiple tags
- Tag archive pages at `/tags/[tag]`

### Interactions

**Navigation**
- Header with blog name (links to home) and About link
- Smooth scroll behavior
- Active state on current page

**Links**
- Underlined on hover
- Accent color
- External links open in new tab

**Code Blocks**
- Copy button on hover
- Language label displayed

### Edge Cases
- Empty blog: show "No posts yet" message
- Missing frontmatter: fallback to filename as title
- Future dated posts: draft status, excluded

## 5. Component Inventory

### Header
- Blog title (left)
- Navigation: About (right)
- Sticky on scroll with subtle shadow
- States: default, scrolled (shadow appears)

### Post Card (on homepage list)
- Title (linked, hover: accent color)
- Date (secondary color)
- Reading time (secondary color)
- Tags (small pills, if present)
- States: default, hover (title shifts to accent)

### Post Content
- Prose styling: proper heading hierarchy, paragraph spacing
- Blockquotes: left border, italic
- Images: full-width, rounded corners
- Links: accent color, underline on hover
- Code inline: background tint, monospace
- Code blocks: dark background, syntax colors

### Tag Pill
- Small rounded rectangle
- Surface background, secondary text
- Hover: accent background, white text

### Footer
- Simple copyright text
- "Built with Astro" mention

## 6. Technical Approach

### Framework & Build
- **Astro 4.x** with content collections
- **@astrojs/mdx** for MDX support (optional, stretch goal)
- Static site generation (SSG)

### Content Structure
```
src/
├── content/
│   └── blog/
│       ├── first-post.md
│       └── second-post.md
├── layouts/
│   ├── BaseLayout.astro
│   └── PostLayout.astro
├── pages/
│   ├── index.astro
│   ├── about.astro
│   ├── 404.astro
│   ├── blog/
│   │   └── [...slug].astro
│   └── tags/
│       └── [tag].astro
├── components/
│   ├── Header.astro
│   ├── Footer.astro
│   ├── PostCard.astro
│   └── TagPill.astro
└── styles/
    └── global.css
```

### Frontmatter Schema
```yaml
---
title: "Post Title"
description: "Brief description for SEO and post cards"
pubDate: 2024-01-15
tags: ["astro", "markdown"]
draft: false
---
```

### Dependencies
- `astro` (core)
- No additional UI libraries — vanilla Astro + CSS

### Performance Targets
- Lighthouse: 100/100/100/100
- First Contentful Paint: < 1s
- Zero JavaScript on homepage and post pages (except可能的copy button)