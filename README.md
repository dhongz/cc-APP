# CC-APP Template

A Claude Code Application (ccAPP) template demonstrating the base architecture and structure.

## Purpose

This template shows how to build ccAPPs — structured projects designed to extend Claude Code's capabilities. It includes working examples of all core primitives.

## Architecture

ccAPPs are built on four primitives:

| Primitive | Purpose | Location |
|-----------|---------|----------|
| **Commands** | User-initiated actions | `.claude/commands/` |
| **Agents** | Specialized sub-agents | `.claude/agents/` |
| **Skills** | Capability extensions | `.claude/skills/` |
| **MCPs** | External tool connections | `.mcp.json` |

Plus a **knowledge architecture** where information lives:

| Type | Purpose | Location |
|------|---------|----------|
| **Documents** | Reference materials Claude can access | `documents/` |
| **Outputs** | Generated artifacts | `outputs/` |

## Example Theme: Notes & Knowledge

This template uses a notes/knowledge management theme to show how pieces connect:

- **`/summarize`** command → uses **`note-taking`** skill → saves to **`documents/notes/`**
- **`/save`** command → saves session work → writes to **`outputs/sessions/`**
- **`researcher`** agent → searches **`documents/`** using **`knowledge-search`** skill
- **`writer`** agent → follows **`documents/style-guide.md`** → creates content

## Getting Started

1. **Clone this template**
2. **Review `CLAUDE.md`** for project context
3. **Try the commands**: `/summarize test-topic` or `/save my-session`
4. **Customize** for your use case

## Project Structure

```
cc-APP/
├── .claude/
│   ├── commands/           # Slash commands
│   │   ├── summarize.md
│   │   └── save.md
│   ├── agents/             # Sub-agents
│   │   ├── researcher.md
│   │   └── writer.md
│   └── skills/             # Agent skills
│       ├── note-taking/
│       └── knowledge-search/
├── documents/              # Knowledge base
│   ├── notes/
│   ├── templates/
│   └── style-guide.md
├── outputs/                # Generated artifacts
│   └── sessions/
├── .mcp.json               # MCP configuration
├── CLAUDE.md               # Project context
├── ARCHITECTURE.md         # Design philosophy
└── README.md
```

## Learn More

See `ARCHITECTURE.md` for the full design philosophy behind ccAPPs.