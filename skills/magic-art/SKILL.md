---
name: magic-art
description: Generate consistent vibrant fantasy TCG magic effect, skill effect and spell card illustrations from short user descriptions, using the shared card-art style lock.
---

# magic-art: Magic effects, skill effects, and spell card art

> Paths are relative to `card-art-skills/`. This skill determines only what to depict and the composition; how to draw comes entirely from `shared/style_lock.json`.

## 1. When to use

**Use:** For card art focused on a spell or effect itself, such as a burst, vortex, beam, shield, curse, summoning circle, healing light, or meteor rain.
- “I want a fire burst spell.”
- “A lightning spear descending from the sky.”

**Do not use (choose another skill):**
- The caster is the subject and magic is secondary → `character-art`
- The summoned creature is the subject → `character-art`
- The magical implement itself is the subject → `weapon-art`
- A location altered by magic is the subject (such as a cursed forest) → `scene-art`
- Processing multiple CSV rows at once → `batch-generate`

## 2. Required shared files

Read again before each run:
1. `shared/style_lock.json` (read-only): `style`, `quality`, `by_type.magic` (`style_append` and `framing_override`; core detail, luminance control, and background treatment are provided here consistently, and not every element emits light).
2. `shared/negative_lock.json` (read-only): `negative`, `append_by_type.magic` (excludes character close-ups and unreadable effect silhouettes).
3. `shared/assets.json`: `magic_effects`, `loras.style`, `loras.magic`.
4. `shared/prompt_rules.md`.

## 3. Parsing user input

1. Extract explicitly provided elements, forms, colors, casting sources, and creatures, mapping them to §4 fields.
2. Match `name`/`aliases`/`id` in `assets.json → magic_effects` and include the asset settings when matched.
3. Extract exclusions, translate them into English, and add them to the negative prompt.
4. Decide whether to include a creature: one may be added if the user mentions it or if the element has a representative avatar (fire → firebird, lightning → thunder beast). The creature can share the focal point with the effect, casting it or surrounded by it; use the creature composition template. By default, show only the hands or a backlit silhouette of a humanoid caster. If the user wants the caster's face emphasized, skill routing is unclear (`prompt_rules.md` §7 item 4), so ask for confirmation.
5. Check conflicts and whether confirmation is required, then complete and assemble.

## 4. Fields to complete

| key | Field | Core | Completion rule (write in English in the prompt) |
|---|---|---|---|
| `element` | Magic element | ★ | fire, frost, lightning, shadow, holy light, poison, arcane, earth, wind, water… |
| `form` | Effect form | ★ | burst, vortex, beam, spear, shield dome, chains, summoning circle, meteor rain… |
| `color` | Color | | Color scheme sentence: `[element primary colors] color scheme with [complementary color] accents` |
| `lighting` | Lighting | | Follow the element: fire, lightning, and holy light can act as local primary light sources; specify the illuminated area and retain core detail. Earth, wind, and water normally receive ambient light; do not require a white-hot core or emission. |
| `particles_runes` | Particles or runes | | Select a few embers, ice shards, or debris appropriate to the subject; may be empty. Do not automatically add glowing runes or starbursts as decoration. |
| `source` | Casting source | | By default no caster is visible: magic circle, ground, rift in the sky, or tip of a magical implement. When a caster is included, show only hands or a backlit silhouette. |
| `creature` | Related creature (optional) | | A creature as caster, recipient, or effect avatar (phoenix, thunder beast, sea serpent); it may be large and share the focal point with the effect, casting it or surrounded by it. Avoid facial close-ups. |
| `background` | Background | | One environmental cue (storm clouds, ocean surface, cavern, battlefield); brightness depends on the element, and framing supplies motion blur. |
| `framing` | Composition | | The effect is the central focal point (or shares it with a creature), with a readable shape. |

**Core rule:** At least one of `element` and `form` must be identifiable; otherwise ask for confirmation.

**Assembly mapping (`prompt_rules.md` §3):**
- subject: `a [element] [form] spell effect`
- details: `form` details → `color` → `lighting` → `particles_runes` → `source` → `creature` (if present) → `background`
- composition (choose one fixed template based on whether a creature is present; replace only `[ ]`):
  - Effect only: `[vertical|horizontal] composition, spell effect as the central focal point, clear readable shape of the [form noun], energy radiating outward from the center`
  - With creature: `[vertical|horizontal] composition, [creature noun] and its spell effect as the shared focal point, clear readable shape of the [form noun], energy radiating outward`
- framing: use `style_lock.by_type.magic.framing_override` verbatim.
- Aspect ratio: default `2:3`; use `5:4` to fit the reference art frame.

## 5. Fields that must not be changed without authorization

- All strings in `style_lock.json` and `negative_lock.json` (including the magic framing override and appended negative terms).
- The focal-point sentence in the composition template (choose either effect-only or creature version).
- Elements, forms, colors, casting sources, and creatures explicitly specified by the user.
- A matched asset's `description`, `locked_traits`, and `trigger_word`.

## 6. Output format

Follow `prompt_rules.md` §9. Use the keys from §4 in `fields`; the `suggested_loras` keys are `style`, `magic`, and `assets`.

## 7. Example input and output

The example is translated for documentation; actual user-facing explanations and assumptions remain in Traditional Chinese under the shared rules.

**Input:**
```
I want a fire burst spell
```

**Output:**

