# Day 3 — Your First Session: Exploring a Codebase

## Introduction

The most powerful thing about Claude Code isn't writing code from scratch — it's understanding an **existing codebase** in seconds and making targeted, correct changes. This lesson teaches you how to run productive first sessions.

---

## Starting Claude Code

```bash
# Navigate to your project
cd ~/projects/my-app

# Launch Claude Code
claude

# You'll see the welcome prompt:
# ╭────────────────────────────────────╮
# │ ✻ Welcome to Claude Code!          │
# │                                    │
# │ /help for help                     │
# │                                    │
# │ cwd: /home/user/projects/my-app    │
# ╰────────────────────────────────────╝
```

---

## First Requests — Codebase Exploration

### Understand the project structure

```
You: Give me an overview of this codebase. What does it do, 
     how is it structured, and what are the main entry points?
```

Claude Code will:
- Read your directory tree
- Open key files (package.json, README, main.py, etc.)
- Summarise the architecture, stack, and purpose

### Find where something is implemented

```
You: Where is user authentication handled in this codebase?
     Show me the relevant files.
```

```
You: Find all places where we make database queries and 
     tell me if any of them look like SQL injection risks.
```

```
You: What does the /api/orders endpoint do? 
     Trace the full request lifecycle from router to database.
```

### Understand unfamiliar code

```
You: Explain what the worker.py file does, 
     line by line if needed. I've never seen this before.
```

```
You: This codebase uses a custom event system. 
     Explain how it works and give me 3 examples of how to use it.
```

---

## Making Your First Change

```
You: The login page is returning a 500 error when the 
     password is wrong instead of a 401. Find the bug and fix it.
```

Claude Code will:
1. Search for the login handler
2. Read the relevant code
3. Identify the issue
4. Show you the proposed change (diff)
5. Ask for approval
6. Apply the fix
7. Optionally run tests to verify

---

## The Review → Approve → Apply Loop

By default, Claude Code shows you every file change before writing it:

```
Claude: I'd like to make the following changes:

  auth/views.py  (+3, -1)
  ─────────────────────────────
  - except Exception as e:
  -     return jsonify({"error": str(e)}), 500
  + except AuthenticationError:
  +     return jsonify({"error": "Invalid credentials"}), 401
  + except Exception as e:
  +     return jsonify({"error": "Internal server error"}), 500

  Apply these changes? [y/n]
```

This keeps you in control. You see exactly what changes before anything is written.

---

## Running Commands

Claude Code can run shell commands in your project:

```
You: Run the test suite and tell me what's failing.
```

```bash
# Claude Code executes: pytest -v
# Reads the output
# Reports back which tests failed and why
# Can fix the failures if you ask
```

```
You: Install the missing dependencies and start the dev server.
```

---

## Asking About Errors

Paste any error directly:

```
You: I'm getting this error:
     
     Traceback (most recent call last):
       File "app.py", line 42, in process_order
         total = sum(item['price'] for item in order['items'])
     KeyError: 'price'
     
     Fix it.
```

Claude Code will:
- Locate the relevant code
- Understand the data structure
- Apply a correct fix
- Explain what was wrong

---

## Useful First-Session Prompts

```bash
# Architecture overview
"Describe the architecture of this application in a README-style summary"

# Dependency audit
"List all external dependencies and flag any that are outdated or have known vulnerabilities"

# Code quality
"Review this codebase for obvious bugs, security issues, and code smells. 
 Prioritise by severity."

# Onboarding
"I just joined this team. What do I need to know to be productive?
 Write me a new_developer_guide.md"

# Testing gaps
"Which files have no test coverage? List the top 5 most important to test first."

# Performance
"Find the 3 slowest database queries in this codebase and suggest optimisations."
```

---

## What NOT to Expect in a First Session

- **Magic:** Claude Code is powerful but not perfect. Review its changes.
- **Zero errors:** It may suggest an approach that needs tweaking. That's normal.
- **Full context forever:** The context window is finite (~200K tokens). For very large codebases, be specific about which files to focus on.

---

## Ending a Session

```bash
# Type exit or press Ctrl+C
exit

# Or close the terminal window
# Your CLAUDE.md file (Day 4) persists context for next session
```

---

## Key Takeaways

- Start with an overview request — let Claude Code read the codebase first
- Be specific: "fix the login bug" is better than "fix bugs"
- Always review diffs before approving
- Use questions to explore before making changes
- Claude Code remembers nothing between sessions by default — Day 4 solves this
