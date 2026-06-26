# Day 11 — Hooks: Automation & CI/CD Integration

## Introduction

**Hooks** let you run shell commands **before or after** specific Claude Code actions — automatically, every time. They are deterministic (unlike AI responses) and run regardless of what Claude decides to do. This makes them perfect for enforcing code quality, triggering CI/CD steps, and automating team workflows.

---

## What Are Hooks?

Hooks are shell commands that Claude Code runs at specific lifecycle events:

| Hook Event | When It Fires |
|------------|--------------|
| `PreToolUse` | Before Claude uses any tool (read, write, bash, MCP) |
| `PostToolUse` | After Claude uses any tool |
| `Stop` | When Claude finishes responding |
| `Notification` | When Claude sends a notification |

**Example use cases:**
- Run `prettier` after every file write
- Run `eslint` before Claude commits code
- Post a Slack message when a long task completes
- Log every shell command Claude runs for audit
- Run tests automatically after code changes

---

## Hook Configuration

Hooks are configured in `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write \"$CLAUDE_FILE_PATH\""
          }
        ]
      }
    ]
  }
}
```

**Available environment variables in hooks:**
- `$CLAUDE_TOOL_NAME` — name of the tool that was used
- `$CLAUDE_FILE_PATH` — file path (for file operations)
- `$CLAUDE_COMMAND` — bash command that was run
- `$CLAUDE_SESSION_ID` — current session identifier

---

## Practical Hook Examples

### Auto-format after every file write

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

### Run linter before any bash command (pre-commit style)

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash(git commit*)",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint && npm run test"
          }
        ]
      }
    ]
  }
}
```

### Notify Slack when a long task finishes

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "curl -s -X POST $SLACK_WEBHOOK -d '{\"text\":\"Claude Code task completed in session $CLAUDE_SESSION_ID\"}'"
          }
        ]
      }
    ]
  }
}
```

### Log all shell commands for audit

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"[$(date -Iseconds)] $CLAUDE_COMMAND\" >> ~/.claude/audit.log"
          }
        ]
      }
    ]
  }
}
```

### Auto-run tests after any source file change

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(src/**)",
        "hooks": [
          {
            "type": "command",
            "command": "npm test -- --passWithNoTests 2>&1 | tail -5"
          }
        ]
      }
    ]
  }
}
```

---

## CI/CD Integration

Claude Code is designed to run non-interactively in CI/CD pipelines:

### GitHub Actions Example

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run Claude Code review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude --dangerously-skip-permissions --print \
            "Review the changes in this PR. Check for:
             1. Security vulnerabilities
             2. Missing error handling
             3. Test coverage gaps
             4. Performance issues
             Output a markdown report." \
            > review.md

      - name: Post review as PR comment
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs')
            const review = fs.readFileSync('review.md', 'utf8')
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## Claude Code Review\n\n${review}`
            })
```

### GitLab CI Example

```yaml
claude-code-review:
  image: node:20
  script:
    - npm install -g @anthropic-ai/claude-code
    - |
      claude --dangerously-skip-permissions --print \
        "Analyse the changed files in this MR. 
         Report any bugs or security issues." \
        > review.md
    - cat review.md
  artifacts:
    paths: [review.md]
  only:
    - merge_requests
```

### Pre-commit Hook

```bash
# .git/hooks/pre-commit
#!/bin/bash
claude --print \
  "Review the staged changes: $(git diff --cached)
   If there are any critical bugs or security issues, list them.
   If everything looks good, just say OK." \
  | grep -i "ok" || {
    echo "Claude Code flagged issues with staged changes. Review above."
    exit 1
  }
```

---

## Headless / Scriptable Mode

Claude Code is fully scriptable without an interactive session:

```bash
#!/bin/bash
# daily-security-scan.sh

REPORT=$(claude --print \
  "Scan this codebase for:
   1. Hardcoded credentials or API keys
   2. SQL injection vulnerabilities  
   3. Missing input validation on user-facing endpoints
   4. Outdated dependencies with known CVEs (check package.json)
   Output a JSON report with severity levels.")

echo "$REPORT" | jq '.'
echo "$REPORT" > security-report-$(date +%Y%m%d).json
```

---

## Key Takeaways

- Hooks run shell commands at lifecycle events — they are deterministic, not AI-decided
- Common uses: auto-format, lint before commit, notify on completion, audit logging
- Configure hooks in `.claude/settings.json` using tool matchers
- Claude Code works headlessly with `--print` flag — perfect for CI/CD
- Use `--dangerously-skip-permissions` in CI where no human is reviewing diffs
