
## Plugins
---
Plugins bundle skills + agents + hooks + MCP servers into a single installable unit. They're how you share and reuse configurations across repositories and teams.
---

### **Official Marketplace**

The official Anthropic marketplace is automatically available when you start Claude Code. Browse at claude.com/plugins  or from within a session:
```
/plugin
```
Go to the Discover tab to browse available plugins.
---

### **Installing Plugins**

Install from the command line or the interactive UI:
```
/plugin install github@claude-plugins-official
```

Choose an installation scope: [TODO : Table]

Scope	                  Who gets it
User (default)	        You, across all projects
Project                	Everyone who clones the repo
Local	                  You, this project only


After installing, activate with:

```
/reload-plugins
```

---

## **What Plugins Provide**

A single plugin might include:

* Skills: Domain knowledge and workflows
* Agents: Specialized workers
* Hooks: Automated actions
* MCP servers: External connections

All configured and ready to use with one install. Skills from plugins are namespaced with the plugin name to avoid conflicts:

```
/commit-commands:commit    # the "commit" skill from the "commit-commands" plugin
/my-plugin:review          # the "review" skill from "my-plugin"
```


#### **Plugin Categories**

Category            	Examples
Code intelligence	      pyright-lsp, typescript-lsp, gopls-lsp
External integrations	  github, slack, atlassian, notion, sentry
Security	              security-guidance — reviews changes for vulnerabilities
Development workflows	  commit-commands, pr-review-toolkit

---

### **Create a Simple Plugin**

You can create custom plugins to extend Claude Code with your own skills, agents, hooks, and MCP servers. A plugin is a directory with a manifest file and your components — once created, it can be shared with teammates or published to a marketplace. Let's create one to see how it works.

**Step 1** — Create the plugin structure:
```
mkdir -p deploy-plugin/.claude-plugin
mkdir -p deploy-plugin/skills/deploy-check
```

**Step 2** — Create the manifest (deploy-plugin/.claude-plugin/plugin.json):

```
Create a plugin manifest at deploy-plugin/.claude-plugin/plugin.json with name "deploy-plugin", description "Deploy and verify the shopping cart application", and version "1.0.0"
```

**Step 3** — Create the skill (deploy-plugin/skills/deploy-check/SKILL.md):

```
Create deploy-plugin/skills/deploy-check/SKILL.md with the following content:

---
name: deploy-check
description: "Deploy the shopping cart app and verify it's working"
user-invocable: true
allowed-tools:
  - Bash
  - Read
---

Deploy and verify the shopping cart application:

1. Run ./deploy.sh from the project root
2. Wait for the deployment to complete
3. Report the API URL and Frontend URL from the output
4. Test the /api/products endpoint to confirm it returns data
5. Report success or failure with details
```

**Step 4** — Test the plugin locally:
```
/exit
claude --plugin-dir ./deploy-plugin
```

**Step 5** — Invoke the skill:

```
/deploy-check
```

**Observe:** Claude runs the deploy workflow, tests the API, and reports the result. The skill is namespaced with the plugin name to avoid conflicts with other plugins.

The plugin structure on disk:

```
deploy-plugin/
├── .claude-plugin/
│   └── plugin.json        # Manifest (name, description, version)
└── skills/
    └── deploy-check/
        └── SKILL.md       # Skill instructions
```

This same pattern scales to include agents, hooks, MCP servers, and more — all bundled into a single shareable unit.

### **Key Takeaways**
* Plugins package skills, agents, hooks, and MCP servers into reusable units
* The official marketplace is auto-available — browse with /plugin
* Use /reload-plugins to activate newly installed plugins
* Features compose: plugins + MCP + skills work together
