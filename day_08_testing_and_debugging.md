# Day 8 — Running Tests & Debugging

## Introduction

One of Claude Code's most valuable capabilities is its ability to run your test suite, read the output, understand what's failing, fix the issues, and re-run until everything passes — all autonomously. This lesson covers the full testing and debugging workflow.

---

## Running Tests

```bash
# Simple: ask Claude Code to run your tests
You: Run the tests.

# Claude Code discovers your test runner from:
# - package.json scripts
# - Makefile
# - pytest.ini / setup.cfg
# - CLAUDE.md instructions

# Then runs it and reports back:
# Running: npm test
# ✗ 3 tests failed:
#   - auth.test.js: "should reject expired tokens" 
#   - orders.test.js: "should calculate totals with discount"
#   - users.test.js: "should hash password before save"
```

---

## Fix-and-Retry Loop

This is where Claude Code shines:

```
You: Run the tests and fix anything that fails.

Claude Code:
1. Runs: pytest -v
2. Reads: test output (3 failures)
3. Reads: the failing test files + relevant source code
4. Identifies: root cause of each failure
5. Proposes: fixes for each
6. You approve
7. Runs: pytest -v (again)
8. Reports: All 47 tests passing ✔
```

You can run multiple iterations:

```
You: Run tests. Fix failures. Keep iterating until all tests pass.
     Tell me if you can't fix something without breaking other tests.
```

---

## Targeted Testing

```
# Run a specific test file
You: Run only the auth tests.

# Run a specific test
You: Run the test named "should reject expired tokens" and fix it.

# Run with coverage
You: Run tests with coverage and tell me which files 
     have less than 80% coverage.

# Run with specific flags
You: Run the tests in verbose mode with output captured.
```

---

## Debugging Errors

### Strategy 1: Paste the error

```
You: I'm getting this error when I run the app:

     Error: Cannot find module '@/components/auth/LoginForm'
     at Function.Module._resolveFilename (node:internal/modules)
     
     Fix it.
```

Claude Code will:
- Trace the import path
- Find where the file is (or isn't)
- Either fix the import path or create the missing file

### Strategy 2: Ask Claude Code to reproduce it

```
You: Run the app and trigger the login flow. 
     Tell me what errors appear in the console.
```

### Strategy 3: Add debugging

```
You: The payment processing sometimes returns undefined. 
     Add detailed logging to the payment service so we can 
     trace the exact point of failure, then trigger a payment 
     and show me the logs.
```

### Strategy 4: Rubber duck the problem

```
You: Walk me through exactly what happens when a user submits 
     the checkout form. Trace the code path step by step and 
     flag any place where the total could become undefined.
```

---

## Reading Log Files

```
You: The app has been logging to /var/log/myapp/error.log.
     Read the last 100 lines and tell me what's causing the 
     recurring "connection timeout" errors.
```

```
You: The Celery worker logs are in logs/worker.log.
     Group the errors by type and tell me which one is most 
     frequent in the last 24 hours.
```

---

## Writing Tests

```
You: Write tests for the OrderService class. 
     Cover the happy path, edge cases, and error conditions.
     Use the same patterns as the existing UserService tests.
```

```
You: The calculateShipping function in utils/shipping.js has no tests.
     Write a comprehensive test suite for it.
     Include: free shipping threshold, international rates, 
     weight limits, and invalid input handling.
```

---

## Fixing Flaky Tests

```
You: The test "should process concurrent requests" fails intermittently.
     Read it, understand why it might be flaky, and fix it 
     to be deterministic.
```

---

## Common Testing Workflows

### Pre-commit check
```bash
# In a shell script or hook:
claude "Run linting and tests. If anything fails, 
        list the failures but don't fix them — just report."
```

### Feature development loop
```
You: I need to add email verification to the signup flow.
     
     1. Write the tests first (TDD)
     2. Show me the tests for approval
     3. Implement the feature
     4. Run tests to confirm they pass
     5. Show me a summary of what was added
```

### Debugging production issues
```
You: We had an incident at 14:32 UTC today. The payment service 
     returned 500 errors for 8 minutes. Read the error logs in 
     logs/payment-2025-06-25.log and help me write a post-mortem.
```

---

## Key Takeaways

- Tell Claude Code to "run tests and fix failures" — it handles the loop
- Paste error messages directly into the conversation for fastest diagnosis
- Use coverage reports to find untested code, then ask Claude to write tests
- Claude Code reads log files directly — great for debugging production issues
- Always specify your preferred test patterns so Claude matches your style
