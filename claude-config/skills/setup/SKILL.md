---
name: setup
version: 1.0.0
description: |
  First-time setup wizard for Claude MEOS.
  Creates your workspace, configures Claude, and installs agents and skills.

  Usage:
    /setup              - Run the interactive setup wizard

allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - AskUserQuestion
  - Glob
---

# Claude MEOS - Setup Wizard

**One-time interactive setup that creates your personal workspace and configures Claude Code.**

---

## STEP 1: Welcome

Display:
```
Welcome to Claude MEOS!

This setup will:
1. Create your workspace folder structure
2. Generate personalized CLAUDE.md configuration files
3. Install AI agents and skills
4. Optionally set up QMD local knowledge base

Let's get started.
```

---

## STEP 2: Gather User Info

Use AskUserQuestion for each:

**Question 1:** "What's your name?"
- Options: (free text via Other)
- Store as [USER_NAME]

**Question 2:** "Where should your workspace live?"
- Options:
  - "~/workspace" (Recommended)
  - "Current directory"
  - Other (custom path)
- Store as [WORKSPACE_PATH]
- Expand ~ to full home path using: `echo ~`

---

## STEP 3: Detect Kit Repository

Find where claude-meos templates are:

1. Check if current directory contains workspace/CLAUDE.md.template:
   - `ls workspace/CLAUDE.md.template 2>/dev/null`
2. Check parent directory
3. Check ~/www/claude-meos/
4. Store found path as [KIT_REPO]

If not found: Report error "Could not find claude-meos templates. Make sure you're running from the cloned repo directory."

---

## STEP 4: Create Workspace Structure

```bash
mkdir -p [WORKSPACE_PATH]/projects
mkdir -p [WORKSPACE_PATH]/notes/daily
mkdir -p [WORKSPACE_PATH]/notes/captures
mkdir -p [WORKSPACE_PATH]/templates
```

---

## STEP 5: Generate Workspace CLAUDE.md

1. Read `[KIT_REPO]/workspace/CLAUDE.md.template`
2. Replace all `{{USER_NAME}}` with [USER_NAME]
3. Replace all `{{WORKSPACE_PATH}}` with [WORKSPACE_PATH]
4. Write to `[WORKSPACE_PATH]/CLAUDE.md`

---

## STEP 6: Copy Workspace Templates

Copy template files:
1. Read `[KIT_REPO]/workspace/templates/daily-note.md` → Write to `[WORKSPACE_PATH]/templates/daily-note.md`
2. Read `[KIT_REPO]/workspace/templates/morning-start.md` → Write to `[WORKSPACE_PATH]/templates/morning-start.md`
3. Read `[KIT_REPO]/workspace/templates/project-claude-md.md` → Write to `[WORKSPACE_PATH]/templates/project-claude-md.md`

---

## STEP 7: Set Up Global Claude Config

Use AskUserQuestion:
"Would you like to set up a global Claude configuration at ~/.claude/CLAUDE.md?"
- Options:
  - "Yes, create it" (Recommended)
  - "No, skip this"

**If yes:**
1. Check if ~/.claude/CLAUDE.md already exists
2. If exists: Ask "~/.claude/CLAUDE.md already exists. Overwrite or skip?"
   - Options: "Overwrite", "Skip"
3. Read `[KIT_REPO]/claude-config/CLAUDE.md.template`
4. Replace `{{USER_NAME}}` with [USER_NAME]
5. Write to `~/.claude/CLAUDE.md`

---

## STEP 8: Install Agents

Create agent directories and copy files:

```bash
mkdir -p ~/.claude/agents/commands
```

Copy each agent:
1. `[KIT_REPO]/claude-config/agents/team-research.md` → `~/.claude/agents/team-research.md`
2. `[KIT_REPO]/claude-config/agents/team-review.md` → `~/.claude/agents/team-review.md`
3. `[KIT_REPO]/claude-config/agents/commands/execute.md` → `~/.claude/agents/commands/execute.md`
4. `[KIT_REPO]/claude-config/agents/commands/build.md` → `~/.claude/agents/commands/build.md`

For each: Read source, Write to destination. Skip if destination already exists (report "already exists, skipping").

---

## STEP 9: Install Skills

```bash
mkdir -p ~/.claude/skills/setup
mkdir -p ~/.claude/skills/assist
```

Copy:
1. `[KIT_REPO]/claude-config/skills/setup/SKILL.md` → `~/.claude/skills/setup/SKILL.md`
2. `[KIT_REPO]/claude-config/skills/assist/SKILL.md` → `~/.claude/skills/assist/SKILL.md`

---

## STEP 10: QMD Setup (Optional)

Use AskUserQuestion:
"Would you like to set up QMD (local knowledge base for searching your notes)?"
- Options:
  - "Yes, set it up"
  - "No, skip for now"
  - "What is QMD?"

**If "What is QMD?":** Explain: "QMD is a local semantic search engine that indexes your notes and projects. It lets Claude search your workspace content using `qmd query 'topic'`. It requires Node.js." Then re-ask.

**If yes:**
1. Check if qmd is installed: `which qmd`
2. If not installed: Run `npm install -g qmd` (confirm with user first)
3. Create QMD config:
```bash
mkdir -p ~/.config/qmd
```
4. Write ~/.config/qmd/index.yml:
```yaml
collections:
  notes:
    path: [WORKSPACE_PATH]/notes
    patterns: ["**/*.md"]
  projects:
    path: [WORKSPACE_PATH]/projects
    patterns: ["**/*.md"]
```
5. Copy QMD reference: `[KIT_REPO]/claude-config/references/qmd.md` → `~/.claude/references/qmd.md`

---

## STEP 11: Update Settings (Optional)

Use AskUserQuestion:
"Would you like to update ~/.claude/settings.json with recommended settings?"
- Show what will be added: agent teams support, QMD MCP server (if configured)
- Options:
  - "Yes, update settings"
  - "No, I'll configure manually"

**If yes:**
1. Read existing ~/.claude/settings.json (if exists)
2. Merge in:
   - `env.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS`: "1"
   - `mcpServers.qmd` (only if QMD was set up): `{"command": "qmd", "args": ["mcp"]}`
3. Write back (preserve existing settings)
4. If file doesn't exist, create with just these settings

---

## STEP 12: Create First Project (Optional)

Use AskUserQuestion:
"Would you like to create your first project?"
- Options:
  - "Yes, let's create one"
  - "No, I'll do it later with /assist new-project"

**If yes:**
1. Ask project name (validate: no spaces, use hyphens)
2. Ask purpose
3. Ask mode: personal/professional/public
4. Ask code repo path (or "none")
5. Create `[WORKSPACE_PATH]/projects/[name]/`
6. Read project template, replace placeholders, write CLAUDE.md
7. Update workspace CLAUDE.md projects table

---

## STEP 13: Summary

Display:
```
Setup complete!

Created:
  Workspace:   [WORKSPACE_PATH]/
  Global config: ~/.claude/CLAUDE.md
  Agents:      ~/.claude/agents/ (4 agents)
  Skills:      ~/.claude/skills/ (setup, assist)
  [QMD:        ~/.config/qmd/index.yml] (if configured)
  [Project:    [WORKSPACE_PATH]/projects/[name]/] (if created)

Next steps:
  1. Open Claude Code in your workspace: cd [WORKSPACE_PATH] && claude
  2. Try: /assist start
  3. Create a project: /assist new-project my-project
  4. Start your day: /assist daily

Happy building!
```
