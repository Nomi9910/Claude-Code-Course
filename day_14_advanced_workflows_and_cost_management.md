# Day 14 — Advanced Workflows & Cost Management

## Introduction

This lesson covers power-user patterns that make Claude Code dramatically more productive, and practical strategies for managing token usage and costs — especially important for teams at scale.

---

## Advanced Workflow Patterns

### Pattern 1: Plan-Then-Execute

For complex tasks, separate planning from execution:

```
# Step 1: Plan (cheap — mostly reading and thinking)
You: Read the codebase and create a detailed plan for adding 
     multi-tenant support. List every file that needs to change,
     what changes are needed, and potential risks.
     Do NOT make any changes yet.

# Review the plan carefully

# Step 2: Execute (now Claude knows exactly what to do)
You: Execute the plan we just discussed. Start with the database 
     migrations, then the model changes, then the API layer.
```

This pattern reduces errors and wasted tokens from failed attempts.

### Pattern 2: Checkpointing Long Tasks

```
You: We're going to add rate limiting to the API.
     
     Step 1 only: Add the rate limiting middleware. 
     Show me the changes and stop.
     
[Review and approve]

     Step 2: Apply the middleware to the routes.
     Show me the changes and stop.
     
[Review and approve]

     Step 3: Add tests for rate limiting behaviour.
```

Breaking large tasks into checkpoints lets you course-correct early.

### Pattern 3: The Exploration-then-Implement Pattern

```
# Exploration phase (use haiku for speed)
/model haiku
You: Read every controller file and list what authentication 
     patterns they use. Just read, don't change anything.

# Implementation phase (use sonnet/opus for quality)
/model sonnet
You: Based on the patterns you found, standardise all controllers 
     to use the jwt_required decorator. Apply the changes.
```

### Pattern 4: Iterative Refinement

```
You: Write a function that parses webhook payloads from Stripe.

[Claude writes it]

You: Now add support for the new payment_intent.succeeded event type.

[Claude extends it]

You: The function is getting complex. Refactor it to use a 
     strategy pattern with a handler per event type.

[Claude refactors]

You: Write tests for it.
```

### Pattern 5: Agentic Batch Processing

```bash
# Process a list of files or tasks
claude "For each Python file in src/legacy/:
        1. Add type hints to all function signatures
        2. Replace any print() statements with proper logging
        3. Add a docstring to any function missing one
        Process them all and give me a summary of changes."
```

---

## Cost Management

### Understanding Costs

Claude Code charges per token (input + output):

| Model | Input | Output |
|-------|-------|--------|
| Haiku | Cheapest | Cheapest |
| Sonnet | Mid | Mid |
| Opus | Most expensive | Most expensive |

Check current pricing: https://www.anthropic.com/pricing

**Track costs in your session:**
```
/cost

# Output:
# Session Cost Breakdown
# ─────────────────────────────────────
# Tokens used:  34,521 input / 8,234 output
# Cache hits:   21,400 tokens (saved ~$0.12)
# Models used:  sonnet-4.6 (main), haiku-4.5 (sub-agents)
# Estimated:    $0.31
```

### Cost Reduction Strategies

**1. Use the right model for the task**
```
# Use haiku for: searching, reading, exploration, summarising
/model haiku
You: Find all files that import from the deprecated auth module.

# Use sonnet for: implementation, refactoring, general work
/model sonnet  
You: Refactor those imports to use the new auth module.

# Use opus for: complex reasoning, architecture decisions
/model opus
You: Design the new microservices architecture for this monolith.
```

**2. Keep CLAUDE.md concise**
CLAUDE.md is read every session. Keep it focused — 200-400 lines is plenty. Long CLAUDE.md files add token cost to every session.

**3. Use /clear to reset bloated context**
```
/status  # Check context usage
# "Context: 180K / 200K" — nearly full

/clear   # Reset to 0 tokens
You: Now fix the payment validation bug in orders.py
# Fresh 200K context, focused on one task
```

**4. Be specific to avoid unnecessary reads**
```
# Expensive (reads many files):
You: Fix the bug in the auth system.

# Cheaper (targeted):
You: Fix the token expiry validation in auth/middleware.py line 45.
```

**5. Use --print for one-off tasks**
```bash
# Non-interactive = no session overhead
claude --print "What does the config.py file do?"
```

**6. Leverage prompt caching**
Claude Code automatically caches repeated context (your CLAUDE.md, long file reads). Cache hits are significantly cheaper. Keep your project files stable during a session to maximise cache hits.

---

## Rate Limits

Claude Code enforces weekly rate limits for subscription users. If you hit limits:

```bash
# Check remaining usage
/status

# Options:
# 1. Wait for the weekly limit to reset
# 2. Switch to API key billing (pay-per-use, no weekly limit)
# 3. Upgrade your plan tier
```

> Note: Anthropic doubled Claude Code rate limits in May 2026 and removed peak-hours reductions on Pro and Max plans.

---

## Model Selection Reference

```bash
# Fast and cheap — exploration, search, summaries
/model haiku

# Balanced — most daily work, implementation, refactoring
/model sonnet

# Most powerful — architecture, complex reasoning, difficult bugs
/model opus

# Switch mid-session based on what you're doing
/model haiku    # explore first
/model sonnet   # implement after
```

---

## Key Takeaways

- Plan-then-execute reduces wasted tokens on incorrect attempts
- Use haiku for exploration, sonnet for implementation, opus for hard problems
- `/cost` shows you real-time session spend
- `/clear` resets context when it's nearly full
- Be specific in your requests — targeted prompts cost less and work better
- Prompt caching (automatic) makes repeated reads much cheaper
