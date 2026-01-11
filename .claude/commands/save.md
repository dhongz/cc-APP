---
description: Save current session work to an organized output folder
argument-hint: [session-name]
allowed-tools: Read, Bash(mkdir:*), Bash(cp:*), Bash(echo:*), Bash(date:*)
---

Save the current session's artifacts to `outputs/sessions/`.

## Instructions

1. Create session folder: `outputs/sessions/$(date +%Y-%m-%d)-$ARGUMENTS/`
2. Save generated artifacts to the session folder
3. Create `_summary.md` with session overview
4. Log action to root `_log.md`

## Session Structure

```
outputs/sessions/YYYY-MM-DD-[name]/
├── _summary.md      # What was accomplished
└── [artifacts]      # Generated files
```

## Context

- Current date: !`date +%Y-%m-%d`
- Existing sessions: !`ls outputs/sessions/ 2>/dev/null || echo "No sessions yet"`
