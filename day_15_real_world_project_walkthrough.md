# Day 15 — Real-World Project Walkthrough

## Introduction

This final lesson brings everything together in a realistic scenario: you've just joined a team, you have a feature to build, and you'll use Claude Code from start to finish — onboarding, planning, implementing, testing, reviewing, and deploying.

---

## The Scenario

**Project:** A Node.js REST API for a task management SaaS
**Your task:** Add email notifications when tasks are assigned to a user
**What exists:** Auth, tasks CRUD, users — no notifications yet

---

## Step 1: Onboard with Claude Code (Day 3 + 4)

```bash
cd ~/projects/task-api
claude
```

```
You: I just joined this team and need to add email notifications 
     for task assignments. Before I start:
     
     1. Give me an overview of the codebase architecture
     2. Show me how the existing features are structured (auth, tasks)
     3. Tell me what testing patterns are used
     4. Identify where email notification logic should go
     
     Then create a CLAUDE.md for this project.
```

Claude Code reads the codebase and produces:
- Architecture overview
- Pattern analysis (Express + Mongoose + Jest)
- Suggested location (`src/services/notifications/`)
- A CLAUDE.md capturing everything

```bash
git add CLAUDE.md
git commit -m "Add CLAUDE.md for project context"
```

---

## Step 2: Connect Tools via MCP (Day 9)

```bash
# Add GitHub MCP to create PRs and issues from CLI
claude mcp add-json github \
  '{"type":"http","url":"https://api.githubcopilot.com/mcp/",
    "headers":{"Authorization":"Bearer YOUR_GITHUB_PAT"}}' \
  --scope project

# Add SendGrid MCP (or similar email service)
claude mcp add sendgrid \
  --transport stdio \
  -- npx -y @sendgrid/mcp-server \
  --api-key $SENDGRID_API_KEY
```

---

## Step 3: Plan the Feature (Day 14 — Plan-then-Execute)

```
You: Plan the email notification feature for task assignments.
     Don't write any code yet.
     
     I need:
     - Email sent to assignee when a task is assigned to them
     - Email template with: task title, description, due date, link
     - Retry logic if email fails (don't fail the assignment itself)
     - The assignment API should not be slowed down by email sending
     - Tests for the notification service
     
     List every file you'd create or modify.
```

Claude Code produces a detailed plan:
```
Files to CREATE:
  src/services/notifications/emailService.js
  src/services/notifications/templates/taskAssigned.html
  src/services/notifications/notificationQueue.js
  tests/services/notifications/emailService.test.js

Files to MODIFY:
  src/controllers/tasks.js    (trigger notification on assign)
  src/config/index.js          (add email config)
  package.json                 (add nodemailer / SendGrid SDK)
  .env.example                 (add EMAIL_* vars)
```

---

## Step 4: Set Up Skills (Day 12)

```bash
mkdir -p .claude/commands
```

```markdown
<!-- .claude/commands/run-tests.md -->
# Run Tests

Run the full test suite and report results.

Steps:
1. `npm test -- --coverage`
2. Report: pass/fail count, coverage percentage
3. If failures, show the failing test names and error messages
4. If coverage drops below 80%, flag it as a warning
```

```markdown
<!-- .claude/commands/deploy-review.md -->
# Pre-Deploy Review

Before deploying, verify:
1. All tests pass: `npm test`
2. No lint errors: `npm run lint`
3. No console.log statements in src/
4. .env.example is up to date with any new variables
5. CHANGELOG.md has an entry for today's changes
```

---

## Step 5: Implement the Feature (Day 7 + 8)

```
You: Create the notification feature branch and implement it 
     following the plan we discussed.
     
     Start with:
     1. Create the git branch: feature/task-assignment-notifications
     2. Install nodemailer: npm install nodemailer
     3. Create the email service
     4. Create the HTML template
     5. Show me each file before writing it
```

Claude Code creates the branch and implements step by step, showing diffs.

After implementation:

```
You: /run-tests
```

```
Running: npm test -- --coverage
✗ 2 tests failing:
  - emailService.test.js: "should send email to assignee"
  - tasks.test.js: "should trigger notification on assignment"

Fixing...
```

Claude Code reads the failures, fixes them, re-runs:
```
✔ 47 tests passing (94% coverage)
```

---

## Step 6: Code Review (Day 12)

```
You: /review-pr
```

Claude Code reviews its own work against the checklist:

```
PR Review Summary
─────────────────
Changes: 6 files (+287 lines, -12 lines)

✔ Feature implemented correctly
✔ Async email sending (won't slow down assignment)
✔ Retry logic with exponential backoff
✔ Tests: 94% coverage on new code
✔ Template is responsive HTML

⚠ SUGGESTION: Email template missing unsubscribe link
              (required by CAN-SPAM)
⚠ SUGGESTION: Consider rate limiting notifications per user

✗ WARNING: SENDGRID_API_KEY not added to .env.example

Recommendation: Address the WARNING, consider the suggestions.
```

```
You: Fix the warning and add the unsubscribe link.
     Skip the rate limiting — we'll add that in a follow-up.
```

---

## Step 7: Create the Pull Request (MCP)

```
You: Create a GitHub PR for this branch. 
     Title: "feat: add email notifications for task assignments"
     
     Write a description that includes:
     - What the feature does
     - How it works (async queue, retry logic)
     - Testing: 47 tests passing, 94% coverage
     - New environment variables needed
     - Screenshots if any (there aren't any for an API)
```

Claude Code uses the GitHub MCP server:
```
[calls github.create_pull_request()]
Created PR #47: "feat: add email notifications for task assignments"
URL: https://github.com/myorg/task-api/pull/47
```

---

## Step 8: Pre-Deploy Check

```
You: /deploy-review
```

```
Pre-Deploy Review
─────────────────
✔ All 47 tests pass
✔ No lint errors (0 warnings)
✔ No console.log in src/ (0 found)
✔ .env.example updated (SENDGRID_API_KEY, EMAIL_FROM added)
⚠ CHANGELOG.md has no entry for today

Adding CHANGELOG entry...
Done. Ready to deploy.
```

---

## Complete Course Summary

| Day | What You Learned |
|-----|-----------------|
| 1 | Claude Code overview, architecture, surfaces |
| 2 | Installation via npm/Homebrew, authentication methods |
| 3 | First session, codebase exploration, reviewing diffs |
| 4 | CLAUDE.md for persistent instructions and auto-memory |
| 5 | Slash commands, keyboard shortcuts, CLI flags |
| 6 | Permission model, trust levels, config rules |
| 7 | File operations, multi-file changes, git integration |
| 8 | Test runner integration, debugging, fix-and-retry |
| 9 | MCP protocol, adding servers (DB, GitHub, Slack) |
| 10 | Sub-agents, parallel tasks, context management |
| 11 | Hooks for automation, CI/CD integration, headless mode |
| 12 | Skills, custom slash commands, team distribution |
| 13 | VS Code and JetBrains IDE extensions |
| 14 | Advanced patterns, cost management, model selection |
| 15 | Full end-to-end project walkthrough |

---

## Your Next Steps

1. **Install Claude Code** → `npm install -g @anthropic-ai/claude-code`
2. **Create a CLAUDE.md** → in your main project
3. **Add one MCP server** → GitHub or your database
4. **Write one custom skill** → `/review-pr` or `/run-tests`
5. **Add one hook** → auto-format after file writes

Each of these small steps compounds into a dramatically more productive workflow.

> Official documentation: https://code.claude.com/docs/en/overview
> Community & updates: https://www.anthropic.com/news

**Congratulations on completing the Claude Code course! 🎉**
