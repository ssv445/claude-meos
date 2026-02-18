---
name: meos
version: 1.0.0
description: |
  MEOS — My Extensible Operating System.
  Manages session start, project status, daily notes, reflections, and project creation.

  Usage:
    /meos start          - Session initialization
    /meos status         - Quick project status table
    /meos daily          - Create/open today's daily note
    /meos standup        - Quick standup summary
    /meos eod            - End of day reflection
    /meos new-project    - Create new project structure

allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Bash
  - Task
  - AskUserQuestion
---

# MEOS — Personal Workspace Assistant

**Daily workflow management: session start, project tracking, daily notes, reflections.**

---

## WORKSPACE DETECTION (RUN FIRST)

Before any command, locate the workspace:

1. **Check current directory:** Look for CLAUDE.md containing "Workspace" or project management sections
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

**If not found:** Report "Could not locate workspace. Run /init-meos first or navigate to your workspace directory."

---

## COMMAND ROUTER

Parse the first argument:
- `start` - Session initialization
- `status` - Quick status check
- `daily` - Daily note management
- `eod` - End of day reflection
- `standup` - Quick standup summary
- `new-project <name>` - Create new project

**No command:** Show help text.

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
  /meos start          - Session start with status overview
  /meos status         - Quick project status table
  /meos daily          - Create/open today's daily note
  /meos standup        - Quick standup summary
  /meos eod            - End of day reflection
  /meos new-project    - Create new project (requires name)

Examples:
  /meos start
  /meos daily
  /meos new-project my-awesome-project
```
