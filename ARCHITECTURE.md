# Architecture Philosophy: Building ccAPPs

> A mental framework for designing Claude Code Applications (ccAPPs)

---

## Core Pattern: Skills, Agents, MCPs

Every ccAPP is built on three fundamental primitives:

### Skills = Capabilities + Slash Commands

**What they are:** The primary building block. Skills teach Claude how to do things, and can also serve as user-triggered slash commands.

**Three modes, controlled by frontmatter:**

| Mode | Frontmatter | Use case |
|------|-------------|----------|
| **Slash command** | `disable-model-invocation: true` | User triggers explicitly: `/deploy`, `/summarize` |
| **Auto-loaded** | *(default)* | Claude uses when relevant: `note-taking`, `knowledge-search` |
| **Background knowledge** | `user-invocable: false` | Always-on context Claude loads silently: `writing-style`, `brand-voice` |

**When to create one:**
- User wants a slash command for a deliberate action
- Claude needs domain-specific knowledge or methods it doesn't have natively
- Instructions appear in multiple places (extract to a reusable skill)

**Examples:**
- `/summarize` — user-triggered slash command, saves notes to knowledge base
- `knowledge-search` — auto-loaded, teaches Claude how to search documents/
- `writing-style` — background knowledge, silently enforces conventions
- `/deploy` — user-triggered, runs deployment workflow with side effects

**Key characteristic:** Skills are **methods** — they define how work gets done, at whatever level of user control you need.

### Agents = Specialized Isolation

**What they are:** Focused sub-agents that run in isolation with specific skills and narrow context.

