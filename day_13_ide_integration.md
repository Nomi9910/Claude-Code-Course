# Day 13 — IDE Integration (VS Code & JetBrains)

## Introduction

Claude Code works inside your IDE so you can access it without leaving your editor. The IDE integrations share the same engine as the CLI — your CLAUDE.md files, settings, and MCP servers all work identically.

---

## VS Code Integration

### Installation

1. Open VS Code
2. Press `Ctrl+Shift+X` to open Extensions
3. Search: **Claude Code**
4. Click **Install**
5. Reload VS Code when prompted

### Authenticating

```
Ctrl+Shift+P → "Claude Code: Sign In"
```

Follow the browser flow to connect your Anthropic account or API key.

### Using Claude Code in VS Code

The Claude Code panel opens on the right side of your editor:

```
View → Claude Code
OR
Ctrl+Shift+P → "Claude Code: Open"
```

**Key interactions:**

```
# Ask about selected code
1. Select code in editor
2. Right-click → "Ask Claude Code"
   OR press Ctrl+Shift+A

# Fix selected code
1. Select problematic code
2. Right-click → "Fix with Claude Code"

# Explain selected code
1. Select code
2. Right-click → "Explain with Claude Code"

# Generate code at cursor
1. Position cursor
2. Ctrl+Shift+P → "Claude Code: Generate"
3. Type what you want
```

### VS Code Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+A` | Ask Claude about selection |
| `Ctrl+Shift+C` | Open Claude Code panel |
| `Ctrl+Enter` | Submit message |
| `Escape` | Cancel/close panel |

### Inline Suggestions

Claude Code can provide inline code suggestions as you type (similar to GitHub Copilot):

```
VS Code Settings → Claude Code → Enable inline suggestions
```

### Working with the Current File

```
In Claude Code panel:

"Refactor this file to use async/await"
"Add error handling to every async function in this file"
"What does the selected function do?"
"Add TypeScript types to this JavaScript file"
```

Claude Code has context of the currently open file and your selection automatically.

---

## JetBrains Integration

Supported IDEs: IntelliJ IDEA, PyCharm, WebStorm, GoLand, PhpStorm, CLion, DataGrip, Rider

### Installation

1. Open your JetBrains IDE
2. Go to: **File → Settings → Plugins → Marketplace**
3. Search: **Claude Code**
4. Click **Install**
5. Restart IDE when prompted

### Authenticating

```
Settings → Tools → Claude Code → Sign In
```

### Using Claude Code in JetBrains

The Claude Code panel appears in the right sidebar:

```
View → Tool Windows → Claude Code
```

**Key interactions:**

```
# Context menu (right-click in editor):
- "Ask Claude Code" — ask about selected code
- "Explain with Claude Code"
- "Fix with Claude Code"
- "Generate Tests"
- "Refactor"

# Keyboard shortcut:
Alt+Shift+C → Open Claude Code with current selection
```

### Code Generation in JetBrains

```
// Type a comment describing what you want:
// create a function that validates an email address
[Press Ctrl+\ to trigger Claude Code]
```

### Running Terminal Commands from JetBrains

Claude Code in JetBrains can use the embedded terminal:

```
"Run the tests for the currently open file"
"Install the missing dependency and add it to requirements.txt"
```

---

## Tips for IDE Usage

### Provide context with your question

```
# Less effective:
"Fix this"

# More effective:
"This function is supposed to validate UK postcodes but it's 
 accepting invalid formats like XX11. Fix the regex."
```

### Use selection for precision

Select exactly the code you want to discuss before asking. Claude Code gets that selection as context — you don't need to copy-paste it.

### Multi-file tasks from IDE

For tasks spanning multiple files, the CLI is often more powerful. Use the IDE integration for:
- Explaining/refactoring individual functions
- Adding types, docs, or tests to the current file
- Quick fixes and code generation

Use the terminal CLI for:
- Refactoring across many files
- Running full test suites
- Git operations
- Complex, multi-step tasks

### Syncing IDE and CLI

Both use the same configuration:
- `.claude/settings.json` — permission rules
- `.claude/commands/` — custom slash commands
- `.claude/CLAUDE.md` — project instructions
- `.mcp.json` — MCP server config

Changes made in CLI sessions (like learned memories) are visible in IDE sessions and vice versa.

---

## Key Takeaways

- VS Code and JetBrains extensions share the same engine as the CLI
- Right-click context menu is the fastest way to use Claude Code in an IDE
- Your CLAUDE.md, settings, and MCP servers work in IDE mode too
- IDE integration is best for single-file tasks; CLI is better for multi-file work
- Keyboard shortcuts (`Ctrl+Shift+A` in VS Code, `Alt+Shift+C` in JetBrains) are your friends
