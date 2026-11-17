---
name: scene-art
description: Generate consistent vibrant fantasy TCG location, environment and field card illustrations from short user descriptions, using the shared card-art style lock.
---

# scene-art: Location, Environment, and Field Card Illustrations

> Paths are relative to `card-art-skills/`. This skill decides subject and composition; `shared/style_lock.json` decides style.

## 1. When to Use

Use for card art centered on a location or environment, such as ruins, castles, forests, volcanoes, harbors, dungeons, or floating islands. Examples: ancient ruins covered in snow and ice; a sky castle floating above a sea of clouds.

Use `character-art` when the environment is merely a character's background; `magic-art` when the active spell effect itself is the subject (e.g. a meteor shower); and `batch-generate` for multiple CSV rows.

## 2. Required Shared Files

Read again before each execution:
1. `shared/style_lock.json` (read-only): `style`, `quality`, `by_type.scene` (`style_append` and `framing_override`, the latter replacing global `framing`).
2. `shared/negative_lock.json` (read-only): `negative`, `append_by_type.scene` (excludes people, crowds, close-ups, and blank backgrounds).
3. `shared/assets.json`: `scenes`, `loras.style`, `loras.scene`.
4. `shared/prompt_rules.md`.

## 3. Parsing User Input

1. Extract explicit location, time, weather, landmarks, mood, color, and creatures into §4 fields. Determine `scene_kind`: architecture or terrain is `landmark`; a single thematic object such as a vortex, planet, crystal cluster, giant flower, or portal is `focal_object`.
2. Match `name`, `aliases`, or `id` in `assets.json → scenes` and incorporate matched asset settings.
3. Translate requested exclusions into English and append to the negative prompt.
4. Landscape, panorama, or wide view → `3:2`; otherwise default to `2:3`.
5. People and crowds conflict with the negative lock; handle under `prompt_rules.md` §6. Creatures such as birds, bats, and giant beasts are **not conflicts**.
6. Determine whether confirmation is needed, then complete and assemble.

## 4. Fields to Complete

| Key | Field | Core | Completion rule (English in prompt) |
|---|---|---|---|
| `scene_kind` | Scene kind | | `landmark` (default) or `focal_object` (single thematic object such as vortex, planet, crystal cluster, giant flower, or portal). |
| `location_type` | Location type | ★ | Specific location and condition (ancient ruins covered in snow, volcanic fortress, sunken temple). |
| `landmark` | Main landmark | | **One dominant landmark or thematic object**, filling much of the frame (a towering broken archway, a giant ancient tree, a floating citadel). |
| `layers` | Foreground / midground / background | | All three for `landmark`: foreground…, midground…, background…. Optional for `focal_object`; a sentence describing the surroundings may be used instead. |
| `time_weather` | Time and weather | | Choose a combination enhancing mood and colors (sunset + drifting clouds, night + moonlight). Use environmental light; add glowing landmarks, runes, or aurora only when the theme requires them. |
| `mood` | Mood | | One or two adjectives; default to wondrous fantasy grandeur (wondrous, awe-inspiring, mysterious, ominous). |
| `color` | Color | | `[dominant environmental color] color scheme with [complementary color] accents`; one dominant hue per image. |
| `creatures` | Creatures (optional) | | Add only if mentioned by the user or representative of the location; smaller than the landmark (flock of birds, distant sea serpent). Do not add humans. |
| `framing` | Composition | | In portrait 2:3 place the landmark in the upper middle; in landscape 3:2 place it centrally or on a third. |

**Core rule:** `location_type` must be identifiable; otherwise confirm.

**Assembly (`prompt_rules.md` §3):**
- Subject: `location_type` plus `landmark`.
- Details: `layers` (foreground → midground → background) → `time_weather` → `mood` → `color` → `creatures` if present.
- Composition (fixed templates; choose by `scene_kind`, replace only brackets):
  - landmark: `[vertical|horizontal] environment illustration, [landmark noun] as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background`
  - focal_object: `[vertical|horizontal] environment illustration, [object noun] as the dominant focal point filling much of the frame, immersive surrounding environment`
- Framing: copy `style_lock.by_type.scene.framing_override` verbatim.
- Ratio: default `2:3`; landscape or panorama `3:2`; reference art frame `5:4`.

## 5. Fields Not to Change Without Authorization

- All strings in `style_lock.json` and `negative_lock.json`, including scene framing override and appended negatives.
- The single dominant focal point in the composition template; retain foreground, midground, and background for `landmark`.
- Explicit user location, time, weather, landmark, color, and creatures.
- Matched asset `description`, `locked_traits`, and `trigger_word`.

## 6. Output Format

Follow `prompt_rules.md` §9. Use §4 keys in `fields`; `suggested_loras` uses `style`, `scene`, and `assets`.

## 7. Example Input and Output

The English example table and assumptions illustrate actual Traditional Chinese user-facing output under the shared rules. The example input is translated for documentation; retain the original user input at runtime.

