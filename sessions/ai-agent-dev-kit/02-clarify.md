# Clarify: AI Agent Dev Kit

**Session:** ai-agent-dev-kit
**Date:** 2026-02-23
**Rounds of clarification:** 3

---

## Problem

AI coding agents (Claude Code, Codex, etc.) lack a structured development workflow out of the box. Developers either jump straight into coding without proper exploration/planning, or they manually manage context by hand — choosing which files and background to feed the agent. This leads to wasted tokens, incorrect assumptions, and inconsistent results. There's no reusable "operating system" that enforces a disciplined spec-driven process while intelligently managing what the agent knows.

## Audience

AI/software engineers who already use AI coding assistants and want a more structured, repeatable workflow. Users declare their skill level, preferences, and working style upfront via a profile — the kit adapts to them rather than being one-size-fits-all.

## Motivation

The creator is an AI engineer who experiences these pain points firsthand. The AI coding tool ecosystem is exploding but the *workflow layer* on top of these tools is still immature. Building this now captures a real gap before the space consolidates.

## Vision

**Short-term (v1):** A usable Claude Code kit with slash commands that guide a developer through Explore → Plan → Code with automatic memory management. Good enough to use on real projects as a side-project tool.

**Long-term:** Expand to support multiple AI coding agents (Codex, Cursor, etc.) using the same parallel memory system and workflow structure. The kit becomes an agent-agnostic "spec-driven development layer."

## Constraints

- **Agent target:** Claude Code first (expand later)
- **Timeline:** Side project built in free time — no hard deadline, but v1 should be production-usable
- **Tech:** Markdown-based memory files, slash commands (Claude Code SKILLS system)
- **Memory system:** Parallel to Claude Code's built-in `.claude/` — independent system stored in its own directory (e.g., `.kit/`) so it's portable to other agents in the future

## Scope

### In scope (v1)

| Command | Purpose |
|---|---|
| `/profile` | One-time user onboarding — writes `# About Me` section directly into CLAUDE.md (skill level, preferences, behaviors). Re-runnable to update. |
| `/kickoff` | Initialize project — creates CLAUDE.md via Claude Code's `/init`, injects spec-driven workflow conventions |
| `/explore` | Research & clarify technical, framework, and business decisions or whatever user want to explore. Auto-saves memories. |
| `/plan_docs` | Generate general plan + detailed plans — concise, implementation-ready, source of truth. No unrelated content. |
| `/gen_test` | Generate test cases following TDD approach |
| `/implement` | Implement code and tests, auto-creates a todo list, executes tasks in order |

### Memory architecture (proposed)

```
.kit/
├── project-memory/
│   └── architecture.md          # Tech stack, conventions, key decisions
├── task-memory/
│   └── <task-name>/
│       ├── explore.md           # Research findings, clarifications
│       ├── plan.md              # Plans generated
│       └── decisions.md         # Key decisions made during the task
├── agent-memory/
│   └── lessons.md               # Reusable insights, mistakes to avoid
└── index.md                     # Memory index for quick agent lookup
```

**User profile** lives in CLAUDE.md (`# About Me` section), not in `.kit/` — this keeps user preferences in the place Claude Code already reads on every conversation.

**Memory management philosophy — curated knowledge, not summaries:**

The agent actively maintains memory quality by saving useful specifics and removing unnecessary content:

| Save (keep) | Remove (prune) |
|---|---|
| Specs and requirements | Conversational back-and-forth |
| Decisions **with the "why"** (reasoning) | Intermediate thinking / dead-end explorations |
| Architectural choices and trade-offs | Outdated decisions that were superseded |
| Key findings from `/explore` | Redundant info already captured elsewhere |
| Actionable patterns and lessons | Generic info that's easily searchable |

This makes `.kit/` a **living, curated knowledge base** — not append-only, not just summaries, but actively maintained high-quality context. The agent reads `index.md` to determine which memories are relevant to the current task and pulls only those into context.

### Out of scope (v1)

- Multi-agent support (Codex, Cursor, etc.) — deferred to future versions
- GUI or web interface — CLI/slash-command only
- Team/org features — single-developer use first
- Auto-inferring user profile — manual `/profile` only for now

---

## Clarified Idea Summary

The **AI Agent Dev Kit** is a Claude Code-first toolkit that gives AI/software engineers a structured **spec-driven development workflow** via slash commands. Users start with `/profile` to declare who they are (written to CLAUDE.md's `# About Me`), `/kickoff` to initialize a project with the right conventions, then follow the core loop: `/explore` (research and clarify) → `/plan_docs` (create implementation-ready plans) → `/gen_test` (TDD test cases) → `/implement` (code with auto-managed todo list).

The key differentiator is **intelligent memory management** — the kit maintains a `.kit/` directory of curated markdown knowledge (project context, task-specific findings, decisions with reasoning, agent-learned lessons). Unlike simple summaries, the agent actively maintains memory quality: saving useful specifics (specs, decisions with "why", key findings) and pruning unnecessary content (outdated decisions, conversational fluff, redundant info). The agent autonomously selects which memories are relevant to the current task via `index.md`, so the developer never has to manually curate context. This system is intentionally independent of Claude Code's built-in `.claude/` directory so it can be ported to other AI coding agents in the future.

---

*Next step: Run `/research` to investigate the landscape, or `/clarify` again to go deeper.*
