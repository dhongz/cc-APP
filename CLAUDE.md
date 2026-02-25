# CC-APP Template

> A Claude Code Application (ccAPP) template demonstrating base architecture

## Overview

This template shows the standard structure for building ccAPPs. It uses a **notes & knowledge management** theme to demonstrate how skills, agents, and documents work together.

## Quick Start

| Command | What it does |
|---------|--------------|
| `/summarize [topic]` | Summarize content and save to knowledge base |
| `/save [name]` | Save session work to `outputs/sessions/` |

## How It Works

```
User: "/summarize api-design"
  ↓
Skill: summarize/SKILL.md executes
  ↓
Skill: note-taking formats the content
  ↓
Output: documents/notes/api-design.md created
```

## Structure

```
.claude/
├── agents/         # Specialized sub-agents
│   ├── researcher.md   → Gathers information
│   └── writer.md       → Drafts content
└── skills/         # Skills and slash commands
    ├── summarize/      → Creates notes in knowledge base
    ├── save/           → Saves session to outputs/
    ├── note-taking/    → How to format notes
    └── knowledge-search/ → How to search documents/

documents/          # Knowledge base (Claude can read & write)
├── notes/          # Saved notes from /summarize
├── templates/      # Reusable formats
└── style-guide.md  # Writing standards

outputs/            # Session artifacts from /save
└── sessions/
```

## Customizing This Template

1. **Add skills** for new capabilities in `.claude/skills/`
2. **Add agents** for specialized tasks in `.claude/agents/`
3. **Configure MCPs** in `.mcp.json` for external tools
