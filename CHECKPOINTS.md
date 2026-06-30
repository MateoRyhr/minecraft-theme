# CHECKPOINTS — Final State Evaluation

> In multi-agent systems, the path is not evaluated — the destination is.
> These are the objective checkpoints that a judge (human or AI) can use
> to decide if the project is healthy.

## C1 — The harness is complete

- [ ] Base files exist: `AGENTS.md`, `feature_list.json`,
      `progress/current.md`, `CHECKPOINTS.md`, `docs/verification.md`.
- [ ] Subagents are defined in `.opencode/agents/`.
- [ ] `feature_list.json` has coherent state (max 1 `in_progress`,
      valid statuses).
- [ ] `minecraft-theme/` directory exists with extension structure.

## C2 — The state is coherent

- [ ] At most one feature in `in_progress` in `feature_list.json`.
- [ ] Every `done` feature has code that follows `docs/01-conventions.md`
      (theme JSON structure, extension.js patterns, audio engine conventions).
- [ ] `progress/current.md` is empty or describes the active session
      (does not contain garbage from previous sessions).

## C3 — The code respects the architecture

- [ ] Theme JSON files follow the structure in `docs/02-architecture.md`
      (required color keys, tokenColors, semanticHighlighting).
- [ ] `extension.js` follows activation pattern with proper subscriptions.
- [ ] Audio engine uses platform-specific players (mpg123/afplay/PowerShell).
- [ ] No `console.log` statements in production code.
- [ ] No orphaned TODOs without context.
- [ ] No Half-Life references remain (HEV, hl2, lambda, combine, etc.).
- [ ] Files do not exceed line limits (300 lines extension.js, 200 lines JSON).

## C4 — The verification is real

- [ ] `vsce package` produces a valid `.vsix` file without errors.
- [ ] All theme JSON files pass `JSON.parse()` validation.
- [ ] All SVG icon files are valid XML.
- [ ] CSS files have no syntax errors.
- [ ] The reviewer has approved the current feature.
- [ ] The work has been verified (theme loads in VS Code, sounds play, or human confirms).

## C5 — The session was closed properly

- [ ] No suspicious untracked files (temporary files, build artifacts, `.env`).
- [ ] `progress/history.md` has an entry for the last session.
- [ ] The last feature worked on is reflected with its correct status in
      `feature_list.json`.
- [ ] Cleanup is complete (no leftover debug state, no Half-Life remnants).

---

**How to use this file:** a reviewer agent (`.opencode/agents/reviewer.md`)
goes through each checkbox, marks `[x]` or `[ ]`, and rejects session closure
if any boxes remain empty in C1-C5.
