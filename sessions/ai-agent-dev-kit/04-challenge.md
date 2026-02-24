# Challenge: Praxis-kit (Round 3 — Plan & Distribution)

**Session:** ai-agent-dev-kit
**Date:** 2026-02-23

---

## Strengths Acknowledged

This is the strongest the idea has been. The plan is detailed, the architecture is research-backed, and the distribution strategy is smart. What's working well:

- **The plan is implementation-ready.** Five milestones with concrete tasks, dependency order, and testing criteria. You could start building tomorrow.
- **The multi-channel distribution is the right call.** `npx praxis-kit` for complete setup, Vercel Skills for discovery, Claude Code Plugin for depth. Each channel serves a distinct purpose — no redundancy.
- **"Zero runtime dependencies" for the CLI is smart.** Node.js `fs` + `path` only means no supply chain risk, no version conflicts, and fast execution. This is a real competitive advantage over heavier installers.
- **The `npx praxis-kit` merging with `/kickoff` concept is elegant.** One command from zero to ready. Fewest post-install steps of any competitor in the space.
- **The repo structure serves all channels from the same SKILL.md files.** No duplication, no drift between channels.

Previously flagged issues that are now **resolved:**
- ~~index.md is a single point of failure~~ → Rebuildable cache with directory-scan fallback
- ~~SKILL.md instruction overload~~ → Extracted `_memory-protocol.md`
- ~~YAML frontmatter inconsistency~~ → In-body metadata markers
- ~~No distribution strategy~~ → Multi-channel with clear priorities and timeline
- ~~Decision numbering conflicts~~ → Timestamp-based naming

---

## Assumptions Tested

| # | Assumption | Status | Evidence |
|---|-----------|--------|----------|
| 1 | `npx praxis-kit` is the right primary distribution channel | **Validated** | cc-sdd uses the exact same pattern (`npx cc-sdd@latest`) and supports 7 agents. claude-code-spec-workflow uses `npm i -g`. The npm-based install is proven in this space. |
| 2 | Vercel Skills will provide meaningful discovery | **Likely but unproven** | skills.sh had 26,000+ installs in its first weeks. But those installs skew toward simple, single-purpose skills (linters, formatters). A complex 6-skill kit with a memory system may not fit the "browse and install" discovery pattern as naturally. |
| 3 | Users will install project-locally (not globally) | **Reasonable but untested** | The plan defaults to project-local. But developers who use Praxis-kit across multiple projects will re-run `npx praxis-kit` for every new project. That's friction. cc-sdd supports both — worth considering. |
| 4 | The CLI can detect which agents are installed | **Feasible** | `npx add-skill` and `npx skills` both do agent detection. The logic is simple: check if `.claude/`, `.cursor/`, etc. directories exist or if the agent binary is in PATH. |
| 5 | `npx praxis-kit` can replace the first-time `/kickoff` experience | **Risky** | The CLI runs as a Node.js script *outside* Claude Code. It can create files and directories, but it can't interact with the agent. `/kickoff` runs *inside* Claude Code and can ask the user questions, adapt to the project context, and explain what it's creating. Merging them loses the interactive, agent-driven onboarding. |
| 6 | Same SKILL.md files can serve both Vercel Skills (`skills/`) and Claude Code native (`.claude/skills/`) | **Needs verification** | Vercel Skills expects skills in `skills/<name>/SKILL.md`. Claude Code expects `.claude/skills/<name>/SKILL.md`. The YAML frontmatter format may differ slightly between ecosystems. Also: Vercel uses kebab-case (`plan-docs`), Claude Code plan uses underscore (`plan_docs`). This naming mismatch needs resolution. |
| 7 | The memory protocol still works when `_memory-protocol.md` lives in `.kit/` but skills are in `.claude/skills/` | **Unvalidated** | When a skill runs, it needs to read `_memory-protocol.md`. If the protocol is in `.kit/` and the skill is in `.claude/skills/`, the skill must know the relative path to `.kit/`. This works for project-local installs but may break for global skill installs where `.kit/` doesn't exist yet. |

