## Memory

---
An unconfigured Claude Code session is like hiring a brilliant engineer and giving them no onboarding — tremendous capability, zero project context. Without configuration, you spend time every session re-explaining your project structure, coding style, and conventions.

`CLAUDE.md` solves this. It's a plain markdown file that Claude reads automatically at the start of every session. Whatever you put in this file becomes part of Claude's working knowledge.

---

### **Where CLAUDE.md Lives**

Claude Code supports CLAUDE.md files at multiple levels: [TODO: Table]

Tier	    |  Location	            |  Purpose                                        |	Git
Global	~/.claude/CLAUDE.md	  Your personal preferences across all projects	    Not in any repo
Project	  ./CLAUDE.md (repo root)	  Team-shared standards. This is where 90% of the value lives.	  Committed
Local	    ./CLAUDE.local.md (repo root)	  Personal overrides for this project only	    Gitignored
Subdirectory	./src/CLAUDE.md	      Loaded when Claude works in that directory	            Committed


More specific wins: if your global CLAUDE.md says "use tabs" but the project CLAUDE.md says "use 2-space indentation," the project rule wins.

---

### What Goes in Project CLAUDE.md
A well-structured project CLAUDE.md has four sections:

#### **Commands**

The highest-value section. Saves Claude from reading config files every session.

```
## Commands
./deploy.sh          # build and deploy backend + frontend to AWS
cd backend && pytest # run tests
cd frontend && npm run build  # build frontend
```


#### **Architecture**

How the codebase is organized. Prevents Claude from exploring the directory tree every session.

```
## Architecture
Flask REST API + React frontend on AWS serverless.

Key layers:
- backend/routes/     — API endpoint handlers
- backend/services/   — Business logic
- backend/models/     — DynamoDB data access
- frontend/src/pages/ — React page components
- frontend/src/services/api.js — All HTTP calls to backend
```

#### **Data Model**

Entity names and fields. Prevents Claude from hallucinating schema fields that don't exist.
```
## Data Model
Products: productId, name, price, category, image, description
Cart: sessionId, productId, name, price, image, quantity
Orders: sessionId, orderId, items, subtotal, tax, total, shippingAddress, status, createdAt
```

#### **Intentional Decisions**

The section most teams miss. Without it, Claude will "fix" things that look wrong but are deliberate.

```
## Intentional Design Decisions
- Fixed session ID "workshop-user" — no auth needed for workshop
- Tax hardcoded at 8% — intentional simplicity
- Products seeded on first API call — no separate seed script
- No test framework pre-configured — tests are added as a workshop exercise
```

---

### Build and Test Your CLAUDE.md

Now that you know what goes in a CLAUDE.md, let's create one for the shopping cart project and see it in action.

#### **Step 1 — Create CLAUDE.md with /init**

```
/init
```

Claude scans the source files and project structure, then generates a `CLAUDE.md` with commands, architecture, and conventions it discovers. Review what it created.


#### **Step 2 — Refine it with a specific rule**

The generated file is a good start, but let's add an explicit code style rule:

```
Add the following to our CLAUDE.md under a "Code style" section: All Python functions MUST have a docstring, use snake_case for all function names, always add type hints to function parameters and return values.
```


#### **Step 3 — Verify it works**

Ask Claude to add a function and observe that it follows the new rules:

```
Add a helper function to calculate shipping cost in cart_service.py
```

**Observe:** Claude adds a docstring, uses snake_case, and includes type hints — because it read the updated CLAUDE.md.

---

### **Growing CLAUDE.md Over Time**

Your first CLAUDE.md should be 10-20 lines — just the essentials. Then let it grow from real usage:

1. Claude makes a mistake
2. You correct it
3. You add a rule to CLAUDE.md so it never happens again
4. 
After a few sessions of active development, your CLAUDE.md will contain exactly the rules that matter — every one earned from a real correction, no speculative bloat.

Keep It Under 200 Lines
CLAUDE.md is loaded in full at session start. Longer files consume more context and reduce adherence. If Claude already does something correctly without the instruction, cut it.

---

### **The .claude/ Directory**

CLAUDE.md is not the only configuration file. The .claude/ directory at your project root is the complete extension system:

