---
name: meos
version: 2.0.0
description: |
  MEOS — My Extensible Operating System.
  Your personal workspace OS: setup, daily workflow, project tracking, and skill management.

  Usage:
    /meos init             - First-time setup wizard
    /meos start            - Session initialization
    /meos status           - Quick project status table
    /meos daily            - Create/open today's daily note
    /meos standup          - Quick standup summary
    /meos eod              - End of day reflection
    /meos new-project      - Create new project structure
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Bash
  - Task
  - AskUserQuestion
---

# MEOS — My Extensible Operating System

**One skill for everything: setup, daily workflow, project tracking, skill discovery.**

---

## COMMAND ROUTER

Parse the first argument:
- `init` - First-time setup wizard
- `start` - Session initialization
- `status` - Quick status check
- `daily` - Daily note management
- `standup` - Quick standup summary
- `eod` - End of day reflection
- `new-project <name>` - Create new project
**No command:** Show help text.

---

## WORKSPACE DETECTION

**For all commands except `init`**, locate the workspace first:

1. **Check current directory:** Look for CLAUDE.md containing workspace sections
   - Use Bash: `grep -q "How Claude Should Help" CLAUDE.md 2>/dev/null && echo "FOUND" || echo "NOT_FOUND"`

2. **Search upward:**
   ```bash
   dir="$PWD"
   for i in {1..5}; do
     if [ -f "$dir/CLAUDE.md" ] && grep -q "How Claude Should Help" "$dir/CLAUDE.md" 2>/dev/null; then
       echo "$dir"
       exit 0
     fi
     dir="$(dirname "$dir")"
   done
   echo "NOT_FOUND"
   ```

3. **Fallback:** Check `~/workspace`

4. **Store as [WORKSPACE]** for all file paths below.

**If not found:** Report "Could not locate workspace. Run `/meos init` first or navigate to your workspace directory."

---

## COMMAND: init

**One-time interactive setup that creates your personal workspace and configures Claude Code.**

### Step 1: Welcome

Display:
```
Welcome to Claude MEOS!

This setup will:
1. Create your workspace folder structure
2. Generate personalized CLAUDE.md configuration files
3. Install the /meos skill globally
4. Optionally set up QMD local knowledge base

Let's get started.
```

### Step 2: Gather User Info

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

### Step 3: Detect Kit Repository

Find where claude-meos templates are:

1. Check if current directory contains workspace/CLAUDE.md.template:
   - `ls workspace/CLAUDE.md.template 2>/dev/null`
2. Check parent directory
3. Check ~/www/claude-meos/
4. Store found path as [KIT_REPO]

If not found: Report error "Could not find claude-meos templates. Make sure you're running from the cloned repo directory."

### Step 4: Create Workspace Structure

```bash
mkdir -p [WORKSPACE_PATH]/projects
mkdir -p [WORKSPACE_PATH]/notes/daily
mkdir -p [WORKSPACE_PATH]/notes/captures
mkdir -p [WORKSPACE_PATH]/templates
```

### Step 5: Generate Workspace CLAUDE.md

1. Read `[KIT_REPO]/workspace/CLAUDE.md.template`
2. Replace all `{{USER_NAME}}` with [USER_NAME]
3. Replace all `{{WORKSPACE_PATH}}` with [WORKSPACE_PATH]
4. Write to `[WORKSPACE_PATH]/CLAUDE.md`

### Step 6: Copy Workspace Templates

Copy template files:
1. Read `[KIT_REPO]/workspace/templates/daily-note.md` → Write to `[WORKSPACE_PATH]/templates/daily-note.md`
2. Read `[KIT_REPO]/workspace/templates/morning-start.md` → Write to `[WORKSPACE_PATH]/templates/morning-start.md`
3. Read `[KIT_REPO]/workspace/templates/project-claude-md.md` → Write to `[WORKSPACE_PATH]/templates/project-claude-md.md`

### Step 7: Set Up Global Claude Config

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

### Step 8: Install Skill Globally

Copy the meos skill so it works in any project:

```bash
mkdir -p ~/.claude/skills/meos
```

Copy:
- `[KIT_REPO]/.claude/skills/meos/SKILL.md` → `~/.claude/skills/meos/SKILL.md`

