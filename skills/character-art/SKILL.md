---
name: character-art
description: Write English card-art prompts for an individual character, monster, or summoned creature using the shared style lock. Use this skill for a standard illustration and rare alternate art of the same character in the same form.
---

# Character illustrations

Paths are relative to `card-art-skills/`. This skill defines subject and composition; `shared/style_lock.json` defines style.

## 1. When to use this skill

Use for a single figure: a person, fantasy species or profession, monster, dragon, or summoned unit. For a standard illustration and rare alternate art of the same character and form, use §6.

| Other requests | Route |
|---|---|
| Base and evolved forms, a sequence showing growth, aging, or machine modifications, or a single missing period in an existing character timeline | `evolution-art` |
| Weapon, item, or equipment as the subject | `weapon-art` |
| Location or environment as the subject | `scene-art` |
| Spell effect as the subject, with the caster in a supporting role | `magic-art` |
| Multiple CSV rows | `batch-generate` |
| Unclear subject | Ask for clarification under `shared/prompt_rules.md` §7 |

## 2. Required shared files

Before each run, read `shared/prompt_rules.md` and reload:

- `shared/style_lock.json` (read-only). Use `style`, `quality`, and the `style_append` and `framing_override` entries under `by_type.character`.
- `shared/negative_lock.json` (read-only). Use `negative` and `append_by_type.character`.
- `shared/assets.json`. Look up `characters`, `loras.style`, and `loras.character`.

## 3. Parse the request

1. Map explicit details to §4 fields and mark their source as the user.
2. Match names, aliases, and IDs against `assets.json → characters`; apply `description`, `locked_traits`, `trigger_word`, and `lora_file` under the shared rules and identify the asset library as their source.
3. Translate explicit exclusions into English and append them to the negative prompt.
4. Map full-body and half-body requests to their respective composition templates. Apply the user's orientation or aspect ratio under `shared/prompt_rules.md` §2.
5. Check conflicts and confirmation conditions under the shared rules §§6–7, including the core-field requirement in §4 below.
6. If confirmation is required, present proposed settings and questions, then wait. Otherwise complete the fields and assemble under shared rules §§3–4.

## 4. Character fields

Write prompt field values in English.

| Key | Meaning | Completion rule |
|---|---|---|
| `race_type` | Species or character type (core) | Default to `human` when only a profession is given. Describe build and distinctive features such as skin, horns, tusks, ears, or wings. |
| `role` | Profession or identity (core) | Infer from the species and props where appropriate. A wand may suggest a shaman or mage. |
| `outfit` | Clothing | Specify materials and layers consistent with role and faction. Add metal ornaments or gems only when supported by the character design. |
| `weapon_prop` | Weapon or prop | Infer from the role when needed. Preserve the user's choice of prop and fill in material details. |
| `pose` | Pose | Give the figure a clear silhouette and keep the face visible. A hand reaching toward the viewer can convey movement. |
| `expression` | Expression | Match the faction and personality. Favor lively expressions such as a confident grin, bright smile, or fierce glare by default. |
| `element_faction` | Element or faction | Infer from the props, clothing, and wording. Write `[main colors] color scheme with [complementary color] accents` under the shared rules §3. |
| `background_magic` | Background or magical effects | May be empty. Use an environmental background for ordinary characters. Add small localized effects only when the subject calls for them, such as during spellcasting. Rune circles, starbursts, and glowing particles are optional. |
| `framing` | Framing | Default to `half-body`. Use `full-body` when requested or when necessary to show a complete outfit or stance. |

At least one of `race_type` and `role` must be identifiable. If both are unclear, ask for confirmation.

Assemble the subject from `race_type` and `role`, for example `a muscular orc tribal shaman, grey-green skin, prominent tusks`. Append details in this order: `outfit`, `weapon_prop`, `pose`, `expression`, `element_faction`, `background_magic`.

Use the applicable fixed composition template, replacing only the bracketed orientation:

- Half-body: `[vertical|horizontal] half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible`
- Full-body: `[vertical|horizontal] full-body character portrait, single character, character as the clear focal point with a readable silhouette, entire figure in frame from head to feet`

Use `style_lock.by_type.character.framing_override` verbatim. Default to vertical `2:3`. The character template also supports horizontal `5:4` for landscape or reference-frame compositions. For a user-specified ratio, follow §3 above.

## 5. Protected details

