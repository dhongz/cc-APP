---
description: Summarize content and save to the knowledge base
argument-hint: [topic-name]
allowed-tools: Read, Grep, Glob, Bash(echo:*), Bash(mkdir:*), Bash(cat:*)
---

Summarize the current context or specified content and save it to the knowledge base.

## Instructions

1. Gather relevant content from conversation or specified files
2. Create a structured summary with:
   - **Title**: Clear, descriptive name
   - **Overview**: 2-3 sentence summary
   - **Key Points**: Bullet list of main takeaways
   - **Details**: Important specifics worth preserving
   - **Related**: Links to related documents if any
3. Save to `documents/notes/$ARGUMENTS.md` (use kebab-case for filename)
4. If no topic provided, use a descriptive name based on content

## Output Format

```markdown
# [Title]

> [2-3 sentence overview]

## Key Points
- Point 1
- Point 2

## Details
[Expanded information]

## Related
- [Link to related doc](path)
```
