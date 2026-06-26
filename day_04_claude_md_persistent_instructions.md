# Day 4 — CLAUDE.md: Persistent Instructions & Memory

## Introduction

By default, Claude Code starts fresh every session — it knows nothing about your project standards, your team preferences, or what you told it yesterday. **CLAUDE.md** solves this. It's a markdown file Claude Code reads automatically at the start of every session, giving you persistent project context without repeating yourself.

---

## What Is CLAUDE.md?

`CLAUDE.md` is a markdown file in your project root (or home directory) that Claude Code reads before every session. Think of it as a permanent briefing document for your AI collaborator.

```
my-project/
├── CLAUDE.md           ← Claude Code reads this first, every time
├── src/
├── tests/
└── README.md
```

---

## Creating Your First CLAUDE.md

```bash
# In your project root:
touch CLAUDE.md
claude  # Now Claude Code will read it on startup
```

Or ask Claude Code to create it for you:
```
You: Create a CLAUDE.md for this project based on what you 
     can see in the codebase.
```

---

## What to Put in CLAUDE.md

### Project Overview
```markdown
# My E-commerce Platform

## What This Is
A Django REST API powering a B2B e-commerce platform.
Backend: Django 4.2 + PostgreSQL + Redis + Celery
Frontend: Separate React repo (not in this directory)
```

### Architecture Decisions
```markdown
## Architecture

- We use repository pattern — never query the DB directly in views
- Business logic lives in services/, not in models or views
- All async tasks use Celery (see tasks.py in each app)
- Authentication is JWT-based using djangorestframework-simplejwt
```

### Coding Standards
```markdown
## Coding Standards

- Python: Follow PEP 8, max line length 88 (Black formatter)
- All new code must have type hints
- Docstrings required for all public functions (Google style)
- No raw SQL — use Django ORM or stored procedures in db/procedures/
```

### How to Run Things
```markdown
## Commands

# Run development server
make dev

# Run tests (always use this, not pytest directly)
make test

# Run specific test file
pytest tests/test_orders.py -v

# Apply migrations
python manage.py migrate

# Create migration
python manage.py makemigrations <app_name>
```

### What to Avoid
```markdown
## Important Constraints

- NEVER modify migration files — create new ones instead
- NEVER commit .env files — use .env.example as template
- NEVER bypass the permission system in permissions.py
- The orders app is owned by the fulfilment team — ask before changing
```

### Preferred Libraries
```markdown
## Preferred Libraries

- HTTP requests: httpx (not requests)
- Date/time: pendulum (not datetime directly)
- Config: dynaconf (settings are in config/)
- Testing: pytest + factory_boy for fixtures
```

---

## CLAUDE.md Locations & Hierarchy

Claude Code reads CLAUDE.md files from multiple locations, in this order:

```
1. ~/.claude/CLAUDE.md          ← your global personal preferences
2. /project-root/CLAUDE.md      ← project-wide standards (commit to Git)
3. /project-root/src/CLAUDE.md  ← subdirectory-specific rules (optional)
```

**Use case example:**
- `~/.claude/CLAUDE.md` → "I prefer concise explanations. Always use type hints."
- `/project/CLAUDE.md` → "This is a Node.js API. Use pnpm. Tests in Vitest."
- `/project/frontend/CLAUDE.md` → "React 18, Tailwind, no class components."

---

## Auto Memory

Beyond CLAUDE.md, Claude Code builds **automatic memory** as it works:

```
You: The build command is "pnpm build --mode production"
Claude Code: Got it. I'll remember that for this project.
             [Saves to project memory automatically]
```

Auto-memories are stored separately from CLAUDE.md and build up over time — things like:
- Build commands Claude discovered
- Debugging insights from previous sessions
- File locations it found useful
- Project-specific patterns it learned

---

## Real-World CLAUDE.md Template

```markdown
# Project: [Your Project Name]

## Quick Context
[One paragraph: what the project does, who uses it, current state]

## Stack
- Language: 
- Framework: 
- Database: 
- Cache: 
- Testing: 
- CI/CD: 

## Key Commands
```bash
# Start dev environment
[command]

# Run tests
[command]

# Lint & format
[command]

# Build for production
[command]
```

## Architecture Notes
[Key decisions, patterns, what lives where]

## Coding Standards
[Style guide, naming conventions, patterns to follow/avoid]

## Things to NEVER Do
[Hard rules — security, data integrity, team conventions]

## Current Focus / Active Work
[What the team is working on right now — update periodically]

## Useful Files to Know
- `src/config.ts` — all environment configuration
- `src/lib/auth.ts` — authentication utilities
- `src/types/` — shared TypeScript type definitions
```

---

## Keeping CLAUDE.md Updated

```
You: Update CLAUDE.md to reflect that we migrated from 
     PostgreSQL to PlanetScale last week.
```

```
You: Add to CLAUDE.md that the payment service is now 
     microserviced at payment-service.internal:8080
```

Treat CLAUDE.md like living documentation — update it when the project changes.

---

## Key Takeaways

- CLAUDE.md is read automatically at the start of every session
- Commit project-level CLAUDE.md to Git — it benefits the whole team
- Use `~/.claude/CLAUDE.md` for personal preferences across all projects
- Auto-memory supplements CLAUDE.md with things Claude learned during sessions
- The better your CLAUDE.md, the less time you spend re-explaining context
