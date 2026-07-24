## MCP

---
The first few systems — skills, hooks, and subagents — all operate within Claude Code's local environment. MCP is the bridge to the outside world. It's a standardized protocol that lets Claude interact with external services — databases, GitHub, Slack, AWS, custom internal APIs — through a consistent tool interface.

Think of MCP as USB-C for AI tools. Before USB-C, every device had its own connector. Before MCP, every AI-tool integration required custom code. MCP provides a standard interface: any service that implements the MCP protocol becomes a tool Claude can discover and use automatically.
---

### **What MCP Servers Provide**

An MCP server exposes three types of capabilities: [TODO : Table]

Capability	            Description	                              Example
Tools	          Functions Claude can call	                      create_issue, query_database, search_docs
Resources	      Data Claude can read via @ mentions	            Project README, database schema, API docs
Prompts	        Pre-built prompt templates as slash commands	  "Summarize this PR", "Generate migration SQL"

---
### **Configuration: Three Scopes**

MCP servers are configured at three scopes: [TODO: Table]

Scope	                  Location                        	        Description
Local (default)	    `~/.claude.json` (per-project entry)	    Personal to you, only this project. Default when running claude mcp add.
Project	            `.mcp.json` at project root	Committed to git.         Every team member gets the same servers.
User	              `~/.claude.json` (top-level)	              Personal servers across all your projects. Use --scope user.


### **.mcp.json Format**


The primary method for team-shared MCP configuration:
```
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "aws-knowledge": {
      "type": "http",
      "url": "https://knowledge-mcp.global.api.aws"
    }
  }
}
```


The ${VARIABLE} syntax resolves from your shell environment at runtime — secrets stay out of git while the configuration is shareable.

You can also add servers via CLI:

```
# HTTP server (hosted)
claude mcp add --transport http aws-knowledge https://knowledge-mcp.global.api.aws

# Stdio server (local process)
claude mcp add playwright -- npx -y @playwright/mcp@latest
```
---

### **Security Considerations**

MCP servers run outside Claude Code's sandbox:

* They execute with your local user permissions — access to file system, network, and credentials
* Always vet MCP servers before adding them (treat like third-party npm packages)
* Use environment variables for credentials — never hardcode tokens
* Enterprise teams should audit and allowlist approved servers
---

#### **Connect to the AWS Knowledge MCP Server**

AWS publishes a fully managed, remote MCP server that gives Claude access to the latest AWS documentation, API references, and best practices — no local install required.

**Step 1** — Create .mcp.json at the project root:

```
cat > .mcp.json << 'EOF'
{
  "mcpServers": {
    "aws-knowledge": {
      "type": "http",
      "url": "https://knowledge-mcp.global.api.aws"
    }
  }
}
EOF
```
No API keys, no local install, no authentication required.

**Step 2 —** Start Claude Code:

```
claude
```

Claude Code detects the new MCP server and asks you to approve it. Choose "Use this and all future MCP servers in this project" so it won't re-prompt on every session.

**Step 3 —** Check MCP status:

```
/mcp
```

You should see aws-knowledge listed as connected.

**Step 4 —** Query AWS docs through MCP:

```
What are the latest best practices for S3 bucket security?
```

**Observe:** Claude calls the AWS Knowledge MCP tools to retrieve current documentation rather than relying on its training data. Watch the tool calls — you'll see MCP tool invocations distinct from Claude's built-in tools.

**Step 5 —** Use MCP for your shopping cart project:

Using the AWS docs, what's the recommended way to set up DynamoDB TTL for cart items that should expire after 24 hours?



MCP transforms Claude from a code-only tool into a connected platform — this server required zero setup (just a URL), and the same pattern works for GitHub, Slack, databases, and custom internal APIs.