| Field | Setting | Source |
|---|---|---|
| Magic element | Fire | User |
| Effect form | Burst: a radial eruption of flames outward from a single point | User + completed detail |
| Color | Blazing orange and crimson primary colors with electric blue complementary accents | Automatically completed |
| Lighting | Flames light nearby smoke, with controlled brightness and visible inner flame layers | Automatically completed |
| Particles or runes | A few flying embers and molten droplets | Automatically completed |
| Casting source | A fractured runic circle on scorched ground | Automatically completed |
| Related creature | None | Default |
| Background | Smoky, scorched battlefield | Automatically completed |
| Composition | Vertical 2:3, effect centered | Default |

```json
{
  "skill": "magic-art",
  "asset_type": "magic",
  "user_input": "I want a fire burst spell",
  "needs_confirmation": false,
  "confirmation_reasons": [],
  "fields": {
    "element": "fire",
    "form": "burst, explosive radial eruption of flames bursting outward from a single point",
    "color": "blazing orange and crimson color scheme with electric blue accents",
    "lighting": "the flames lighting nearby smoke with controlled brightness and visible inner flame layers",
    "particles_runes": "a few flying embers and molten droplets",
    "source": "erupting from a fractured runic circle on scorched ground",
    "creature": "",
    "background": "smoky scorched battlefield in the background",
    "framing": "vertical, effect centered, pure effect template"
  },
  "auto_filled": ["color", "lighting", "particles_runes", "source", "creature", "background", "framing"],
  "assumptions": ["No caster or creature was specified: use a ground runic circle as the source, with no character or creature depicted. Tell me if you want an avatar such as a firebird."],
  "matched_assets": [],
  "positive_prompt": "a fire burst spell effect, explosive radial eruption of flames bursting outward from a single point, blazing orange and crimson color scheme with electric blue accents, the flames lighting nearby smoke with controlled brightness and visible inner flame layers, a few flying embers and molten droplets, erupting from a fractured runic circle on scorched ground, smoky scorched battlefield in the background, vertical composition, spell effect as the central focal point, clear readable shape of the burst, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime-style magic VFX, clearly defined effect shapes, layered color and texture, controlled luminance preserving core detail, emission appropriate to the spell's element, explosive dynamic composition, effect fills most of the frame, element-appropriate background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative_prompt": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, character portrait focus, face close-up, indistinct effect silhouette",
  "chatgpt_prompt": "Create an image from this prompt, used as written:\n\na fire burst spell effect, explosive radial eruption of flames bursting outward from a single point, blazing orange and crimson color scheme with electric blue accents, the flames lighting nearby smoke with controlled brightness and visible inner flame layers, a few flying embers and molten droplets, erupting from a fractured runic circle on scorched ground, smoky scorched battlefield in the background, vertical composition, spell effect as the central focal point, clear readable shape of the burst, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime-style magic VFX, clearly defined effect shapes, layered color and texture, controlled luminance preserving core detail, emission appropriate to the spell's element, explosive dynamic composition, effect fills most of the frame, element-appropriate background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration\n\nAvoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, character portrait focus, face close-up, indistinct effect silhouette\n\nAspect ratio: 2:3 portrait (1024x1536)",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": {
    "style": { "file": "", "weight": null, "status": "reserved" },
    "magic": { "file": "", "weight": null, "status": "reserved" },
    "assets": []
  },
  "lock_exceptions": [],
  "style_lock_version": "0.4.0",
  "negative_lock_version": "0.3.2"
}
```

**ChatGPT paste block:** Use the example JSON's `chatgpt_prompt` verbatim in a text block, as required by `prompt_rules.md` §12.

## 8. Test examples

### Test 1: Clear input (direct output)

**Input:** `A lightning spear falls from the sky and strikes the ground`

**Expected:**
- `needs_confirmation: false`
- `element` = lightning, `form` = spear-shaped lightning strike (user); complete the casting source as a split in the clouds.
- Vertical 2:3 suits the top-to-bottom movement.
- Expected beginning of the prompt:
  `a lightning spear spell effect, a massive spear-shaped bolt of lightning plunging from the sky and striking the ground, electric yellow and white color scheme with violet accents, the impact flash lighting up the cracked earth, scattering sparks and shattered rock fragments, charred fissures branching across the ground from the impact point, descending from a split in dark storm clouds, stormy sky in the background, vertical composition, spell effect as the central focal point, clear readable shape of the spear, energy radiating outward from the center`

### Test 2: Ambiguous input (confirmation required)

**Input:** `A very powerful spell`

**Expected:**
- `needs_confirmation: true`; reason: neither `element` nor `form` can be determined.
- **Do not output a final prompt.**
- Provide a proposed settings table, for example: shadow element / vortex / deep purple and magenta primary colors with gold complementary accents / ground magic circle / 2:3.
- Questions (at most 3): 1) Element? 2) Effect form (burst, beam, shield, summoning circle…)? 3) Include a creature avatar?

### Test 3: Unclear skill routing

**Input:** `A mage casts healing light; the mage's face should be visible`

**Expected:**
- `needs_confirmation: true`; reason: the focus could be either the spell or the character (`prompt_rules.md` §7 item 4).
- Options:
  - A (recommended): Keep `magic-art`, with the healing light column as the focus and the mage as a backlit half-body silhouette below it. The face is visible but occupies a small portion of the image (does not violate `face close-up` and requires no exception).
  - B: Switch to `character-art`, with the mage as the subject and healing light as `background_magic`.
- Do not silently add a facial close-up of the mage to a magic prompt.