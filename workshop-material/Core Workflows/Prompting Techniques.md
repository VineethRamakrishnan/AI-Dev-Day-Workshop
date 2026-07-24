## Prompting Techniques

How you talk to Claude Code determines the quality of what you get back. This section covers the key techniques.

---
### Intent vs. Instructions

This exercise demonstrates the key mindset shift when working with Claude Code — describe the outcome you want, not the exact steps.

```Open backend/services/order_service.py in your editor. Notice how the code has no comments or docstrings — just raw Python with zero documentation. Keep that in mind for what follows.```

#### Step 1 — Micro-managed prompt (anti-pattern)

Tell Claude exactly what to do:

Open backend/services/order_service.py. Find the line with "def create_order". Add a comment above it that says "# Create a new order".

**Observe**: Claude does exactly what you asked — nothing more. It acts as a typist, not a thinker.


#### Step 2 — Intent-driven prompt

Now describe intent instead — tell Claude what you want, not how:

```This codebase has almost no code comments. Add useful documentation comments to the most important files — focus on anything a new developer would need to understand.```

**Observe:** Claude explores the codebase on its own, decides which files matter, and adds meaningful comments — including docstrings, inline explanations, and module-level descriptions.


#### The takeaway
The intent-driven prompt produced better results because Claude used its own judgment about what mattered. You described the outcome; Claude figured out the path.

This is the paradigm shift — from "writing code with AI help" to "orchestrating an AI that writes code." The instinct to micromanage is strong. Resist it.

#### Rule of Thumb
Be specific about the what and why. Let Claude decide the how.

---
### Providing Context
The more precise your instructions, the fewer corrections you'll need.

#### Reference Files with @

Include file content directly in your prompt:

```explain the logic in @backend/services/cart_service.py```

#### Be Specific
Less effective	        More effective
"fix the bug"	        "Remove from cart is not working. When I click remove, the item stays. Investigate and fix it."
"add tests"	          "Add unit tests for the backend services. Cover edge cases. Use pytest."
"add a feature"	      "Add a product search feature. Users type in a search box and the product list filters in real time by name."

---
### Breaking Down Complex Tasks

For larger tasks, break them into numbered steps. Claude executes them in order:

1. Add a "quantity" input field next to each product's Add to Cart button
2. Update the addToCart API call to send the quantity value
3. Verify the cart shows the correct quantity after adding

This gives Claude a clear execution plan while still letting it decide how to implement each step.
