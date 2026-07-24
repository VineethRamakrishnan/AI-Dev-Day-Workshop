### Module 1: Core Workflows

Learn the fundamental patterns for working with Claude Code — from exploring codebases to shipping code.

Claude Code is an agentic coding tool that reads your codebase, edits files, runs commands, and integrates with your development tools. Available in your terminal, IDE, desktop app, and browser.

It understands your entire project context and works across multiple files to build features, fix bugs, and automate development tasks.

---
The Agentic Loop

When you give Claude a task, it works through three phases that blend together. Claude decides what each step requires based on what it learned from the previous step, chaining dozens of actions together and course-correcting along the way.

<img width="1830" height="720" alt="image" src="https://github.com/user-attachments/assets/6bb84e8d-e425-4362-a342-12f7c030c1ba" />


Source: [How Claude Code works — Anthropic ](https://code.claude.com/docs/en/how-claude-code-works#the-agentic-loop)

#### Phase 1: Gather Context
Claude starts by understanding the problem. It searches files, reads documentation, traces code paths, and builds a mental model of the relevant parts of your codebase. A simple question might end here. A complex task uses this phase to inform what comes next.

#### Phase 2: Take Action
With context in hand, Claude makes changes — editing files, running commands, installing dependencies, or creating new files. It plans its approach and executes, adapting as it goes.

#### Phase 3: Verify Results
Claude checks its own work by running tests, builds, or linters. If something fails, it loops back to gather more context and take corrective action. A bug fix might cycle through all three phases repeatedly until tests pass.

---

The loop adapts to the task at hand. A question about your codebase might only need context gathering. A bug fix cycles through all three phases repeatedly. A refactor might involve extensive verification.

You're part of this loop too. You can interrupt at any point to steer Claude in a different direction, provide additional context, or ask it to try a different approach. Claude works autonomously but stays responsive to your input.

**Key Mental Shift**
You describe what you want. Claude figures out how to build it — exploring, planning, and implementing autonomously.


The Shopping Cart Project
Your workshop environment comes pre-loaded with a Shopping Cart application — a full-stack serverless app built with Flask (backend) and React (frontend), deployed on AWS using API Gateway, Lambda, and DynamoDB.

The project is located at /workshop/shopping-cart and your terminal starts there automatically.

This sample project will be used throughout the module to demonstrate Claude Code features in action.
