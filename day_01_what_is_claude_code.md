# Day 1 — What Is Claude Code?

## Introduction

Claude Code is an **agentic coding tool** built by Anthropic. It reads your entire codebase, edits files, runs commands, searches the web, and integrates with your development tools — all from a conversation in natural language.

Unlike a chat assistant that only suggests code, Claude Code **acts**: it opens files, writes changes, runs tests, fixes errors, and iterates — the same way a skilled developer would, but at machine speed.

> Official docs: https://code.claude.com/docs/en/overview

---

## How It Differs From Other AI Coding Tools

| Feature | Claude Code | GitHub Copilot | ChatGPT |
|---------|------------|----------------|---------|
| Reads whole codebase | ✔ | Partial | ✗ |
| Edits files directly | ✔ | ✔ (limited) | ✗ |
| Runs shell commands | ✔ | ✗ | ✗ |
| Multi-file reasoning | ✔ | ✗ | ✗ |
| MCP tool integrations | ✔ | ✗ | ✗ |
| Works in terminal | ✔ | ✗ | ✗ |
| Persistent memory (CLAUDE.md) | ✔ | ✗ | ✗ |

---

## Where Claude Code Runs

Claude Code is available on **multiple surfaces** — all powered by the same underlying engine:

### 1. Terminal CLI (Full-featured)
The primary interface. Install via npm, then type `claude` in any project directory.

```bash
# Install
npm install -g @anthropic-ai/claude-code

# Launch in any project
cd /my/project
claude
```

### 2. VS Code Extension
Install "Claude Code" from the VS Code marketplace. Opens a chat panel inside your editor. Same capabilities as CLI with file-awareness built in.

### 3. JetBrains Plugin
Available for IntelliJ IDEA, PyCharm, WebStorm, GoLand, and other JetBrains IDEs via the marketplace.

### 4. Claude Desktop App
A standalone desktop application available for macOS, Windows, and Linux. Includes the "Claude Code" agent mode.

### 5. Web (claude.ai)
Access Claude Code agent mode directly from your browser at claude.ai with a Pro or Max subscription.

---

## What Claude Code Can Do

```
You: "Add user authentication to this Flask app"

Claude Code:
  → reads your project structure
  → identifies existing routes, models, and templates
  → creates auth/views.py, auth/models.py, auth/forms.py
  → updates app/__init__.py to register the blueprint
  → adds login_required decorators to protected routes
  → updates requirements.txt with flask-login, bcrypt
  → runs: pip install -r requirements.txt
  → runs: python -m pytest tests/
  → fixes any failing tests
  → reports: "Authentication added. 3 new files, 8 files modified."
```

All from a single natural language request.

---

## The Three-Layer Architecture

Understanding Claude Code's architecture helps you use it effectively:

```
┌────────────────────────────────────────────────┐
│  Core Layer (Main Conversation)                │
│  Your chat + context  ← 200K token window      │
│  (1M with premium plans)                       │
├────────────────────────────────────────────────┤
│  Delegation Layer (Sub-agents)                 │
│  Spawned for complex tasks, clean context      │
│  Return only summaries to main conversation    │
├────────────────────────────────────────────────┤
│  Extension Layer (MCP + Hooks + Skills)        │
│  Databases, GitHub, Sentry, Slack, APIs        │
│  Guaranteed shell commands, reusable workflows │
└────────────────────────────────────────────────┘
```

**Key insight:** Most users work only in the Core Layer, watching context fill up and costs climb. Days 10–14 of this course show you how to use the Delegation and Extension layers to stay efficient.

---

## Who Should Use Claude Code

Claude Code is designed for:

- **Software engineers** — feature development, refactoring, debugging
- **DevOps / SRE / sysadmins** — infrastructure scripts, automation, config management
- **Data engineers** — pipeline development, SQL optimisation, ETL scripting
- **Security engineers** — code review, vulnerability scanning, hardening scripts
- **Anyone who writes code** and wants a tireless pair programmer

---

## Requirements

- **Node.js** 18+ (for the CLI)
- **A Claude subscription** (Pro, Max, Team, or Enterprise) OR an Anthropic Console API key
- **macOS, Linux, or Windows** (WSL recommended on Windows for bash tool support)

---

## What You'll Learn in This Course

| Day | Topic |
|-----|-------|
| 1 | What is Claude Code? (this file) |
| 2 | Installation & authentication |
| 3 | Your first session — exploring a codebase |
| 4 | CLAUDE.md — persistent instructions |
| 5 | Slash commands & keyboard shortcuts |
| 6 | Permissions & the trust model |
| 7 | Working with files & diffs |
| 8 | Running tests & fixing failures |
| 9 | MCP — connecting external tools |
| 10 | Sub-agents & parallel tasks |
| 11 | Hooks — automation & CI/CD |
| 12 | Skills & custom slash commands |
| 13 | IDE integration (VS Code & JetBrains) |
| 14 | Advanced workflows & cost management |
| 15 | Real-world project walkthrough |
