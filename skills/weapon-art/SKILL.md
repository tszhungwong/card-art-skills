---
name: weapon-art
description: Generate consistent vibrant fantasy TCG weapon, item and equipment illustrations from short user descriptions, including weapons held by a supporting figure, using the shared card-art style lock.
---

# weapon-art: Weapon, Item, and Equipment Illustrations

> Paths are relative to `card-art-skills/`. This skill decides subject and composition; `shared/style_lock.json` decides style.

## 1. When to Use

Use for card art centered on a single weapon, item, or equipment, such as a sword, gun, bow, staff, shield, amulet, gauntlet, or grimoire. Examples: a frost staff; a shield engraved with a dragon; a knight holding a longsword as the main subject.

A hand or person may hold or display the weapon; the wielder is secondary, while the weapon is large and prominent with its outline and key details visible. Do not add a wielder when none is specified. For an explicitly unattended display, append the requested exclusions to the negative prompt.

Use `character-art` when a holding or wearing character is the subject, `magic-art` when the weapon's spell effect is the subject, and `batch-generate` for multiple CSV rows.

## 2. Required Shared Files

Read again before each execution:
1. `shared/style_lock.json` (read-only): `style`, `quality`, `by_type.weapon` (`style_append` and `framing_override`). The override already fixes diagonal placement and the thematic background; do not rewrite them.
2. `shared/negative_lock.json` (read-only): `negative`, `append_by_type.weapon` (excludes piles, duplicate weapon silhouettes, and background echoes; allows hands and wielders).
3. `shared/assets.json`: `weapons`, `loras.style`, `loras.weapon`.
4. `shared/prompt_rules.md`.

## 3. Parsing User Input

1. Extract explicit type, element, material, color, motifs, etc. into §4 fields.
2. Match `name`, `aliases`, or `id` in `assets.json → weapons` and incorporate matched asset settings.
3. Translate requested exclusions into English and append to the negative prompt.
4. Default ratio is `2:3`; a landscape request gives `3:2`.
5. A single weapon is a composition rule; holding it is not a conflict and needs neither confirmation nor a `lock_exceptions` entry. Use `character-art` if the person is the subject. Handle other conflicts under `prompt_rules.md` §6.
6. Determine whether confirmation is needed under `prompt_rules.md` §7 and the core field below; complete and assemble.

## 4. Fields to Complete

| Key | Field | Core | Completion rule (English in prompt) |
|---|---|---|---|
| `weapon_type` | Weapon type | ★ | Specific type: longsword, greatsword, staff, wand, spear, halberd, bow, dagger, axe, gun-blade, shield, amulet, gauntlet, grimoire… |
| `material` | Material | | Primary and secondary materials (polished steel, ancient wood, bone, crystal, gold ornament, leather-wrapped grip); faceted gemstones may be included. |
| `color` | Color | | `[main color scheme] color scheme with [complementary color] accents`, with the main color chosen for element or theme. |
| `engraving_runes` | Motifs or runes | | Match element or faction and specify location (along the blade, around the guard, spiraling along the shaft); default to engravings or inlays, not automatic glow. |
| `vfx` | Effects | | May be empty; ordinary weapons have no added effects. Only when needed, add localized frost mist, crackling lightning, or flame wreath without obscuring the outline; control emission under shared rules. |
| `display` | Background display | | Describe a specific thematic environment appropriate to the element or theme (frozen glacier valley, jungle canopy, ancient ruins, stormy night sky). If held, describe wielder and grip here, e.g. `held by a knight with the wielder visually secondary`; hands grip naturally without hiding key details. Framing supplies diagonal placement. |
| `framing` | Composition | | Fixed: one weapon as main subject, large and prominent, key parts visible; a secondary wielder may appear. |

**Core rule:** `weapon_type` must be identifiable; weapon, equipment, and artifact are not specific types. Otherwise confirm.

