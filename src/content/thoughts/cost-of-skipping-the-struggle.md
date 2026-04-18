---
title: "Cost of Skipping the Struggle"
description: "The cost of skipping the struggle in the age of artificial intelligence"
pubDate: 2026-04-15
tags: ["Software Development", "Artificial Intelligence"]
---

# Cost of Skipping the Struggle

Every productivity gain in engineering moves a cost somewhere else. 
Everything in engineering is a tradeoff.

That was true before AI, it is true with AI, and it will still be true after the current AI wave settles down. This is not just a tooling trend. It is a general constraint of engineering. You do not get leverage from nowhere. In physics, energy is transformed, not created. In software, the same principle shows up as tradeoffs: if a tool removes cost in one place, it usually moves that cost somewhere else.

AI coding agents are no exception.

They can reduce the time it takes to produce code, explore implementation paths, scaffold solutions, or get unstuck. That is real leverage. But it is not magic. If code is generated faster and at greater scale, some other part of the system absorbs the cost. Sometimes its the price you pay for the tool. Sometimes that cost shows up in review. Sometimes in debugging. Sometimes in architecture drift. Sometimes in overconfidence. And sometimes it shows up inside the engineer, as less time spent building the mental models that make future work easier.

So the question is not whether AI creates value. Of course it does.

The better question is: where does the cost move?

If an agent saves me thirty minutes of typing, that is usually a great trade.
If it saves me the hour in which I would have learned how the system actually works, that is a different trade entirely.

That is the tradeoff I think we are still underestimating.

My current view is simple: AI coding agents probably improve short-term throughput, but they can also reduce the exact kind of effortful exploration that builds durable understanding of a codebase.

This is not an anti-AI argument.
It is a learning argument.

## What developers call “FAFO” has real learning theory behind it
A lot of developers have felt this without using academic language for it.

You grep around.
You trace the wrong call path.
You inspect a weird stack trace.
You click through ten files that turn out not to matter.
You finally find the bug.
Then, while fixing it, you discover a second problem that was hiding nearby.

That process feels messy, but it is not empty work.

What developers casually call “the more you f*ck around, the more you find out” often looks a lot like what learning science calls generation, retrieval, and productive struggle. Decades of research suggest that effortful processing during learning often produces better long-term retention and transfer than smooth, low-friction performance [1][2]. The generation effect shows that people remember information better when they generate it themselves rather than simply receiving it [3]. Even unsuccessful retrieval attempts can improve later learning [4]. Kapur’s work on productive failure makes a related point: struggling with a problem before seeing the answer can improve deeper understanding and transfer [5].

That maps surprisingly well to debugging.

Manually locating a bug forces you to generate hypotheses.
Manually tracing control flow forces you to retrieve prior knowledge.
Manually navigating a codebase forces you to separate signal from noise.

Classic program-comprehension research showed that developers build internal representations of code structure and behavior as they work through software [6]. Later work on software evolution showed that programmers constantly ask questions like:

* where is this behavior implemented?
* what can reach this state?
* what else will this change break?
* what assumptions are encoded here?
Those questions are not overhead. They are the work of understanding a system [7][8].

This matters because expert debugging is rarely just “read the error and patch the line.” It depends on having a useful internal map of:

* data flow
* control flow
* ownership boundaries
* invariants
* hidden dependencies
* failure hotspots
* which modules are trustworthy and which ones are haunted
* Manual exploration builds that map.

If an AI agent jumps from bug report to plausible patch, you may still get the immediate fix, but you skip a lot of the map-building that would have made the next five bugs easier.

And in mature systems, the map is often more valuable than the patch.

## Better system knowledge produces better AI use

There is another tradeoff here that people miss: AI output quality depends heavily on the quality of the human using it.

If you know the system and the codebase better, you prompt better.
You scope better.
You constrain better.
You notice bad assumptions faster.
You reject fake confidence earlier.
You ask for the right abstraction level.
You can tell the difference between a patch that merely passes and a patch that actually fits the system.

That is why two engineers using the same agentic tool can get very different results.

Put a junior and a senior side by side with tools like Codex, Claude Code, or OpenCode, and they are not really using the same system. They are using the same interface, but they are bringing very different internal models to it. The senior engineer usually has a stronger sense of where to look, what to ask, what constraints matter, which parts of the answer are suspicious, and how the proposed change will interact with the rest of the codebase.

In an MVP phase, those differences may be harder to notice. The system is smaller, the dependencies are shallower, and “good enough” often goes a long way. But as the project grows, the gap widens.

In larger, older, and more complex codebases, results depend less on raw code generation and more on context: architecture, invariants, side effects, domain rules, operational constraints, and the history of previous mistakes. Recent work on brownfield programming tasks found that students completed tasks faster with GitHub Copilot, but also reported concerns about not always understanding how or why the suggestions worked [17]. Related trust research similarly found that even when trust in AI assistants increases, developers still need traditional skills in comprehension, debugging, and testing to verify output [15].

