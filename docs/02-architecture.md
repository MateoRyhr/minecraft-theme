# Architecture — System Design

> This file defines the system architecture for the VSC Minecraft Theme
> extension: VS Code Extension API integration, theme loading, audio
> playback pipeline, and file icon theme.

---

## 1. System Overview

```
┌──────────────────────────────────────────────────────────────────┐
│                      VS Code Extension Host                      │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐     │
│  │                    minecraft-theme/                      │     │
│  │                                                         │     │
│  │  extension.js  (activation, hooks, sound engine)        │     │
│  │       │                                                  │     │
│  │       ├──► contributes.themes      → VS Code Theme API   │     │
│  │       ├──► contributes.iconThemes  → VS Code Icon API    │     │
│  │       ├──► contributes.configuration → VS Code Settings  │     │
│  │       ├──► contributes.commands    → Command Palette      │     │
│  │       ├──► contributes.keybindings → Keyboard Shortcuts   │     │
│  │       ├──► contributes.markdown.previewStyles → CSS       │     │
│  │       └──► contributes.walkthroughs → Welcome Guide       │     │
│  │                                                         │     │
│  │  Audio Engine:                                           │     │
│  │       └──► child_process.exec → mpg123 / afplay / PS    │     │
│  │                                                         │     │
│  │  Assets:                                                │     │
│  │       ├── themes/*.json        (color theme definitions) │     │
│  │       ├── audio/*.mp3          (Minecraft sound effects)  │     │
│  │       ├── icons/*.svg          (file icon SVGs)          │     │
│  │       ├── styles/*.css         (markdown preview CSS)     │     │
│  │       └── images/*.png         (walkthrough/media)        │     │
│  └─────────────────────────────────────────────────────────┘     │
│                                                                  │
│  VS Code API Surface:                                            │
│  └── vscode.workspace.getConfiguration('minecraft-theme')        │
│  └── vscode.workspace.onDidSaveTextDocument                      │
│  └── vscode.workspace.onDidCreateFiles / onDidDeleteFiles       │
│  └── vscode.window.onDidChangeActiveTextEditor                   │
│  └── vscode.window.onDidOpenTerminal                             │
│  └── vscode.window.createTerminal                                │
│  └── vscode.commands.registerCommand                             │
│  └── vscode.Disposable (subscription management)                 │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. Extension Activation

### Activation Flow

```
VS Code launches
        ↓
Extension host starts
        ↓
Activation event fires (onStartupFinished)
        ↓
extension.js activate(context) called
        ↓
1. Check platform audio dependency (mpg123/afplay/PowerShell)
2. Register configuration change listeners
3. Register all commands (enableSounds, disableSounds, togglePanel)
4. Register event listeners (save, create, delete, tab switch, terminal)
5. Play startup sound (if enabled)
        ↓
Extension is active
```

### Lifecycle

| Phase | What happens |
|-------|-------------|
| `activate(context)` | Sets up all listeners, checks dependencies, plays startup sound |
| Runtime | Listens for VS Code events, plays sounds in response to configured events |
| `deactivate()` | Stops any playing audio processes, cleans up subscriptions |

### VS Code Extension API Entry Points

| API | Purpose |
|-----|---------|
| `vscode.workspace.getConfiguration('minecraft-theme')` | Read user settings for sound toggles |
| `vscode.workspace.onDidChangeConfiguration` | React to settings changes at runtime |
| `vscode.workspace.onDidSaveTextDocument` | Trigger save sound |
| `vscode.workspace.onDidCreateFiles` | Trigger file creation sound |
| `vscode.workspace.onDidDeleteFiles` | Trigger file deletion sound |
| `vscode.window.onDidChangeActiveTextEditor` | Trigger tab switch sound |
| `vscode.window.onDidOpenTerminal` | Trigger terminal open sound |
| `vscode.window.onDidCloseTerminal` | Trigger terminal close sound |
| `vscode.commands.registerCommand` | Expose enable/disable/toggle commands |
| `vscode.Disposable.push` | Manage extension lifecycle |

---

## 3. Audio Engine Architecture

### Component Model

```
User Action (save file, open terminal, etc.)
        ↓