---

## Risk Assessment

### Technical Risks

| Risk | Severity | Likelihood | Mitigation |
|------|----------|------------|------------|
| **`npx praxis-kit` and `/kickoff` overlap creates confusion** — Two ways to initialize the project. Users who install via Vercel Skills still need `/kickoff`. Users who use `npx praxis-kit` get initialization done but still see `/kickoff` as an available command. What happens if they run `/kickoff` after `npx praxis-kit`? | Medium | High | Define clear roles: `npx praxis-kit` = install skills + create `.kit/` structure + write CLAUDE.md. `/kickoff` = re-initialize or update an existing setup. Make `/kickoff` idempotent — if `.kit/` exists, it verifies/repairs rather than overwrites. Document this clearly. |
| **Skill naming mismatch across channels** — Vercel Skills convention uses kebab-case (`plan-docs`, `gen-test`). Claude Code native skills use underscore (`plan_docs`, `gen_test`). The slash command name is derived from the directory name. If you publish to Vercel as `plan-docs`, the command becomes `/plan-docs`, not `/plan_docs`. | Medium | High | Pick one convention and stick with it. Recommendation: use kebab-case everywhere (`plan-docs`, `gen-test`) since that's the Vercel standard and works in Claude Code too. Update the plan to use kebab-case consistently. The user's original `/plan_docs` and `/gen_test` need to become `/plan-docs` and `/gen-test`. |
| **`_memory-protocol.md` location ambiguity** — The plan shows it in `.kit/` (Kit File Structure) but also references it being copied to `.claude/skills/` (Milestone 5 CLI tasks). Where does it actually live? If in `.kit/`, skills need to know the path. If in `.claude/skills/`, it's an installation artifact, not project memory. | Medium | Medium | Put `_memory-protocol.md` in `.claude/skills/` (alongside the SKILL.md files). It's an instruction file, not a memory file. The CLI copies it there during install. `.kit/` should only contain project-specific memory data, not system instructions. The templates directory (`_templates/`) is correctly in `.kit/` since it's used by the memory system. |
| **Vercel Skills install doesn't set up `.kit/`** — Users who discover Praxis-kit via skills.sh and run `npx skills add` only get SKILL.md files. No `.kit/`, no `_memory-protocol.md`, no CLAUDE.md updates. The first time they run any skill, it will fail or behave unpredictably. | Medium | High | Make every skill's first instruction: "If `.kit/` directory doesn't exist, guide the user to run `/kickoff` (or `npx praxis-kit`)." This is a graceful fallback, not a silent failure. Alternatively, have `/kickoff` be smart enough to detect and create everything that's missing. |
| **CLAUDE.md write conflicts** — `npx praxis-kit` appends to CLAUDE.md. `/kickoff` also writes to CLAUDE.md. `/profile` writes to CLAUDE.md. If the user has their own CLAUDE.md content, these writes could create duplicates or corrupt the file. | Medium | Medium | Each write operation should: (1) Read the current CLAUDE.md, (2) Check if the section already exists (e.g., look for `# About Me` or `## Spec-Driven Workflow`), (3) Update in place if found, append if not. The CLI and skills both need this idempotent behavior. |
| **npm package size and freshness** — The npm package includes all SKILL.md files. When skills are updated, users need to re-run `npx praxis-kit@latest`. But the installed files in `.claude/skills/` are copies — they don't auto-update. Stale skills could cause subtle issues. | Low | Medium | Include a version marker in installed files (e.g., comment at top of each SKILL.md: `<!-- praxis-kit v1.0.0 -->`). The CLI can check for version mismatches. For v1, document that users should re-run `npx praxis-kit@latest` after updates. Claude Code Plugin distribution solves this with auto-updates in the future. |

