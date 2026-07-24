## Extensions

Extensions let you add reusable workflows, automated actions, and specialized agents to Claude Code. They go beyond what CLAUDE.md can do — from on-demand knowledge to guaranteed automation.
---

## What Are Skills?

Skills are the unified extension system in Claude Code. Each skill is a markdown file with two parts: **frontmatter** that tells Claude when to use it, and **instructions** that tell Claude what to do. They serve a dual purpose:

* Reusable knowledge that Claude auto-discovers when relevant
* Slash commands that you trigger explicitly with /name

---

### **Skill File Format**

Each skill has a SKILL.md file with YAML frontmatter that controls behavior, followed by markdown instructions:

```
---
name: code-review
description: "Review code changes for bugs, edge cases, and improvements"
user-invocable: true
argument-hint: "<file-or-directory>"
allowed-tools:
  - Read
  - Grep
  - Glob
---

Review the code in $ARGUMENTS for:

1. Bugs or logic errors
2. Missing edge case handling
3. Security concerns (injection, exposed secrets)
4. Performance issues
5. Readability and naming

For each issue found, provide:
- The file and line reference
- What the problem is
- A suggested fix

If no issues are found, confirm the code looks good.
```

### Key frontmatter fields: [TODO: Table]

Field	        |    Purpose
name	                 Skill identifier (used as /name for slash commands)
description	           When to trigger — Claude uses this for auto-discovery
user-invocable	       If false, hidden from / menu (default: true — all skills are user-invocable)
argument-hint	          Hint text shown after /name in autocomplete
allowed-tools	          Restrict which tools the skill can use
context	                Set to fork to run in an isolated subagent context
disable-model-invocation	  If true, skill only triggers when you invoke it manually
$ARGUMENTS	                Special variable — replaced with user input after /name

---

#### Skills Directory Structure

Skills are directory-based packages. The SKILL.md is the entrypoint, but the directory can contain templates, schemas, scripts — anything the skill needs:

```
.claude/
└── skills/
    └── generate-api/
        ├── SKILL.md              # Entrypoint — instructions + frontmatter
        ├── route-template.py     # Template Claude uses as a starting point
        ├── openapi-schema.yaml   # Reference schema Claude checks against
        └── examples/
            ├── good-route.py     # Well-structured example
            └── bad-route.py      # Anti-pattern to avoid
```

This makes skills more powerful than prompts — they bundle instructions plus reference materials plus templates as self-contained, portable units.

Where skills live: [TODO : Table]

Location	    |    Scope
`.claude/skills/` (repo root)	Team-shared, committed to git
`~/.claude/skills/`           Personal, all projects
Bundled (ships with Claude Code)  	Available everywhere


#### **Bundled Skills**

Claude Code ships with bundled skills available in every project:

Skill	Description [TODO: Table]

/code-review	            Review current diff for bugs in a fresh subagent
/batch	                  Process multiple files or operations in a single pass
/debug	                  Systematic debugging workflow with root cause analysis
/loop	                    Iterative refinement loop — repeat until quality criteria met
/simplify	                Reduce complexity — fewer lines, clearer logic
/claude-api	              Generate code that calls the Anthropic API


#### **Modes of Skill Invocation**

Skills activate in two ways: [TODO: TAble]

Auto-Discovered (Passive)	| User-Invoked (Active)
Claude reads the skill's description field	          Developer types /skill-name to trigger
Loads automatically when working context matches	    `user-invocable: true` must be set
No user action required	                               $ARGUMENTS captures text after the command
Good for: conventions, standards, patterns	            Good for: workflows, multi-step processes


Many skills work in both modes. A deploy skill can auto-discover when Claude notices you're making deployment-related changes, and also be explicitly invoked with /deploy.

### **Dynamic Context with !command**

Skills can inject dynamic information into their instructions using !`command` syntax. The command runs at skill-load time and its output is substituted inline:

```
---
name: review-changes
description: Review recent code changes
user-invocable: true
---
Review the following changes:

!`git diff --stat`

Focus on: correctness, edge cases, and test coverage.
```

When invoked, !`git diff --stat` is replaced with the actual diff output before Claude reasons over it — making the skill adaptive to current project state.

---

### **Create a Coding Standards Skill**

Let's create a coding standards skill for the shopping cart project that demonstrates both static instructions and dynamic context injection.

This skill provides coding standards for all components, with API-specific standards pulled from a documentation file in the project.

**Step 1** — Create the skill directory:
```
!mkdir -p .claude/skills/coding-standards
```

**Step 2 —** Ask Claude to write the SKILL.md file:

```
Create a SKILL.md file at .claude/skills/coding-standards/SKILL.md with the following content:

---
name: coding-standards
description: "Apply project coding standards when writing or reviewing code"
user-invocable: true
argument-hint: "<file-or-directory>"
---

# Coding Standards

## General (all components)
- All functions must have docstrings explaining purpose and parameters
- Use descriptive variable names — no single-letter variables except loop counters
- Handle errors explicitly — never silently swallow exceptions
- Keep functions under 50 lines — extract helpers for complex logic

## Frontend (React)
- Components use PascalCase for filenames and function names
- State management through context — no prop drilling beyond 2 levels
- All user-facing text must be in a single constants file
- Use CSS classes from styles.css — no inline styles

## Backend (Python/Flask)
- Follow REST conventions: GET for reads, POST for creates, PUT for updates, DELETE for removes
- Return consistent JSON structure: { "data": ... } for success, { "error": "..." } for failure
- Validate all input before processing
- Never access DynamoDB directly — always go through the models layer

## API Standards (from project documentation)

!`cat docs/api-standards.md 2>/dev/null || echo "No api-standards.md found — use general REST conventions above"`

---

Apply these standards when reviewing or writing code in: $ARGUMENTS

After applying standards, provide a summary:
- List each file that was modified
- For each file, note which standards were applied
- Flag any areas that could not be fixed automatically
```