```
<repo root>/
├── CLAUDE.md                   # Project instructions (committed)
├── CLAUDE.local.md             # Personal overrides (gitignored)
└── .claude/
    ├── settings.json           # Permissions & config
    ├── settings.local.json     # Personal settings (gitignored)
    ├── rules/                  # Path-scoped instructions
    ├── agents/                 # Specialist sub-agent definitions
    ├── skills/                 # Reusable slash commands
    └── hooks/                  # Lifecycle automation scripts
```

### Rules
CLAUDE.md loads on every session. But what about instructions that only matter for specific files? You don't want testing conventions consuming context when Claude is working on route handlers.

The `.claude/rules/` directory solves this. Place markdown files here — each covering one topic, with a descriptive filename like `testing.md` or `api-design.md.` Rules without frontmatter load on every session (like CLAUDE.md). Rules with a `paths` field only load when Claude works on matching files.

**Example:** A rule that only applies to React frontend components:

```
---
paths:
  - "frontend/**/*.jsx"
---
# React Component Standards
- Use functional components only — no class components
- Destructure props in the function signature
- Keep components under 100 lines — extract sub-components for complex UI
- No inline styles — use CSS classes from styles.css
```

When Claude opens or edits a file matching frontend/**/*.jsx, this rule loads. When Claude is working on a Python backend file, it stays out of context — saving tokens and keeping instructions relevant.

---

When to use rules vs CLAUDE.md: [TODO: Table]

Use CLAUDE.md when...                           |  	Use .claude/rules/ when...
The instruction applies to every session	        The instruction only applies to certain files
It's a project-wide convention	                  Different file types have different standards
It's a key command (test, build, deploy)	        Loading it for every session wastes context



### Create a Rule

Step 1 — Create a rule for React frontend components:

```
Create a .claude/rules/react-components.md file with paths scoped to "frontend/**/*.jsx". Add rules: use functional components only, destructure props in the function signature, keep components under 100 lines, and no inline styles.
```

Step 2 — Verify it activates:

```
Read frontend/src/components/ProductCard.jsx and suggest improvements based on the project's coding standards.
```

**Observe:** Claude should apply the React component standards from the rule. If you then ask Claude to work on a backend Python file, it won't mention functional components or inline styles — the rule only activates for frontend .jsx files.

#### **Permissions in settings.json**
While CLAUDE.md controls what Claude knows, settings.json controls what Claude can do. The permissions key defines allow/deny rules using the format Tool(specifier) where specifier is a glob pattern.

```
{
  "permissions": {
    "allow": [
      "Read(**)",
      "Edit(backend/**)",
      "Bash(pytest*)",
      "Bash(git status)"
    ],
    "deny": [
      "Edit(.env*)",
      "Bash(rm -rf*)"
    ]
  }
}
```

#### **How it works:**
[TODO: Table]

Level	    |      Behavior
allow	        Claude proceeds without asking
ask (default)	Claude stops and asks for permission
deny	        Claude cannot proceed — even if you approve. Hard guardrail.


Deny always wins over allow. If you allow Read(**) but deny Read(.env*), the .env files are blocked.

#### **Test Permission Guardrails**

**Step 1** — Ask Claude to configure a deny rule:
```
Add a deny rule to .claude/settings.json that blocks editing any file in the frontend/ folder. Keep the existing settings.
```

**Step 2** — Test the denied operation:
```
Edit frontend/src/App.jsx and add a comment at the top
```

**Observe:** Claude refuses — it cannot edit frontend files because of the deny rule. This is a hard guardrail enforced by the platform, not a suggestion Claude can override.

We'll build this directory out in the next section — Extensions.

---

### Auto-Memory

Claude Code also has auto-memory — a separate system from CLAUDE.md. While CLAUDE.md contains instructions you write, auto-memory contains notes Claude writes itself based on your corrections and preferences. Auto-memory is enabled by default.

Check if auto-memory is enabled
```
/memory
```

You should see Auto-memory: on at the top of the menu. If it shows off, toggle it from this menu.

#### Trigger a correction

Make a correction during your session — Claude will remember it for future sessions:

```
Remember: in this project, always use camelCase for variable names in Python, not snake_case.
```

See where it was saved
Exit Claude and check the memory file directly:

```
/exit
```
```
cat ~/.claude/projects/*/memory/MEMORY.md
```
It's plain markdown — you can edit or delete entries anytime.