### Market Risks

| Risk | Severity | Likelihood | Mitigation |
|------|----------|------------|------------|
| **cc-sdd is already there** — cc-sdd uses the exact same `npx` pattern, supports 7 agents, and has an established npm presence. Praxis-kit's distribution strategy isn't differentiated — it's table stakes. | Medium | High | Distribution is not the differentiator — memory is. cc-sdd has workflow but no persistent memory system. Praxis-kit's value is the `.kit/` curated knowledge base, not the installer. The `npx` command is just the delivery vehicle. Focus marketing on what happens *after* install: "Your agent remembers." |
| **skills.sh discovery saturation** — With 26,000+ installs and a growing catalog, standing out on skills.sh will get harder. A 6-skill kit competes with single-purpose skills for attention. | Low | Medium | Create a strong README and description optimized for skills.sh discovery. Consider publishing individual skills *and* the full kit — users can install just `/explore` if they want, then upgrade to the full kit. This also tests individual skill value independently. |

### Execution Risks

| Risk | Severity | Likelihood | Mitigation |
|------|----------|------------|------------|
| **Building the CLI adds scope to Milestone 5** — The plan already has Milestone 5 loaded with dogfooding, stress testing, README, and publishing. Adding CLI development (detect agents, copy files, modify CLAUDE.md, handle flags, handle edge cases) is significant additional work. | Medium | High | The CLI is ~100-200 lines of Node.js. Don't over-engineer it. v1 CLI: detect Claude Code only (skip multi-agent detection), copy files, create `.kit/`, append to CLAUDE.md. No interactive prompts, no flags beyond `--help`. Add `--skip-kit` and agent detection in v1.1. |
| **Maintaining two directory structures** — `skills/` (Vercel) and `.claude/skills/` (Claude Code) both contain SKILL.md files. Even if they're copies or symlinks, keeping them in sync is a maintenance burden. A change to one must be reflected in the other. | Medium | Medium | Use a build script or symlinks. Recommendation: make `skills/` the source of truth, and have the CLI (or a `prepare` npm script) copy them to `.claude/skills/` at build time. Or simply: use symlinks in the repo and document that contributors should modify `skills/` only. |
| **Testing the distribution itself is a separate effort** — The plan says "dogfood on 3 project types" but doesn't distinguish between testing *skills* and testing *distribution*. Installing via `npx praxis-kit` on a fresh project is a different test than running `/explore` on a project where skills are already installed. | Low | Medium | Add explicit distribution testing to Milestone 5: (1) Fresh install via `npx praxis-kit` on empty project, (2) Fresh install via `npx skills add` on empty project, (3) Upgrade from v1.0.0 to v1.0.1, (4) Install on project that already has CLAUDE.md with custom content. |

---

## Blind Spots & Overlooked Factors

1. **The `/kickoff` vs `npx praxis-kit` overlap is a real design problem.** The plan currently has both doing overlapping work (create `.kit/`, write CLAUDE.md). This will confuse users. The cleanest resolution: `npx praxis-kit` installs skills only (files into `.claude/skills/`). `/kickoff` handles all project-specific initialization (`.kit/`, CLAUDE.md). This separates "install the tool" from "initialize my project." Every distribution channel (npm, Vercel Skills, manual copy) ends with "now run `/kickoff`." One consistent onboarding path regardless of how you installed.

2. **No upgrade story.** What happens when Praxis-kit v1.1 ships with improved SKILL.md files? Users who installed v1.0 have stale copies in `.claude/skills/`. The plan mentions re-running `npx praxis-kit@latest` but doesn't address: will it overwrite customizations? Will it break the existing `.kit/` state? Will it touch CLAUDE.md again? You need a `npx praxis-kit update` command (or at least an `--update` flag) that only replaces SKILL.md files and `_memory-protocol.md`, leaving `.kit/` and CLAUDE.md untouched.

