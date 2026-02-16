# Brainstormer — Usage Guide

This guide walks you through a complete brainstorming session, from raw idea to actionable project plan.

## Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- This project opened as your working directory in Claude Code

## Quick Start

```
/seed I want to build an app that helps people find hiking trails based on their fitness level
```

That's it — Claude will capture your idea and guide you through the rest.

## Phase-by-Phase Walkthrough

### Phase 1: Seed — `/seed`

**What it does:** Captures your raw idea exactly as you say it.

**How to use it:**

```
# With your idea inline
/seed I want to build a marketplace for freelance designers

# Or just run it and type your idea when prompted
/seed
```

**Tips:**
- Don't worry about structure or clarity — just dump your thoughts
- Stream of consciousness is fine: "something like Uber but for dog walkers, maybe also cat sitters, idk"
- You can include bullet points, questions, half-formed thoughts — anything goes

**Output:** `sessions/<your-idea>/01-seed.md`

---

### Phase 2: Clarify — `/clarify`

**What it does:** Claude becomes an interviewer, asking targeted questions to understand what you really mean.

**How to use it:**

```
# General clarification
/clarify

# Focus on a specific dimension
/clarify technical constraints
/clarify audience
```

**What to expect:**
- Claude mirrors back what it understood (so you can correct misunderstandings)
- 3–5 questions per round covering: problem, audience, motivation, vision, constraints, scope
- Multiple rounds are normal — keep going until the idea feels crisp

**Tips:**
- It's OK to say "I don't know yet" — that's useful information too
- If a question doesn't feel relevant, say so
- Short answers are fine; Claude will follow up if it needs more

**Output:** `sessions/<your-idea>/02-clarify.md`

---

### Phase 3: Research — `/research`

**What it does:** Claude searches the web to investigate competitors, market trends, and technical options.

**How to use it:**

```
# Full research sweep
/research

# Focus on a specific area
/research competitors
/research tech stack options
```

**What to expect:**
- A report covering: existing solutions, market opportunity, tech feasibility, similar projects
- Tables comparing competitors with strengths/weaknesses
- Links to sources
- Concrete tech stack recommendations

**Tips:**
- If research reveals something surprising, consider running `/clarify` again to adjust your idea
- Ask follow-up questions if you want to dig deeper into a specific finding

**Output:** `sessions/<your-idea>/03-research.md`

---

### Phase 4: Challenge — `/challenge`

**What it does:** Claude plays devil's advocate, stress-testing your idea to find weaknesses before you invest time building.

**How to use it:**

```
# Full challenge
/challenge

# Focus on a specific concern
/challenge technical feasibility
/challenge market fit
```

**What to expect:**
- Honest assessment of assumptions (validated vs. risky)
- Risk tables with severity, likelihood, and mitigations
- Blind spots you may have missed
- Alternative approaches if critical issues are found

**Tips:**
- Don't take it personally — the goal is to make the idea stronger
- If a risk feels serious, loop back: run `/clarify` or `/research` to address it
- The challenge phase often reveals the most valuable insights

**After this phase, you decide:**
- Ready? Run `/plan`
- Need more info? Run `/research` or `/clarify`
- Want to pivot? Run `/seed` with a revised idea

**Output:** `sessions/<your-idea>/04-challenge.md`

---

### Phase 5: Plan — `/plan`

**What it does:** Synthesizes everything into a concrete, actionable project plan.

**How to use it:**

```
# Full project plan
/plan

# Focus on a specific section
/plan just the MVP
/plan technical architecture only
```

**What to expect:**
- Problem statement and value proposition
- Target audience with personas
- Prioritized feature list (must-have / should-have / nice-to-have)
- MVP scope — the smallest thing that delivers value
- Tech stack recommendations with justifications
- Milestones with task breakdowns
- Risk mitigations from the challenge phase

**Output:** `sessions/<your-idea>/05-plan.md`

---

### Bonus: Review — `/review`

**What it does:** Revisit any phase's output and make changes.

**How to use it:**

```
# Review a specific phase
/review seed
/review clarify
/review research
/review 04

# See all phases and pick one
/review
```

**Tips:**
- If you change something in an early phase (like audience), Claude will suggest re-running later phases
- Use this to iterate without starting over

---

## Looping Between Phases

The workflow isn't strictly linear. Phases 2–4 are designed to loop:

```
/clarify → /research → /challenge → not satisfied? → /clarify again
```

Common loop patterns:

| Situation | What to do |
|-----------|-----------|
| Research revealed a competitor you didn't know about | `/clarify` to refine your differentiator |
| Challenge found a major technical risk | `/research` to find alternative approaches |
| Challenge showed the audience is too broad | `/clarify` to narrow the target |
| Plan feels too ambitious | `/challenge` to identify what to cut |

## Managing Sessions

Each idea gets its own folder under `sessions/`:

```
sessions/
├── hiking-trail-app/
│   ├── 01-seed.md
│   ├── 02-clarify.md
│   └── ...
└── freelance-marketplace/
    ├── 01-seed.md
    └── ...
```

- If you have one session, commands auto-detect it
- If you have multiple, Claude will ask which one to use
- Start a new idea anytime with `/seed`

## Example Session

Here's what a typical session looks like:

```
You:    /seed I want to build a tool that helps remote teams do better async standups

Claude: [Saves idea, mirrors it back, suggests /clarify]

You:    /clarify

Claude: [Asks 3-5 questions about the problem, audience, etc.]

You:    [Answers questions]

Claude: [Saves clarified summary, suggests /research]

You:    /research

Claude: [Searches web, produces competitor analysis and tech options]

You:    /challenge

Claude: [Identifies 5 risks with mitigations, suggests proceeding]

You:    /plan

Claude: [Produces full project plan with MVP, milestones, and tasks]
```

Total time: ~15-30 minutes for a complete idea-to-plan workflow.
