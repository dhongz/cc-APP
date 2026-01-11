---
name: knowledge-search
description: Searches the documents folder for relevant information. Use when looking up existing notes, finding documentation, or answering questions about saved knowledge.
allowed-tools: Read, Grep, Glob
---

# Knowledge Search

Find and retrieve information from the knowledge base.

## Knowledge Base Structure

```
documents/
├── notes/           # Saved notes and summaries
├── templates/       # Reusable templates
└── style-guide.md   # Writing style reference
```

## Search Strategy

1. **Check for exact matches** in filenames first
2. **Search by folder** for topic-specific content
3. **Full-text search** for keywords within files
4. **Check templates/** for reusable formats

## Response Format

When returning search results:

```markdown
## Found: [Document Title]

**Source:** [path/to/document.md]

> [Relevant excerpt]

### Summary
[Brief summary of what was found]
```

## When to Apply

Apply when:
- User asks "what do we have on X?"
- Looking up previous notes or decisions
- Finding templates or examples
- Answering questions about documented knowledge