3. **`.kit/` should probably be gitignored.** The plan says `.kit/` is "git-trackable" as a feature, but think about what gets committed: `agent-memory/lessons.md` contains agent-specific learnings, `task-memory/` contains in-progress work, `index.md` is a cache. These are ephemeral to the developer+agent pair, not project artifacts. If a team member clones the repo and gets someone else's agent memories, that's confusing. Recommendation: gitignore `.kit/` by default, with a documented option to track `project-memory/` (architecture, decisions) if the team wants shared project knowledge. The kit's *skills* (`.claude/skills/`) should be committed. The *memory* (`.kit/`) should be developer-local.

4. **What happens when the user runs skills outside the intended order?** The plan describes a linear flow (kickoff → explore → plan → test → implement), but real usage is nonlinear. What if a user runs `/implement` directly without `/plan_docs`? What if they run `/gen_test` without `/explore`? Each skill needs graceful degradation: "I don't see a plan for this task. Would you like me to create one first, or proceed without a plan?" This isn't in the plan's SKILL.md instructions.

5. **The Vercel Skills naming will cause a command naming issue.** If you publish to Vercel Skills as `plan-docs`, but the user also has the Claude Code native version as `plan_docs`, they'll have duplicate commands with different names. The `npx skills add` installer doesn't know to remove the native version. You can't serve both channels from the same repo without picking one naming convention.

---

## Alternative Approaches / Pivots

**If the `/kickoff` vs `npx praxis-kit` overlap proves confusing:**
- **Pivot A: CLI installs, `/kickoff` initializes.** `npx praxis-kit` only copies SKILL.md files + `_memory-protocol.md` to `.claude/skills/`. Period. No `.kit/`, no CLAUDE.md. The first command the user runs is `/kickoff`, which creates `.kit/`, writes CLAUDE.md, and walks them through setup interactively. This is the cleanest separation: *install the tool* vs. *set up the project*. Every distribution channel converges on the same `/kickoff` experience.

**If maintaining two directory structures is unsustainable:**
- **Pivot B: Single source in `skills/`.** Drop the `.claude/skills/` directory from the repo entirely. The CLI copies from `skills/` to `.claude/skills/` during install. Manual install instructions say "copy `skills/` to `.claude/skills/`." No duplication, no drift.

**If `.kit/` in git causes team confusion:**
- **Pivot C: Split project-memory from ephemeral memory.** `project-memory/` (architecture, decisions) goes in a committed location like `.praxis/` or even inside `.claude/`. `task-memory/`, `agent-memory/`, and `index.md` stay in a gitignored `.kit/`. This splits "team knowledge" from "agent workspace."

---

## Verdict

**The plan is solid and ready to build.** The core idea has been validated across three rounds. The distribution strategy is well-researched and multi-channel. The architecture addresses every risk from previous challenges.

**Three things to resolve before building:**

1. **Clarify the `/kickoff` vs `npx praxis-kit` boundary.** Strongest recommendation: CLI installs skills only, `/kickoff` initializes the project. This eliminates overlap and gives every distribution channel the same onboarding path. (See Pivot A above.)

2. **Pick one naming convention.** Use kebab-case (`plan-docs`, `gen-test`) everywhere — Vercel Skills, Claude Code, CLI. Update the plan to reflect this. It's a small change now, a painful migration later.

3. **Decide on `.kit/` git behavior.** Should it be committed or gitignored? This affects the "git-trackable" positioning. Recommendation: gitignore by default, document how to selectively track `project-memory/` for teams.

These are all solvable with quick decisions — none of them are blockers. The idea has been challenged three times and it keeps getting stronger. Time to build.

---

*Next steps:*
- *Ready to build? Start with the first steps in the plan.*
- *Want to address the open questions? Run `/clarify` to resolve the `/kickoff` vs `npx` boundary, naming, and git behavior.*
- *Want to update the plan? Run `/plan` to incorporate these findings.*
