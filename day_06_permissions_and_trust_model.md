# Day 6 — Permissions & the Trust Model

## Introduction

Claude Code runs in your terminal with access to your files and shell. Understanding **what it can do**, **what it asks before doing**, and **how to configure the guardrails** is essential for safe, productive use.

---

## The Permission Model

Claude Code uses a tiered permission system:

```
┌──────────────────────────────────────────────────────┐
│  ALWAYS ALLOWED (no prompt)                          │
│  • Read files in the current project directory       │
│  • Run read-only commands: ls, cat, grep, find, git  │
│  • Fetch publicly accessible URLs                    │
├──────────────────────────────────────────────────────┤
│  ASKS PERMISSION (per action)                        │
│  • Write or delete files                             │
│  • Run commands that modify state                    │
│  • Install packages                                  │
│  • Make API calls via MCP servers                    │
├──────────────────────────────────────────────────────┤
│  NEVER ALLOWED (hard limits)                         │
│  • Actions outside the project directory by default  │
│  • Anything explicitly blocked in settings           │
└──────────────────────────────────────────────────────┘
```

---

## How Permission Prompts Work

When Claude Code wants to do something that needs approval:

```
Claude: I'd like to run the following command:
        
        npm install express helmet cors
        
        Allow? [y/n/a] (a = allow all similar for this session)
```

**Options:**
- `y` — allow this one action
- `n` — deny; Claude will find another approach
- `a` — allow all similar actions for the rest of this session
- `Ctrl+C` — abort the entire current task

---

## Viewing & Editing Permissions

```bash
# Inside a Claude Code session:
/permissions

# Output:
# Current Permission Settings
# ──────────────────────────────────────────
# File reads:     ✔ Allowed (project directory)
# File writes:    ⚠ Ask each time
# Shell commands: ⚠ Ask each time
# Network (MCP):  ✔ Connected servers only
# Global files:   ✗ Blocked
```

---

## Permission Configuration Files

Permissions are stored in `.claude/settings.json` (project-level) and `~/.claude/settings.json` (user-level):

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(pytest *)",
      "Bash(git *)",
      "WriteFile(src/**)",
      "WriteFile(tests/**)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(curl * | bash)",
      "WriteFile(.env)",
      "WriteFile(*.pem)"
    ]
  }
}
```

### Permission Rule Syntax

```
Bash(<pattern>)       — allow/deny shell commands matching pattern
WriteFile(<glob>)     — allow/deny writing to file paths matching glob
ReadFile(<glob>)      — allow/deny reading file paths
WebFetch(<domain>)    — allow/deny fetching from specific domains
mcp__<server>__<tool> — allow/deny specific MCP tool calls
```

### Examples

```json
{
  "permissions": {
    "allow": [
      "Bash(make *)",
      "Bash(docker compose *)",
      "Bash(git add * && git commit *)",
      "WriteFile(src/**/*.ts)",
      "WriteFile(tests/**)",
      "Bash(npm run lint)",
      "Bash(npm run test)"
    ],
    "deny": [
      "Bash(sudo *)",
      "Bash(rm -rf /)",
      "Bash(curl * | sh)",
      "WriteFile(.env*)",
      "WriteFile(*.key)",
      "WriteFile(*.pem)"
    ]
  }
}
```

---

## Trust Levels

### Default (Recommended for most work)
```bash
claude
# Asks permission for writes and shell commands
# Safe for working in production codebases
```

### Session-wide Approval
```
You: /permissions
     [Enable "auto-approve safe commands" for this session]
```

### Full Auto-Approve (Trusted Environments Only)
```bash
# Bypass all permission prompts — use only in sandboxes/CI
claude --dangerously-skip-permissions "Apply all the changes in the plan"
```

> ⚠️ Only use `--dangerously-skip-permissions` in sandboxed environments (Docker containers, CI/CD pipelines with no production access). Never in a terminal connected to production systems.

---

## What Claude Code Won't Do

Regardless of permissions, Claude Code will:

- **Never** run commands designed to exfiltrate your data
- **Never** access files outside the configured project scope without explicit permission
- **Never** make purchases or financial transactions
- **Always** show you what it's about to do before doing it (unless you've granted auto-approve)
- **Always** respect `--safe-mode` which disables all customisations and runs with minimal permissions

---

## .mcp.json and Permissions

When MCP servers are configured (Day 9), they also follow the permission model:

```json
{
  "permissions": {
    "allow": [
      "mcp__github__create_pull_request",
      "mcp__github__list_issues"
    ],
    "deny": [
      "mcp__github__delete_repository"
    ]
  }
}
```

**Best practice:** Give each MCP server the narrowest credential possible. Read-only tokens where possible.

---

## Recommended Permission Setup by Environment

### Personal Dev Machine
```json
{
  "permissions": {
    "allow": ["Bash(npm *)", "Bash(git *)", "Bash(make *)"],
    "deny":  ["Bash(sudo *)", "WriteFile(.env)"]
  }
}
```

### CI/CD Pipeline
```bash
# Use --dangerously-skip-permissions with a read-only API key
# Scope the Claude API key to read-only operations
claude --dangerously-skip-permissions "Run linting and tests, report results"
```

### Production Server (Not Recommended)
Claude Code is not designed to run interactively on production servers. Use it locally or in CI/CD to generate scripts, then deploy those scripts via your normal deployment pipeline.

---

## Key Takeaways

- Claude Code asks before writing files or running state-changing commands
- Use `allow`/`deny` rules in `.claude/settings.json` to pre-authorise common operations
- Keep `.env` and credentials files in the deny list
- `--dangerously-skip-permissions` is for sandboxed/CI environments only
- MCP server tools respect the same permission system
