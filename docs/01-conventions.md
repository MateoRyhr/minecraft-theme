# Conventions — Coding Style Rules

> This file defines the coding conventions for the VSC Minecraft Theme
> extension: VS Code Extension API, JSON theme definitions, audio engine,
> and icon theme assets.

---

## 1. File Organization

### Extension Layout

```
minecraft-theme/
├── extension.js             # Extension activation + sound engine
├── package.json             # Extension manifest (marketplace, contributes)
├── CHANGELOG.md             # Release notes
├── LICENSE                  # MIT license
├── .vscodeignore            # Packaging exclusion rules
├── themes/                  # VS Code color theme JSON files
│   ├── Minecraft-color-theme.json
│   ├── Minecraft-Plains-color-theme.json
│   ├── Minecraft-Desert-color-theme.json
│   ├── Minecraft-Nether-color-theme.json
│   ├── Minecraft-End-color-theme.json
│   ├── Minecraft-Villager-color-theme.json
│   ├── Minecraft-Piglin-color-theme.json
│   ├── Minecraft-Enderman-color-theme.json
│   ├── Minecraft-Steve-color-theme.json
│   └── Minecraft-Creeper-color-theme.json
├── audio/                   # Minecraft sound effect .mp3 files
│   ├── cave_sound.mp3
│   ├── item_pickup.mp3
│   ├── block_placed.mp3
│   ├── block_break.mp3
│   ├── chest_open.mp3
│   ├── creeper_hiss.mp3
│   ├── click.mp3
│   ├── door_open.mp3
│   └── door_close.mp3
├── icons/                   # File icon SVG assets (pixel-art style)
│   ├── minecraft-js-icon.svg
│   ├── minecraft-ts-icon.svg
│   ├── minecraft-css-icon.svg
│   ├── minecraft-html-icon.svg
│   ├── minecraft-json-icon.svg
│   ├── minecraft-md-icon.svg
│   ├── minecraft-folder.svg
│   └── minecraft-folder-open.svg
├── styles/
│   └── minecraft-markdown.css
└── images/                  # Walkthrough and marketplace images
    ├── minecraft-logo.png
    └── walkthrough-*.png
```

---

## 2. Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Theme JSON files | PascalCase + `-color-theme.json` | `Minecraft-Nether-color-theme.json` |
| Audio files | snake_case + `.mp3` | `cave_sound.mp3` |
| Icon SVG files | `minecraft-` prefix + kebab-case | `minecraft-js-icon.svg` |
| CSS files | `minecraft-` prefix + kebab-case | `minecraft-markdown.css` |
| Image files | kebab-case | `walkthrough-select-theme.png` |
| Extension JS | `extension.js` (singular, VS Code convention) | `extension.js` |
| Configuration IDs | `minecraft-theme.*` prefix | `minecraft-theme.enableSounds` |
| Command IDs | `minecraftTheme.*` prefix (camelCase) | `minecraftTheme.enableAllSounds` |
| Walkthrough IDs | `minecraft-*` prefix | `minecraft-walkthrough` |

---

## 3. Theme JSON Conventions

### Color Theme Structure

Every theme file must follow this structure:

