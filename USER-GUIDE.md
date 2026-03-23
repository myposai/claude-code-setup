# Claude Code Power User Guide

Your setup: **GSD v1.25.1** (project orchestration) + **ECC cherry-picks** (code quality) + **4 official plugins** + **custom n8n MCP**.

---

## Quick Reference: What to Use When

| You want to... | Use this |
|---|---|
| Start a new project from scratch | `/gsd:new-project` |
| Plan a feature phase | `/gsd:plan-phase <number>` |
| Execute a planned phase | `/gsd:execute-phase <number>` |
| Do a quick one-off task | `/gsd:quick` |
| Build a feature with guided workflow | `/feature-dev:feature-dev <description>` |
| Write tests first, then code | `/ecc:tdd` |
| Debug a stubborn issue | `/gsd:debug <description>` |
| Review a PR | `/code-review:code-review <PR number>` |
| Commit + push + open PR | `/commit-commands:commit-push-pr` |
| Just commit | `/commit-commands:commit` |
| Check project progress | `/gsd:progress` |
| Resume after a break | `/gsd:resume-work` |
| Pause and save context | `/gsd:pause-work` |
| Capture a quick idea | `/gsd:note <text>` |
| Audit CLAUDE.md quality | `/claude-md-management:claude-md-improver` |
| Update CLAUDE.md with session learnings | `/claude-md-management:revise-claude-md` |
| Clean up merged branches | `/commit-commands:clean_gone` |

---

## Daily Workflow

### Starting Your Day
```
/gsd:resume-work          # Restores full context from last session
/gsd:progress             # Shows where you left off + next action
```

### The GSD Cycle (for planned work)
```
/gsd:discuss-phase 7      # Clarify what phase 7 needs (optional)
/gsd:plan-phase 7         # Creates detailed PLAN.md with tasks
/gsd:execute-phase 7      # Executes plan with atomic commits per task
/gsd:verify-work 7        # Validates the result against goals
```

### Quick Tasks (skip the ceremony)
```
/gsd:quick                # For small ad-hoc work with GSD quality guarantees
```
Use `--discuss` or `--research` flags if the quick task needs context gathering first.

### Feature Development (for complex features)
```
/feature-dev:feature-dev Add webhook retry logic with exponential backoff
```
This runs a 7-phase guided workflow: Discovery > Exploration > Questions > Architecture > Implementation > Review > Summary.

### Test-Driven Development
```
/ecc:tdd
```
Enforces red-green-refactor: scaffold interfaces > write tests FIRST > implement minimal code to pass > refactor. Targets 80%+ coverage.

### Ending Your Day
```
/gsd:pause-work           # Saves full context for tomorrow
```

---

## Automatic Protections (Hooks)

These run automatically — you don't invoke them:

| Hook | When | What it does |
|---|---|---|
| **Config Protection** | Before any Write/Edit | Blocks you from weakening linter/formatter configs (.ruff.toml, .prettierrc, etc.) |
| **Console Warn** | After editing .js/.ts/.py | Warns if `console.log()` or `print()` debug statements are left in |
| **Quality Gate** | After editing .py | Auto-runs ruff to catch lint issues immediately |
| **Context Monitor** | After every tool use | Warns at 35% remaining context, critical alert at 25% |
| **Session Evaluator** | When session ends | Extracts reusable patterns for continuous learning |
| **Status Line** | Always visible | Shows model, current task, context usage bar |

---

## Project Management

### Roadmap & Milestones
```
/gsd:stats                # Project statistics and timeline
/gsd:add-phase <desc>     # Add a new phase to the roadmap
/gsd:insert-phase 3.1 ... # Insert urgent work between phases 3 and 4
/gsd:remove-phase 8       # Remove a future phase
/gsd:complete-milestone    # Archive current milestone, start next
```

### Notes & Todos
```
/gsd:note Fix the webhook timeout edge case    # Quick capture
/gsd:add-todo Refactor test executor            # More formal task
/gsd:check-todos                                # List and work on pending items
```

### Debugging
```
/gsd:debug The webhook returns 502 after 30 seconds
```
Uses scientific method: evidence > hypothesis > test. State persists across context resets.