### **What this demonstrates:**

* Static standards for frontend and backend are written directly in the SKILL.md
* Dynamic context via !`cat docs/api-standards.md` pulls in API-specific standards from a project file at runtime
* If the file doesn't exist yet, it gracefully falls back to a message
* The skill works both ways: Claude auto-discovers it when writing code, or you invoke /coding-standards backend/ to review a directory


#### **Demo — invoke it:**

Restart Claude to pick up the new skills directory, then invoke:

```
/exit
claude --continue
```
```
/coding-standards backend/services/
```

Claude reviews the backend services against the standards defined in the skill.

### **Hooks — Deterministic Automation**

Hooks are shell commands that execute automatically at specific points in Claude's lifecycle. Unlike CLAUDE.md (advisory), hooks are guaranteed to execute.

### **Hook Events**  [Todo: Table]


Event	              When it fires
PreToolUse	        Before a tool call executes — can block it
PostToolUse	        After a tool call succeeds
UserPrompt          Submit	When you submit a prompt, before Claude processes it
Stop	              When Claude finishes responding — can force it to continue
Session             Start	When a session begins


### **Configuration Format**

Hooks are defined in `.claude/settings.json` with three levels: event → matcher → handler:

```
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx eslint --fix $FILE"
          }
        ]
      }
    ]
  }
}
```

This runs ESLint after every file write or edit. The matcher filters by tool name (e.g., Bash, Write, Edit, Read).

### **Common Patterns** [TODO : Table]

Pattern	                      Event + Matcher	          What it does
Auto-lint after edits	        PostToolUse + Write|Edit	Run linter on changed files
Block destructive commands	  PreToolUse + Bash	        Deny rm -rf commands
Run tests before stopping	    Stop                    	Prevent Claude from finishing until tests pass



### **Create a Hook**

Let's create a hook that blocks destructive rm -rf commands, then test it.

**Step 1** — Add the hook to `.claude/settings.json`:

Add the following hooks configuration to `.claude/settings.json`:

```
"hooks": {
  "PreToolUse": [
    {
      "matcher": "Bash",
      "hooks": [
        {
          "type": "command",
          "command": "if grep -q 'rm -rf'; then echo 'Blocked: rm -rf is not allowed' >&2; exit 2; fi"
        }
      ]
    }
  ]
}
```

**Step 2** — Verify the hook is loaded:

```
/hooks
```

You should see the new PreToolUse hook listed.

**Step 3** — Test it by asking Claude to run a blocked command:

```
Run rm -rf /tmp/test in the terminal
```

**Observe:** Claude attempts the command but the hook blocks it before execution. You'll see "Blocked: rm -rf is not allowed". The command never runs.

---

### **Custom Subagents — Isolated Workers**

Subagents are specialized AI assistants that handle specific tasks in their own context window. Use them when a side task would flood your main conversation with file reads, logs, or search results — the subagent does that work separately and returns only the summary.

#### **Built-in Subagents**

Claude Code includes three built-in subagents:

Subagent	              Model	              Purpose
Explore	            Haiku (fast)          	Read-only codebase search and analysis
Plan	              Inherits	              Research for plan mode
general-purpose	    Inherits              	Complex multi-step tasks requiring exploration and action

Claude automatically delegates to these based on the task.

### **Create a Custom Subagent**

Subagent files are markdown with YAML frontmatter. Store them in:

**Location	        Scope**
`.claude/agents/`	  This project (committed to git)
`~/.claude/agents/`	All your projects (personal)



### **Key frontmatter fields:** [TODO: Table]

Field	      Purpose
name	      Unique identifier
description	When Claude should delegate to this subagent
tools	      Which tools it can use (inherits all if omitted)
model	      `sonnet, opus, haiku, or inherit`
memory	     Persistent memory scope: user, project, or local




#### **Create a Subagent for the Shopping Cart**

**Step 1 —** Create the agents directory and ask Claude to write the agent file:
```
!mkdir -p .claude/agents
```

```
Create a file at .claude/agents/security-reviewer.md with the following content:

---
name: security-reviewer
description: Reviews code for security vulnerabilities. Use proactively when working on APIs or user input handling.
tools: Read, Grep, Glob, Bash
model: sonnet
---
You are a senior security engineer. When invoked, analyze the code for:
- Injection vulnerabilities (SQL, XSS, command injection)
- Missing input validation
- Exposed secrets or API keys
- Insecure data handling
- Authentication and authorization flaws

Provide specific file and line references with suggested fixes.
```

**Step 2 —** Restart to pick up the new agents directory, then verify:

```
/exit
claude --continue
```
```
/agents
```

**Step 3** — Use it:
```
Use the security-reviewer subagent to review the backend routes for vulnerabilities
```
**Observe:** Claude delegates to the subagent, which runs in its own context, reads the route files, analyzes them for security issues, and returns a summary of findings to your main conversation — without cluttering your context with all the file reads.

---

### **Key Takeaways**

* Skills are reusable knowledge + workflows that load on demand or via slash commands
* Bundled skills (/debug, /loop, /simplify) are available out of the box
* Skills can bundle templates, scripts, and reference files in their directory
* Use !command! for dynamic context that adapts to project state
* Hooks guarantee actions happen every time (not advisory like CLAUDE.md)
* Custom subagents isolate specialized tasks in their own context
* Build your setup incrementally: add extensions when Claude repeats a workflow
