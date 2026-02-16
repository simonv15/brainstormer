# Brainstormer

Transform vague ideas into actionable project plans through a structured brainstorming workflow powered by Claude Code.

## How It Works

Brainstormer guides you through 5 phases using slash commands:

```
/seed  →  /clarify  →  /research  →  /challenge  →  /plan
```

| Command | Phase | What happens |
|---------|-------|-------------|
| `/seed` | Capture | Dump your raw idea — no judgment, no editing |
| `/clarify` | Understand | Targeted Q&A to extract what you really mean |
| `/research` | Investigate | Web research on competitors, market, and tech |
| `/challenge` | Stress-test | Devil's advocate — risks, assumptions, blind spots |
| `/plan` | Build | Actionable project plan with milestones and tasks |
| `/review` | Iterate | Revisit and refine any phase's output |

Phases 2–4 can loop — go back to clarify after research, or research more after a challenge reveals gaps.

## Getting Started

1. Open this project in Claude Code
2. Run `/seed` and describe your idea
3. Follow the workflow through each phase
4. Find your outputs in `sessions/<your-idea>/`

For a detailed walkthrough of every command with examples and tips, see the **[Usage Guide](GUIDE.md)**.

## Project Structure

```
brainstormer/
├── CLAUDE.md              # Project instructions for Claude
├── README.md              # This file
├── .claude/skills/        # Slash command definitions
├── sessions/              # Your brainstorming sessions
└── templates/             # Output format templates
```

Each session produces 5 documents:

- `01-seed.md` — Your raw idea
- `02-clarify.md` — Clarified intent and scope
- `03-research.md` — Market and technical research
- `04-challenge.md` — Risk assessment and stress-test
- `05-plan.md` — Complete project plan
