# Claude MEOS — My Extensible Operating System

Your personal OS, powered by Claude Code. Clone, run `/meos init`, and Claude becomes your active work partner — managing projects, tracking daily notes, and keeping you focused.

**Built for Claude Code beginners** who want a structured workspace without configuring everything from scratch.

## What You Get

**Workspace** — a structured folder for managing your work life:
- Daily notes with morning planning and evening reflection
- Project tracking with CLAUDE.md-driven status and tasks
- Quick capture for ideas, links, and meeting notes

**Claude Config** — one skill and a memory system:
- `/meos` command — setup, session start, daily notes, project status, standup, end-of-day
- `/find-skills` — discover and install skills from [skills.sh](https://skills.sh)
- 4-layer memory — CLAUDE.md instructions, MEMORY.md quick ref, lessons.md for mistakes, QMD for deep search

## Quickstart

```bash
# 1. Clone
git clone https://github.com/ssv445/claude-meos.git
cd claude-meos

# 2. Run setup — the skill works instantly, no copying needed
claude
# Then type: /meos init
```

The `/meos init` wizard will:
1. Ask your name and workspace path
2. Create workspace folders and generate your CLAUDE.md files
3. Install the `/meos` skill globally
4. Optionally configure QMD semantic search
5. Offer to create your first project

## Usage

After setup, open Claude Code in your workspace:

```bash
cd ~/workspace  # or wherever you set it up
claude
```

### All Commands

| Command | What it does |
|---------|-------------|
| `/meos init` | First-time setup wizard |
| `/meos start` | Morning kickoff — shows what's pending, asks what to focus on |
| `/meos daily` | Creates today's daily note (carries forward incomplete tasks) |
| `/meos status` | Quick table of all projects with health scores |
| `/meos standup` | Quick standup summary — yesterday/today/blockers |
| `/meos eod` | Evening reflection — planned vs done, set tomorrow's priority |
| `/meos new-project` | Create a new project with CLAUDE.md |
| `/find-skills` | Discover and install skills from [skills.sh](https://skills.sh) |

## Architecture

### 4-Layer Memory

| Layer | What | Where | When |
|-------|------|-------|------|
| CLAUDE.md | Instructions for Claude's behavior | Repo root / `~/.claude/` | Always loaded |
| MEMORY.md | Per-project quick reference | `~/.claude/projects/*/memory/` | Auto-loaded, 200 line limit |
| lessons.md | Cross-project mistake prevention | `~/.claude/tasks/lessons.md` | Referenced on corrections |
| QMD | Deep semantic search | `~/.config/qmd/` | Queried on demand |

### Folder Structure

```
claude-meos/                         # Clone this repo
  .claude/skills/meos/SKILL.md      # /meos — workspace management
  .claude/skills/find-skills/SKILL.md # /find-skills — skill discovery (3rd party)
  workspace/                         # Template for your workspace
  claude-config/                     # References, settings
  docs/                              # Documentation

your-workspace/                      # Created by /meos init
  CLAUDE.md                          # Workspace instructions
  projects/my-project/CLAUDE.md      # Per-project status & tasks
  notes/daily/                       # YYYY-MM-DD.md daily notes
  templates/                         # Note templates

~/.claude/                           # Installed globally by /meos init
  CLAUDE.md                          # Global instructions
  skills/meos/                       # The /meos skill
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
