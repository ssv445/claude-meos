---
name: meos-find-skill
version: 1.0.0
description: |
  Search and install skills from skills.sh into Claude Code.
  Browse the open agent skills ecosystem, preview skills before installing.

  Usage:
    /meos-find-skill                  - Browse popular skills
    /meos-find-skill search <query>   - Search for skills by keyword
    /meos-find-skill install <repo>   - Install a skill from GitHub
    /meos-find-skill list             - Show installed skills
    /meos-find-skill update           - Update all installed skills

allowed-tools:
  - Bash
  - Read
  - WebFetch
  - AskUserQuestion
---

# /meos-find-skill — Skills.sh Browser & Installer

**Search, preview, and install skills from [skills.sh](https://skills.sh) into Claude Code.**

---

## PREREQUISITE CHECK

Before any command, verify `skills` CLI is available:

```bash
npx skills --version 2>/dev/null
```

If it fails, inform the user:
"The skills CLI is needed. It runs via npx so no install is required — just ensure you have Node.js/npm installed."

---

## COMMAND ROUTER

Parse the first argument:

| Command | Action |
|---------|--------|
| (none) | Browse popular skills |
| `search <query>` | Search skills.sh for matching skills |
| `install <owner/repo>` | Install a specific skill |
| `list` | Show currently installed skills |
| `update` | Update all installed skills |

---

## COMMAND: (no args) — Browse Popular

1. Fetch the skills.sh homepage to show trending skills:
   - Use WebFetch on `https://skills.sh` to get the leaderboard
   - Extract top 10-15 skills with: name, description, install count, source repo

2. Present as a numbered list:
   ```
   Popular skills on skills.sh:

   1. find-skills (vercel-labs/skills) — 257K installs
      Search and discover agent skills
   2. best-practices (vercel-labs/skills) — 26K installs
      Coding best practices for AI agents
   ...
   ```

3. Ask user (AskUserQuestion):
   "Would you like to install any of these?"
   - Options: "Yes, pick one", "Search for something specific", "No thanks"

4. If "Yes": Ask which number, then run the install flow
5. If "Search": Ask for search query, then run the search flow

---

## COMMAND: search <query>

1. Run the skills CLI search:
   ```bash
   npx skills find "<query>" 2>&1 | head -50
   ```

2. If the CLI search returns results interactively (requires TTY), fall back to web search:
   - Use WebFetch on `https://skills.sh` with prompt to find skills matching the query
   - Also try: `https://github.com/search?q=SKILL.md+<query>&type=code`

3. Present results to user with: name, source repo, description

4. Ask user (AskUserQuestion):
   "Would you like to install any of these?"
   - Options: list top 3-4 results by name

5. If selected, run the install flow for that skill

---

## COMMAND: install <owner/repo>

**Arguments:** GitHub shorthand (e.g., `vercel-labs/skills`) or full URL

### Step 1: Preview the skill

Before installing, show the user what they're getting:

1. Fetch the repo to find SKILL.md files:
   ```bash
   npx skills add <owner/repo> -a claude-code --list 2>&1
   ```

2. If that doesn't work, use WebFetch on the GitHub repo to find SKILL.md files

3. Show the user:
   - Skill name(s) available in the repo
   - Description from SKILL.md frontmatter
   - Tools the skill uses (from `allowed-tools`)

### Step 2: Choose scope

Ask user (AskUserQuestion):
"Where should this skill be installed?"
- Options:
  - "Global (~/.claude/skills/) — available everywhere" (Recommended)
  - "Project (.claude/skills/) — this project only"

### Step 3: Install

Run the install command:

**Global:**
```bash
npx skills add <owner/repo> -a claude-code -g -y
```

**Project:**
```bash
npx skills add <owner/repo> -a claude-code -y
```

### Step 4: Verify

```bash
npx skills list -a claude-code 2>&1
```

Show the user what was installed and how to use it (the skill's `/command` name).

---

## COMMAND: list

Show currently installed skills:

```bash
npx skills list -a claude-code 2>&1
```

If empty or command fails, also check directly:
```bash
echo "=== Global skills ===" && ls ~/.claude/skills/ 2>/dev/null
echo "=== Project skills ===" && ls .claude/skills/ 2>/dev/null
```

Format as a table:
```
| Skill | Location | Source |
|-------|----------|--------|
```

---

## COMMAND: update

Update all installed skills to latest versions:

```bash
npx skills update -a claude-code -y 2>&1
```

Report what was updated.

---

## HELP TEXT

If command is "help":

```
/meos-find-skill — Search & install skills from skills.sh

Commands:
  /meos-find-skill                    Browse popular skills
  /meos-find-skill search <query>     Search for skills
  /meos-find-skill install <repo>     Install from GitHub (e.g., vercel-labs/skills)
  /meos-find-skill list               Show installed skills
  /meos-find-skill update             Update all skills

Examples:
  /meos-find-skill search "docker"
  /meos-find-skill install vercel-labs/skills
  /meos-find-skill list

Skills are installed via npx skills (skills.sh CLI).
Browse all skills at https://skills.sh
```
