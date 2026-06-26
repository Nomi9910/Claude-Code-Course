# Day 5 — Slash Commands & Keyboard Shortcuts

## Introduction

Claude Code has a rich set of built-in slash commands and keyboard shortcuts that dramatically speed up your workflow. This lesson covers every command you'll use regularly.

---

## Slash Commands Reference

Type `/` in the Claude Code prompt to see available commands. Here are the most important ones:

### Session & Status

```
/help              Show all available commands
/status            Show current session info: model, tokens used, costs,
                   MCP servers connected, auth status

/cost              Breakdown of tokens and estimated cost for this session

/model             Show the current model being used
/model sonnet      Switch to claude-sonnet-4-6 (fast, balanced)
/model opus        Switch to claude-opus-4-7 (most powerful, slower)
/model haiku       Switch to claude-haiku-4-5 (fastest, cheapest)
```

### Memory & Context

```
/memory            View and edit saved memories for this project
/clear             Clear conversation history (start fresh context)
                   → Useful when context is getting full or confused
```

### MCP (Model Context Protocol)

```
/mcp               List all connected MCP servers and their status
                   Shows: server name, connection status, available tools
```

### Tools & Permissions

```
/permissions       View and edit what Claude Code is allowed to do
                   → Read/write files, run commands, network access
```

### Custom Slash Commands (Skills)

```
/review-pr         Custom command you define (see Day 12)
/deploy-staging    Custom command you define
/run-tests         Custom command you define
```

> Custom commands are defined in `.claude/commands/` — covered in Day 12.

---

## Keyboard Shortcuts

### Navigation

| Shortcut | Action |
|----------|--------|
| `↑` / `↓` | Browse command history |
| `Ctrl+R` | Search command history |
| `Ctrl+A` | Move cursor to start of line |
| `Ctrl+E` | Move cursor to end of line |
| `Ctrl+W` | Delete word backwards |

### Session Control

| Shortcut | Action |
|----------|--------|
| `Ctrl+C` | Cancel current operation / interrupt |
| `Ctrl+D` | Exit Claude Code (EOF) |
| `Ctrl+L` | Clear screen (keep history) |

### During Claude Code's Response

| Shortcut | Action |
|----------|--------|
| `Ctrl+C` | Interrupt Claude mid-response |
| `Escape` | Cancel pending action |

---

## CLI Flags (Used When Launching Claude Code)

These flags go on the command line when you start Claude Code, not inside the session:

```bash
# Non-interactive: pass a prompt directly and exit
claude "What is the entry point of this application?"
claude "List all TODO comments in the src/ directory"

# Run a task and exit (useful in scripts and CI)
claude --print "Summarise this codebase in 3 bullet points"

# Continue the most recent session
claude --continue

# Resume a specific session by ID
claude --resume <session-id>

# Run in a specific directory
claude --cwd /path/to/project "Fix the failing tests"

# Bypass permissions (use in trusted environments only)
claude --dangerously-skip-permissions "Apply all the changes"

# Verbose output for debugging
claude --verbose

# Safe mode: disables all customisations for troubleshooting
claude --safe-mode

# No streaming (wait for complete response before printing)
claude --no-stream "Generate a summary"

# Output JSON (for scripting)
claude --output-format json "List all files you would touch to add auth"
```

---

## Inline Flags During a Session

```
# Ask Claude to use a specific model for one response
You: /model opus
     Now refactor the entire authentication module

# Check costs mid-session
You: /cost

# Switch models back
You: /model sonnet
```

---

## Effective Use Patterns

### Pattern 1: Quick one-liner tasks

```bash
# From your shell — no interactive session needed
claude "Add a README badge for the CI status"
claude "What does the deploy.sh script do?"
claude "Find all hardcoded API keys in this repo"
```

### Pattern 2: Review before a meeting

```bash
claude "Summarise every change made in the last 10 git commits 
        in plain English for a non-technical stakeholder"
```

### Pattern 3: Generate then continue

```bash
# Generate a file and stay in session to refine it
claude
> Create a comprehensive test suite for the auth module
> [reviews output]
> Add edge cases for expired tokens
> [reviews again]
> /cost
> Exit
```

### Pattern 4: Using --print in scripts

```bash
#!/bin/bash
# Generate a PR description from git diff
DESCRIPTION=$(git diff main..HEAD | claude --print "Write a PR description for these changes")
echo "$DESCRIPTION"
gh pr create --title "Feature: ..." --body "$DESCRIPTION"
```

---

## Key Takeaways

- `/status` and `/cost` are your go-to commands for session awareness
- `/model` lets you switch between fast (haiku/sonnet) and powerful (opus) mid-session
- `/clear` resets context without exiting — useful when Claude gets confused
- CLI `--print` flag makes Claude Code scriptable for automation
- `Ctrl+C` is safe — it interrupts the current action without exiting