Read source, Write to destination. Skip if destination already exists (report "already exists, skipping").

### Step 9: QMD Setup (Optional)

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

### Step 10: Update Settings (Optional)

Use AskUserQuestion:
"Would you like to update ~/.claude/settings.json with recommended settings?"
- Show what will be added: QMD MCP server (if configured)
- Options:
  - "Yes, update settings"
  - "No, I'll configure manually"

**If yes:**
1. Read existing ~/.claude/settings.json (if exists)
2. Merge in:
   - `mcpServers.qmd` (only if QMD was set up): `{"command": "qmd", "args": ["mcp"]}`
3. Write back (preserve existing settings)
4. If file doesn't exist, create with just these settings

### Step 11: Create First Project (Optional)

Use AskUserQuestion:
"Would you like to create your first project?"
- Options:
  - "Yes, let's create one"
  - "No, I'll do it later with /meos new-project"

**If yes:**
1. Ask project name (validate: no spaces, use hyphens)
2. Ask purpose
3. Ask mode: personal/professional/public
4. Ask code repo path (or "none")
5. Create `[WORKSPACE_PATH]/projects/[name]/`
6. Read project template, replace placeholders, write CLAUDE.md
7. Update workspace CLAUDE.md projects table

### Step 12: Summary

Display:
```
Setup complete!

Created:
  Workspace:   [WORKSPACE_PATH]/
  Global config: ~/.claude/CLAUDE.md
  Skill:       ~/.claude/skills/meos/
  [QMD:        ~/.config/qmd/index.yml] (if configured)
  [Project:    [WORKSPACE_PATH]/projects/[name]/] (if created)

Next steps:
  1. Open Claude Code in your workspace: cd [WORKSPACE_PATH] && claude
  2. Try: /meos start
  3. Create a project: /meos new-project my-project
  4. Start your day: /meos daily

Happy building!
```

---

## COMMAND: start

**Session initialization**

### Step 1: Gather Context (Parallel)

Launch these reads simultaneously:

**Daily Notes:**
- Calculate today: `date +%Y-%m-%d`
- Calculate yesterday: `date -v-1d +%Y-%m-%d` (macOS) or `date -d 'yesterday' +%Y-%m-%d` (Linux)
- Read both daily notes if they exist

**Project Scans:**
- Glob for `[WORKSPACE]/projects/*/CLAUDE.md`
- Read each, extract: project name, status, top pending task (first `- [ ]` item)

**Git Activity:**
- Run `git -C [WORKSPACE] log --oneline -10 2>/dev/null`

### Step 2: Synthesize

**If this is a fresh workspace (no daily notes, no completed tasks, few/no projects):**

Show a welcome message instead of empty sections:
```
Welcome to your workspace! Looks like you're just getting started.

Here's what you can do:
  /meos new-project my-project  - Create your first project
  /meos daily                   - Start today's daily note

Once you have projects and daily notes, /meos start will show your
accomplishments, pending tasks, and priorities automatically.
```
Then ask: **"What would you like to work on?"**

**Otherwise, show the full status:**

**Recent Accomplishments**
- Completed tasks from daily notes (lines with `- [x]`)
- Key commits from git log

**Today's Status**
- Focus items if set
- Incomplete tasks carried over

**Pending Priorities**
```
| Project | Status | Top Task |
|---------|--------|----------|
```

**Tomorrow's Priority** (from yesterday's note, if exists)

### Step 3: Engage

End with: **"What would you like to focus on today?"**

If today's daily note doesn't exist: "I notice today's daily note doesn't exist yet. Would you like me to create it?"

---

## COMMAND: status

**Quick status across all projects**

1. Glob: `[WORKSPACE]/projects/*/CLAUDE.md`
2. For each: Read and extract project name (H1), status, first unchecked task
3. Calculate health score per project:
   - Count total tasks (`- [ ]` + `- [x]`) and completed tasks (`- [x]`)
   - Check last updated date from CLAUDE.md
   - Score: "Good" (active + recent updates + tasks moving), "Stale" (no updates in 7+ days), "Blocked" (has tasks but none completed recently)
4. Format as table:
```
| Project | Status | Health | Top Task |
|---------|--------|--------|----------|
```
5. Sort: Active first, then Paused, then Passive
6. Truncate long tasks to 50 chars

