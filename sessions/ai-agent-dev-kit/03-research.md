# Research: AI Agent Dev Kit

**Session:** ai-agent-dev-kit
**Date:** 2026-02-23

---

## Existing Solutions & Competitors

| Solution | Description | Strengths | Weaknesses | Link |
|----------|-------------|-----------|------------|------|
| **GitHub Spec Kit** | Official GitHub toolkit for spec-driven development. Constitution → Specify → Plan → Tasks workflow. Agent-agnostic (Copilot, Claude Code, Gemini CLI, Cursor, Windsurf). | Backed by GitHub/Microsoft. Agent-agnostic. Strong community. Microsoft Learn course available. Well-documented spec-driven methodology. | No memory management. No user profiling. No TDD-specific phase. Configuration-heavy ("constitution" files). | [github/spec-kit](https://github.com/github/spec-kit) |
| **AI DevKit** | Universal CLI toolkit for AI-assisted dev. Works across Cursor, Claude Code, Codex, etc. Has memory management, skills registry, and phase templates. | Multi-agent support. Memory system (CLI-based, on-demand retrieval). Skill registry with custom registries. Active development. | Memory is CLI-based (not auto-selected by agent). No user profile system. Broader scope may mean less depth per agent. | [codeaholicguy/ai-devkit](https://github.com/codeaholicguy/ai-devkit) |
| **claude-code-spec-workflow** | Claude Code-specific automated workflows. Requirements → Design → Tasks → Implementation. Also has bug fix workflow. | Claude Code native. npm installable. Cached context for performance. Has MCP server version with web dashboard. | Claude Code only (though MCP version is broader). No memory persistence across sessions. Development shifting to MCP version. | [Pimzino/claude-code-spec-workflow](https://github.com/Pimzino/claude-code-spec-workflow) |
| **ai-rulez** | Universal configuration manager. Define rules in one YAML file, generate synced instructions for Claude, Cursor, Copilot, etc. | 18 preset generators. One config, many agents. Solves the "rules file fragmentation" problem. | Config management only — no workflow, no memory, no skills. Doesn't guide the development process itself. | [Goldziher/ai-rulez](https://github.com/Goldziher/ai-rulez) |
| **SuperClaude** | Configuration framework for Claude Code with specialized commands, cognitive personas, and dev methodologies. | Rich persona system. Specialized commands. Development methodology aware. | Claude Code only. More configuration framework than workflow toolkit. No persistent memory system. | [awesome-claude-code list](https://github.com/hesreallyhim/awesome-claude-code) |

### Key Takeaway

The space is active and growing fast. **GitHub Spec Kit** is the heavyweight for spec-driven development but lacks memory management and user profiling. **AI DevKit** is the closest direct competitor — it has memory, skills, and multi-agent support — but its memory is "pull-based" (agent calls CLI when needed) rather than "auto-selected" by the agent. **No existing tool combines user profiling + auto-memory selection + TDD-first workflow + spec-driven phases** in one package. That's your gap.

---

## Market Opportunity & Trends

**Strong tailwinds:**

- **Spec-Driven Development is going mainstream.** GitHub officially backing it with Spec Kit, Microsoft publishing training courses on it, and Martin Fowler writing about it signals this is not a niche anymore. ([Martin Fowler on SDD](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html))
- **Memory/context engineering is the 2026 hot topic.** Mem0 research showing 26% accuracy boost and 90% token savings. Multiple "AI memory products" lists appearing. The New Stack calling it "a new paradigm." ([Mem0 Research](https://mem0.ai/research), [The New Stack](https://thenewstack.io/memory-for-ai-agents-a-new-paradigm-of-context-engineering/))
- **Context rot is a proven problem.** Research shows GPT-4 accuracy drops from 99% to 70% at 32K tokens, Claude 3.5 Sonnet from 88% to 30%. More context ≠ better results. Smart context selection is essential. ([Context Window Scaling](https://dasroot.net/posts/2026/02/context-window-scaling-200k-tokens-help/))
- **Configuration proliferation.** Research paper shows Claude Code repos have the broadest configuration footprint. Developers are managing CLAUDE.md, .cursor/rules, AGENTS.md, etc. separately. ([Configuring Agentic AI Coding Tools](https://arxiv.org/html/2602.14690v1))
- **awesome-claude-code has 109+ curated skills/plugins** — the ecosystem is thriving and there's appetite for quality tooling. ([awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code))

**Potential headwinds:**

- **Fast-moving landscape.** Tools emerge and become obsolete quickly. GitHub Spec Kit went from 0 to Microsoft Learn course in weeks.
- **Platform risk.** Claude Code's skills system could evolve in ways that break or obsolete custom kits.
- **Crowded space.** Many developers are building similar workflow tools — differentiation matters more than timing.

### Key Takeaway

Timing is excellent. The market is validating both spec-driven development and intelligent memory management simultaneously. The risk is that the space moves fast, so a focused v1 with clear differentiation (auto-memory + user profile + TDD) is better than trying to be comprehensive.

---

## Technical Feasibility

**Claude Code's native capabilities support the entire plan:**

- **Skills system** — Custom slash commands via `.claude/skills/` with SKILL.md frontmatter. Unified with slash commands since v2.1.3. Supports both project-local and global skills. ([Claude Code Skills Docs](https://code.claude.com/docs/en/skills))
- **CLAUDE.md** — Project-level instructions loaded into every conversation. Should stay under ~150 lines for effectiveness. The `/kickoff` command can write this. ([Claude Code CLAUDE.md Best Practices](https://alexop.dev/posts/claude-code-customization-guide-claudemd-skills-subagents/))
- **Sub-agents** — Claude Code supports specialized sub-agents with custom instructions and tool permissions. Each skill can invoke sub-agents for specific phases. ([Agent Factory SDD Chapter](https://agentfactory.panaversity.org/docs/General-Agents-Foundations/spec-driven-development))
- **Task system** — Built-in todo/task tracking that `/implement` can leverage for ordered execution.
- **Hooks** — Shell commands that execute in response to tool events — could be used for auto-saving memory after certain actions.

**Memory architecture — file-based markdown is the right call for v1:**

- Mem0 shows structured memory delivers massive gains (90% token savings), but its full architecture (vector DB, graph memory) is overkill for a file-based kit.
- Markdown files in a `.kit/` directory are readable by humans and agents, version-controllable, and portable across agents.
- The `index.md` approach (agent reads index to decide which memories to load) is a lightweight version of what Mem0 calls "retrieval" — good enough for v1.
- If scaling is needed later, the markdown files could be indexed into a vector store without changing the user-facing interface.

### Recommended Tech Stack Options

- **Option A:** Pure Claude Code Skills (markdown only) — Skills in `.claude/skills/`, memory in `.kit/`, no external dependencies. Simplest, most portable. **Recommended for v1.**
- **Option B:** Skills + MCP Server — Add an MCP server for memory retrieval (similar to Pimzino's approach). More powerful but adds complexity and an external process.

### Key Takeaway

Everything needed is available natively in Claude Code. No external dependencies required for v1. The markdown-based memory system is technically simple but strategically smart — it's the approach that's most portable to other agents later.

---

## Similar Projects & Inspiration

- **GitHub Spec Kit**: The "constitution" concept (immutable project principles) is worth borrowing for `/kickoff`. Their Specify → Plan → Tasks flow validates the Explore → Plan → Code structure. ([github/spec-kit](https://github.com/github/spec-kit))
- **AI DevKit**: Their skills registry architecture and the idea of memory as a standalone CLI (not preloaded) is a design pattern to study. The key learning: memory should be on-demand, not dumped into every prompt. ([codeaholicguy/ai-devkit](https://github.com/codeaholicguy/ai-devkit))
- **Mem0**: Their research proves that structured memory with intelligent retrieval massively outperforms full-context approaches. The 90% token savings benchmark is the target. ([Mem0 Research Paper](https://arxiv.org/abs/2504.19413))
- **claude-code-spec-workflow**: Their cached context and session-based approach shows that performance optimization matters for DX. Also proves the workflow can be distributed via npm. ([Pimzino/claude-code-spec-workflow](https://github.com/Pimzino/claude-code-spec-workflow))
- **OpenAI Agents SDK Context Personalization Cookbook**: Shows a practical pattern for context engineering with long-term memory notes — the "memory note" pattern aligns well with the `.kit/` memory files approach. ([OpenAI Cookbook](https://cookbook.openai.com/examples/agents_sdk/context_personalization))

---

## Potential Partners & Platforms

- **awesome-claude-code** — Get listed here for visibility in the Claude Code ecosystem. ([hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code))
- **awesome-claude-skills** — Multiple curated lists exist for Claude skills. ([travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills), [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills))
- **npm registry** — Both Pimzino and AI DevKit distribute via npm. Consider this for easy installation.
- **Anthropic's Claude Code community** — As the skills ecosystem grows, Anthropic may feature quality kits.
- **vibecoding.app, awesomeclaude.ai** — Review/directory sites already covering tools in this space.

---

## Research Summary (Round 1)

- **GitHub Spec Kit is the market leader** for spec-driven development but has no memory management, no user profiling, and no TDD workflow — that's your differentiation gap.
- **AI DevKit is the closest competitor** with memory + skills + multi-agent support, but its memory is pull-based (CLI-invoked), not auto-selected by the agent. Your "agent autonomously selects relevant memories" approach is a meaningful differentiator.
- **Context rot is scientifically validated** — more tokens ≠ better results. Smart memory selection isn't just a nice-to-have, it's a correctness and cost issue.
- **Claude Code's native skills system provides everything needed** for v1 — no external dependencies, no MCP server, just markdown files and skill definitions.
- **The space is crowded but fragmented** — no single tool combines user profiling + auto-memory + TDD-first + spec-driven workflow. A focused, opinionated kit can carve a distinct niche.

---
---

# Deep Dive: Memory Management

**Date:** 2026-02-23
**Focus:** How to design the `.kit/` memory system — architecture patterns, curation strategies, and what's proven in the industry.

---

## 1. How Existing Tools Handle Memory

### Claude Code's Built-in Auto Memory

Claude Code already has a memory system at `~/.claude/projects/<project>/memory/`. Key design choices to learn from: ([Claude Code Memory Docs](https://code.claude.com/docs/en/memory), [Claude Memory Evolution Blog](https://yuanchang.org/en/posts/claude-code-auto-memory-and-hooks/))

- **MEMORY.md** is the entrypoint — first 200 lines auto-loaded into system prompt every session
- **Topic files** (e.g., `debugging.md`, `api-conventions.md`) are NOT auto-loaded — agent reads them on-demand
- Claude writes these notes **for itself**, not as instructions from the user
- Hooks fire at key moments: `Stop` (after every response), `PreCompact` (before context compaction), `SessionEnd`

**Implication for your kit:** Your `.kit/` system should complement this, not duplicate it. Claude Code's auto-memory handles "what Claude noticed" — your `.kit/` should handle **"what the workflow decided"** (specs, architectural decisions, plans).

### claude-mem (Persistent Memory Plugin)

A Claude Code plugin that captures, compresses, and re-injects context across sessions. ([claude-mem GitHub](https://github.com/thedotmack/claude-mem), [claude-mem Docs](https://docs.claude-mem.ai/introduction))

- Captures tool usage as "observations" (1,000–10,000 tokens each)
- Compresses to ~500-token semantic summaries using Claude's Agent SDK
- Stores in **SQLite** with full-text search
- Auto-injects context from last 10 sessions on startup
- "Endless Mode" achieves ~95% context window reduction, enabling ~20x more tool uses

**Implication:** Proves compression works massively for token efficiency. But it's summary-based — your approach of keeping *specifics* (specs, decisions with "why") while removing *fluff* is a different and potentially better strategy for development workflows.

### AI DevKit Memory

Designed to be "smaller, stricter, and more boring — no fancy tech." ([codeaholicguy/ai-devkit](https://github.com/codeaholicguy/ai-devkit))

- Memory is a **standalone CLI** — agent calls it when needed, gets structured minimal output
- Not preloaded into every session
- Stores: coding patterns, project guidelines, actionable insights
- Available via MCP and directly via CLI

**Implication:** Their "agent calls when needed" model is pull-based. Your approach should be smarter — the skill itself knows what memory categories to load based on what phase it's executing.

### Mem0 (Research-Grade Memory Layer)

The most sophisticated approach, backed by research. ([Mem0 Research](https://mem0.ai/research), [Mem0 Paper](https://arxiv.org/abs/2504.19413))

- **Extraction phase:** Ingests latest exchange + rolling summary + recent messages → LLM extracts "memory candidates" (facts, preferences, decisions)
- **Update phase:** Each new fact compared to similar entries in vector DB → LLM decides: **ADD**, **UPDATE**, **DELETE**, or **NOOP**
- Results: 26% accuracy boost, 91% lower latency, **90% token savings**

**Key insight for your kit:** The ADD/UPDATE/DELETE/NOOP decision framework is exactly what you need for "curated knowledge, not summaries." Your agent should make these same decisions on each memory file — just using markdown files instead of a vector DB.

---

## 2. Proven Memory Architecture Patterns

### The Four Memory Types (CoALA Framework)

Academic research identifies four categories of agent memory. ([Making Sense of Memory in AI Agents](https://www.leoniemonigatti.com/blog/memory-in-ai-agents.html))

| Type | What it stores | Your `.kit/` equivalent |
|------|---------------|------------------------|
| **Working Memory** | Current conversation context | Claude Code's context window (not in `.kit/`) |
| **Semantic Memory** | Factual knowledge, decisions | `project-memory/`, `task-memory/*/decisions.md` |
| **Episodic Memory** | Past actions and experiences | `task-memory/*/explore.md`, `agent-memory/lessons.md` |
| **Procedural Memory** | System instructions, guidelines | CLAUDE.md, SKILL.md files |

### The Letta Four-Tier Model

A design-focused architecture used in production. ([Synix: Agent Memory Systems](https://synix.dev/articles/agent-memory-systems/))

| Tier | Role | Your equivalent |
|------|------|-----------------|
| **Core Memory** | Agent-managed blocks of key info | `index.md` + `project-memory/` |
| **Message Buffer** | Recent conversation | Claude Code handles this |
| **Recall Memory** | Conversation history | Not needed — conversations are ephemeral |
| **Archival Memory** | Explicit knowledge storage | `task-memory/`, `agent-memory/` |

### EverMemOS: Type-Stratified Storage

Uses **7 distinct memory types** with tailored extraction per type. ([Agent Memory Paper List](https://github.com/Shichun-Liu/Agent-Memory-Paper-List))

Relevant insight: **different memory types need different save/prune strategies.** A spec document should rarely be pruned. A decision log should be updated when decisions change. Lessons learned should be consolidated over time.

---

## 3. Memory Curation Strategies (Save vs. Prune)

### Mem0's ADD/UPDATE/DELETE/NOOP Framework

The most practical model for your "curated knowledge" approach. For each piece of information, the agent decides: ([Mem0 Paper](https://arxiv.org/abs/2504.19413))

| Operation | When to use | Example in your kit |
|-----------|-------------|---------------------|
| **ADD** | Fact is new, not in any memory file | New architectural decision: "Using PostgreSQL for persistence" |
| **UPDATE** | Existing memory needs augmentation or correction | Decision changed: "Switched from PostgreSQL to SQLite — reason: simpler for v1" |
| **DELETE** | Information is contradicted or obsolete | Removed old tech stack exploration that led nowhere |
| **NOOP** | Nothing new or useful to store | General conversation, questions already answered |

### Temporal Annotation (from Graphiti)

Each memory entry should carry timestamps for lifecycle management. ([Synix: Agent Memory Systems](https://synix.dev/articles/agent-memory-systems/))

Suggested frontmatter for your markdown memory files:

```yaml
---
created: 2026-02-23
last_updated: 2026-02-23
status: active          # active | superseded | archived
superseded_by: null     # link to newer decision if replaced
tags: [architecture, database, decision]
---
```

This lets the agent quickly filter: only load `status: active` memories, skip `superseded` ones.

### Anthropic's Own Context Engineering Guidance

Anthropic's official recommendations for managing agent context. ([Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents))

Key principles that apply directly to your memory system:

1. **"Find the smallest set of high-signal tokens that maximize the likelihood of the desired outcome"** — Don't store everything, store what changes agent behavior
2. **Keep lightweight identifiers, load data on demand** — Your `index.md` approach aligns with this. Index stores pointers, agent loads full files only when needed
3. **Structured note-taking > conversation replay** — Agents should write persistent notes (your memory files), not replay old conversations
4. **Compaction at the right moments** — Use Claude Code's `PreCompact` and `SessionEnd` hooks as triggers for memory save/prune operations

### Manus's "File System as Memory" Approach

Manus (a production AI agent) treats the file system as unlimited context. ([Manus Context Engineering Blog](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus))

Key patterns:

- **Leave wrong turns in context** — don't erase failures from memory, they help avoid repeating mistakes (maps to your `agent-memory/lessons.md`)
- **Use todo.md for recitation** — writing current objectives to a file combats the "lost-in-the-middle" effect. Your `index.md` serves a similar role
- **Compression must be restorable** — keep file paths and references so the agent can navigate back to full details. Don't compress away the pointers

---

## 4. Practical Architecture Recommendations for `.kit/`

Based on all research, here's a refined architecture:

### Memory File Structure

```
.kit/
├── index.md                         # Memory map — agent reads this first
├── project-memory/
│   ├── architecture.md              # Tech stack, patterns, conventions
│   ├── requirements.md              # Core requirements and specs
│   └── decisions/
│       └── 001-database-choice.md   # Individual decisions with reasoning
├── task-memory/
│   └── <task-name>/
│       ├── context.md               # What was explored, key findings
│       ├── plan.md                  # Implementation plan
│       ├── decisions.md             # Task-specific decisions
│       └── status.md               # Current state, what's done/remaining
├── agent-memory/
│   ├── lessons.md                   # Mistakes to avoid, patterns that work
│   └── patterns.md                  # Reusable code patterns discovered
└── archive/                         # Superseded/completed task memories
    └── <old-task-name>/
```

### Memory Operations Per Skill

| Skill | Reads | Writes | Prunes |
|-------|-------|--------|--------|
| `/explore` | `index.md`, relevant `project-memory/`, relevant `task-memory/` | `task-memory/<task>/context.md`, `project-memory/decisions/` | Remove dead-end explorations, keep only findings |
| `/plan_docs` | `index.md`, `task-memory/<task>/context.md`, `project-memory/` | `task-memory/<task>/plan.md` | Remove draft plans when final plan is written |
| `/gen_test` | `index.md`, `task-memory/<task>/plan.md` | (test files in codebase, not in .kit/) | N/A |
| `/implement` | `index.md`, `task-memory/<task>/plan.md`, `agent-memory/` | `agent-memory/lessons.md`, `task-memory/<task>/status.md` | Archive completed task memory |

### Index.md Design

The `index.md` should be a **structured, scannable map** — not prose. Suggested format:

```markdown
# Memory Index

## Active Tasks
- **auth-system**: Implementing JWT auth → [context](task-memory/auth-system/context.md) | [plan](task-memory/auth-system/plan.md)

## Project Knowledge
- **Architecture**: Next.js + PostgreSQL + Drizzle ORM → [details](project-memory/architecture.md)
- **Requirements**: Core MVP specs → [details](project-memory/requirements.md)

## Recent Decisions
- #003 Use JWT over sessions (2026-02-23) → [why](project-memory/decisions/003-jwt-auth.md)
- #002 PostgreSQL over SQLite (2026-02-22) → [why](project-memory/decisions/002-database.md)

## Agent Lessons
- 4 lessons recorded → [view](agent-memory/lessons.md)
```

### When to Save/Prune (Trigger Points)

| Trigger | Action |
|---------|--------|
| End of `/explore` | Save key findings to `context.md`. ADD/UPDATE decisions. DELETE dead-end notes. |
| End of `/plan_docs` | Save plan to `plan.md`. UPDATE `index.md` with task entry. |
| End of `/implement` (task complete) | Move task memory to `archive/`. UPDATE `agent-memory/lessons.md` with learnings. |
| Decision changes | UPDATE the decision file, set old version to `status: superseded`. |
| Manual correction by user | User directly edits `.kit/` files (they're just markdown). |

---

## 5. How Existing Memory Tools Compare to Your Approach

| Dimension | Claude Code Auto Memory | claude-mem | AI DevKit | Mem0 | **Your `.kit/`** |
|-----------|------------------------|------------|-----------|------|------------------|
| Storage | Markdown files | SQLite | CLI-managed | Vector DB + Graph | Markdown files |
| What's stored | Agent's self-notes | Compressed observations | Coding patterns, guidelines | Extracted facts | Specs, decisions (with why), findings, lessons |
| Retrieval | First 200 lines auto-loaded | Last 10 sessions auto-injected | Agent calls CLI on-demand | Semantic similarity search | Agent reads index.md, loads relevant files |
| Curation | Agent writes freely | AI compression | Manual + CLI | ADD/UPDATE/DELETE/NOOP | **ADD/UPDATE/DELETE/NOOP per skill execution** |
| Token efficiency | ~200 lines loaded always | ~95% reduction | Minimal output per query | 90% savings | Load only what current skill needs |
| Human-readable | Yes (markdown) | No (SQLite) | Partial | No (vector DB) | **Yes (markdown, git-trackable)** |
| Portable to other agents | No (Claude Code-specific) | No | Yes (via MCP) | Yes (API) | **Yes (plain files)** |

---

## Research Summary (Round 2: Memory Management)

- **Mem0's ADD/UPDATE/DELETE/NOOP framework** is the proven model for memory curation. Adapt this for your markdown-based system — each skill execution ends with the agent deciding what to add, update, delete, or skip for each memory file. ([Mem0 Research](https://mem0.ai/research))
- **Temporal metadata matters.** Add `created`, `last_updated`, `status` frontmatter to memory files so the agent can quickly filter active vs. superseded knowledge. Inspired by Graphiti's temporal knowledge graph. ([Synix: Agent Memory Systems](https://synix.dev/articles/agent-memory-systems/))
- **Index.md should be a structured map, not prose.** A scannable list of tasks, decisions, and knowledge pointers — the agent reads this in seconds and knows exactly which files to load. Inspired by Manus's todo.md recitation pattern. ([Manus Blog](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus))
- **Each skill should have defined memory read/write/prune operations.** Not every skill touches every memory file. `/explore` writes findings, `/implement` writes lessons, completed tasks get archived. This keeps memory lean.
- **Your approach fills a unique niche:** Human-readable, git-trackable, agent-portable markdown files with structured curation (not just compression or summaries). No existing tool does this. ([Anthropic Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents))
- **Claude Code hooks (`PreCompact`, `SessionEnd`) are natural triggers** for memory save operations — use them to ensure memories are persisted even if the user doesn't complete a full skill cycle. ([Claude Memory Hooks](https://yuanchang.org/en/posts/claude-code-auto-memory-and-hooks/))

---
---

# Deep Dive: Packaging & Distribution

**Date:** 2026-02-23
**Focus:** How to package Praxis-kit so users can easily install it and use it directly with Claude Code (and potentially other agents).

---

## 1. Distribution Channels Available Today

### Option A: Vercel Skills Ecosystem (`npx skills add`)

The dominant distribution channel for agent skills as of early 2026. ([Vercel Skills](https://vercel.com/docs/agent-resources/skills), [skills.sh directory](https://skills.sh/))

**How it works:**
- Users run `npx skills add your-github/praxis-kit`
- CLI auto-detects installed coding agents (Claude Code, Cursor, Codex, etc.)
- Copies/symlinks SKILL.md files into agent-specific directories
- Skills appear on [skills.sh](https://skills.sh/) directory automatically via install telemetry

**What you need to publish:**
- A GitHub repo with skills in standard directories (`skills/`, `.agents/skills/`, or agent-specific paths)
- Each skill has a `SKILL.md` with YAML frontmatter (`name`, `description`)
- No registry submission — installs auto-register on skills.sh

**Pros:**
- One command install across 37+ agents (Claude Code, Cursor, Codex, OpenCode, Gemini CLI, etc.)
- Built-in discovery via skills.sh leaderboard
- No npm publish needed — works directly from GitHub
- Vercel-backed ecosystem with strong momentum (26,000+ installs in first weeks)

**Cons:**
- Only installs SKILL.md files — can't run setup scripts (no `.kit/` initialization)
- Your memory system requires a `/kickoff` step anyway, so this may be acceptable
- Ecosystem controlled by Vercel

**Verdict:** Best for **discovery and reach**. Users install skills, then run `/kickoff` to set up `.kit/`.

---

### Option B: Claude Code Plugin System (`/plugin install`)

Claude Code's native plugin system, with Anthropic's official marketplace. ([Claude Code Plugin Docs](https://code.claude.com/docs/en/discover-plugins), [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official))

**How it works:**
- You create a marketplace repo with `.claude-plugin/marketplace.json`
- Users add your marketplace: `/plugin marketplace add your-github/praxis-kit`
- Then install: `/plugin install praxis-kit@your-marketplace-name`
- Supports scopes: user (global), project, local

**What you need to publish:**
- GitHub repo with `.claude-plugin/marketplace.json` listing your plugins
- Each plugin can include skills, agents, hooks, and MCP servers
- Can bundle multiple components (skills + hooks + setup logic)

**Pros:**
- Native to Claude Code — no external tooling needed
- Can bundle hooks (e.g., `PreCompact` for auto-memory saves)
- Supports auto-updates — users get new versions automatically
- Anthropic's official marketplace could feature your plugin
- Richer than skills-only — can include agents, hooks, MCP servers

**Cons:**
- Claude Code only — no multi-agent reach
- Still requires `/kickoff` for `.kit/` initialization
- Marketplace ecosystem is newer, less established than skills.sh
- More complex to maintain (marketplace.json + plugin structure)

**Verdict:** Best for **Claude Code depth**. Lets you bundle skills + hooks + setup. Pair with Option A for multi-agent reach.

---

### Option C: npm Package with Setup Script (`npx praxis-kit`)

Pattern used by cc-sdd and claude-code-spec-workflow. ([cc-sdd](https://github.com/gotalab/cc-sdd), [claude-code-spec-workflow](https://www.npmjs.com/package/@pimzino/claude-code-spec-workflow))

**How it works:**
- You publish an npm package with a CLI setup script
- Users run `npx praxis-kit@latest` (or `npx praxis-kit init`)
- Script copies skills into `.claude/skills/`, optionally initializes `.kit/`, writes to CLAUDE.md

**What you need to publish:**
- npm package with `bin` entry pointing to a setup script
- Script handles file copying, directory creation, CLAUDE.md updates
- Can include interactive prompts (language, agent selection, etc.)

**Example (cc-sdd pattern):**
```bash
# One-command install with options
npx praxis-kit@latest
npx praxis-kit@latest --agent claude-code --lang en
```

**Pros:**
- Familiar to all JS/TS developers
- Can run setup logic (create `.kit/`, write CLAUDE.md conventions, etc.)
- Version management via npm (`@latest`, `@1.0.0`)
- Can support multiple agents via flags (like cc-sdd supports 7 agents)
- Could combine `/kickoff` + install into one step

**Cons:**
- Requires Node.js/npm on user's system
- npm publish process (account, versioning, etc.)
- Not discoverable on skills.sh unless also published there
- Setup script is a one-time run — no built-in update mechanism

**Verdict:** Best for **power users and complete setup**. One command does everything: install skills + initialize `.kit/` + write CLAUDE.md.

---

### Option D: `add-skill` CLI (`npx add-skill`)

A third-party tool specifically for installing agent skills from repos. ([add-skill.org](https://add-skill.org/))

**How it works:**
- Users run `npx add-skill your-github/praxis-kit`
- Tool auto-detects installed agents (Claude Code, Codex, Cursor, OpenCode)
- Copies skills into correct agent-specific directories
- Supports `--skill` flags for selective install, `-g` for global

**Pros:**
- Simple and focused — just installs SKILL.md files
- Multi-agent (4 agents: Claude Code, Codex, Cursor, OpenCode)
- Supports both project-local and global installation
- Non-interactive mode for CI/CD

**Cons:**
- Smaller ecosystem than Vercel's skills
- Skills-only — can't run setup scripts
- Less discovery than skills.sh

**Verdict:** Alternative to Option A with similar trade-offs. Less reach but simpler.

---

### Option E: Manual Copy (Current Plan Default)

The simplest approach — documented in the guide already.

```bash
git clone https://github.com/your-github/praxis-kit.git
cp -r praxis-kit/.claude/skills/ your-project/.claude/skills/
```

**Pros:**
- Zero dependencies, works everywhere
- User sees exactly what's being installed
- Good for initial development and dogfooding

**Cons:**
- No versioning or updates
- Multi-step process
- No discovery

**Verdict:** Keep as **fallback** and development workflow. Not a distribution strategy.

---

## 2. Recommended Distribution Strategy

### Multi-Channel Approach (recommended)

Don't pick one — use multiple channels that serve different purposes:

| Channel | Purpose | Priority | When to build |
|---------|---------|----------|---------------|
| **`npx praxis-kit`** (npm) | Complete one-command setup (skills + `.kit/` + CLAUDE.md) | **Primary** | Milestone 1-2 |
| **Vercel Skills** (`npx skills add`) | Discovery + multi-agent reach | **Secondary** | Milestone 3 |
| **Claude Code Plugin** (`/plugin install`) | Deep Claude Code integration (skills + hooks) | **Secondary** | Milestone 4 |
| **Manual copy** | Development, docs, fallback | **Fallback** | Already done |

### Why This Order

1. **`npx praxis-kit` first** — Because Praxis-kit isn't just skills. It needs `.kit/` directory creation and CLAUDE.md setup. Only an npm setup script can do that in one command. This replaces the current "git clone + copy + run /kickoff" with just `npx praxis-kit`.

2. **Vercel Skills second** — Once the skills are stable, publish to skills.sh for discovery. Users who find you there can install skills instantly, then run `/kickoff` for the memory system. This gives you visibility across 37+ agents.

3. **Claude Code Plugin third** — When you're ready to add hooks (`PreCompact` for auto-memory saves, etc.), package as a Claude Code plugin. This is the "premium" distribution for Claude Code power users.

### What `npx praxis-kit` Should Do

```
$ npx praxis-kit@latest

  Praxis-kit v1.0.0

  ✓ Detected Claude Code
  ✓ Copied 6 skills to .claude/skills/
  ✓ Created .kit/ directory structure
  ✓ Added workflow conventions to CLAUDE.md

  Ready! Open Claude Code and run /profile to get started.
```

The npm package would:
1. Detect which agents are installed (start with Claude Code)
2. Copy SKILL.md files to `.claude/skills/`
3. Copy `_memory-protocol.md` to `.claude/skills/`
4. Create `.kit/` directory with templates (index.md, project-memory/, etc.)
5. Append spec-driven workflow conventions to CLAUDE.md (if not already present)
6. Print a getting-started message

This effectively **merges the install step with `/kickoff`** — one command to go from zero to ready.

---

## 3. Repo Structure for Multi-Channel Distribution

```
praxis-kit/
├── package.json                    # npm package config
├── bin/
│   └── cli.js                      # npx praxis-kit setup script
├── skills/                         # Vercel skills format (for npx skills add)
│   ├── kickoff/SKILL.md
│   ├── profile/SKILL.md
│   ├── explore/SKILL.md
│   ├── plan-docs/SKILL.md
│   ├── gen-test/SKILL.md
│   └── implement/SKILL.md
├── .claude/skills/                 # Claude Code native format (for manual copy)
│   ├── _memory-protocol.md
│   ├── kickoff/SKILL.md
│   ├── profile/SKILL.md
│   ├── explore/SKILL.md
│   ├── plan-docs/SKILL.md
│   ├── gen-test/SKILL.md
│   └── implement/SKILL.md
├── .claude-plugin/                 # Claude Code plugin format
│   └── marketplace.json
├── templates/                      # .kit/ templates (copied during setup)
│   ├── index.md
│   ├── architecture.md
│   └── requirements.md
└── README.md
```

The same SKILL.md files serve all three channels — just referenced from different locations.

---

## 4. Competitive Packaging Comparison

| Tool | Install Command | What it installs | Setup needed after? |
|------|----------------|------------------|---------------------|
| **cc-sdd** | `npx cc-sdd@latest` | Skills + docs + templates | No — ready to use |
| **AI DevKit** | `npx ai-devkit` | CLI + skills + memory CLI | Some config |
| **GitHub Spec Kit** | `pip install github-spec-kit` / `uvx github-spec-kit` | CLI tool | Run `spec init` |
| **claude-code-spec-workflow** | `npm i -g @pimzino/claude-code-spec-workflow` | Global CLI + skills | Run setup command |
| **Praxis-kit (proposed)** | `npx praxis-kit` | Skills + `.kit/` + CLAUDE.md | Just run `/profile` |

Praxis-kit's advantage: **fewest post-install steps**. One command gets you skills + memory system + workflow conventions. Only `/profile` (optional, one-time) remains.

---

## Research Summary (Round 3: Packaging & Distribution)

- **Multi-channel distribution is the smart play.** No single channel covers everything Praxis-kit needs. npm for complete setup, Vercel Skills for discovery, Claude Code Plugin for deep integration.
- **`npx praxis-kit` should be the primary channel** because Praxis-kit isn't just skills — it needs `.kit/` creation and CLAUDE.md setup that only a setup script can automate.
- **Vercel's skills.sh is the discovery layer.** With 37+ agents and 26,000+ installs in its first weeks, it's the npm-of-skills. Publishing there gives Praxis-kit visibility across the entire agent ecosystem.
- **Claude Code's plugin system is the depth layer.** It supports skills + hooks + MCP servers in one package. When Praxis-kit adds hooks for auto-memory persistence, this becomes the premium distribution channel.
- **The repo structure can serve all channels simultaneously.** Same SKILL.md files referenced from `skills/` (Vercel), `.claude/skills/` (manual), and `.claude-plugin/` (plugin system).
- **cc-sdd validates the `npx` pattern.** They use `npx cc-sdd@latest` with language and agent flags, supporting 7 agents. Praxis-kit can follow the same pattern.

---

*Next step: Run `/challenge` to stress-test the idea, or `/plan` to update the project plan with distribution strategy.*
