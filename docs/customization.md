# Customization

## Adding a New Agent

Create a markdown file in `.claude/agents/` (repo-local) or `~/.claude/agents/` (global):

```markdown
---
name: my-agent
description: What this agent does (shown in picker)
tools: Bash, Read, Glob, Grep
model: sonnet
color: yellow
---

# My Agent

Instructions for the agent...
```

**Frontmatter fields:**
- `name` - Agent identifier
- `description` - Shown when Claude picks agents. Include example usage.
- `tools` - Comma-separated list of allowed tools
- `model` - `haiku` (fast/cheap), `sonnet` (balanced), `opus` (powerful)
- `color` - Terminal color for the agent's output
- `memory` - `project` to give agent access to project memory

**Subdirectories:** Organize agents in folders (e.g., `commands/`, `review/`).

## Adding a New Skill

Create a folder in `.claude/skills/my-skill/` (repo-local) or `~/.claude/skills/my-skill/` (global) with a SKILL.md:

```markdown
---
name: my-skill
version: 1.0.0
description: |
  What this skill does.

  Usage:
    /my-skill arg1     - Does something

allowed-tools:
  - Read
  - Write
  - Bash
  - AskUserQuestion
---

# My Skill

Step-by-step instructions...
```

Users invoke it with `/my-skill`.

## Adding MCP Servers

Add to `.mcp.json` in your workspace root (or `~/.claude/settings.json` for global):

```json
{
  "mcpServers": {
    "server-name": {
      "command": "command-to-run",
      "args": ["arg1", "arg2"]
    }
  }
}
```

See [mcp-servers.md](mcp-servers.md) for specific server setup guides.

## Modifying Templates

Templates live in your workspace's templates/ folder:
- `daily-note.md` - Daily note structure
- `morning-start.md` - Morning planning template
- `project-claude-md.md` - New project CLAUDE.md template
- `weekly-review.md` - Weekly review template

Edit these to match your workflow. Supported placeholders:
- `{{DATE}}` - Replaced with YYYY-MM-DD
- `{{DAY}}` - Replaced with day name (Monday, etc.)
- `{{PROJECT_NAME}}`, `{{PURPOSE}}`, etc. - Used in project template

## Customizing Your Workspace CLAUDE.md

Key sections you might want to modify:
- **On Session Start** - What Claude checks when you open a session
- **Folder Structure** - If you add new directories
- **Conventions** - Your naming and organization preferences
- **Quick Commands** - If you add custom skills

## Finding Community Skills & Agents

Use `/find-skills` to discover skills and agents built by the community:

```
/find-skills
```

This uses [skills.sh](https://skills.sh) to browse, preview, and install skills with one command.