---

## COMMAND: daily

**Create or open today's daily note**

1. Calculate: `date +%Y-%m-%d` -> [TODAY], `date +%A` -> [DAY]
2. Check: `[WORKSPACE]/notes/daily/[TODAY].md`
3. **If exists:** Show content, say "Here's today's note. What would you like to update?"
4. **If not exists:**
   - Read template: `[WORKSPACE]/templates/morning-start.md` (fallback: `daily-note.md`)
   - Replace `{{DATE}}` -> [TODAY], `{{DAY}}` -> [DAY]
   - **Smart Carry-Forward:** Read yesterday's note. Find incomplete tasks (`- [ ]`). If any exist, add them under a "### Carried Forward" section in the new note before the main task section. This ensures nothing falls through the cracks.
   - Write to `[WORKSPACE]/notes/daily/[TODAY].md`
   - Show the new note (highlight carried-forward items if any)
   - Ask: "What are your top 3 priorities for today?"

---

## COMMAND: standup

**Quick standup summary (yesterday/today/blockers)**

1. Calculate today and yesterday dates
2. Read yesterday's daily note:
   - Extract completed tasks (`- [x]`)
   - Extract incomplete tasks (`- [ ]`)
3. Read today's daily note (if exists):
   - Extract focus items and planned tasks
4. Format standup:

```
**Yesterday:**
- [completed items from yesterday's note]

**Today:**
- [focus items / planned tasks from today's note]
- [if no today note: "No daily note yet — run /meos daily to plan"]

**Blockers:**
- [incomplete items carried over from yesterday, if any]
- [if none: "None"]
```

5. Keep it concise — this is for quick async standups or team updates.

---

## COMMAND: eod

**End of day reflection**

1. Calculate today's date
2. Read `[WORKSPACE]/notes/daily/[TODAY].md`
   - If doesn't exist: "No daily note found. Run `/meos daily` first."
3. Count: `- [ ]` (incomplete) vs `- [x]` (complete)
   - Show: "You completed X of Y tasks today"
4. Interactive reflection (AskUserQuestion):
   - "What went well today?"
   - "What could be improved?"
   - "What's the top priority for tomorrow?"
5. Update the note's reflection section:
   ```markdown
   ### What Went Well
   [response 1]

   ### What to Improve
   [response 2]
   ```
   And update "Tomorrow's priority:" line with response 3

---

## COMMAND: new-project

**Create a new project structure**

1. Parse project name from arguments
   - Validate: no spaces (use hyphens)
   - If invalid: "Project name cannot contain spaces. Use hyphens: my-project"

2. Check if `[WORKSPACE]/projects/[name]/` already exists
   - If exists: "Project already exists at [path]"

3. Create directory: `mkdir -p [WORKSPACE]/projects/[name]`

4. Ask for details (AskUserQuestion):
   - "What is the purpose of this project?"
   - "What mode?" Options: personal, professional, public
   - "Code repository path? (or 'none')"

5. Calculate date: `date +%Y-%m-%d`

6. Read project template: `[WORKSPACE]/templates/project-claude-md.md`
   - Replace placeholders: {{PROJECT_NAME}}, {{PURPOSE}}, {{STATUS}} (Active), {{MODE}}, {{CODE_REPO}}, {{DATE}}
   - Write to `[WORKSPACE]/projects/[name]/CLAUDE.md`

7. Update `[WORKSPACE]/CLAUDE.md` projects table:
   - Find the Projects table
   - Add row: `| [name] | active | [mode] | [code-repo] | [purpose-brief] |`

8. Confirm: "Created project: [name] at [WORKSPACE]/projects/[name]/"

---

## HELP TEXT

If no command provided:

```
MEOS — My Extensible Operating System

Commands:
  /meos init             - First-time setup wizard
  /meos start            - Session start with status overview
  /meos status           - Quick project status table
  /meos daily            - Create/open today's daily note
  /meos standup          - Quick standup summary
  /meos eod              - End of day reflection
  /meos new-project      - Create new project (requires name)

  Tip: Use /find-skills to discover and install more skills

Examples:
  /meos start
  /meos daily
  /meos new-project my-awesome-project
```
