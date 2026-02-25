# CC-APP Template

A Claude Code Application (ccAPP) template demonstrating the base architecture and structure.

## Purpose

This template shows how to build ccAPPs — structured projects designed to extend Claude Code's capabilities. It includes working examples of all core primitives.

## Architecture

ccAPPs are built on three primitives:

| Primitive | Purpose | Location |
|-----------|---------|----------|
| **Skills** | Capabilities, slash commands, and background knowledge | `.claude/skills/` |
| **Agents** | Specialized sub-agents | `.claude/agents/` |
| **MCPs** | External tool connections | `.mcp.json` |

Plus a **knowledge architecture** where information lives:

| Type | Purpose | Location |
|------|---------|----------|
| **Documents** | Reference materials Claude can access | `documents/` |
| **Outputs** | Generated artifacts | `outputs/` |

## Skill Types

Skills do three different things depending on their frontmatter:

| Type | Frontmatter | How it runs |
|------|-------------|-------------|
| **Slash command** | `disable-model-invocation: true` | You invoke with `/skill-name` |
| **Auto-loaded** | *(default)* | Claude loads when relevant |
| **Background knowledge** | `user-invocable: false` | Claude loads silently, hidden from `/` menu |

## Example Theme: Notes & Knowledge

This template uses a notes/knowledge management theme to show how pieces connect:

- **`/summarize`** skill → uses **`note-taking`** skill → saves to **`documents/notes/`**
- **`/save`** skill → saves session work → writes to **`outputs/sessions/`**
- **`writing-style`** skill → auto-loaded by Claude when writing (background knowledge)
- **`/research`** skill → forks an Explore subagent → returns findings
- **`researcher`** agent → searches **`documents/`** using **`knowledge-search`** skill
- **`writer`** agent → follows **`documents/style-guide.md`** → creates content

## Getting Started

1. **Clone this template**
2. **Review `CLAUDE.md`** for project context
3. **Try the skills**: `/summarize test-topic` or `/save my-session`
4. **Customize** for your use case

## Project Structure

```
cc-APP/
├── .claude/
│   ├── agents/             # Sub-agents
│   │   ├── researcher.md
│   │   └── writer.md
│   └── skills/             # All skills and slash commands
│       ├── summarize/          → /summarize (user-triggered)
│       ├── save/               → /save (user-triggered)
│       ├── research/           → /research (runs in Explore subagent)
│       ├── note-taking/        → auto-loaded when taking notes
│       ├── knowledge-search/   → auto-loaded when searching docs
│       └── writing-style/      → background knowledge, hidden from menu
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
