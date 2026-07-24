### Installing-claude-code

Claude Code installs with a single command:

```curl -fsSL https://claude.ai/install.sh | bash```

NOTE: The installation auto-updates in the background.


### Connecting-to-amazon-bedrock

Claude Code supports multiple authentication backends. In this workshop, we use Amazon Bedrock.

#### Prerequisites
Before configuring Claude Code with Bedrock, ensure you have:

1. An AWS account with Bedrock access enabled
2. Access to desired Claude models (for example, Claude Sonnet 4.6) in Bedrock
3. AWS CLI installed and configured (optional — only needed if you don't have another mechanism for getting credentials)
4. Appropriate IAM permissions


### Environment-variables

The key environment variables for Bedrock:

```
# Enable Bedrock integration
export CLAUDE_CODE_USE_BEDROCK=1

# Set your AWS region
export AWS_REGION=us-east-1
```

These are configured in the workshop environment via .claude/settings.json. Claude Code uses the standard AWS credential chain — no separate API key needed.


### How-claude-code-uses-models

Claude Code uses two models for different purposes:

* **Primary model (ANTHROPIC_MODEL)** — The main model that handles your coding tasks: reading files, writing code, running commands, and reasoning about your codebase. This is the model you interact with directly.

* **Small/fast model (ANTHROPIC_SMALL_FAST_MODEL)** — A lighter model used for background tasks like generating session titles, summaries, and compacting context. These tasks don't need full reasoning power and benefit from lower latency and cost.


### Default-model-settings-on-bedrock

When no model is explicitly configured:

Model	Bedrock Default

Primary model	us.anthropic.claude-sonnet-4-5-20250929-v1:0
Small/fast model	Same as primary model (because Haiku may not be enabled in every account)

For team rollouts, pin specific model versions so everyone uses the same model and you control when to upgrade. In this workshop, we pin:

```
ANTHROPIC_MODEL=global.anthropic.claude-sonnet-4-6
ANTHROPIC_SMALL_FAST_MODEL=global.anthropic.claude-haiku-4-5-20251001-v1:0
```

Without pinning, Claude Code's built-in default can change between releases.


### Interactive-setup-wizard

If you're setting up Claude Code with Bedrock outside this workshop, you can use the built-in wizard:

1. Run claude
2. At the login prompt, select 3rd-party platform → Amazon Bedrock
3. Follow the prompts to configure credentials and region

### Configuration Verification

```
echo $CLAUDE_CODE_USE_BEDROCK
echo $ANTHROPIC_MODEL
```

You should see:

1
A Bedrock model ID (e.g., global.anthropic.claude-sonnet-4-6)


### Start-claude-code

```
claude
```

### Verify-with-status

Once Claude Code is running, check that everything is connected:

```
/status
```

You should see:

Provider: Amazon Bedrock
Model: The configured Sonnet model
Region: us-east-1


### Key-commands-to-know

Before we move on, familiarize yourself with these essential commands:

Command	What it does

claude	  Start Claude Code
Esc	      Stop Claude mid-action (context preserved)
Esc + Esc	Open rewind menu
/clear	  Reset context between tasks
/help	    Show available commands
/status	  Show provider, model, and region
claude --continue	  Resume the most recent session
claude --resume	    Pick a session from a list
Shift+Tab	          Toggle plan mode
