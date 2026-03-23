# Claude Code Setup: GSD + ECC Best-of-Both-Worlds

A curated Claude Code enhancement setup combining **GSD** (Get Shit Done) for project orchestration with cherry-picked **everything-claude-code (ECC)** components for code quality.

## What's Included

| Component | Count | Source |
|---|---|---|
| Agents | 2 (Python reviewer, Security reviewer) | ECC |
| Hooks | 6 (config protection, console warn, quality gate, session evaluator, + 2 utilities) | ECC |
| Hook libraries | 3 (utils, resolve-formatter, package-manager) | ECC |
| Commands | 1 (`/ecc:tdd` — TDD workflow) | ECC |
| Rules | 4 (coding-style, testing, security, python-patterns) | ECC |
| Skills | 2 (security-review, continuous-learning) | ECC |
| Docker config | Dockerfile, docker-compose.yml, .dockerignore | Custom |

> **Note:** This repo contains only the ECC cherry-picks and Docker config. GSD must be installed separately via its own installer.

## Prerequisites

- [Claude Code CLI](https://claude.ai/claude-code) installed
- [GSD](https://github.com/get-shit-done/gsd) installed (provides 15 agents, 38 commands, 3 hooks)
- [Official plugins](https://github.com/anthropics/claude-plugins-official) enabled (feature-dev, claude-md-management, commit-commands, code-review)

## Installation

### Quick Install (copy files to Claude Code config)

```bash
# Agents
cp agents/*.md ~/.claude/agents/

# Hooks + libraries
cp hooks/ecc-*.js ~/.claude/hooks/
mkdir -p ~/.claude/hooks/ecc-lib
cp hooks/lib/*.js ~/.claude/hooks/ecc-lib/

# Commands
mkdir -p ~/.claude/commands/ecc
cp commands/ecc/*.md ~/.claude/commands/ecc/

# Skills
mkdir -p ~/.claude/skills/security-review ~/.claude/skills/continuous-learning
cp skills/security-review/* ~/.claude/skills/security-review/
cp skills/continuous-learning/* ~/.claude/skills/continuous-learning/

# Rules (project-level — copy to your project's .claude/rules/)
mkdir -p <your-project>/.claude/rules
cp rules/*.md <your-project>/.claude/rules/
```

### Update settings.json

Merge the hook configuration from `settings-template.json` into your `~/.claude/settings.json`. The template adds:

- **PreToolUse** — config protection (blocks weakening linter configs)
- **PostToolUse** — console.log/print() warnings + quality gate (ruff after .py edits)
- **Stop** — session evaluator (continuous learning pattern extraction)

### Docker Setup (for n8n-ai-agent or similar)

```bash
cp docker/Dockerfile <your-project>/
cp docker/docker-compose.yml <your-project>/
cp docker/.dockerignore <your-project>/

# Create secrets file (not committed)
# Populate .env.docker with your environment variables

docker compose up -d --build
```

## Usage Guide

See [USER-GUIDE.md](USER-GUIDE.md) for the complete workflow reference.

## Architecture Decision

**Why this merge instead of using ECC fully?**

- GSD's hierarchical planning (roadmaps, milestones, phases, state management) has no equivalent in ECC
- ECC's 200+ skills and 28 agents are mostly irrelevant for a Python-focused project
- Cherry-picking gives us code quality tooling without context bloat
- Both systems use `~/.claude/` — prefixing files (`gsd-*` vs `ecc-*`) prevents collisions

## Credits

- [GSD (Get Shit Done)](https://github.com/get-shit-done/gsd) — Project orchestration
- [everything-claude-code](https://github.com/affaan-m/everything-claude-code) — Code quality components (Anthropic hackathon winner)
- [claude-plugins-official](https://github.com/anthropics/claude-plugins-official) — Official Anthropic plugins
