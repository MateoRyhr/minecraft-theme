# Feature #6 — Faction & Character Theme Variants

## Files Created

1. `minecraft-theme/themes/Minecraft-Villager-color-theme.json` — warm browns, emerald greens, parchment off-whites
2. `minecraft-theme/themes/Minecraft-Piglin-color-theme.json` — crimson reds, gold yellows, netherrack browns
3. `minecraft-theme/themes/Minecraft-Enderman-color-theme.json` — deep purples/black, ender pearl cyan highlights
4. `minecraft-theme/themes/Minecraft-Steve-color-theme.json` — classic blue jeans, cyan shirt, skin tones
5. `minecraft-theme/themes/Minecraft-Creeper-color-theme.json` — dark greens, black, creeper-face lime green accents

## Files Modified

- `minecraft-theme/package.json` — Added 5 theme entries to `contributes.themes` after "Minecraft: The End"

## Verification

- All 5 theme files: valid JSON ✓
- Each theme defines all required color keys ✓
- All themes under 200 lines (175 each) ✓
- package.json is valid JSON ✓
- All 5 themes registered in contributes.themes ✓
- No Half-Life references in new files or package.json ✓