So even if AI becomes dramatically better at producing code, system knowledge does not become less important.
It becomes more leveraged.

The better you understand the codebase, the more value you extract from the tool.
The less you understand it, the easier it is to mistake plausible output for correct output.

## Automation has always had this downside
This is not a new problem invented by LLMs. It is a general automation problem.

Bainbridge’s classic “Ironies of Automation” argued that automation often removes the routine work through which humans build and maintain skill, while still leaving humans responsible for the hardest edge cases [9]. Parasuraman, Sheridan, and Wickens later formalized how different levels of automation change human involvement and skill maintenance [10].

That pattern should sound familiar.

If AI handles syntax lookup, fine.
If AI handles boilerplate, fine.
If AI helps draft tests, probably fine.

But if AI repeatedly handles diagnosis, decomposition, and patch design, the human can slowly become a reviewer of solutions rather than a builder of understanding.

That is where deskilling starts.

## AI agents are also a form of cognitive offloading
Another useful lens is cognitive offloading.

Risko and Gilbert define cognitive offloading as using the external world to reduce internal cognitive demand [11]. That can be smart. Notes, diagrams, search engines, calculators, TODO apps, and dashboards all do this. Offloading is often rational and useful.

But it changes what gets stored internally.

Sparrow, Liu, and Wegner made a similar point in their work on the “Google effect”: when people expect information to remain easily available externally, they tend to remember less of the information itself and more about where to find it [12].

AI coding agents may be doing something similar for software work.

Instead of remembering how this subsystem behaves, I remember that the agent can summarize it.
Instead of learning how to localize this kind of bug, I remember the prompt that usually gets a decent answer.
Instead of building a richer internal model, I build a workflow for external recovery.

That can still be effective.
But it is not the same thing as expertise.

## The evidence for short-term benefits is real
To be clear, the productivity side of the argument is not hype. The current evidence does show real short-term benefits in many contexts.

A 2024 Communications of the ACM study on GitHub Copilot found strong links between Copilot use and perceived productivity across multiple dimensions, with junior developers seeing especially large gains [13]. A 2024 field experiment with developers at Microsoft and Accenture found suggestive evidence that access to Copilot increased weekly pull-request output [14]. In education, a 2025 study on AI-assisted pair programming found gains in student motivation, lower programming anxiety, and stronger programming performance relative to individual work, though human-human pairing still produced richer collaboration and social presence [16].

So the claim cannot be “AI is bad for coding.”
That would be lazy and false.

The better claim is narrower:

AI appears very good at improving immediate performance.
We still know much less about its long-term effects on debugging judgment, codebase understanding, and durable skill formation.

That distinction matters.

Because short-term performance and long-term learning are not the same thing [2].

## The real open question is long-term skill formation
This is the part that matters most to me, and it is also the part where the evidence is still thin.

We have decent evidence that AI can make developers faster.
We have growing evidence that AI can make coding feel easier, more fluid, and more productive [13][14][16].
We also have early evidence that trust calibration matters, and that developers may use the tool successfully without fully understanding why its suggestions work [15][17].

What we do not yet have, at least not at the same level of confidence, is strong long-term evidence on questions like:

* Do heavy users of coding agents get better or worse at root-cause analysis over time?
* Do they build weaker mental models of large systems?
* Does constant outsourcing of diagnosis reduce transfer to unfamiliar debugging tasks?
* My guess is that the answer is not binary.
* Does AI mainly compress the path to expertise, or does it partly replace the practice that expertise depends on?

Some uses of AI compress learning. Some uses replace it.

And the difference depends on which part of the cognitive loop the human still owns.

## Not all struggle is good
There is an important caveat here.

I am not arguing that all friction is educational.

Some struggle is productive.
Some struggle is just thrashing.

Kirschner, Sweller, and Clark made this point years ago in their critique of minimally guided instruction: leaving learners completely unguided is often inefficient and sometimes harmful [18].

The same is true in software.

Spending four hours rediscovering a missing environment variable is not noble.
Clicking random files without a hypothesis is not deep learning.
Manual suffering is not a methodology.

The useful zone is somewhere in the middle:

the human still owns the problem framing
the human does first-pass localization
the human forms hypotheses
the human tests assumptions
the AI helps with recall, summarization, alternative implementations, and boilerplate
That is assisted reasoning.
Not replaced reasoning.

## A practical rule of thumb for using AI without outsourcing understanding
If the task is mostly compression, let AI help aggressively.

Use it for:

* boilerplate
* syntax
* test scaffolding
* summarizing unfamiliar code
* explaining libraries
* generating option lists
* speeding up search and recall
* If the task is mostly cognition, stay in the loop.