**Assembly (`prompt_rules.md` §3):**
- Subject: `a [element/adjective] [weapon_type]`, e.g. `a frost magic staff`.
- Details: `material` → `color` → `engraving_runes` → `vfx` → `display`.
- Composition (fixed; replace only brackets):
  `single [item noun] as the main subject, [item noun] large and dominant in the frame, [key part] and key details clearly visible`
  Examples of `[key part]`: blade, crystal head, crossbow limbs, shield face.
- Framing: copy `style_lock.by_type.weapon.framing_override` verbatim.
- Ratio: default `2:3`; landscape `3:2` to match the reference art frame.

## 5. Fields Not to Change Without Authorization

- All strings in `style_lock.json` and `negative_lock.json`, including weapon framing and appended negatives.
- `single … as the main subject` and `key details clearly visible` in the composition template.
- Explicit user type, material, color, motifs, and damage (e.g. blade cracks must not be omitted).
- Matched asset `description`, `locked_traits`, and `trigger_word`.

## 6. Output Format

Follow `prompt_rules.md` §9. Use §4 keys in `fields`; `suggested_loras` uses `style`, `weapon`, and `assets`.

## 7. Example Input and Output

The English example table and assumptions illustrate actual Traditional Chinese user-facing output under the shared rules. The example input is translated for documentation; retain the original user input at runtime.

**Input:** `I want to generate a frost magic staff`

**Output:**

| Field | Setting | Source |
|---|---|---|
| Weapon type | Frost magic staff | User |
| Material | Pale ancient wood shaft, polished silver bands, gold fittings, large faceted ice crystal | Auto-filled |
| Color | Icy blue and white with warm gold accents | Auto-filled |
| Motifs or runes | Frost runes spiraling along the shaft, inlaid with cyan enamel | Auto-filled |
| Effects | Thin cold mist near the crystal head | Auto-filled |
| Background display | Glacier valley under a clear sky; diagonal placement from locked framing | Auto-filled and locked |
| Composition | Portrait 2:3, staff large and prominent, crystal head visible | Default |

```json
{
  "skill": "weapon-art",
  "asset_type": "weapon",
  "user_input": "I want to generate a frost magic staff",
  "needs_confirmation": false,
  "confirmation_reasons": [],
  "fields": {
    "weapon_type": "frost magic staff",
    "material": "pale ancient wood shaft wrapped in polished silver bands, gold fittings, crowned with a large faceted ice crystal",
    "color": "icy blue and white color scheme with warm gold accents",
    "engraving_runes": "frost runes engraved in a spiral along the shaft, inlaid with cyan enamel",
    "vfx": "a thin curl of cold mist close to the crystal head",
    "display": "set against a frozen glacier valley under a clear sky",
    "framing": "single staff, large and dominant, crystal head clearly visible"
  },
  "auto_filled": ["material", "color", "engraving_runes", "vfx", "display", "framing"],
  "assumptions": ["A glacier valley matches the frost theme; diagonal placement comes from the style lock weapon framing."],
  "matched_assets": [],
  "positive_prompt": "a frost magic staff, pale ancient wood shaft wrapped in polished silver bands, gold fittings, crowned with a large faceted ice crystal, icy blue and white color scheme with warm gold accents, frost runes engraved in a spiral along the shaft, inlaid with cyan enamel, a thin curl of cold mist close to the crystal head, set against a frozen glacier valley under a clear sky, single staff as the main subject, staff large and dominant in the frame, crystal head and key details clearly visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, detailed hard-surface rendering, material-appropriate reflections, faceted gemstones with restrained highlights, weapon placed diagonally across the frame, softly blurred thematic environment matching the weapon's element, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative_prompt": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, weapon pile, weapon rack, double-exposure weapon echo, duplicate weapon silhouette, oversized weapon shadow in the background",
  "chatgpt_prompt": "Create an image from this prompt, used as written:\n\na frost magic staff, pale ancient wood shaft wrapped in polished silver bands, gold fittings, crowned with a large faceted ice crystal, icy blue and white color scheme with warm gold accents, frost runes engraved in a spiral along the shaft, inlaid with cyan enamel, a thin curl of cold mist close to the crystal head, set against a frozen glacier valley under a clear sky, single staff as the main subject, staff large and dominant in the frame, crystal head and key details clearly visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, detailed hard-surface rendering, material-appropriate reflections, faceted gemstones with restrained highlights, weapon placed diagonally across the frame, softly blurred thematic environment matching the weapon's element, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration\n\nAvoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, weapon pile, weapon rack, double-exposure weapon echo, duplicate weapon silhouette, oversized weapon shadow in the background\n\nAspect ratio: 2:3 portrait (1024x1536)",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": {
    "style": { "file": "", "weight": null, "status": "reserved" },
    "weapon": { "file": "", "weight": null, "status": "reserved" },
    "assets": []
  },
  "lock_exceptions": [],
  "style_lock_version": "0.4.0",
  "negative_lock_version": "0.3.2"
}
```

