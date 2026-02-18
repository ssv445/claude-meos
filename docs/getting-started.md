# Getting Started

## Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/claude-code) installed and authenticated
- Git installed
- Node.js (optional, needed for QMD knowledge base)

## Step 1: Clone the Repository

```bash
git clone https://github.com/ssv445/claude-assist-kit.git
cd claude-assist-kit
```

Or if using as a GitHub template: click "Use this template" and clone your copy.

## Step 2: Copy the Setup Skill

The `/setup` skill needs to be in your Claude config first:

```bash
mkdir -p ~/.claude/skills/setup
cp claude-config/skills/setup/SKILL.md ~/.claude/skills/setup/
```

## Step 3: Run Setup

Open Claude Code in the cloned directory:

```bash
claude
```

Then run the setup wizard:

```
/setup
```

The wizard will:
1. Ask your name and preferred workspace location
2. Create the workspace folder structure
3. Generate personalized CLAUDE.md files
4. Install AI agents to ~/.claude/agents/
5. Install skills to ~/.claude/skills/
6. Optionally set up QMD local knowledge base
7. Optionally create your first project

## Step 4: Open Your Workspace

Navigate to your new workspace and start Claude Code:

```bash
cd ~/workspace  # or wherever you chose
claude
```

## Step 5: Try It Out

Start your session:
```
/assist start
```

This scans your daily notes and projects, shows pending items, and asks what you want to focus on.

## Step 6: Create Your First Project

```
/assist new-project my-first-project
```

Claude will ask for the project's purpose, mode, and optional code repo path, then create the project structure.

## Daily Workflow

**Morning:**
```
/assist daily
```
Creates today's daily note from template and asks for your priorities.

**During the day:** Work normally. Claude remembers your workspace context.

**End of day:**
```
/assist eod
```
Reviews your task completion, guides you through reflection, and sets tomorrow's priority.

## Troubleshooting

### "Could not locate workspace"
Make sure you're running Claude Code from within your workspace directory, or that ~/workspace exists.

### Agents not available
Verify agents were copied to ~/.claude/agents/. Re-run /setup if needed.

### QMD not working
1. Check installation: `which qmd`
2. Install if missing: `npm install -g qmd`
3. Verify config exists: `cat ~/.config/qmd/index.yml`
4. Index your content: `qmd index`