Event Listener (e.g., onDidSaveTextDocument)
        ↓
Configuration Check (is sound enabled? is this specific event enabled?)
        ↓
Sound Map Lookup (find file, volume, priority for event)
        ↓
Platform Detection (Linux? macOS? Windows?)
        ↓
Player Execution
  ├── Linux:    exec('mpg123 -f <volume> <file>')
  ├── macOS:    exec('afplay -v <volume> <file>')
  └── Windows:  exec('powershell -c (New-Object Media.SoundPlayer "<file>").PlaySync()')
        ↓
Process Tracking (store child process for potential kill)
```

### Sound Priority System

| Priority | Behavior | Used For |
|----------|----------|----------|
| 0 | Can be interrupted by any other sound | Tab switch, click |
| 1 | Waits for no other priority 0/1 to finish before playing | File save, file open/close |
| 2 | Never interrupted, queues if another priority 2 is playing | Startup, terminal error |

### Configuration Schema

```json
{
  "minecraft-theme.enableSounds": { "type": "boolean", "default": true },
  "minecraft-theme.enableStartupSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableFileSaveSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableFileCreatedSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableFileDeletedSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableFileOpenSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableFileClosedSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableTerminalOpenSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableTerminalErrorSound": { "type": "boolean", "default": true },
  "minecraft-theme.enableTabSwitchSound": { "type": "boolean", "default": true }
}
```

### Audio File Requirements

- Format: MP3 (mono or stereo)
- Sample rate: 44100 Hz recommended
- Duration: 0.5-5 seconds for UI events, up to 30 seconds for startup
- Volume normalization: all files should have similar perceived loudness
- File naming: snake_case descriptive names

---

## 4. Color Theme Architecture

### Theme File Format

Each theme is a standalone JSON file in `minecraft-theme/themes/`. VS Code loads them
based on `package.json` `contributes.themes` entries.

### Theme Registry (package.json)

```json
"contributes": {
    "themes": [
        { "label": "Minecraft", "uiTheme": "vs-dark", "path": "./themes/Minecraft-color-theme.json" },
        { "label": "Minecraft: Plains", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Plains-color-theme.json" },
        { "label": "Minecraft: Desert", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Desert-color-theme.json" },
        { "label": "Minecraft: Nether", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Nether-color-theme.json" },
        { "label": "Minecraft: The End", "uiTheme": "vs-dark", "path": "./themes/Minecraft-End-color-theme.json" },
        { "label": "Minecraft: Villager", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Villager-color-theme.json" },
        { "label": "Minecraft: Piglin", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Piglin-color-theme.json" },
        { "label": "Minecraft: Enderman", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Enderman-color-theme.json" },
        { "label": "Minecraft: Steve", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Steve-color-theme.json" },
        { "label": "Minecraft: Creeper", "uiTheme": "vs-dark", "path": "./themes/Minecraft-Creeper-color-theme.json" }
    ]
}
```

### Theme Color Palette Reference

| Theme | Primary Background | Accent Color | Key Inspiration |
|-------|-------------------|--------------|-----------------|
| Minecraft (General) | `#1A1A2E` deep dark | `#4B7B3E` grass green | General Minecraft aesthetic |
| Plains | `#2E3B1E` dark green | `#8B9D6B` sage | Grasslands, flowers |
| Desert | `#3D3024` dark brown | `#C4A67D` sand | Sand, terracotta |
| Nether | `#2E1111` blood red | `#E85D3A` lava orange | Netherrack, lava |
| The End | `#1A1A2E` void black | `#B080FF` ender purple | End stone, chorus |
| Villager | `#2E241E` dark wood | `#4B7B3E` emerald green | Villager trades |
| Piglin | `#2E1111` crimson | `#FFD700` golden | Gold, crimson forest |
| Enderman | `#0A0A14` pure dark | `#4AE0E0` ender pearl cyan | Enderman eyes |
| Steve | `#1E2B3A` blue-gray | `#4B9BE8` cyan | Steve's shirt |
| Creeper | `#1A2E1A` dark green | `#4AE04A` creeper lime | Creeper face |

---

## 5. Icon Theme Architecture

### File Icon Theme Structure

```
minecraft-icon-theme.json
├── iconDefinitions       ← Maps icon names to SVG file paths
│   ├── _js               → ./icons/minecraft-js-icon.svg
│   ├── _ts               → ./icons/minecraft-ts-icon.svg
│   ├── _css              → ./icons/minecraft-css-icon.svg
│   ├── _html             → ./icons/minecraft-html-icon.svg
│   ├── _json             → ./icons/minecraft-json-icon.svg
│   ├── _md               → ./icons/minecraft-md-icon.svg
│   ├── _folder           → ./icons/minecraft-folder.svg
│   └── _folder_open      → ./icons/minecraft-folder-open.svg
├── fileExtensions        ← Maps file extensions to icon names
│   ├── "js"  → "_js"
│   ├── "ts"  → "_ts"
│   └── ...
├── fileNames             ← Maps exact file names to icon names
│   ├── "README.md" → "_md"
│   └── ...
└── folderNames           ← Maps folder names to icons
    ├── "src"  → "_folder"
    └── ...
```

### SVG Icon Guidelines

- Minecraft pixel-art style (16x16 or 32x32 base resolution)
- Use Minecraft block/item imagery: grass block for source, book for markdown, etc.
- SVG should be minimal in path data
- Use solid fills, avoid gradients (pixel-art aesthetic)
- Color palette must work on both light and dark themes

---

## 6. Markdown Preview Styles Architecture

### CSS Loading

VS Code loads the CSS file specified in `contributes.markdown.previewStyles`
whenever the markdown preview is rendered. The CSS is scoped to the preview iframe.

### Theming Approach

| Element | Minecraft Theme |
|---------|----------------|
| Body background | Stone/wood texture feel |
| Headings | Bold, Minecraft-themed colors |
| Blockquotes | Achievement-style popup look |
| Code blocks | Command block style background |
| Links | Minecraft green |
| Lists | Item markers like crafting recipes |
| Tables | Like crafting table grid |
| Horizontal rules | Like a stone pickaxe divider |

---

## 7. Walkthrough Architecture

### Walkthrough Registration

```json
"walkthroughs": [
    {
        "id": "minecraft-walkthrough",
        "title": "Welcome to Minecraft Theme",
        "description": "Equip your tools and customize your coding experience...",
        "steps": [
            { "id": "select-theme", "title": "Equip Your Armor", ... },
            { "id": "select-icons", "title": "Craft Your Tools", ... },
            { "id": "enable-sounds", "title": "Tune Your Ear", ... }
        ]
    }
]
```

Walkthroughs appear in VS Code's Welcome page. Each step can include:
- A `title` and `description` with markdown
- A `media` object (image or SVG)
- Command links: `[Label](command:commandId)` in the description

---

## 8. Packaging & Distribution

### vsce Packaging

```
npm install -g @vscode/vsce
vsce package    → produces minecraft-theme-<version>.vsix
```

### .vscodeignore

```gitignore
.gitignore
.vscode/
node_modules/
.git/
*.map
src/
```

### Marketplace Metadata

- **Publisher:** Configured in `package.json` `publisher` field
- **Extension ID:** `publisher.minecraft-theme`
- **Categories:** `Themes`, `Other` (if includes sounds)
- **Tags:** `minecraft`, `theme`, `dark theme`, `sound`, `icon theme`
- **Gallery banner color:** Match the base theme's primary background
- **Icon:** 128x128 PNG recognizable as Minecraft-themed
