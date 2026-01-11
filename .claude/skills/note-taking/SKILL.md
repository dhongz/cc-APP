---
name: note-taking
description: Structures and formats notes for the knowledge base. Use when creating notes, summaries, or documentation that will be saved to documents/.
allowed-tools: Read, Grep, Glob
---

# Note-Taking

How to create well-structured notes for the knowledge base.

## Note Format

Every note should include:

```markdown
# [Title]

> [Brief 1-2 sentence summary]

## Key Points
- Main takeaway 1
- Main takeaway 2

## Details
[Expanded information organized by topic]

## Related
- [Link to related note](../path/to/note.md)
```

## Organization

Save notes to these locations:

| Type | Location |
|------|----------|
| Meeting notes | `documents/notes/meetings/` |
| Research | `documents/notes/research/` |
| Decisions | `documents/notes/decisions/` |
| General | `documents/notes/` |

## Naming Convention

Use kebab-case: `topic-name.md`

Examples:
- `api-design-decisions.md`
- `2024-01-15-team-sync.md`
- `user-authentication-research.md`

## When to Apply

Apply when:
- Creating new notes via `/summarize`
- Documenting research findings
- Recording meeting notes or decisions
