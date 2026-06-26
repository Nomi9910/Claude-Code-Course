# Day 2 — Installation & Authentication

## Introduction

This lesson covers every way to install Claude Code and all authentication options. Getting this right means you'll never hit a "not authorised" wall mid-session.

---

## Method 1: npm (Recommended for Terminal CLI)

Claude Code is published as an npm package: `@anthropic-ai/claude-code`

```bash
# Requires Node.js 18 or higher
node --version          # confirm >= 18

# Install globally
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version

# Launch Claude Code in your project
cd ~/my-project
claude
```

**Why global install?** You want `claude` available in every project directory, not just one.

```bash
# If you see permission errors on Linux/macOS:
# Option A — Use a Node version manager (recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install --lts
nvm use --lts
npm install -g @anthropic-ai/claude-code

# Option B — Fix npm global directory
mkdir -p ~/.npm-global
npm config set prefix ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
npm install -g @anthropic-ai/claude-code
```

---

## Method 2: Homebrew (macOS)

```bash
# Stable release (recommended — skips releases with regressions)
brew install --cask claude-code

# Or install directly via npm after brew installs node
brew install node
npm install -g @anthropic-ai/claude-code
```

---

## Method 3: VS Code Extension

1. Open VS Code
2. Press `Ctrl+Shift+X` (Extensions panel)
3. Search: **Claude Code**
4. Click Install
5. Open Command Palette (`Ctrl+Shift+P`) → "Claude Code: Sign In"

---

## Method 4: JetBrains Plugin

1. Open your JetBrains IDE (IntelliJ, PyCharm, etc.)
2. Go to: **Settings → Plugins → Marketplace**
3. Search: **Claude Code**
4. Install → Restart IDE
5. Look for the Claude Code panel in the right sidebar

---

## Authentication Options

### Option A — Claude.ai Account (Pro / Max / Team / Enterprise)

If you have a Claude subscription, this is the simplest path:

```bash
claude
# On first launch, Claude Code opens a browser window
# Sign in with your Anthropic account
# Authorise Claude Code
# You're authenticated — return to terminal
```

Subscription tiers and what you get:
- **Pro** — Sufficient for personal use, moderate rate limits
- **Max** — Higher rate limits, priority access
- **Team** — Multiple seats, shared billing
- **Enterprise** — SSO, audit logs, custom rate limits, admin controls

> Check current plans at: https://claude.ai/upgrade

### Option B — Anthropic Console API Key

Useful for CI/CD, servers, or when you want pay-per-use billing:

```bash
# 1. Generate key at: https://console.anthropic.com/settings/keys
# 2. Set the environment variable:
export ANTHROPIC_API_KEY="sk-ant-api03-..."

# Add to ~/.bashrc or ~/.zshrc for persistence:
echo 'export ANTHROPIC_API_KEY="sk-ant-api03-..."' >> ~/.bashrc
source ~/.bashrc

# Launch Claude Code — it picks up the key automatically
claude
```

### Option C — Third-Party Providers (CLI and VS Code only)

Claude Code CLI and VS Code extension support alternative model providers:

```bash
# Amazon Bedrock
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION=us-east-1
# AWS credentials via ~/.aws/credentials or IAM role

# Google Vertex AI
export CLAUDE_CODE_USE_VERTEX=1
export CLOUD_ML_REGION=us-east5
export ANTHROPIC_VERTEX_PROJECT_ID=my-gcp-project

claude
```

---

## Post-Install Configuration

```bash
# Check Claude Code version
claude --version

# View all CLI flags and options
claude --help

# Update to latest version
npm update -g @anthropic-ai/claude-code

# Check current authentication status
claude /status

# Open settings interactively
claude /settings
```

---

## Windows Setup

Claude Code works natively on Windows, but WSL gives the best experience:

```powershell
# PowerShell — native Windows install
npm install -g @anthropic-ai/claude-code
claude
```

```bash
# WSL (Windows Subsystem for Linux) — recommended
# Install WSL first: wsl --install
# Then inside WSL:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install --lts
npm install -g @anthropic-ai/claude-code
claude
```

**Why WSL?** The `bash` tool in Claude Code uses your shell. With native Windows (no Git for Windows), it falls back to PowerShell, which limits some operations. WSL gives Claude full bash access.

---

## Auto-Updates

Claude Code automatically updates itself in the background when installed via npm. You can also:

```bash
# Force update
npm update -g @anthropic-ai/claude-code

# Check for updates
claude update

# Pin to a specific version (not recommended for most users)
npm install -g @anthropic-ai/claude-code@2.1.100
```

---

## Troubleshooting Installation

```bash
# "command not found: claude" after install
# → npm global bin not in PATH
npm bin -g                    # shows where npm puts global bins
echo $PATH                    # check if that path is included
# Fix: add npm global bin to PATH in ~/.bashrc

# "EACCES: permission denied" during install
# → Don't use sudo with npm — use nvm instead (see Method 1 above)

# "syntax error near unexpected token '<'" during curl
# → You're behind a proxy or firewall; try: npm install -g @anthropic-ai/claude-code

# Verify everything works
claude --version
claude "Hello! What files are in the current directory?"
```
