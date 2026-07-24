### Exploring the Codebase

Start by understanding what you're working with. Claude Code excels at codebase exploration — it reads files, traces connections, and explains architecture without you pointing it at specific files.

#### Get a High-Level Overview

```give me an overview of this codebase```

#### Dive Deeper
```explain the architecture - how does the frontend connect to the backend?```

```what API endpoints are available and how are they routed?```

```trace the flow when a user adds an item to the cart, from the React component to DynamoDB```

#### Find Relevant Code
```find the files that handle order creation```

```show me how the cart total is calculated including tax```

---

#### Multi-Turn Conversations
Claude remembers everything within a session. Build on previous answers instead of repeating context:

```how does the cart service work?```

Then follow up naturally:

```now show me how the order service uses it```

```are there any edge cases not handled in that flow?```

You don't need to re-explain what "it" or "that flow" refers to — Claude tracks the conversation.
