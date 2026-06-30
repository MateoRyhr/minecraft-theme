# Project — AI Agent Navigation Map

> This file is the **entry point** for any agent working in this
> repository. It is NOT a rule book: it is a **map**. Read only what
> you need when you need it (progressive disclosure).

---

## 1. Before Starting (mandatory)

1. Read `feature_list.json` and verify: maximum 1 feature in `in_progress`,
   all statuses are valid (`pending`, `in_progress`, `done`, `blocked`).
2. Read `progress/current.md` to understand the state left from the last session.
3. Read `feature_list.json` and pick **one** task with status `pending`. Do
   not work on more than one at a time.

---

## 2. Project Summary

> **Project name:** VSC Minecraft Theme
> **Description:** A VS Code extension with multiple color themes inspired by Minecraft. Includes ambient Minecraft sound effects (block breaking, item pickup, mob sounds), biome/faction/character-themed variants, Minecraft-styled file icons, and interactive walkthroughs. Strongly inspired by the Half-Life Dark Theme predecessor.
> **Technology stack:** VS Code Extension API (v1.90+), Node.js, TypeScript, JSON theme definitions, audio asset pipeline
> **Target platform/environment:** Visual Studio Code (desktop), cross-platform (Windows/macOS/Linux)
> **Audio playback:** `mpg123` (Linux), `afplay` (macOS), built-in Windows audio (PowerShell)

---

## 3. Repository Map

| File / folder              | What it contains                                              | When to read it |
|----------------------------|---------------------------------------------------------------|-----------------|
| `feature_list.json`        | Task list with status (pending/in_progress/done)              | Always, at start |
| `progress/current.md`      | Current session state                                         | Always, at start |
| `progress/history.md`      | Append-only log of previous sessions                          | If you need historical context |
| `docs/01-conventions.md`   | Coding style rules for VS Code extension, TS, JSON themes     | Before writing code |
| `docs/02-architecture.md`  | System architecture: extension activation, theme loading, audio | Before implementing |
| `docs/verification.md`     | How to verify your work works                                 | Before declaring a task `done` |
| `CHECKPOINTS.md`           | Objective criteria for "correct final state"                  | For self-evaluation |
| `.opencode/agents/`        | Subagent definitions (leader, implementer, reviewer, explorer) | If orchestrating work |
| `minecraft-theme/`         | VS Code extension source (extension.js, themes/, audio/, icons/, styles/) | To implement extension features |
| `minecraft-theme/audio/`   | Minecraft sound effect assets (.mp3)                          | For audio feature implementation |
| `minecraft-theme/themes/`  | VS Code color theme JSON definitions                          | For theme creation |
| `minecraft-theme/icons/`   | File icon SVG assets for Minecraft icon theme                 | For icon theme creation |
| `minecraft-theme/styles/`  | Custom CSS for markdown preview styling                       | For markdown styling |

---

## 4. Hard Rules (non-negotiable)

- **One feature at a time.** Do not mix changes from multiple tasks in the same session.
- **Do not declare a task `done` without review.** The reviewer must approve and you
  must verify the work functions.
- **Document what you do** in `progress/current.md` as you work, not at the end.
- **Leave the repository clean** before closing the session (see §6).
- **If you don't know something, look in `docs/`** before inventing it.
- **Audio assets:** All `.mp3` files must be pre-loaded into `minecraft-theme/audio/`. Do not generate audio.
- **Theme colors:** Must use VS Code Theme API color references (`editor.background`, `sideBar.background`, etc.) — no custom CSS color overrides for the editor chrome.
- **Keep functions focused.** If a file exceeds the line limit defined in your conventions, split it.
- **Never commit secrets.** Use `.env` for any credentials and keep `.env` out of version control.

---

## 5. How to Pick a Task

```
1. Open feature_list.json
2. Filter by status == "pending"
3. Pick the one with the lowest "id"
4. Change its status to "in_progress" and save
5. Note in progress/current.md: feature, start time, brief plan
```

---

## 6. Session Close (lifecycle)

Before finishing:

1. If the task is done: mark `status: "done"` in `feature_list.json`.
2. Move the summary from `progress/current.md` to the end of `progress/history.md`.
3. Empty `progress/current.md` leaving only the template.
4. Do not leave temporary files, debug print statements, or TODOs without context.
5. Verify that `minecraft-theme/` builds/packages correctly with `vsce package` (if feature impacts packaging).

---

## 7. If You Get Blocked

- Re-read the relevant section of `docs/`.
- If you cannot proceed, **do not invent a workaround**:
  document the blockage in `progress/current.md` and stop the session.

---

## 8. Available Subagents

The leader (main agent) can launch the following subagents:

| Subagent | Role | Tools |
|----------|------|-------|
| `implementer` | Writes code in `minecraft-theme/`, creates/modifies extension files, implements features | read, write, edit, glob, grep, bash, task |
| `reviewer` | Validates code against conventions and checkpoints | read, glob, grep, bash |
| `explorer` | Researches VS Code Extension API, theme color references, audio libraries | read, glob, grep, bash, websearch, webfetch |

### Effort Scaling

| Complexity | Subagents |
|------------|-----------|
| Trivial (1 theme JSON file) | 1 implementer |
| Medium (2-3 files: theme + extension.js + CSS) | 1 implementer + 1 reviewer |
| Complex (new system, research needed: audio pipeline, icon theme) | 1 explorer → 1 implementer → 1 reviewer |
| Very complex (multiple themes + icons + sounds + walkthrough) | Split into sub-tasks and reapply |

---

## 9. Quick Architecture

```
┌─────────────────────────────────────────────────────┐
│              VS Code Extension Host                 │
│                                                     │
│  minecraft-theme/                                   │
│  ├── extension.js    ← activation + sound engine    │
│  ├── themes/         ← color theme JSON files       │
│  ├── audio/          ← Minecraft sound .mp3 files   │
│  ├── icons/          ← file icon SVG assets         │
│  ├── styles/         ← custom markdown CSS          │
│  └── package.json    ← extension manifest           │
│                                                     │
│  Extension API:                                     │
│  └── vscode.workspace.getConfiguration()            │
│  └── vscode.window.createTreeView()                 │
│  └── vscode.commands.registerCommand()              │
│  └── vscode.themeColor (via contributes.themes)     │
└─────────────────────────────────────────────────────┘
```

See `docs/02-architecture.md` for full details.
