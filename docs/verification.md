# Verification — How to Demonstrate That Work Functions

> Golden rule: **the agent does not say "it works", it demonstrates it**.
> Every feature ends with executable evidence, not assertions.

## Verification Flow

```
implementer writes code
        ↓
implementer self-verifies against docs/01-conventions.md
        ↓
reviewer validates manually (structure, code, conventions)
        ↓
build succeeds (vsce package / lint)
        ↓
human tests in VS Code → gives feedback
        ↓
positive feedback + reviewer approved → feature marked done
```

## Level 1 — Code Structure (mandatory)

Every source file must:

1. Have the correct file extension (`.js`, `.json`, `.css`, `.svg`, `.md`).
2. Follow the module/organization conventions (`themes/`, `audio/`, `icons/`, `styles/`).
3. Have appropriate structure (valid JSON, valid JS, valid CSS).
4. Not exceed the line limit defined in `docs/01-conventions.md`.

## Level 2 — Code Conventions (mandatory)

All code must respect `docs/01-conventions.md`:

- Theme JSON: valid JSON, required color keys present, Minecraft-inspired palette.
- extension.js: proper activation pattern, no `console.log`, event listeners registered as subscriptions.
- Audio: correct file references, platform-specific playback, dependency check at activation.
- Icons: Minecraft pixel-art style SVGs, proper naming convention.
- CSS: scoped to markdown preview, no VS Code chrome overrides.
- package.json: correct contributes structure, valid JSON.

Verification (reviewer):

```bash
# Check for console.log
grep -rn "console\.log" minecraft-theme/

# Validate all JSON files
for f in minecraft-theme/themes/*.json minecraft-theme/package.json minecraft-theme/*-icon-theme.json; do
    node -e "JSON.parse(require('fs').readFileSync('$f','utf8'))" && echo "$f: valid JSON"
done

# Check for any types (not applicable in JS, but check for missing try/catch)
grep -rn "exec\(" minecraft-theme/extension.js | grep -v "try\|catch"
```

## Level 3 — Project Architecture (mandatory)

Every module must:

1. Follow the organization defined in `docs/02-architecture.md`.
2. Use proper VS Code API patterns (subscriptions, disposables).
3. Have clear responsibility boundaries (extension.js handles activation/events, JSON files define themes).
4. Audio engine correctly dispatches to platform-specific players.

## Level 4 — Module Communication (mandatory)

Verify that:

1. `package.json` `contributes` correctly references all theme/icon/style/walkthrough files.
2. Audio file paths in `extension.js` use `path.join(__dirname, 'audio', file)`.
3. Configuration IDs use the `minecraft-theme.` prefix.
4. Command IDs use the `minecraftTheme.` prefix.
5. Walkthroughs reference correct command IDs and image paths.

## Level 5 — Build/Test Verification (mandatory before marking done)

### Package Verification

```bash
# Install vsce if not present
npm install -g @vscode/vsce

# Package the extension (dry run to check structure)
vsce package --no-yarn
# must exit 0 and produce a .vsix file

# Verify .vsix was created
ls *.vsix
```

### JSON Validation

```bash
# Validate all theme files
node -e "
const fs = require('fs');
const dir = 'minecraft-theme/themes';
fs.readdirSync(dir).forEach(f => {
    const content = fs.readFileSync(dir + '/' + f, 'utf8');
    JSON.parse(content);
    console.log(f + ': valid');
});
"
```

### Extension Load Test

The extension should activate without errors when installed in VS Code:

1. Open VS Code
2. Install the .vsix (Extensions → ... → Install from VSIX)
3. Open VS Code Developer Tools (Help → Toggle Developer Tools)
4. Verify no errors related to `minecraft-theme` appear in the console
5. Select a Minecraft theme from the theme picker
6. Select the Minecraft icon theme from the file icon theme picker
7. Verify sounds play for the configured events

### Checklist for Reviewer

```bash
# Check all required files exist
ls minecraft-theme/extension.js
ls minecraft-theme/package.json
ls minecraft-theme/LICENSE
ls minecraft-theme/CHANGELOG.md
ls minecraft-theme/themes/*.json
ls minecraft-theme/audio/*.mp3
ls minecraft-theme/icons/*.svg
ls minecraft-theme/styles/*.css
ls minecraft-theme/images/*.png
```

## Anti-Patterns (do not do)

- ❌ "I added the code, it should work." → must verify `vsce package` passes.
- ❌ Invalid JSON in theme files → must validate with `JSON.parse()`.
- ❌ No dependency check for audio player → must check `mpg123`/`afplay` at activation.
- ❌ Hardcoded audio paths → must use `path.join(__dirname, ...)`.
- ❌ Marking the feature as `done` without build passing + reviewer approval.
- ❌ Theme colors that don't provide sufficient contrast → must verify WCAG AA minimum.
- ❌ Using `console.log` in production extension code → use `vscode.window` for diagnostics.

## Final Verification Before Closing

1. The reviewer has gone through `CHECKPOINTS.md` and all checks pass.
2. `vsce package` produces a valid `.vsix` file.
3. All JSON files are valid (themes, package.json, icon theme).
4. The human has verified the feature in VS Code (theme loads, sounds play).
6. `progress/current.md` reflects the verification result.

If anything fails, **do not** mark anything as `done`. Record the blockage
in `progress/current.md` with status `blocked` in `feature_list.json`.
