## Wrap-Up & Next Steps
---
## What You Learned
Module	Key Skill
Getting Started	Install Claude Code, connect to Bedrock, verify setup
1. Core Workflows	The agentic loop: Explore → Plan → Implement → Verify → Deploy
2. Memory & Extensions	CLAUDE.md, rules, skills, hooks, and subagents teach Claude your project
3. Integrations & Plugins	MCP connects Claude to external services; plugins package extensions
4. Orchestration at Scale	Non-interactive scripting, parallel worktrees, agent teams, and dynamic workflows
5. Capstone Project	Full SDLC with Claude Code: requirements → design → build → test → deploy

---
### Best Practices Recap
Start Simple, Build Incrementally
Don't configure everything up front. Add configuration when you see a trigger:
[TODO:Table]
Trigger	                                                Action
Claude gets a convention wrong twice	                  Add it to CLAUDE.md
You keep typing the same prompt	                        Save it as a skill
You paste data Claude can't see	                        Connect an MCP server
A side task floods your context	                        Route it through a subagent
Something must happen every time	                      Write a hook
A second repo needs the same setup	                    Package as a plugin
Different file types need different rules	              Use .claude/rules/ with path scoping

---

### **Context is Your Most Important Resource**

* /clear between unrelated tasks
* Use subagents for research (keeps your main context clean)
* After two failed corrections, start fresh with a better prompt
* Keep CLAUDE.md under 200 lines — bloated files get ignored
* Use /context to check what's consuming space

---

### **Always Verify**
* Give Claude tests to run
* Have Claude show evidence, not just assert success
* Use /code-review for an unbiased review of changes
* A fresh context catches things the implementation context missed

---

### **Continue Learning**

#### **Official Documentation**


[Claude Code Overview |https://code.claude.com/docs/en/overview]
[Best Practices|https://code.claude.com/docs/en/best-practices]
[Common Workflows |https://code.claude.com/docs/en/tutorials]
[Memory & CLAUDE.md |https://code.claude.com/docs/en/memory]
[Skills |https://code.claude.com/docs/en/skills]
[Hooks |https://code.claude.com/docs/en/hooks]
[MCP |https://code.claude.com/docs/en/mcp]
[Dynamic Workflows |https://code.claude.com/docs/en/workflows]
[Claude Code on Amazon Bedrock |https://code.claude.com/docs/en/amazon-bedrock]


---

### **Key Commands Reference**
**Command	Purpose** [TODO:Table]

claude	Start Claude Code
claude -p "prompt"	Non-interactive mode (scriptable)
claude --continue	Resume last session
claude --resume	Pick a session from a list
claude --worktree <name>	Isolated parallel session
/init	Generate starter CLAUDE.md
/clear	Reset context
/compact	Summarize context to free space
/context	Check context window usage
/memory	View/toggle auto-memory
/mcp	Check MCP server connections
/plugin	Browse plugin marketplace
/hooks	View configured hooks
/agents	Manage subagents
/workflows	Manage dynamic workflow runs
/deep-research <question>	Built-in research workflow
/code-review	Review current diff
Shift+Tab	Toggle plan mode
Esc	Stop Claude mid-action
!command	Run bash without leaving Claude

---
### Feedback

We'd love to hear how this workshop worked for you. Please share:

What worked well
What could be improved
Features you'd like to explore further


---
### Thank You
You've progressed from running your first Claude Code command to orchestrating parallel workflows at scale. The skills you learned today apply to any codebase, any language, and any development workflow.

The most important takeaway: Claude Code gets better the more you invest in teaching it your project. Start with CLAUDE.md, add skills and hooks as patterns emerge, and let the tool compound in value over time.

