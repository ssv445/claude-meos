# Lessons Learned

Cross-project learning log. Claude updates this after user corrections to prevent repeating mistakes.

Format:
```
## [Category]

### [Short title]
- **Context**: What was happening
- **Mistake**: What went wrong
- **Rule**: How to prevent it next time
```

---

## Git & Version Control

### Always check before force-pushing
- **Context**: Fixing a failed CI pipeline
- **Mistake**: Used `git push --force` without checking if teammates had pushed commits
- **Rule**: Always `git fetch && git log origin/main..HEAD` before any force push. Prefer `--force-with-lease`.

---

## File & Project Structure

### Don't mix code and management files
- **Context**: Adding meeting notes to a code repository
- **Mistake**: Put planning docs in the code repo, cluttering it
- **Rule**: Code repos get code only. Planning, notes, and research go in the workspace projects/ folder.

---

## Debugging

### Trace errors to their source
- **Context**: Investigating a "Permission denied" error in the UI
- **Mistake**: Assumed it was an auth issue and spent time on token logic
- **Rule**: Error messages pass through layers and can be misleading. Always trace to the originating service/function before fixing.

---

## Claude Interactions

### Be specific about what changed
- **Context**: Asking Claude to "fix the bug"
- **Mistake**: Vague request led to Claude changing unrelated code
- **Rule**: Point to specific files, error messages, and expected vs actual behavior. The more context, the better the fix.

---

<!-- Add new lessons below as you work. Claude will update this file after corrections. -->