Preserve all strings in both locks, the composition template terms `single character` and `face fully visible` where applicable, explicit user details (including species, props, colors, hairstyle, and gender), and the matched asset's `description`, `locked_traits`, and `trigger_word`. Resolve conflicts under the shared rules §6.

## 6. Output

Follow the shared rules §9. Present the settings table in Traditional Chinese, followed by the JSON record and the copyable ChatGPT block from §12. Use the §4 keys for `fields` and the keys `style`, `character`, and `assets` for `suggested_loras`.

### Optional standard illustration and rare alternate art

1. Establish one character and form using §§3–4. Preserve the user's requirements and any matched asset description and locked traits. Write each identifying feature once in English, then copy it verbatim into the corresponding field and prompt for both versions. For grouped markings, specify their body location, count, and arrangement relative to the character's body. If an accent color is restricted to a location, name the color and its body part, object, or environmental feature in the same phrase in both prompts.
2. Distinguish the alternate through an action, event, or camera angle. Choose poses and viewpoints that keep the identifying features visible in each standalone image. Keep the character in the same form. Leave lettering, frames, and rarity finishes such as foil or embossing to card production. If either version requires confirmation, hold both final prompts under the shared rules §§6–7.
3. Present a Traditional Chinese comparison table of shared details, version-specific details, and their sources. Deliver the standard version first and the alternate second. Assemble each independently under the shared rules §§3–4 and give each the complete §9 record and §12 paste block. Use `skill: "character-art"`, `asset_type: "character"`, and the existing §4 fields in both. Describe each illustration fully, avoiding references to the other one. When saving under the shared rules §10, end the slugs with `-standard` and `-alt-art`, respectively.
4. When the user requests image verification, generate each image independently from its complete text prompt. Count the markings, check their orientation, and inspect the placement of accent colors. Hide the version labels and identify the visible action or composition that distinguishes the two images. If a landscape detail should appear between limbs, inspect that exact opening. Record deviations in the first outputs.

#### Choosing the alternate's subject and framing

Choose one primary direction that fits the request, and express it through the existing fields. For action-led art, put a specific instant and the relevant body movement in `pose`, then frame the contact point or obstacle in `background_magic`. For quiet narrative art, identify a visible relationship between the character and an object, such as the gaze meeting a reflection or a tool joining two torn edges. Specify the evidence of the event, its location, and enough space to read it. Keep these directions optional within the same character workflow.

Closer framing can support either direction. Describe the complete head surface, feature placement, and materials in both versions when these define identity, especially for masks and constructs. During verification, check for new hair, eyes, facial features, or accessories introduced by the model. Inspect color restrictions across clothing, props, and the environment as well as the body.

For a reflection, verify the reflected marking and the intended intersection with a crack, ripple, or other detail. After hiding labels, ask the reviewer to describe the event before revealing the intended story. Record whether the visible event is recognizable, whether the exact spatial detail matches, and whether the character identity survives. Keep initial failures visible in the report alongside any later retries.

## 7. Worked example

Input: `An orc holding a magic wand`

The table and explanatory notes below are English translations for this guide. In a live response, write the table and assumptions in Traditional Chinese, preserve the user's original input in `user_input`, and use English for prompt field values and both prompts.

| Field | Setting | Source |
|---|---|---|
| Species | Orc | User |
| Role | Tribal shaman | Inferred |
| Outfit | Layered fur mantle, bone beads, crimson ritual sash with gold trim, leather bracers with orange gemstones | Inferred |
| Prop | Magic wand of twisted wood with a faceted fire crystal and restrained inner glow | User specified the wand; material and crystal inferred |
| Pose | Raises the wand and reaches toward the viewer with the other hand | Inferred |
| Expression | Fierce, confident grin with bared tusks | Inferred |
| Element or faction | Fire; orange and crimson with teal accents | Inferred |
| Background | A few embers near the wand tip and a rocky canyon | Inferred |
| Framing | Vertical 2:3 half-body portrait | Default |

