# Day 7 — Working with Files & Reviewing Diffs

## Introduction

Most of what Claude Code does involves reading and writing files. This lesson teaches you how to control which files it works with, how to review proposed changes before they're applied, and how to handle multi-file operations safely.

---

## How Claude Code Reads Files

Claude Code reads files automatically based on what it needs to answer your question or complete a task. It starts from your current working directory.

```
You: How does the authentication middleware work?

Claude Code:
→ Reads: middleware/auth.js
→ Reads: config/auth.js
→ Reads: models/User.js
→ Answers based on what it found
```

You can also explicitly ask it to read specific files:

```
You: Read the ARCHITECTURE.md file and summarise the deployment strategy.

You: Look at the last 5 git commits and tell me what changed.

You: Compare how error handling works in routes/api.js vs routes/web.js
```

---

## Controlling Which Files Claude Code Can Access

By default, Claude Code has read access to all files in your project. You can restrict this:

```json
// .claude/settings.json
{
  "permissions": {
    "deny": [
      "ReadFile(secrets/**)",
      "ReadFile(*.key)",
      "ReadFile(*.pem)"
    ]
  }
}
```

Or in `.claude/ignore` (similar to .gitignore):

```
# .claude/ignore
secrets/
*.pem
*.key
.env
node_modules/
dist/
__pycache__/
.terraform/
```

Files listed here are invisible to Claude Code — it won't read or write them.

---

## Reviewing File Changes (Diffs)

Every time Claude Code wants to write a file, it shows you a diff first:

```
Claude: I'd like to make the following changes:

  src/auth/middleware.js  (+12, -3)
  ────────────────────────────────────────────
  @@ -45,7 +45,16 @@
   
   function verifyToken(req, res, next) {
  -  const token = req.headers.authorization;
  +  const authHeader = req.headers.authorization;
  +  
  +  if (!authHeader || !authHeader.startsWith('Bearer ')) {
  +    return res.status(401).json({ error: 'Missing or invalid token format' });
  +  }
  +  
  +  const token = authHeader.split(' ')[1];
  +  
     if (!token) {
       return res.status(401).json({ error: 'No token provided' });
     }
  
  Apply this change? [y/n]
```

**Reading the diff:**
- Lines starting with `+` are added
- Lines starting with `-` are removed
- Context lines (no prefix) show surrounding code for orientation

---

## Multi-File Operations

When Claude Code needs to change multiple files:

```
You: Rename the User model to Account throughout the entire codebase.

Claude: This will affect 23 files. Here's the plan:
        
        1. models/User.js → models/Account.js (rename + content)
        2. controllers/userController.js (update imports)
        3. routes/users.js (update references)
        [... 20 more files]
        
        Show me the changes file by file, or apply all? [show/apply/cancel]
```

Best practice: use `show` for large refactors, `apply` only for simple changes you fully understand.

---

## Creating New Files

```
You: Create a new middleware file for rate limiting using express-rate-limit.
     Follow the same pattern as middleware/auth.js

Claude: I'll create middleware/rateLimit.js:

  [shows the new file content]
  
  Create this file? [y/n]
```

---

## Deleting Files

```
You: The legacy/old_api/ directory is unused. Delete it.

Claude: I'd like to delete the following files:
        - legacy/old_api/v1/routes.js
        - legacy/old_api/v1/handlers.js
        - legacy/old_api/models.js
        - legacy/old_api/ (directory)
        
        This cannot be undone. Proceed? [y/n]
```

**Safety tip:** Always have a clean git state before asking Claude Code to delete files. That way, `git checkout` can restore anything accidentally removed.

---

## Working with Git

Claude Code integrates naturally with git:

```
You: What changed since the last release tag?
→ Runs: git log v1.2.0..HEAD --oneline

You: Stage only the test files and commit them.
→ Runs: git add tests/ && git commit -m "Add tests for auth module"

You: Create a branch for this feature, make the changes, and open a PR.
→ Runs: git checkout -b feature/add-rate-limiting
→ Makes changes
→ Runs: git add -A && git commit -m "feat: add rate limiting middleware"
→ Runs: git push origin feature/add-rate-limiting
→ Opens PR via gh CLI (if installed)
```

---

## Practical File Workflows

### Refactoring with confidence

```bash
# Before starting a large refactor:
git stash  # or commit current work

# Then ask Claude Code to refactor
You: Extract the database connection logic from app.js into 
     a separate db/connection.js module. Update all imports.

# Review each file change
# If something looks wrong:
You: Undo that last change to routes/api.js — keep the original
```

### Code review assistance

```
You: Review all files changed in the last commit. 
     Flag any bugs, security issues, or style violations.
```

### Documentation generation

```
You: Add JSDoc comments to every exported function in src/utils/.
     Follow the existing style in src/auth/utils.js
```

### Large-scale find and replace

```
You: Find all uses of the old config.get() API and 
     replace them with the new config.read() API. 
     Show me each change before applying.
```

---

## Key Takeaways

- Claude Code shows a diff before every file write — always review it
- Use `.claude/ignore` to hide sensitive files from Claude Code
- For multi-file operations, ask to see changes file by file
- Keep a clean git state before large refactors — easy rollback
- Claude Code understands git natively — use it for branching and commits