**Input:** `I want to generate ancient ruins covered in snow and ice`

**Output:**

| Field | Setting | Source |
|---|---|---|
| Scene kind | Landmark | Automatically determined |
| Location type | Ancient ruins covered in snow and ice | User |
| Main landmark | Tall broken stone arch, half-buried in snow and carved with weathered runes | Auto-filled |
| Foreground / midground / background | Snow-covered broken columns and frozen rubble / arch hung with icicles / misty frozen mountains | Auto-filled |
| Time and weather | Dusk, light snow, cold wind carrying fine snow | Auto-filled |
| Mood | Solemn and awe-inspiring | Auto-filled |
| Color | Icy blue and white with warm gold accents; golden dusk light through the arch | Auto-filled |
| Creatures | None | Default |
| Composition | Portrait 2:3; arch fills much of upper middle | Default |

```json
{
  "skill": "scene-art",
  "asset_type": "scene",
  "user_input": "I want to generate ancient ruins covered in snow and ice",
  "needs_confirmation": false,
  "confirmation_reasons": [],
  "fields": {
    "scene_kind": "landmark",
    "location_type": "ancient ruins covered in snow and ice",
    "landmark": "a towering broken stone archway carved with weathered runes, half-buried in snow",
    "layers": "foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains",
    "time_weather": "dusk, light snowfall, cold wind carrying fine snow",
    "mood": "solemn and awe-inspiring atmosphere",
    "color": "icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch",
    "creatures": "",
    "framing": "vertical, archway dominant at upper-middle"
  },
  "auto_filled": ["scene_kind", "landmark", "layers", "time_weather", "mood", "color", "creatures", "framing"],
  "assumptions": ["No people or creatures were specified: the scene defaults to neither, with the landmark as the focal point."],
  "matched_assets": [],
  "positive_prompt": "ancient ruins covered in snow and ice, a towering broken stone archway carved with weathered runes, half-buried in snow, foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains, dusk, light snowfall, cold wind carrying fine snow, solemn and awe-inspiring atmosphere, icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch, vertical environment illustration, the archway as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast, single dominant focal subject filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative_prompt": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, people, crowd, human figure, close-up portrait, flat empty background",
  "chatgpt_prompt": "Create an image from this prompt, used as written:\n\nancient ruins covered in snow and ice, a towering broken stone archway carved with weathered runes, half-buried in snow, foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains, dusk, light snowfall, cold wind carrying fine snow, solemn and awe-inspiring atmosphere, icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch, vertical environment illustration, the archway as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast, single dominant focal subject filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration\n\nAvoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, people, crowd, human figure, close-up portrait, flat empty background\n\nAspect ratio: 2:3 portrait (1024x1536)",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": {
    "style": { "file": "", "weight": null, "status": "reserved" },
    "scene": { "file": "", "weight": null, "status": "reserved" },
    "assets": []
  },
  "lock_exceptions": [],
  "style_lock_version": "0.4.0",
  "negative_lock_version": "0.3.2"
}
```

**ChatGPT paste block:** Use the example JSON's `chatgpt_prompt` verbatim in a text block, as required by `prompt_rules.md` §12.

## 8. Test Examples

### Test 1: Explicit input and landscape (direct output)

**Input:** `A sky castle floating above a sea of clouds, landscape card`

**Expected:** `needs_confirmation: false`; `aspect_ratio: "3:2"`, `resolution_hint: "1216x832"` because landscape was requested; composition uses `horizontal environment illustration …`; the color sentence has only one dominant hue. Expected prompt opening:
`a floating sky castle above a sea of clouds, a tall white spired citadel on a fractured floating rock with small waterfalls pouring off its edges, foreground of drifting cloud banks and broken floating stones, midground castle with chained smaller islets, background of distant sunlit cloud towers, sunrise, high winds, wondrous and adventurous atmosphere, bright sky blue and gold color scheme with warm pink accents, horizontal environment illustration, the castle as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background`

### Test 2: Ambiguous input (confirmation required)

**Input:** `An imposing place`

**Expected:** `needs_confirmation: true` because `location_type` is indeterminate. Do not output a final prompt. Provide the proposed completion settings table, e.g. volcanic fortress / night and ash / giant black stone gate above a lava river / three depth layers / imposing mood / lava orange-red with cyan accents / 2:3. Ask at most three questions: location type (natural, architecture, ruins, otherworld); time and weather; portrait or landscape.

### Test 3: Conflict with lock

**Input:** `A bustling market with many people shopping`

**Expected:** `needs_confirmation: true`; many people conflict with `people, crowd, human figure, multiple characters` in the negative lock. Options:
- A (recommended): an empty market with lantern-covered stalls, fluttering drapes, smoking fires, and birds in flight (creatures do not conflict) to suggest bustle.
- B: remove `people`, `crowd`, `human figure`, and `multiple characters` for this output only, depict small distant crowd silhouettes, and record the exception in `lock_exceptions`.

Never silently remove locked terms.