Be more careful when the real value of the task is:

* bug localization
* root-cause analysis
* understanding a subsystem
* architectural reasoning
* designing a change in unfamiliar code
* deciding what not to change
* writing the postmortem
* A simple workflow I like is:

1. localize the problem yourself first
2. write down your hypothesis before asking the model
3. ask the model for alternatives, not just the answer
4. verify the result line by line
5. explain the fix in your own words afterward
6. If you skip step 5, there is a good chance you borrowed a result without really purchasing the understanding.

## The core tradeoff
I think AI coding agents are best understood as tools that can trade local productivity for global understanding.

Sometimes that trade is absolutely worth it.
Sometimes it is not.

If your goal is to ship a migration script by noon, take the speed.
If your goal is to become the engineer who can walk into a strange codebase, find the bug, explain the failure mode, and fix it without breaking three adjacent systems, then you probably still need to do more of the wandering, tracing, and debugging yourself.

Because in software, the patch is not the only output.

The engineer is also an output.

---

### References

[1] Bjork, R. A. (1994). Memory and metamemory considerations in the training of human beings. In J. Metcalfe & A. Shimamura (Eds.), Metacognition: Knowing about Knowing. MIT Press.

[2] Soderstrom, N. C., & Bjork, R. A. (2015). Learning versus performance: An integrative review. Perspectives on Psychological Science, 10(2), 176-199.

[3] Slamecka, N. J., & Graf, P. (1978). The generation effect: Delineation of a phenomenon. Journal of Experimental Psychology: Human Learning and Memory, 4(6), 592-604.

[4] Kornell, N., Hays, M. J., & Bjork, R. A. (2009). Unsuccessful retrieval attempts enhance subsequent learning. Journal of Experimental Psychology: Learning, Memory, and Cognition, 35(4), 989-998.

[5] Kapur, M. (2008). Productive failure. Cognition and Instruction, 26(3), 379-424.

[6] Pennington, N. (1987). Stimulus structures and mental representations in expert comprehension of computer programs. Cognitive Psychology, 19(3), 295-341.

[7] Sillito, J., Murphy, G. C., & De Volder, K. (2006). Questions programmers ask during software evolution tasks.

[8] LaToza, T. D., & Myers, B. A. (2010). Developers ask reachability questions.

[9] Bainbridge, L. (1983). Ironies of automation. Automatica, 19(6), 775-779.

[10] Parasuraman, R., Sheridan, T. B., & Wickens, C. D. (2000). A model for types and levels of human interaction with automation. IEEE Transactions on Systems, Man, and Cybernetics Part A, 30(3), 286-297.

[11] Risko, E. F., & Gilbert, S. J. (2016). Cognitive offloading. Trends in Cognitive Sciences, 20(9), 676-688.

[12] Sparrow, B., Liu, J., & Wegner, D. M. (2011). Google effects on memory: Cognitive consequences of having information at our fingertips. Science, 333(6043), 776-778.

[13] Ziegler, A., Kalliamvakou, E., Li, X. A., Rice, A., Rifkin, D., Simister, S., Sittampalam, G., & Aftandilian, E. (2024). Measuring GitHub Copilot’s impact on productivity. Communications of the ACM, 67(3), 54-63. https://doi.org/10.1145/3633453

[14] Cui, K. Z., Demirer, M., Jaffe, S., Musolff, L., Peng, S., & Salz, T. (2024). The productivity effects of generative AI: Evidence from a field experiment with GitHub Copilot. https://doi.org/10.21428/e4baedd9.3ad85f1

[15] Shah, A., Rexin, T., Tomson, E., Porter, L., Griswold, W. G., & Soosai Raj, A. G. (2025). Evolution of programmers’ trust in generative AI programming assistants. Proceedings of the 25th Koli Calling International Conference on Computing Education Research. https://doi.org/10.1145/3769994.3770029

[16] Fan, G., Liu, D., Zhang, R., & Pan, L. (2025). The impact of AI-assisted pair programming on student motivation, programming anxiety, collaborative learning, and programming performance: A comparative study with traditional pair programming and individual approaches. International Journal of STEM Education, 12, Article 16. https://doi.org/10.1186/s40594-025-00537-3

[17] Shihab, M. I. H., Hundhausen, C., Tariq, A., Haque, S., Qiao, Y., & Mulanda, B. (2025). The effects of GitHub Copilot on computing students’ programming effectiveness, efficiency, and processes in brownfield programming tasks. Proceedings of the ACM Conference on International Computing Education Research. https://doi.org/10.1145/3702652.3744219

[18] Kirschner, P. A., Sweller, J., & Clark, R. E. (2006). Why minimal guidance during instruction does not work. Educational Psychologist, 41(2), 75-86.