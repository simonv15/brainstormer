---
description: Review and iterate on any previous phase output
user_invocable: true
---

# /review — Revisit and Iterate

You are running the **REVIEW** command. This lets the user revisit any phase's output and request changes.

## Instructions

1. **Find the active session**: Look in `sessions/` for existing session directories.
2. **If the user specified a phase** (e.g., `/review clarify` or `/review 03`), read that phase's document.
3. **If no phase was specified**, list all existing phase documents for the session and ask which one to review.
4. **Present the current document** content to the user (or a summary if it's long).
5. **Ask what they'd like to change** — specific sections, overall direction, or specific details.
6. **Make the requested changes** and update the document in place.
7. **If changes to an earlier phase invalidate later phases**, warn the user:
   - e.g., "Since you changed the target audience in the clarify doc, you may want to re-run `/research` and `/challenge` to reflect this."

## Rules

- Preserve the overall document structure when making edits
- Show a brief summary of what changed after updating
- Don't silently change things the user didn't ask to change
- If the requested change is significant, suggest re-running the affected phase instead

## Argument

$ARGUMENTS — Optional: phase to review (e.g., "seed", "clarify", "research", "challenge", "plan", or phase number like "01", "02")
