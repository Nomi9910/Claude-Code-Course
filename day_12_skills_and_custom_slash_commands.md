# Day 12 — Skills & Custom Slash Commands

## Introduction

**Skills** are reusable, shareable workflows you define once and invoke with a slash command. They package your team's best practices — PR review checklists, deployment procedures, security scans — into commands that anyone on the team can run consistently.

---

## What Are Skills?

A skill is a markdown file in `.claude/commands/` that defines a workflow. When you type `/review-pr` in Claude Code, it reads the corresponding skill file and follows its instructions.

```
.claude/
└── commands/
    ├── review-pr.md       → /review-pr
    ├── deploy-staging.md  → /deploy-staging
    ├── security-scan.md   → /security-scan
    └── write-tests.md     → /write-tests
```

---

## Creating Your First Skill

```bash
mkdir -p .claude/commands
```

```markdown
<!-- .claude/commands/review-pr.md -->
# PR Review

Review the current git diff against main for a pull request.

## What to check:

1. **Correctness** — Does the code do what the PR description says?
2. **Tests** — Are there adequate tests for new functionality?
3. **Security** — Any SQL injection, XSS, exposed secrets, missing auth?
4. **Performance** — Any N+1 queries, missing indexes, inefficient loops?
5. **Style** — Does it follow our coding standards in CLAUDE.md?
6. **Breaking changes** — Anything that would break existing API consumers?

## Output format:

Produce a structured review with:
- Summary of changes
- Issues found (CRITICAL / WARNING / SUGGESTION)
- Approval recommendation

Run: `git diff main...HEAD` to see the changes.
```

Now use it:
```
You: /review-pr
Claude Code: [follows the skill instructions automatically]
```

---

## Advanced Skill Examples

### Deploy to staging

```markdown
<!-- .claude/commands/deploy-staging.md -->
# Deploy to Staging

Deploy the current branch to the staging environment.

## Steps:

1. Run tests: `npm test`
   - If tests fail, STOP and report the failures. Do not continue.

2. Build: `npm run build`

3. Check Docker is running: `docker info`

4. Deploy: `docker compose -f docker-compose.staging.yml up -d --build`

5. Wait 30 seconds for services to start, then run health check:
   `curl -f http://staging.internal/health`

6. Tail logs for 10 seconds to check for errors:
   `docker compose -f docker-compose.staging.yml logs --tail=50`

7. Report: tell me if deploy succeeded or failed, and any warnings from logs.
```

### Security scan

```markdown
<!-- .claude/commands/security-scan.md -->
# Security Scan

Perform a security audit of this codebase.

## Checks to run:

1. **Secrets** — Search for hardcoded API keys, passwords, tokens
   Patterns: `api_key`, `secret`, `password =`, `token =`, `sk-`, `ghp_`

2. **SQL Injection** — Find raw SQL queries with string concatenation

3. **Dependencies** — Run `npm audit` or `pip-audit` (whichever applies)

4. **Auth** — List all API endpoints and verify each has auth middleware

5. **Environment** — Verify .env is in .gitignore; check git log for secret exposure

## Output:
Produce SECURITY_REPORT.md with all findings, severity levels (CRITICAL/HIGH/MEDIUM/LOW), 
and remediation steps for each issue.
```

### Write tests for a file

```markdown
<!-- .claude/commands/write-tests.md -->
# Write Tests

Write comprehensive tests for the file I specify.

## Process:

1. Ask me: "Which file should I write tests for?"
2. Read that file carefully
3. Read existing test files to understand the testing patterns we use
4. Write tests covering:
   - Happy path (expected inputs)
   - Edge cases (empty, null, boundary values)
   - Error cases (invalid inputs, exceptions)
   - Each public function/method independently

5. Use the same testing framework and assertion style as existing tests
6. Name tests descriptively: "should [behaviour] when [condition]"
7. Show me the test file before creating it
```

---

## Skill Variables

You can pass arguments to skills:

```markdown
<!-- .claude/commands/create-feature.md -->
# Create Feature: $ARGUMENTS

Create a new feature branch and scaffold the files for: $ARGUMENTS

Steps:
1. Create branch: `git checkout -b feature/$ARGUMENTS`
2. Create the main implementation file
3. Create the test file
4. Update relevant imports
5. Add an entry to CHANGELOG.md
```

Usage:
```
You: /create-feature user-notifications
```

---

## Team Skills (Plugins)

For teams, skills can be packaged into **plugins** — versioned bundles of MCP servers + slash commands + skills that the whole team installs with one command.

```bash
# Install a team plugin (distributed via npm or marketplace)
/plugin install @mycompany/engineering-toolkit

# Now everyone gets:
/security-scan     → company security checklist
/deploy-prod       → company deployment workflow
/incident-response → runbook for production incidents
```

---

## Sharing Skills with Your Team

```bash
# Skills in .claude/commands/ → commit to Git → whole team gets them
git add .claude/commands/
git commit -m "Add team slash commands for review and deploy"
git push
```

When teammates pull the branch, they get all the skills immediately.

---

## Built-in Skills

Claude Code ships with built-in skills. Use `/help` to see them. You can disable built-in skills if you don't need them:

```json
// .claude/settings.json
{
  "disableBundledSkills": true
}
```

---

## Key Takeaways

- Skills are markdown files in `.claude/commands/` that become slash commands
- They encode your team's best practices into repeatable, consistent workflows
- Commit skills to Git so the whole team shares them
- Skills can run commands, check outputs, and produce structured reports
- Advanced teams package skills + MCP servers into plugins for easy distribution
