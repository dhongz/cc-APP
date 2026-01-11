# Architecture Philosophy: Building ccAPPs

> A mental framework for designing Claude Code Applications (ccAPPs)

---

## Core Pattern: Commands, Agents, Skills, MCPs

Every ccAPP is built on four fundamental primitives:

### Commands = User Intentions

**What they are:** Entry points for deliberate, user-initiated actions

**When to create one:** When a user knows exactly what they want to accomplish

**Examples:**
- `/run [playbook]` - Execute a workflow
- `/save-notes` - Store meeting notes (in a sales ccAPP)
- `/generate-report` - Create a quarterly report
- `/organize-brand` - Process uploaded files

**Key characteristic:** Commands are **intentional** - the user is saying "I want to do THIS specific thing right now"

### Agents = Specialized Isolation

**What they are:** Focused sub-agents that run in isolation with specific skills and narrow context

**When to create one:** When a task requires:
- **Specialized expertise** (editor, researcher, fact-checker)
- **Isolated context** (you don't want to pollute main conversation)
- **Repeatable specialization** (same type of work, different contexts)

**Examples:**
- `researcher` - Deep web research without cluttering main context
- `editor` - QA review with brand compliance focus
- `competitor-analyzer` - Analyze competing content
- `fact-checker` - Verify claims against sources

**Key characteristic:** Agents are **specialists** - they do one thing really well, with blinders on to everything else

### Skills = Capability Extensions

**What they are:** Reusable instructions that extend what Claude can do naturally

**When to create one:** When Claude needs to know HOW to do something that isn't innate

**Examples:**
- `session` - How to create folders, version artifacts, manage outputs
- `brand-voice` - How to find and apply voice guidelines
- `seo-guidelines` - How to optimize for search
- `knowledge-search` - How to search your knowledge base

**Key characteristic:** Skills are **methods** - they teach Claude new capabilities it can use across contexts

### MCPs = Tool Infrastructure

**What they are:** Model Context Protocol servers that provide specialized tools and data sources

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

**Key characteristic:** MCPs are **infrastructure** - they extend Claude's tooling so skills can accomplish more

---

## The Knowledge Architecture

Beyond commands/agents/skills, every ccAPP needs places for knowledge to live. Think of it as an information architecture with different volatility levels:

### 1. Evergreen Context (Stable Reference)

**Purpose:** The "how things work here" knowledge that's always relevant and slowly evolves

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

**In this ccAPP:** `brand-context/brand-kit/`

**Questions to ask:**
- What stable knowledge does the system need to reference on every task?
- What defines "how we do things"?
- What changes slowly but matters always?

### 2. Dynamic Context (Growing Knowledge)

**Purpose:** The "what we know" knowledge that accumulates and grows over time

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

**In this ccAPP:** `brand-context/knowledge-base/`

**Questions to ask:**
- What knowledge accumulates over time?
- What should be searchable but not always loaded?
- What examples/assets/data does the system reference?

### 3. Input Buckets (User → System)

**Purpose:** Staging areas where users drop things for the system to process

**Characteristics:**
- Temporary holding area
- Processed then archived/moved
- Clear processing trigger (usually a command)
- One-way flow: user puts in, system takes out

**Examples:**
- Upload folder for brand files
- Inbox for documents to analyze
- Queue for items to process

**In this ccAPP:** `brand-upload/`

**Questions to ask:**
- Where do users provide raw inputs?
- What needs processing before it's useful?
- What should be batch-processed vs. real-time?

### 4. Output Repositories (System → User)

**Purpose:** Where the system saves its work for users to consume

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

**In this ccAPP:** `content/sessions/`

**Questions to ask:**
- What artifacts does the system create?
- How should they be organized (by date? by project? by type?)
- What metadata matters for retrieval?
- Should old versions be preserved?

### 5. Working Memory (Session State)

**Purpose:** What the system needs to remember within and across sessions

**Characteristics:**
- Session-specific or persistent
- Logs, state, progress tracking
- May be temporary or permanent

**Examples:**
- Action logs (_log.md)
- Session inputs (_inputs.md)
- Progress checkpoints
- Feedback queue

**In this ccAPP:** `_log.md`, `_inputs.md`, `feedback/`

**Questions to ask:**
- What does the system need to remember during a task?
- What needs to persist between sessions?
- What history is valuable for debugging or auditing?

### 6. Background Job Context (Async Work)

**Purpose:** Information accessible for work that happens outside the main conversation

**Characteristics:**
- Readable by spawned agents
- Self-contained enough for isolation
- Clear triggering conditions

**Examples:**
- Research briefs for researcher agent
- Content for editor to review
- URLs for competitor analysis

**In this ccAPP:** Passed via agent prompts + access to sessions/

**Questions to ask:**
- What work should happen in the background?
- What context does that work need?
- How do results get surfaced back?

---

## The Design Questions Framework

When building a new ccAPP, ask yourself these questions in order:

### Phase 1: Define Workflows (Commands)

1. **What are the user's primary goals?**
   - What are they trying to accomplish?
   - What are the 3-5 main things they'll do repeatedly?

2. **Which goals are intentional actions?**
   - What requires the user to consciously decide "I want to do X now"?
   - These become your **commands**

3. **Which actions follow a multi-step process?**
   - Which commands need workflows/playbooks?
   - What are the steps and checkpoints?

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

### Phase 4: Extend Capabilities (Skills)

8. **What doesn't Claude know how to do in your domain?**
   - What special methods, formats, or standards apply?
   - How should Claude structure outputs?
   - What domain-specific knowledge is needed?

9. **What's reusable across multiple workflows?**
   - What instructions appear in multiple places?
   - These become your **skills**

### Phase 5: Structure Knowledge (Folders)

10. **What stable knowledge does every task need?** → Evergreen Context
    - Brand guidelines? Standards? Rules? Voice?

11. **What knowledge accumulates over time?** → Dynamic Context
    - Examples? Research? Past work? Reference materials?

12. **Where do users provide inputs?** → Input Buckets
    - What comes from outside the system?
    - What needs processing or transformation?

13. **Where does work get saved?** → Output Repositories
    - What artifacts are created?
    - How should they be organized?
    - What metadata matters?

14. **What needs to be remembered?** → Working Memory
    - Within a session? Across sessions?
    - For debugging? For continuity?

15. **What work happens asynchronously?** → Background Job Context
    - What can agents do independently?
    - What context do they need?

---

## Example: Designing a Sales Assistant ccAPP

Let's walk through designing a hypothetical sales assistant ccAPP to see this framework in action:

### User Goals
- Log sales calls and extract action items
- Research prospects before meetings
- Generate follow-up emails
- Create monthly reports

### Apply the Framework

**Commands:**
- `/log-call [recording-or-notes]` - Process a sales call
- `/research [company]` - Deep dive on prospect
- `/follow-up [call-id]` - Generate follow-up email
- `/monthly-report` - Aggregate month's activity

**Agents:**
- `researcher` - Deep company/prospect research
- `email-writer` - Craft personalized emails
- `call-analyzer` - Extract insights from call transcripts

**MCPs:**
- `parallel-search` - Web search and fetch capabilities
- `crm-connector` - Read/write to Salesforce/HubSpot
- `transcription` - Audio-to-text conversion

**Skills:**
- `crm-format` - How to structure data for CRM import (uses `crm-connector` MCP)
- `company-research` - How to research companies effectively (uses `parallel-search` MCP)
- `email-style` - How to write in the company's voice

**Knowledge Structure:**
```
sales-assistant/
├── .claude/
│   ├── commands/          # log-call, research, follow-up, monthly-report
│   ├── agents/            # researcher, email-writer, call-analyzer
│   ├── skills/            # crm-format, company-research, email-style
│   └── mcp_config.json    # MCP configuration (parallel-search, crm-connector, etc.)
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

### Commands
- [ ] Are commands user-initiated and intentional?
- [ ] Does each command have a clear trigger/purpose?
- [ ] Are commands distinct from each other?

### Agents
- [ ] Does each agent have a narrow, specialized focus?
- [ ] Would this work benefit from isolated context?
- [ ] Is this something done repeatedly with variation?

### MCPs
- [ ] Does this provide tools that multiple skills can use?
- [ ] Is this connecting to external data/APIs?
- [ ] Would building/using an MCP be better than hardcoding in skills?

### Skills
- [ ] Does the skill teach "how" not "what"?
- [ ] Is it reusable across multiple contexts?
- [ ] Does it extend Claude's native capabilities?
- [ ] Does it leverage MCP tools where appropriate?

### Knowledge Structure
- [ ] Evergreen: Is this always relevant and slowly changing?
- [ ] Dynamic: Does this grow and get searched?
- [ ] Input: Is this a staging area for processing?
- [ ] Output: Is this versioned and well-organized?
- [ ] Memory: Does the system need to remember this?

---

## Anti-Patterns to Avoid

### 1. Command Bloat
**Problem:** Creating a command for every tiny action
**Solution:** Commands should be significant user intentions, not every possible action

### 2. Agent Overuse
**Problem:** Making agents for everything instead of using skills
**Solution:** Only create agents when isolation/specialization truly matters

### 3. Context Mixing
**Problem:** Putting stable and dynamic knowledge together
**Solution:** Separate by volatility and usage pattern

### 4. Output Chaos
**Problem:** Dumping all outputs into one folder
**Solution:** Organize by time, project, type - make it browsable

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
│  ┌──────────┐  ┌─────────┐  ┌────────┐             │
│  │ Commands │  │ Agents  │  │ Skills │             │
│  │(What to  │  │(Special-│  │(How to │             │
│  │  do)     │  │  ists)  │  │  do it)│             │
│  └──────────┘  └─────────┘  └────┬───┘             │
└────────────────────────────────────┼────────────────┘
                                     │
                                     ↓
┌──────────────────────────────────────────────────────┐
│              TOOL INFRASTRUCTURE                     │
│  ┌────────────────────────────────────────────┐     │
│  │  MCPs (APIs, Search, Data Sources)         │     │
│  │  (With what tools)                          │     │
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
Commands define what users want to do, agents and skills define how work gets done (using MCP tools for infrastructure), and the knowledge architecture defines where information lives based on its stability, direction of flow, and usage pattern.

---

*This philosophy applies to any ccAPP: content creation, sales assistance, project management, research tools, and beyond.*
