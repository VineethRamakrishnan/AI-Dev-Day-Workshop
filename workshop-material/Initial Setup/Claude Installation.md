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
