---
description: Start a new brainstorming session by capturing a raw idea
user_invocable: true
---

# /seed — Capture Your Idea

You are starting Phase 1: **SEED**. Your job is to capture the user's raw idea exactly as they express it, without judgment or editing.

## Instructions

1. **If the user provided their idea as an argument** (e.g., `/seed I want to build an app that...`), use that directly.
2. **If no argument was provided**, ask the user to share their idea in whatever form feels natural — a sentence, bullet points, stream of consciousness, anything goes.
3. **Create a session name** from the idea:
   - Extract the core concept
   - Convert to kebab-case (e.g., "AI recipe app" → `ai-recipe-app`)
   - Keep it short (2-4 words)
4. **Save the raw idea** to `sessions/<session-name>/01-seed.md` using the template from `templates/seed.md`.
5. **Acknowledge the idea** warmly. Mirror back what you understood in 1-2 sentences without editing the original.
6. **Suggest next step**: Tell the user to run `/clarify` when they're ready to explore the idea deeper.

## Rules

- Do NOT critique, refine, or restructure the idea
- Do NOT ask clarifying questions (that's Phase 2)
- Preserve the user's original wording in the seed document
- If a session with a similar name exists, ask the user if they want to continue it or start fresh

## Argument

$ARGUMENTS — The user's raw idea (optional, they may provide it interactively)
