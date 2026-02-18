# Concepts

## CLAUDE.md Hierarchy

Claude Code reads instruction files called CLAUDE.md at multiple levels. Each level adds context:

### Global (~/.claude/CLAUDE.md)
- Your personal preferences and principles
- Applies to ALL projects and workspaces
- Contains: coding style, investigation protocol, workflow preferences
- Created by /setup from the template

### Workspace (your-workspace/CLAUDE.md)
- Instructions specific to this workspace
- How Claude should behave when working here
- Project list, folder structure, conventions
- Session start behavior (what to scan, what to show)

### Project (projects/my-project/CLAUDE.md)
- Instructions for a specific project
- Purpose, status, next tasks, key files
- How Claude should help with this project specifically

**How it works:** When you open Claude Code in a directory, it reads all CLAUDE.md files from the current directory up to the root, plus the global one. More specific files override more general ones.

## Agents

Agents are reusable AI personas defined in ~/.claude/agents/. Each agent has:
- A **name** and **description** (shown in Claude's agent picker)
- **Tools** it can use (Bash, Read, Write, etc.)
- **Model** preference (sonnet for speed, opus for quality)
- Detailed **instructions** for its role

### Included Agents

**Orchestrators** (coordinate teams of agents):
- `team-research` - Spawns web + code researchers in parallel, synthesizes findings
- `team-review` - Spawns code reviewers across multiple dimensions, deduplicates report

**Command Agents** (specialized executors):
- `commands/execute` - Safe shell command execution with validation
- `commands/build` - Production build runner with package manager detection

### How to Use Agents

Agents are triggered automatically by Claude when relevant, or you can reference them:
- "Use team-research to investigate this"
- "Run a code review with team-review"

## Skills

Skills are slash commands (like /setup and /assist) that provide structured workflows. Each skill lives in ~/.claude/skills/name/SKILL.md.

### Included Skills

- `/setup` - One-time workspace configuration wizard
- `/assist` - Daily workflow assistant (start, status, daily, eod, new-project)

### How Skills Differ from Agents

| | Skills | Agents |
|---|---|---|
| Triggered by | User typing /command | Claude choosing automatically |
| Structure | Step-by-step workflow | Role-based instructions |
| Interaction | Often interactive (asks questions) | Usually autonomous |
| Use case | User-facing workflows | Background or delegated work |

## Memory

Claude Code has persistent memory at ~/.claude/projects/*/memory/. Key points:
- MEMORY.md is loaded into every session automatically
- Claude can create topic-specific memory files
- Memories persist across conversations
- Useful for: stable patterns, key decisions, user preferences, recurring solutions

## MCP Servers

Model Context Protocol (MCP) servers extend Claude's capabilities by providing additional tools. Configured in ~/.claude/settings.json under `mcpServers`.

**Example: QMD** provides semantic search over your local files, giving Claude the ability to search your notes and projects.

## Daily Notes

The daily note workflow is a core pattern:

1. **Morning** - Create note from template, set 3 focus items, plan tasks
2. **During day** - Update notes, check off tasks, capture ideas
3. **End of day** - Reflect on what worked, set tomorrow's priority

Notes live in workspace/notes/daily/YYYY-MM-DD.md and are scanned by /assist start for continuity.

## Projects

Projects are managed in workspace/projects/. Each has its own CLAUDE.md.

**Status values:** active, paused, passive, completed
**Modes:** personal, professional, public

**Code vs Management Separation:** If a project has code, keep the code in a separate repo. The workspace projects/ folder holds planning, notes, and decisions only.