**ChatGPT paste block:** Use the example JSON's `chatgpt_prompt` verbatim in a text block, as required by `prompt_rules.md` §12.

## 8. Test Examples

### Test 1: Explicit input (direct output)

**Input:** `A greatsword wreathed in black flames, with cracks in the blade`

**Expected:** `needs_confirmation: false`; `weapon_type` is greatsword (user), black flames go in `vfx`, and cracks are retained (the negative lock has no broken-related term). Use `black and crimson color scheme with violet accents`, a matching `display` such as stormy volcanic wasteland, and `aspect_ratio: "2:3"`. Expected prompt opening:
`a cursed greatsword, broad blackened steel blade with deep cracks running along its length, gold crossguard set with a red gemstone, leather-wrapped grip, black and crimson color scheme with violet accents, jagged rune engravings near the guard, wreathed in swirling black flames with crimson edges, set against a stormy volcanic wasteland, single greatsword as the main subject, greatsword large and dominant in the frame, blade and key details clearly visible`

### Test 2: Ambiguous input (confirmation required)

**Input:** `Give me legendary equipment`

**Expected:** `needs_confirmation: true` because `weapon_type` is indeterminate; equipment is not a specific type. Do not output a final prompt. Provide the proposed completion settings table, e.g. longsword / polished silver blade, gold guard, sapphire / gold and sky blue with violet accents / blade runes / ancient temple ruins under a clear sky / 2:3. Ask at most three questions: type (sword, staff, bow, shield, or accessory); element or attribute; preferred material or thematic environment.

### Test 3: Person holding weapon (direct output)

**Input:** `A knight holding a longsword, with the sword as the main subject`

**Expected:** `needs_confirmation: false`, `lock_exceptions: []`; use `weapon-art`, set `weapon_type` to longsword and retain the secondary knight holding it in `display`. The single sword is prominent, blade and details visible, with a natural grip. The negative prompt excludes no standalone `person`, `character`, `hands`, `hand holding weapon`, or `wielder`, but retains anatomical quality exclusions such as `deformed hands` and background echo exclusions. If the knight becomes the main subject, use `character-art`.

### Test 4: Multiple-weapon composition conflict

**Input:** `A hand holding two blades dripping venom`

**Expected:** `needs_confirmation: true`; only two blades conflict with `single … as the main subject`, not the grip. Offer one held blade or a one-time relaxation to a pair, recording only the single-weapon composition relaxation in `lock_exceptions`. Do not require removing the hand or switching to character art; use `character-art` only if the person is the subject. Retain `dripping venom` as `vfx`; e.g. `toxic green and violet color scheme with yellow accents`.

### Test 5: Unattended display

**Input:** `A frost staff, no person or hands`

**Expected:** `needs_confirmation: false`; preserve unattended display without adding a wielder. Append `person, hands` for this output only; do not change the shared negative lock.