```json
{
  "skill": "character-art",
  "asset_type": "character",
  "user_input": "An orc holding a magic wand",
  "needs_confirmation": false,
  "confirmation_reasons": [],
  "fields": {
    "race_type": "orc, grey-green skin, prominent tusks",
    "role": "tribal shaman",
    "outfit": "layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones",
    "weapon_prop": "gnarled wooden magic wand topped with a faceted fire crystal with a restrained inner glow",
    "pose": "raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy",
    "expression": "fierce confident grin, bared tusks",
    "element_faction": "fire element, blazing orange and crimson color scheme with teal accents",
    "background_magic": "a few embers near the wand tip, rocky canyon backdrop",
    "framing": "half-body"
  },
  "auto_filled": ["role", "outfit", "pose", "expression", "element_faction", "background_magic", "framing"],
  "assumptions": [
    "The example uses orc in the usual fantasy sense. The original term could also mean beastfolk.",
    "The requested prop is a small magic wand."
  ],
  "matched_assets": [],
  "positive_prompt": "a muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones, holding a gnarled wooden magic wand topped with a faceted fire crystal with a restrained inner glow, raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy, fierce confident grin, bared tusks, fire element, blazing orange and crimson color scheme with teal accents, a few embers near the wand tip, rocky canyon backdrop, vertical half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime character art, clean lineart, cel shading with soft gradients, expressive eyes with detailed highlights, soft hair highlights, dynamic diagonal composition, character fills the frame, detailed thematic environment background with atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative_prompt": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, chibi, super deformed",
  "chatgpt_prompt": "Create an image from this prompt, used as written:\n\na muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones, holding a gnarled wooden magic wand topped with a faceted fire crystal with a restrained inner glow, raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy, fierce confident grin, bared tusks, fire element, blazing orange and crimson color scheme with teal accents, a few embers near the wand tip, rocky canyon backdrop, vertical half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime character art, clean lineart, cel shading with soft gradients, expressive eyes with detailed highlights, soft hair highlights, dynamic diagonal composition, character fills the frame, detailed thematic environment background with atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration\n\nAvoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, chibi, super deformed\n\nAspect ratio: 2:3 portrait (1024x1536)",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": {
    "style": { "file": "", "weight": null, "status": "reserved" },
    "character": { "file": "", "weight": null, "status": "reserved" },
    "assets": []
  },
  "lock_exceptions": [],
  "style_lock_version": "0.4.0",
  "negative_lock_version": "0.3.2"
}
```

The complete `chatgpt_prompt` in the JSON above is the copyable §12 block, assembled mechanically from `positive_prompt`, `negative_prompt`, and `aspect_ratio`.

## 8. Test cases

### 1. Clear request

Input: `A silver-haired elven archer, full-body, standing in moonlight`

Expect `needs_confirmation: false`. Attribute elf, archer, and full-body framing to the user. Preserve silver hair, express moonlight through palette and lighting, and keep the locked framing string intact. Omit gender when unspecified. Use the full-body composition and `2:3` ratio.

Expected prefix, followed by the locked style, style append, framing, and quality:

`a slender elf archer, long silver hair, pointed ears, fitted leather armor with engraved silver clasps, flowing hooded cloak, longbow of pale carved wood, quiver of fletched arrows, poised stance with an arrow half drawn, calm piercing gaze, moon faction, silver and moonlight blue color scheme with soft violet accents, cold moonlight from behind, softly lit woodland behind the figure, vertical full-body character portrait, single character, character as the clear focal point with a readable silhouette, entire figure in frame from head to feet`

### 2. Ambiguous request

Input: `Draw a very powerful character`

Expect `needs_confirmation: true` because both core fields are unclear. Show a proposed settings table and hold the final prompt. For example, propose a human knight in silver plate armor with gold trim and a crimson cape, pointing a steel greatsword forward with a confident expression. Specify a light element with gold and white colors and sky-blue accents, a temple in soft daylight, and half-body 2:3 framing.

Ask up to three questions about species or type, profession or fighting style, and preferred element or faction. Produce the full output once the user confirms or corrects the settings.

### 3. Conflicts with the locks

Input: `Two knights dueling, drawn in chibi style, with the card title "Oath of Flame" written on the image`

Expect `needs_confirmation: true`. Identify all three conflicts:

1. Two knights conflict with the negative term `multiple characters` and the composition term `single character`. Under option A, propose one knight swinging a sword. Option B requires user approval to remove the negative term and relax the single-character rule for this prompt.
2. The requested style conflicts with `chibi, super deformed` and `consistent proportions`. The style has no one-off exception under the shared rules §6. Offer the locked style or the permanent-change procedure for an explicit request to revise the style lock.
3. The title conflicts with `no text`. Place the title during card layout. Keep the lock in force.

Keep the conflicts visible for confirmation. Exclude the title and the requested chibi style from the positive prompt. If the user chooses A, produce a single knight and use the title's meaning as a visual theme, such as `fire oath knight`.

