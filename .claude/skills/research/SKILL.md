---
name: research
description: Research a topic thoroughly by searching the knowledge base and project files
argument-hint: [topic]
context: fork
agent: Explore
allowed-tools: Read, Grep, Glob
---

Research "$ARGUMENTS" thoroughly using the knowledge base.

## Steps

1. Search `documents/` for any existing notes on this topic
   - Check filenames first: `Glob("documents/**/*$ARGUMENTS*")`
   - Full-text search: `Grep("$ARGUMENTS", "documents/")`

2. Search project files for related context
   - Look for relevant code, configs, or references

3. Return a structured summary:

```markdown
## Research: [Topic]

**Existing notes found:**
- [file path] — [one-line description]

**Key findings:**
- [finding 1]
- [finding 2]

**Gaps / not yet documented:**
- [what's missing]

**Suggested next steps:**
- [action]
```

If nothing is found, say so clearly and suggest where to start.