```jsonc
{
  "name": "Minecraft (Biome/Theme Name)",
  "type": "dark",  // or "light" if applicable
  "colors": {
    // Editor chrome colors
    "editor.background": "#1A1A2E",
    "editor.foreground": "#E0E0E0",
    "editorCursor.foreground": "#FFFFFF",
    "editor.selectionBackground": "#4B7B3E80",
    "editor.lineHighlightBackground": "#2D2D44",
    // Sidebar
    "sideBar.background": "#16162A",
    "sideBar.foreground": "#C0C0C0",
    "sideBarSectionHeader.background": "#1F1F38",
    // Activity bar
    "activityBar.background": "#0F0F1E",
    "activityBar.foreground": "#FFFFFF",
    "activityBar.activeBorder": "#4B7B3E",
    // Title bar
    "titleBar.activeBackground": "#0F0F1E",
    "titleBar.activeForeground": "#FFFFFF",
    // Status bar
    "statusBar.background": "#0F0F1E",
    "statusBar.foreground": "#FFFFFF",
    // Other required keys
    "tab.activeBackground": "#1A1A2E",
    "tab.inactiveBackground": "#12121E",
    "editorGroupHeader.tabsBackground": "#12121E",
    "input.background": "#0F0F1E",
    "input.foreground": "#E0E0E0",
    "dropdown.background": "#1A1A2E",
    "dropdown.foreground": "#E0E0E0",
    "button.background": "#4B7B3E",
    "button.foreground": "#FFFFFF",
    "badge.background": "#4B7B3E",
    "badge.foreground": "#FFFFFF",
    "list.activeSelectionBackground": "#2D2D44",
    "list.hoverBackground": "#1F1F38",
    "panel.background": "#16162A",
    "terminal.background": "#0F0F1E",
    "terminal.foreground": "#E0E0E0"
  },
  "tokenColors": [
    {
      "scope": ["comment", "punctuation.definition.comment"],
      "settings": { "foreground": "#6A6A8A", "fontStyle": "italic" }
    },
    {
      "scope": ["keyword", "storage.type", "storage.modifier"],
      "settings": { "foreground": "#C792EA" }
    },
    {
      "scope": ["string", "string.quoted", "string.regexp"],
      "settings": { "foreground": "#C3E88D" }
    },
    {
      "scope": ["constant.numeric", "constant.language"],
      "settings": { "foreground": "#F78C6C" }
    },
    {
      "scope": ["entity.name.function", "meta.function-call"],
      "settings": { "foreground": "#82AAFF" }
    },
    {
      "scope": ["entity.name.type", "entity.name.class"],
      "settings": { "foreground": "#FFCB6B" }
    },
    {
      "scope": ["variable", "variable.parameter"],
      "settings": { "foreground": "#E0E0E0" }
    },
    {
      "scope": ["support.type.property-name.css"],
      "settings": { "foreground": "#82AAFF" }
    },
    {
      "scope": ["markup.heading", "markup.heading entity.name"],
      "settings": { "foreground": "#82AAFF", "fontStyle": "bold" }
    }
  ],
  "semanticHighlighting": true
}
```

### Required Color Keys

Every theme must define at minimum these color keys:

| Category | Keys |
|----------|------|
| Editor | `editor.background`, `editor.foreground`, `editorCursor.foreground`, `editor.selectionBackground`, `editor.lineHighlightBackground`, `editorLineNumber.foreground` |
| Sidebar | `sideBar.background`, `sideBar.foreground`, `sideBarSectionHeader.background` |
| Activity Bar | `activityBar.background`, `activityBar.foreground`, `activityBar.activeBorder`, `activityBar.inactiveForeground` |
| Title Bar | `titleBar.activeBackground`, `titleBar.activeForeground`, `titleBar.inactiveBackground`, `titleBar.inactiveForeground` |
| Status Bar | `statusBar.background`, `statusBar.foreground`, `statusBarItem.activeBackground` |
| Tabs | `tab.activeBackground`, `tab.activeForeground`, `tab.inactiveBackground`, `tab.inactiveForeground`, `editorGroupHeader.tabsBackground` |
| Input | `input.background`, `input.foreground`, `input.placeholderForeground`, `input.border` |
| Dropdown | `dropdown.background`, `dropdown.foreground`, `dropdown.border` |
| Button | `button.background`, `button.foreground`, `button.hoverBackground` |
| Badge | `badge.background`, `badge.foreground` |
| List | `list.activeSelectionBackground`, `list.activeSelectionForeground`, `list.hoverBackground`, `list.hoverForeground` |
| Panel | `panel.background`, `panel.border` |
| Terminal | `terminal.background`, `terminal.foreground`, `terminal.ansi*` (16 colors) |

### Color Palette Guidelines

