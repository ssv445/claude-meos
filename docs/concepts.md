# Concepts

## CLAUDE.md Hierarchy

Claude Code reads instruction files called CLAUDE.md at multiple levels. Each level adds context:

### Global (~/.claude/CLAUDE.md)
- Your personal preferences and principles
- Applies to ALL projects and workspaces
- Contains: coding style, investigation protocol, workflow preferences
- Claude Code creates this automatically; you can customize it manually

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

Agents are reusable AI personas defined in `.claude/agents/` (repo-local or `~/.claude/agents/` for global). Each agent has:
- A **name** and **description** (shown in Claude's agent picker)
- **Tools** it can use (Bash, Read, Write, etc.)
- **Model** preference (sonnet for speed, opus for quality)
- Detailed **instructions** for its role

MEOS doesn't ship with pre-built agents — instead, use `/find-skills` to discover and install agents from the community, or create your own. See [Customization](customization.md) for how to create agents.

## Skills

Skills are slash commands (like `/meos`) that provide structured workflows. Each skill lives in `.claude/skills/name/SKILL.md` (repo-local or `~/.claude/skills/` for global).

### Included Skills

- `/meos` - Workspace management: init, start, status, daily, standup, eod, new-project
- `/find-skills` - Discover and install skills from skills.sh (bundled 3rd-party skill)

### How Skills Differ from Agents

| | Skills | Agents |
|---|---|---|
| Triggered by | User typing /command | Claude choosing automatically |
| Structure | Step-by-step workflow | Role-based instructions |
| Interaction | Often interactive (asks questions) | Usually autonomous |
| Use case | User-facing workflows | Background or delegated work |

## 4-Layer Memory Architecture

Claude MEOS uses four layers of memory, each serving a different purpose:

### Layer 1: CLAUDE.md (Instructions)
- **What:** How Claude should behave — rules, conventions, workflows
- **Where:** `~/.claude/CLAUDE.md` (global), `workspace/CLAUDE.md`, `projects/*/CLAUDE.md`
- **When loaded:** Always, automatically, at every session start
- **Use for:** Coding style, investigation protocols, project-specific behaviors

### Layer 2: MEMORY.md (Quick Reference)
- **What:** Per-project facts Claude needs to remember across sessions
- **Where:** `~/.claude/projects/*/memory/MEMORY.md`
- **When loaded:** Automatically at session start (first 200 lines)
- **Use for:** Stable patterns, key architectural decisions, user preferences, file paths
- **Tip:** Keep it concise. Create topic-specific files (e.g., `debugging.md`, `patterns.md`) for details and link from MEMORY.md.

### Layer 3: lessons.md (Mistake Prevention)
- **What:** Cross-project learning log in Context/Mistake/Rule format
- **Where:** `~/.claude/tasks/lessons.md`
- **When loaded:** Referenced when Claude makes corrections or encounters similar patterns
- **Use for:** Preventing repeated mistakes, capturing debugging insights
- **Format:**
  ```
  ### [Short title]
  - **Context**: What was happening
  - **Mistake**: What went wrong
  - **Rule**: How to prevent it next time
  ```

### Layer 4: QMD (Deep Semantic Search)
- **What:** Local search engine that indexes all your notes and projects
- **Where:** `~/.cache/qmd/index.sqlite` (index), collections configured via `qmd collection add`
- **When loaded:** On demand — Claude queries it when searching for information
- **Use for:** Finding notes by topic, searching across all projects, answering "what did I write about X?"
- **Commands:** `qmd query "topic"` (best quality), `qmd search "term"` (fast keyword)

### How the Layers Work Together

| Need | Layer |
|------|-------|
| "Always format code this way" | CLAUDE.md |
| "The API base URL is X" | MEMORY.md |
| "Don't use force-push without checking" | lessons.md |
| "What did I write about authentication?" | QMD |

## MCP Servers

Model Context Protocol (MCP) servers extend Claude's capabilities by providing additional tools. Configured in `.mcp.json` (workspace-local) or `~/.claude/settings.json` (global) under `mcpServers`.

**Example: QMD** provides semantic search over your local files, giving Claude the ability to search your notes and projects.

## Daily Notes

The daily note workflow is a core pattern:

1. **Morning** - Create note from template, set 3 focus items, plan tasks
2. **During day** - Update notes, check off tasks, capture ideas
3. **End of day** - Reflect on what worked, set tomorrow's priority

Notes live in workspace/notes/daily/YYYY-MM-DD.md and are scanned by /meos start for continuity.

## Projects

Projects are managed in workspace/projects/. Each has its own CLAUDE.md.

**Status values:** active, paused, passive, completed
**Modes:** personal, professional, public

**Code vs Management Separation:** If a project has code, keep the code in a separate repo. The workspace projects/ folder holds planning, notes, and decisions only.
