# Claude MEOS — My Extensible Operating System

Your personal OS, powered by Claude Code. Clone, run `/init-meos`, and Claude becomes your active work partner — managing projects, tracking daily notes, running code reviews, and keeping you focused.

**Built for Claude Code beginners** who want a structured workspace without configuring everything from scratch.

## What You Get

**Workspace** — a structured folder for managing your work life:
- Daily notes with morning planning and evening reflection
- Project tracking with CLAUDE.md-driven status and tasks
- Quick capture for ideas, links, and meeting notes

**Claude Config** — pre-built agents and skills:
- `/meos` command — session start, daily notes, project status, end-of-day reflection
- `/meos-find-skill` — search and install skills from [skills.sh](https://skills.sh)
- 8 agents — research orchestrator, code review team, build/lint/execute runners
- 4-layer memory — CLAUDE.md instructions, MEMORY.md quick ref, lessons.md for mistakes, QMD for deep search

## Quickstart

```bash
# 1. Clone
git clone https://github.com/ssv445/claude-meos.git
cd claude-meos

# 2. Run setup — skills work instantly, no copying needed
claude
# Then type: /init-meos
```

The `/init-meos` wizard will:
1. Ask your name, workspace path, and code directory
2. Create workspace folders and generate your CLAUDE.md files
3. Install agents and skills to `~/.claude/` (for global access)
4. Optionally configure QMD semantic search
5. Offer to create your first project

## Usage

After setup, open Claude Code in your workspace:

```bash
cd ~/workspace  # or wherever you set it up
claude
```

### Daily Workflow

| Command | What it does |
|---------|-------------|
| `/meos start` | Morning kickoff — shows what's pending, asks what to focus on |
| `/meos daily` | Creates today's daily note from template |
| `/meos status` | Quick table of all projects and their top tasks |
| `/meos eod` | Evening reflection — planned vs done, set tomorrow's priority |
| `/meos new-project` | Create a new project with CLAUDE.md |
| `/meos-find-skill` | Search and install skills from [skills.sh](https://skills.sh) |
| `/meos-find-skill search <q>` | Search skills by keyword |
| `/meos-find-skill install <repo>` | Install a skill from GitHub |

### Code Review

When working in a code repo, Claude automatically uses the review agents:

```
"Do a full code review"
→ Spawns code-standards + architecture + error-handling reviewers in parallel
→ Deduplicates findings into a single prioritized report
```

### Research

```
"Research how to add OAuth to this app"
→ Spawns web researcher + code researcher in parallel
→ Synthesizes external docs with codebase patterns
```

## Architecture

### 4-Layer Memory

| Layer | What | Where | When |
|-------|------|-------|------|
| CLAUDE.md | Instructions for Claude's behavior | Repo root / `~/.claude/` | Always loaded |
| MEMORY.md | Per-project quick reference | `~/.claude/projects/*/memory/` | Auto-loaded, 200 line limit |
| lessons.md | Cross-project mistake prevention | `~/.claude/tasks/lessons.md` | Referenced on corrections |
| QMD | Deep semantic search | `~/.config/qmd/` | Queried on demand |

### Agents

| Agent | Type | Model | Purpose |
|-------|------|-------|---------|
| team-research | Orchestrator | Sonnet | Parallel web + code research |
| team-review | Orchestrator | Sonnet | Multi-dimension code review |
| code-standards | Reviewer | Sonnet | Naming, formatting, conventions |
| architecture | Reviewer | Sonnet | SOLID, modularity, design patterns |
| error-handling | Reviewer | Sonnet | Exceptions, edge cases, logging |
| execute | Command | Haiku | Shell command execution |
| build | Command | Haiku | Production builds |
| lint | Command | Haiku | Linter execution |

### Folder Structure

```
claude-meos/                         # Clone this repo
  .claude/skills/                    # Skills work instantly — no copy needed
    init-meos/SKILL.md               # /init-meos wizard
    meos/SKILL.md                    # /meos daily workflow
    meos-find-skill/SKILL.md         # /meos-find-skill from skills.sh
  workspace/                         # Template for your workspace
  claude-config/                     # Agents, references, settings
  docs/                              # Documentation

your-workspace/                      # Created by /init-meos
  CLAUDE.md                          # Workspace instructions
  projects/my-project/CLAUDE.md      # Per-project status & tasks
  notes/daily/                       # YYYY-MM-DD.md daily notes
  templates/                         # Note templates

~/.claude/                           # Installed globally by /init-meos
  CLAUDE.md                          # Global instructions
  agents/                            # Research, review, command agents
  skills/                            # Skills (also available globally)
  tasks/lessons.md                   # Cross-project learning
```

## Customization

See [docs/customization.md](docs/customization.md) for:
- Adding your own agents
- Creating custom skills
- Configuring MCP servers
- Extending templates

## Docs

- [Getting Started](docs/getting-started.md) — step-by-step walkthrough
- [Concepts](docs/concepts.md) — 4-layer memory, CLAUDE.md hierarchy, agents explained
- [Customization](docs/customization.md) — add agents, skills, MCP servers
- [MCP Servers](docs/mcp-servers.md) — QMD, filesystem, Playwright setup

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and authenticated
- macOS, Linux, or WSL (Claude Code requirement)
- Optional: [QMD](https://github.com/qmd-project/qmd) for semantic search

## License

MIT
