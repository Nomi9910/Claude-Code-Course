# Day 10 — Sub-agents & Parallel Tasks

## Introduction

For complex, multi-step tasks, Claude Code can **spawn sub-agents** — separate AI instances with clean contexts, focused on specific work — and coordinate them in parallel. This is the Delegation Layer from Day 1's architecture diagram, and it's what separates casual Claude Code users from power users.

---

## Why Sub-agents?

**The problem with the Core Layer only:**
```
You → One conversation → context fills up → quality degrades → costs climb
```

Every file Claude Code reads, every command output, every conversation turn eats into your 200K token context window. For complex tasks (refactoring 50 files, reading a large codebase), the main conversation can fill up and Claude starts "forgetting" earlier context.

**The solution — Delegation:**
```
You → Main conversation (stays small)
        ├─ Sub-agent 1: "Analyse the auth module" → returns summary
        ├─ Sub-agent 2: "Analyse the payment module" → returns summary  
        └─ Sub-agent 3: "Analyse the notification module" → returns summary
     → Main conversation gets 3 summaries (not 3 full module reads)
```

Sub-agents do the deep, context-heavy work. Only their **conclusions** return to your main conversation.

---

## How Sub-agents Work

Sub-agents are spawned automatically when Claude Code decides a task is complex enough, or when you explicitly ask for parallel work. As of Claude Code v2.1.172, sub-agents can recursively spawn their own sub-agents, up to 5 levels deep.

```
You: Analyse every controller in src/controllers/ and 
     write a summary of what each one does. Do them in parallel.

Claude Code: I'll spawn parallel agents to analyse each controller:
     
     [Agent 1] Reading: AuthController.js...
     [Agent 2] Reading: OrderController.js...
     [Agent 3] Reading: PaymentController.js...
     [Agent 4] Reading: UserController.js...
     
     [Agents complete]
     
     Summary:
     • AuthController — handles login, logout, token refresh (JWT)
     • OrderController — CRUD for orders, includes Stripe checkout
     • PaymentController — webhook handler for payment events
     • UserController — user profile CRUD, avatar upload
```

---

## Prompting for Parallel Work

```
You: Refactor all 12 service files in src/services/ to use 
     async/await instead of callbacks. Do them in parallel 
     and show me the complete list of changes when done.
```

```
You: Run the test suite for each module independently and 
     give me a consolidated report of failures.
```

```
You: Search the entire codebase for these three anti-patterns 
     simultaneously and report all findings:
     1. Direct database queries in controllers
     2. Hardcoded configuration values
     3. Missing error handling in async functions
```

---

## Model Selection for Sub-agents

Different models have different speed and cost profiles. In complex tasks, Claude Code may use:

- **Claude Haiku** → Fast sub-agents for exploration, reading files, search
- **Claude Sonnet** → Balanced sub-agents for implementation
- **Claude Opus** → Main conversation for planning and high-level decisions

You can influence this:

```
You: Use haiku sub-agents to scan all 200 files for TODO comments,
     then summarise what you found.
```

```bash
# Set default model for the session
/model sonnet    # balanced
/model opus      # maximum capability
/model haiku     # fastest/cheapest
```

---

## Practical Sub-agent Workflows

### Large codebase onboarding
```
You: I've just joined this team. Use parallel agents to analyse 
     each directory in src/ and write me a comprehensive 
     CODEBASE_GUIDE.md covering what each part does and how 
     they connect.
```

### Parallel test generation
```
You: Generate test suites for all files in src/utils/ in parallel.
     Each test file should be in tests/unit/utils/ with the same name.
     Use the existing test patterns from tests/unit/auth/.
```

### Multi-file refactor with verification
```
You: I need to rename the Customer entity to Client throughout 
     the codebase. Use sub-agents to:
     1. Find all occurrences across all files
     2. Apply the rename in parallel across files  
     3. Run tests to verify nothing broke
     4. Report a summary of every file changed
```

### Security audit
```
You: Run a security audit of this codebase using parallel agents:
     Agent 1: Check for SQL injection vulnerabilities
     Agent 2: Check for XSS vulnerabilities
     Agent 3: Check for exposed secrets and hardcoded credentials
     Agent 4: Check for missing authentication on API endpoints
     
     Consolidate findings into a SECURITY_AUDIT.md file.
```

---

## Context Management Tips

```
# When context is getting full — check with:
/status
# Shows: "Context: 156K / 200K tokens used"

# Clear context to start fresh (doesn't exit):
/clear

# Start fresh session on a focused subtask:
claude "Just fix the failing test in tests/auth.test.js"
# Opens new session — clean 200K context
```

---

## Key Takeaways

- Sub-agents keep your main conversation small by doing context-heavy work in parallel
- Ask explicitly for parallel work on tasks involving many files
- Haiku sub-agents are cheap for exploration; Sonnet/Opus for implementation
- Sub-agents return summaries, not raw data — this is what makes the pattern efficient
- Use `/status` to monitor context usage and `/clear` when context fills
