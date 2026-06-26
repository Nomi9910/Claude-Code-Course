# Day 9 — MCP: Connecting External Tools

## Introduction

**MCP (Model Context Protocol)** is Anthropic's open standard for connecting AI tools to external data sources and services. With MCP, Claude Code can query your database, create GitHub issues, read Slack messages, check Sentry errors, and interact with any API your team uses — all from natural language.

> Without MCP: Claude Code can read files and run bash commands.
> With MCP: Claude Code can also query your production database, create Jira tickets, check Sentry errors, review GitHub PRs.

---

## How MCP Works

```
You ─────────────────────────────────────────────────────┐
     "Find all open critical bugs in Sentry and         │
      create GitHub issues for each one"                │
                                                        ▼
Claude Code ─────────────────────────────────────────────┐
     Calls: sentry.list_issues(level="critical")        │
     Reads: 4 critical bugs                             │
     Calls: github.create_issue() × 4                  │
     Reports: "Created 4 GitHub issues"                 │
```

MCP servers run as separate processes (local) or as remote HTTP services. Claude Code connects to them, discovers their tools, and can call those tools during your session.

---

## Adding MCP Servers

### Method 1: Interactive CLI Wizard

```bash
# Inside Claude Code or in terminal:
claude mcp add

# Launches a step-by-step wizard:
# 1. Select server from list, or paste install command
# 2. Enter API keys / connection strings
# 3. Name the server
# 4. Claude Code saves it to config
```

### Method 2: Add by command

```bash
# Add a stdio server (runs a local process)
claude mcp add postgres \
  --transport stdio \
  -- npx -y @modelcontextprotocol/server-postgres \
     postgresql://user:password@localhost:5432/mydb

# Add an HTTP server (connects to remote endpoint)
claude mcp add-json github \
  '{"type":"http","url":"https://api.githubcopilot.com/mcp/",
    "headers":{"Authorization":"Bearer YOUR_GITHUB_PAT"}}'

# Add with scope (where config is saved)
claude mcp add postgres --scope project   # .mcp.json (shared with team)
claude mcp add postgres --scope user      # ~/.claude.json (just for you)
claude mcp add postgres --scope local     # .claude/ (project, not committed)
```

### Method 3: Edit JSON config directly

```bash
# Edit ~/.claude.json for user-level (all projects)
# or .mcp.json in project root (shared, commit to git)

cat > .mcp.json << 'JSON'
{
  "mcpServers": {
    "postgres": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres",
               "postgresql://localhost:5432/mydb"],
      "env": {
        "PGPASSWORD": "yourpassword"
      }
    },
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_GITHUB_PAT"
      }
    },
    "slack": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-..."
      }
    }
  }
}
JSON
```

---

## Managing MCP Servers

```bash
# List all configured servers and their status
claude mcp list

# Check status inside Claude Code session
/mcp

# Output:
# MCP Server Status
# ──────────────────────────────────
# • postgres: connected  (8 tools)
# • github:   connected  (43 tools)
# • slack:    connected  (12 tools)
# • sentry:   ✗ failed   (check credentials)

# Get details on a specific server
claude mcp get postgres

# Remove a server
claude mcp remove postgres

# Login to servers that support OAuth
claude mcp login github
```

---

## Popular MCP Servers

### Databases
```bash
# PostgreSQL
npx -y @modelcontextprotocol/server-postgres postgresql://...

# MySQL
npx -y @modelcontextprotocol/server-mysql mysql://...

# SQLite
npx -y @modelcontextprotocol/server-sqlite /path/to/db.sqlite
```

### Development Tools
```bash
# GitHub (official)
# Add via: claude mcp add-json github '{"type":"http","url":"https://api.githubcopilot.com/mcp/",...}'

# Sentry
npx -y @sentry/mcp-server --access-token=...

# Linear (issue tracking)
npx -y @linear/mcp-server --api-key=...

# Jira
npx -y @atlassian/mcp-server --token=...
```

### Communication
```bash
# Slack
npx -y @modelcontextprotocol/server-slack  # SLACK_BOT_TOKEN env var

# Google Drive / Docs
npx -y @modelcontextprotocol/server-gdrive  # OAuth required
```

### Filesystem & Utilities
```bash
# Brave Search (web search)
npx -y @modelcontextprotocol/server-brave-search  # BRAVE_API_KEY

# Browser automation (Playwright)
npx -y @playwright/mcp@latest
```

---

## Using MCP Tools in Your Session

Once servers are connected, just ask naturally:

```
You: Query the database and find all users who signed up 
     in the last 30 days but never completed their profile.

Claude Code: [calls postgres.query()]
     Found 1,247 users. Here's a breakdown by signup date...

You: Create a GitHub issue for the "users without profiles" 
     retention problem we just found. Assign it to the growth team.

Claude Code: [calls github.create_issue()]
     Created issue #384: "Improve profile completion rate for new users"

You: Post a summary of this finding to #growth-team in Slack.

Claude Code: [calls slack.post_message()]
     Posted to #growth-team.
```

---

## Security Best Practices for MCP

```
1. Least privilege — give each server the narrowest credential possible
   ✔ Read-only database user for postgres server
   ✔ Limited-scope GitHub PAT (just the repos Claude needs)
   ✗ Never give full admin credentials

2. Project scope for team tools — commit .mcp.json to git
   (without secrets — use environment variables for credentials)

3. User scope for personal tools — add to ~/.claude.json
   (tools only you need, like your personal GitHub token)

4. Audit MCP calls — Claude Code shows every tool call it makes
   Watch them in early sessions to understand what's happening

5. Keep servers updated — run npm update on MCP server packages
```

---

## Key Takeaways

- MCP turns Claude Code from a file editor into a full workflow tool
- Three ways to add servers: CLI wizard, command-line, or JSON config
- `.mcp.json` in your project root is the best way to share MCP config with your team
- Always use least-privilege credentials for MCP servers
- `/mcp` inside Claude Code shows all connected servers and their status