---

## Code Quality Tools

### Security Review
Your setup includes an `ecc-security-reviewer` agent that checks:
- OWASP Top 10 vulnerabilities
- Hardcoded secrets
- Input validation gaps
- Injection flaws (SQL, command, XSS)
- Authentication/authorization issues

### Python Review
The `ecc-python-reviewer` agent checks:
- PEP 8 compliance
- Type hints
- ruff/mypy/bandit findings
- Framework patterns

These agents are available for Claude to use automatically during code review and feature development workflows.

### Project-Level Rules (always active)
Your `.claude/rules/` directory enforces:
- **coding-style.md** — Clean code standards (functions <50 lines, files <800 lines)
- **testing.md** — Test expectations (unit, integration, E2E patterns)
- **security.md** — Security-first coding practices
- **python-patterns.md** — Pythonic conventions and patterns

---

## n8n Agent Pipeline

The core pipeline (defined in CLAUDE.md) runs when you paste a Jira ticket:

```
1. Paste ticket text        → Requirements extracted automatically
2. Validation runs          → Asks targeted questions if gaps found
3. Say "proceed"            → Generation + validation + deploy
4. Tests run automatically  → Pass: activate. Fail: retry with fixes (up to 5x)
5. Jira comment posted      → Pipeline complete
```

### Key Commands
```bash
# Health check
op run --env-file=env.1password -- python agent.py --health-check

# Docker (when set up)
docker compose up -d --build      # Start
docker compose logs -f n8n-agent  # Watch logs
docker compose down               # Stop
```

---

## Docker Setup (Persistent Operation)

### First-Time Setup
1. Create secrets file:
   ```bash
   op run --env-file=env.1password -- env | grep -E '^(N8N_|OPENAI_|JIRA_)' > .env.docker
   ```
2. Build and start:
   ```bash
   docker compose up -d --build
   ```
3. Enable Docker Desktop auto-start: Settings > General > "Start Docker Desktop when you sign in"

### The container will:
- Auto-restart on crash (`restart: unless-stopped`)
- Run health checks every 60 seconds
- Persist job state via mounted `./jobs` volume
- Stay isolated from your local Python environment

---

## Tips & Patterns

### Choosing the Right Workflow
- **Simple bug fix** → Just do it directly, or `/gsd:quick`
- **Small feature (< 1 hour)** → `/gsd:quick --discuss`
- **Medium feature (1-4 hours)** → `/feature-dev:feature-dev <description>`
- **Large feature (multi-day)** → `/gsd:plan-phase` + `/gsd:execute-phase`
- **New project** → `/gsd:new-project`

### Model Profiles
```
/gsd:set-profile quality      # Opus for everything (expensive, thorough)
/gsd:set-profile balanced     # Sonnet default, Opus for complex (recommended)
/gsd:set-profile budget       # Haiku where possible (fast, cheap)
```

### When Context Gets Low
The status bar shows context usage. When you see warnings:
1. Finish your current task
2. Use `/gsd:pause-work` to save state
3. Start a fresh session
4. Use `/gsd:resume-work` to continue

### Useful GSD Shortcuts
- `/gsd:do <anything>` — Routes freeform text to the right GSD command
- `/gsd:autonomous` — Run all remaining phases without stopping
- `/gsd:health` — Diagnose and repair planning directory issues
- `/gsd:settings` — Configure workflow toggles

---

## File Locations

| What | Where |
|---|---|
| GSD agents | `~/.claude/agents/gsd-*.md` |
| ECC agents | `~/.claude/agents/ecc-*.md` |
| GSD commands | `~/.claude/commands/gsd/` |
| ECC commands | `~/.claude/commands/ecc/` |
| Hooks | `~/.claude/hooks/` (gsd-* and ecc-*) |
| Hook libraries | `~/.claude/hooks/ecc-lib/` |
| Project rules | `.claude/rules/` (this repo only) |
| Skills | `~/.claude/skills/` |
| Settings | `~/.claude/settings.json` |
| Settings backup | `~/.claude/backups/settings.json.pre-ecc` |
| Project state | `.planning/` |
| Job state | `jobs/{job_id}/state.json` |
