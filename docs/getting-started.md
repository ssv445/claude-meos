# Getting Started

## Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/claude-code) installed and authenticated
- Git installed
- Node.js (optional, needed for QMD knowledge base)

## Step 1: Clone the Repository

```bash
git clone https://github.com/ssv445/claude-meos.git
cd claude-meos
```

Or if using as a GitHub template: click "Use this template" and clone your copy.

## Step 2: Run Setup

Open Claude Code in the cloned directory — skills work instantly, no copying needed:

```bash
claude
```

Then run the setup wizard:

```
/meos init
```

The wizard will:
1. Ask your name and preferred workspace location
2. Create the workspace folder structure
3. Generate personalized CLAUDE.md configuration
4. Optionally set up QMD local knowledge base
5. Optionally create your first project

## Step 3: Open Your Workspace

Navigate to your new workspace and start Claude Code:

```bash
cd ~/workspace  # or wherever you chose
claude
```

## Step 4: Try It Out

Start your session:
```
/meos start
```

This scans your daily notes and projects, shows pending items, and asks what you want to focus on.

## Step 5: Create Your First Project

```
/meos new-project my-first-project
```

Claude will ask for the project's purpose, mode, and optional code repo path, then create the project structure.

## Daily Workflow

**Morning:**
```
/meos daily
```
Creates today's daily note from template and asks for your priorities.

**During the day:** Work normally. Claude remembers your workspace context.

**End of day:**
```
/meos eod
```
Reviews your task completion, guides you through reflection, and sets tomorrow's priority.

## Troubleshooting

### "Could not locate workspace"
Make sure you're running Claude Code from within your workspace directory, or that ~/workspace exists.

### Want agents?
Use `/find-skills` to discover and install agents from the community, or create your own in `~/.claude/agents/`.

### QMD not working
1. Check installation: `which qmd`
2. Install if missing: `npm install -g @tobilu/qmd`
3. Check collections: `qmd status`
4. Re-index: `qmd embed`