- Use Minecraft-inspired colors: grass greens, stone grays, sky blues, wood browns, nether reds, end purples
- Ensure sufficient contrast for readability (WCAG AA minimum: 4.5:1 for text)
- Test with both light and dark syntax highlighting
- Use 4-6 character hex values (#RGB or #RRGGBB)

---

## 4. Extension.js Conventions

### Activation Pattern

```javascript
const vscode = require('vscode');
const path = require('path');
const { exec, execSync } = require('child_process');

/**
 * @param {vscode.ExtensionContext} context
 */
function activate(context) {
    // Register configuration change listener
    context.subscriptions.push(
        vscode.workspace.onDidChangeConfiguration(e => {
            if (e.affectsConfiguration('minecraft-theme')) {
                // Reload settings
            }
        })
    );

    // Register commands
    context.subscriptions.push(
        vscode.commands.registerCommand('minecraftTheme.enableAllSounds', () => { /* ... */ })
    );
}

function deactivate() {
    // Cleanup: stop any playing sounds
}
```

### Sound Map Structure

```javascript
const sounds = {
    startup: { file: 'cave_sound.mp3', volume: 0.6, priority: 2 },
    saveFile: { file: 'item_pickup.mp3', volume: 0.15, priority: 1 },
    fileCreated: { file: 'block_placed.mp3', volume: 0.2, priority: 2 },
    fileDeleted: { file: 'block_break.mp3', volume: 0.2, priority: 2 },
    terminal: { file: 'chest_open.mp3', volume: 0.5, priority: 1 },
    terminalError: { file: 'creeper_hiss.mp3', volume: 0.6, priority: 2 },
    tabSwitch: { file: 'click.mp3', volume: 0.5, priority: 0 },
    fileOpen: { file: 'door_open.mp3', volume: 0.1, priority: 1 },
    fileClosed: { file: 'door_close.mp3', volume: 0.1, priority: 1 }
};
```

### Audio Playback

- **Linux:** Use `mpg123` (check with `command -v mpg123`)
- **macOS:** Use `afplay`
- **Windows:** Use PowerShell `(New-Object Media.SoundPlayer).PlaySync()`
- Check dependency at activation, show install instructions if missing
- Use `child_process.exec` with the appropriate player command
- Set volume via the player's volume flag (e.g., `mpg123 -f <volume>`)
- Track currently playing sound processes to prevent overlap

### File Event Hooks

- Use `vscode.workspace.onDidSaveTextDocument` for save events
- Use `vscame.workspace.onDidCreateFiles` for file creation
- Use `vscode.workspace.onDidDeleteFiles` for file deletion
- Use `vscode.window.onDidChangeActiveTextEditor` for tab switch events
- Use `vscode.window.onDidOpenTerminal` and `vscode.window.onDidCloseTerminal` for terminal
- Use `vscode.window.activeTerminal.sendText` detection for terminal errors

---

## 5. Package.json Conventions

### Configuration Properties Pattern

```json
"contributes": {
    "configuration": {
        "title": "Minecraft Theme",
        "properties": {
            "minecraft-theme.enableSounds": {
                "type": "boolean",
                "default": true,
                "description": "Master switch to enable/disable all Minecraft sounds."
            },
            "minecraft-theme.enableStartupSound": {
                "type": "boolean",
                "default": true,
                "description": "Play a cave sound when VS Code starts."
            }
        }
    }
}
```

### Walkthrough Pattern

```json
"walkthroughs": [
    {
        "id": "minecraft-walkthrough",
        "title": "Welcome to Minecraft Theme",
        "description": "...",
        "steps": [
            {
                "id": "select-theme",
                "title": "Equip Your Armor",
                "description": "Select a Minecraft theme...\n[Select Theme](command:workbench.action.selectTheme)",
                "media": { "image": "images/walkthrough-themes.png", "altText": "..." }
            }
        ]
    }
]
```

---

## 6. JSON Conventions

- Use 4-space indentation for JSON files
- Always include trailing commas? No — JSON does not allow trailing commas
- Use `//` comments only in `.jsonc` files; `.json` files must be valid JSON
- Alphabetical ordering of keys in `package.json` `contributes` sections is recommended
- All theme color values must be valid 6-character hex codes (#RRGGBB)

---

## 7. Line Limits

| File Type | Limit |
|-----------|-------|
| `extension.js` | 300 lines (complexity of sound engine + commands) |
| Theme JSON files | 200 lines |
| CSS files | 200 lines |
| SVG files | 50 lines |

---

## 8. Git Conventions

### Commit Messages (Conventional Commits)

```
feat(themes): add Nether biome color theme
feat(audio): implement Minecraft sound effects engine
feat(icons): add file icon theme with block SVGs
fix(audio): handle missing mpg123 on Linux gracefully
docs: update README with theme screenshots
chore: prune unused assets
```

### Branch Naming

```
feat/base-minecraft-theme
feat/nether-biome-theme
feat/audio-engine
feat/file-icon-theme
fix/terminal-sound-crash
```

---

## 9. Security Best Practices

- **Never commit `.env` files** — but this extension doesn't use env vars
- **No secrets in source code** — config goes through VS Code settings
- **Validate all file paths** — use `path.join(__dirname, 'audio', file)` to prevent path traversal
- **Limit audio file sizes** — reject files > 1MB to prevent resource exhaustion
- **Sanitize shell commands** — never interpolate user input into shell commands for audio playback