**When to create one:** When a task requires:
- **Specialized expertise** (editor, researcher, fact-checker)
- **Isolated context** (don't pollute the main conversation)
- **Repeatable specialization** (same type of work, different inputs)

**Examples:**
- `researcher` — deep knowledge base search without cluttering main context
- `editor` — QA review with brand compliance focus
- `competitor-analyzer` — analyze competing content
- `fact-checker` — verify claims against sources

**Key characteristic:** Agents are **specialists** — they do one thing well, with blinders on to everything else.

### MCPs = Tool Infrastructure

**What they are:** Model Context Protocol servers that provide specialized tools and data sources.

**How they relate to skills:** MCPs are the underlying tool layer that skills leverage. Skills define the "how" (methodology), while MCPs provide the "with what" (specific tools).

**Think of it as:**
```
MCP → Provides tools (search, fetch, database access)
  ↓
Skill → Uses MCP tools to accomplish a method
  ↓
Agent → Uses skills (which use MCP tools)
```

**Examples:**
- `parallel-search` MCP provides `web_search` and `web_fetch` tools
- `knowledge-search` skill uses those tools to search your knowledge base
- `researcher` agent uses the `knowledge-search` skill

**When to use an MCP:**
- You need specialized external tools (APIs, databases, search engines)
- You need data sources that skills can query
- You want to provide low-level capabilities that multiple skills can use

**When to use a Skill instead:**
- You're defining a methodology or approach
- You're teaching Claude domain-specific knowledge
- You're orchestrating multiple MCP tools together

**Key characteristic:** MCPs are **infrastructure** — they extend Claude's tooling so skills can accomplish more.

---

## The Knowledge Architecture

Beyond skills/agents/MCPs, every ccAPP needs places for knowledge to live. Think of it as an information architecture with different volatility levels:

### 1. Evergreen Context (Stable Reference)

**Purpose:** The "how things work here" knowledge that's always relevant and slowly evolves.

**Characteristics:**
- Updated infrequently
- Always consulted
- Defines the rules/voice/standards
- High signal-to-noise ratio

**Examples:**
- Brand voice and style guide
- Product information
- Company standards
- Approved terminology

**Questions to ask:**
- What stable knowledge does the system need to reference on every task?
- What defines "how we do things"?
- What changes slowly but matters always?

### 2. Dynamic Context (Growing Knowledge)

**Purpose:** The "what we know" knowledge that accumulates and grows over time.

**Characteristics:**
- Grows continuously
- Searchable library
- Referenced when relevant
- Examples, assets, research

**Examples:**
- Case studies
- Research papers
- Past work samples
- Customer data

**Questions to ask:**
- What knowledge accumulates over time?
- What should be searchable but not always loaded?
- What examples/assets/data does the system reference?

### 3. Input Buckets (User → System)

**Purpose:** Staging areas where users drop things for the system to process.

**Characteristics:**
- Temporary holding area
- Processed then archived/moved
- Clear processing trigger (usually a skill)
- One-way flow: user puts in, system takes out

**Examples:**
- Upload folder for brand files
- Inbox for documents to analyze
- Queue for items to process

**Questions to ask:**
- Where do users provide raw inputs?
- What needs processing before it's useful?
- What should be batch-processed vs. real-time?

### 4. Output Repositories (System → User)

**Purpose:** Where the system saves its work for users to consume.

**Characteristics:**
- Organized by session/task/date
- Versioned (never overwrite)
- Includes metadata and history
- Readable by both human and system

**Examples:**
- Content sessions with versioned drafts
- Generated reports
- Meeting summaries
- Analysis results

**Questions to ask:**
- What artifacts does the system create?
- How should they be organized (by date? by project? by type?)
- What metadata matters for retrieval?
- Should old versions be preserved?

### 5. Working Memory (Session State)

**Purpose:** What the system needs to remember within and across sessions.

**Characteristics:**
- Session-specific or persistent
- Logs, state, progress tracking
- May be temporary or permanent

**Examples:**
- Action logs (_log.md)
- Session inputs (_inputs.md)
- Progress checkpoints
- Feedback queue

**Questions to ask:**
- What does the system need to remember during a task?
- What needs to persist between sessions?
- What history is valuable for debugging or auditing?

### 6. Background Job Context (Async Work)

**Purpose:** Information accessible for work that happens outside the main conversation.

**Characteristics:**
- Readable by spawned agents
- Self-contained enough for isolation
- Clear triggering conditions

**Examples:**
- Research briefs for researcher agent
- Content for editor to review
- URLs for competitor analysis

**Questions to ask:**
- What work should happen in the background?
- What context does that work need?
- How do results get surfaced back?

---

## The Design Questions Framework

When building a new ccAPP, ask yourself these questions in order:

### Phase 1: Define Workflows (Skills as Slash Commands)

1. **What are the user's primary goals?**
   - What are they trying to accomplish?
   - What are the 3–5 main things they'll do repeatedly?

2. **Which goals are deliberate, user-initiated actions?**
   - What requires the user to consciously decide "I want to do X now"?
   - These become skills with `disable-model-invocation: true`

3. **Which actions follow a multi-step process?**
   - What are the steps and checkpoints?
   - Should any steps run in an isolated subagent (`context: fork`)?

### Phase 2: Identify Specializations (Agents)

4. **What tasks need specialized focus?**
   - What work would benefit from isolated context?
   - What requires deep, specialized expertise?
   - What shouldn't clutter the main conversation?

5. **What tasks are repetitive but varied?**
   - What do you do over and over, but with different inputs?
   - These become your **agents**

### Phase 3: Identify Tool Needs (MCPs)

6. **What external data sources or APIs do you need?**
   - Databases? Search engines? Third-party APIs?
   - These become your **MCPs**

7. **What specialized tools would multiple skills benefit from?**
   - Web search? File processing? Data transformation?
   - Look for existing MCPs or build custom ones

### Phase 4: Extend Capabilities (Auto-loaded Skills)

8. **What doesn't Claude know how to do in your domain?**
   - What special methods, formats, or standards apply?
   - How should Claude structure outputs?
   - What domain-specific knowledge is needed?

9. **What's reusable across multiple workflows?**
   - What instructions appear in multiple places?
   - These become auto-loaded skills

### Phase 5: Add Background Knowledge (Hidden Skills)

10. **What should always be true, regardless of task?**
    - Style guides? Conventions? Constraints?
    - These become skills with `user-invocable: false`

11. **What knowledge do agents need preloaded?**
    - Use the `skills` field on agents to inject skill content at startup

### Phase 6: Structure Knowledge (Folders)

12. **What stable knowledge does every task need?** → Evergreen Context
13. **What knowledge accumulates over time?** → Dynamic Context
14. **Where do users provide inputs?** → Input Buckets
15. **Where does work get saved?** → Output Repositories
16. **What needs to be remembered?** → Working Memory
17. **What work happens asynchronously?** → Background Job Context

---

## Example: Designing a Sales Assistant ccAPP

Let's walk through designing a hypothetical sales assistant ccAPP:

### User Goals
- Log sales calls and extract action items
- Research prospects before meetings
- Generate follow-up emails
- Create monthly reports

### Apply the Framework

**Skills (slash commands — user-triggered):**
- `/log-call` — process a sales call, `disable-model-invocation: true`
- `/research` — deep dive on prospect, `context: fork, agent: Explore`
- `/follow-up` — generate follow-up email, `disable-model-invocation: true`
- `/monthly-report` — aggregate month's activity, `disable-model-invocation: true`

**Skills (auto-loaded — Claude uses when relevant):**
- `crm-format` — how to structure data for CRM import
- `company-research` — how to research companies effectively
- `email-style` — how to write in the company's voice

**Agents:**
- `researcher` — deep company/prospect research
- `email-writer` — craft personalized emails
- `call-analyzer` — extract insights from call transcripts

**MCPs:**
- `parallel-search` — web search and fetch capabilities
- `crm-connector` — read/write to Salesforce/HubSpot
- `transcription` — audio-to-text conversion

**Knowledge Structure:**
```
sales-assistant/
├── .claude/
│   ├── agents/            # researcher, email-writer, call-analyzer
│   ├── skills/            # log-call, research, follow-up, monthly-report
│   │                      # crm-format, company-research, email-style
│   └── mcp_config.json
│
├── context/
│   ├── evergreen/         # STABLE: Company info, products, email templates
│   └── intel/             # DYNAMIC: Prospect research, company profiles
│
├── inbox/                 # INPUT BUCKET: Call recordings to process
│
├── calls/                 # OUTPUT: Processed calls by date
│   └── 2024-01-15-acme/
│       ├── transcript.md
│       ├── summary.md
│       ├── action-items.md
│       └── follow-up-email.md
│
└── reports/               # OUTPUT: Generated reports
    └── 2024-01-monthly.md
```

---

## Pattern Recognition Checklist

Use this to validate your design:

### Skills
- [ ] User-triggered skills have `disable-model-invocation: true`?
- [ ] Auto-loaded skills have descriptions that help Claude know when to use them?
- [ ] Background knowledge skills have `user-invocable: false`?
- [ ] Does each skill teach "how" not "what"?
- [ ] Is it reusable across multiple contexts?

### Agents
- [ ] Does each agent have a narrow, specialized focus?
- [ ] Would this work benefit from isolated context?
- [ ] Is this something done repeatedly with variation?

### MCPs
- [ ] Does this provide tools that multiple skills can use?
- [ ] Is this connecting to external data/APIs?
- [ ] Would building/using an MCP be better than hardcoding in skills?

### Knowledge Structure
- [ ] Evergreen: Is this always relevant and slowly changing?
- [ ] Dynamic: Does this grow and get searched?
- [ ] Input: Is this a staging area for processing?
- [ ] Output: Is this versioned and well-organized?
- [ ] Memory: Does the system need to remember this?

---

## Anti-Patterns to Avoid

### 1. Skill Bloat
**Problem:** Creating a skill for every tiny action
**Solution:** User-triggered skills should be significant intentions, not every possible action

### 2. Agent Overuse
**Problem:** Making agents for everything instead of using skills
**Solution:** Only create agents when isolation/specialization truly matters

### 3. Context Mixing
**Problem:** Putting stable and dynamic knowledge together
**Solution:** Separate by volatility and usage pattern

### 4. Output Chaos
**Problem:** Dumping all outputs into one folder
**Solution:** Organize by time, project, type — make it browsable

### 5. Over-Engineering Structure
**Problem:** Creating folders "just in case"
**Solution:** Start minimal, add structure as real needs emerge

### 6. MCP Misuse
**Problem:** Building an MCP when a skill would suffice, or hardcoding in skills when an MCP exists
**Solution:** Use MCPs for external tools/data, skills for methodology. Check existing MCPs before building custom ones

---

## Summary: The Mental Model

Think of a ccAPP as:

```
┌──────────────────────────────────────────────────────┐
│              USER INTERACTIONS                       │
│  ┌──────────────────────────────────┐  ┌──────────┐ │
│  │             Skills               │  │  Agents  │ │
│  │  /slash-cmd  │  auto  │  hidden  │  │(Special- │ │
│  │ (user-only)  │  load  │  knowl.  │  │  ists)   │ │
│  └──────────────────────────────────┘  └──────────┘ │
└─────────────────────────────────────────────────────-┘
                         ↓
┌──────────────────────────────────────────────────────┐
│              TOOL INFRASTRUCTURE                     │
│  ┌────────────────────────────────────────────┐     │
│  │  MCPs (APIs, Search, Data Sources)         │     │
│  └────────────────────────────────────────────┘     │
└──────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────┐
│           KNOWLEDGE ARCHITECTURE                     │
│  ┌────────────┐  ┌──────────────┐                   │
│  │ Evergreen  │  │   Dynamic    │                   │
│  │  Context   │  │   Context    │                   │
│  └────────────┘  └──────────────┘                   │
│                                                      │
│  ┌────────────┐  ┌──────────────┐                   │
│  │   Input    │  │    Output    │                   │
│  │  Buckets   │  │ Repositories │                   │
│  └────────────┘  └──────────────┘                   │
│                                                      │
│  ┌────────────┐  ┌──────────────┐                   │
│  │  Working   │  │  Background  │                   │
│  │   Memory   │  │Job Context   │                   │
│  └────────────┘  └──────────────┘                   │
└──────────────────────────────────────────────────────┘
```

**The framework in one sentence:**
Skills define what users can do and what Claude knows how to do (using MCP tools for infrastructure), agents handle specialized isolated work, and the knowledge architecture defines where information lives based on its stability, direction of flow, and usage pattern.

---

*This philosophy applies to any ccAPP: content creation, sales assistance, project management, research tools, and beyond.*
