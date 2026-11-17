---
name: batch-generate
description: Batch-generate consistent vibrant fantasy TCG card art prompts from data/batches/batch.csv by applying the matching card-art skill rules to each row, using the shared card-art style lock.
---

# batch-generate: Batch-generate card prompts

> All paths are relative to `card-art-skills/`. This skill does not establish separate rules: apply the matching skill and the shared style lock to every row.

## 1. When to use

- The user wants prompts for multiple cards at once, such as a request to run a batch, generate in bulk, or generate prompts from `batch.csv`.
- The input is `data/batches/batch.csv` unless the user specifies another CSV, in which case read that file instead.
- For a single image, use the corresponding single-image skill instead.

## 2. Required files

Read these files again before each run:
1. `shared/style_lock.json`, `shared/negative_lock.json`, `shared/assets.json`, and `shared/prompt_rules.md`.
2. The `SKILL.md` corresponding to every `asset_type` present in the CSV:
   | asset_type | Rule source |
   |---|---|
   | `character` | `skills/character-art/SKILL.md` |
   | `weapon` | `skills/weapon-art/SKILL.md` |
   | `scene` | `skills/scene-art/SKILL.md` |
   | `magic` | `skills/magic-art/SKILL.md` |
3. `data/batches/batch.csv`.

## 3. Parsing input

**CSV format:**
- UTF-8, optionally with a BOM; the first row is the header; fields are comma-separated and fields containing commas are enclosed in double quotes.
- Required columns: `card_id,asset_type,name,extra_description`.
- Optional column: `aspect_ratio`, which overrides the default ratio and accepts only ratios listed in `prompt_rules.md` §2.
- Ignore other columns.
- If any required header is missing, stop the entire batch and tell the user in Traditional Chinese which column is missing.

**Row validation (`row` is the CSV line number, with the header on line 1):**

| Check | On failure |
|---|---|
| `card_id` is nonempty | Error; skip the row. |
| `card_id` is unique | Mark the second and subsequent occurrences as errors and skip them. |
| After trimming whitespace and lowercasing, `asset_type` is `character`, `weapon`, `scene`, or `magic` | Error; skip the row. |
| At least one of `name` and `extra_description` is nonempty | Error; skip the row. |
| `aspect_ratio`, if supplied, is valid | Warning; use the default ratio and record it in `notes`. |

**Processing each row:**
1. Combine `name` and `extra_description` as the input.
2. For `name`, first match `name`, `aliases`, or `id` in the corresponding array in `assets.json`. Use the asset settings when matched. Otherwise, use only the literal meaning of `name` (for example, the Chinese input meaning “frost staff” becomes `frost staff`). Do **not** put proper nouns, such as personal names or transliterated card names, into the prompt; suggest registering them in `assets.json` in `notes`.
3. Apply §§3–5 of the matching SKILL.md for the `asset_type` (fields, core fields, assembly template, and immutable items), and `prompt_rules.md` §§3–6.
4. **Do not interrupt the batch:** for rows meeting the conditions in `prompt_rules.md` §7, still generate a best-guess prompt, set `needs_confirmation: true`, and explain the reasons and assumptions in Traditional Chinese in `notes`.
5. **Conflicting rows:** always choose option A, preserving the lock; **do not apply any `lock_exceptions`**, and record the conflict in `notes`. Rerun a row only after the user subsequently agrees in the conversation to an exception for that specific `card_id`.
6. Within a batch, descriptions of the same asset (same asset `id` or same `name`) must have a consistent appearance.

## 4. Fields to complete

Use the same fields as §4 of each type's SKILL.md. Complete every field for the row's type before assembling the prompt.

## 5. Do not change without authorization

- All items in §5 of each type's SKILL.md.
- `data/batches/batch.csv` itself: it is read-only; do not correct user data, and record problems in the report.
- Do not apply any lock exceptions in batch mode (see §3, step 5).

## 6. Output format

**1) `output/prompts/batch_prompts.json`**
```json
{
  "meta": {
    "source": "data/batches/batch.csv",
    "generated_at": "<ISO 8601 with timezone>",
    "style_lock_version": "<style_lock._meta.version>",
    "negative_lock_version": "<negative_lock._meta.version>",
    "total_rows": 0,
    "generated": 0,
    "needs_confirmation": 0,
    "errors": 0
  },
  "items": [
    {
      "card_id": "",
      "asset_type": "",
      "final_prompt": "",
      "negative_prompt": "",
      "chatgpt_prompt": "",
      "aspect_ratio": "",
      "suggested_loras": {},
      "needs_confirmation": false,
      "notes": ""
    }
  ],
  "errors": [ { "row": 0, "card_id": "", "reason": "" } ]
}
```
- Keep `items` in CSV order.
- Assemble `final_prompt` and `negative_prompt` according to `prompt_rules.md` §§3 and 4 respectively; construct `chatgpt_prompt` according to §12.
- Follow `prompt_rules.md` §8 for `suggested_loras`, with the keys `style`, `<asset_type>`, and `assets`.

**2) `output/prompts/batch_prompts.csv`**
- Column order: `card_id,asset_type,final_prompt,negative_prompt,aspect_ratio,suggested_loras,needs_confirmation,notes,chatgpt_prompt`. Enclose the multiline `chatgpt_prompt` in double quotes.
- Use UTF-8 **with BOM** so Excel displays Chinese correctly. Enclose every field in double quotes and escape each double quote inside a field by doubling it.
- Store `suggested_loras` as a single-line JSON string.
- **Convert JSON to CSV programmatically** using Node.js or Python; do not write it by hand, to avoid escaping errors.

**3) `output/reports/batch_report.md`** (Traditional Chinese)
- Statistics: total rows, successes, rows needing confirmation, errors.
- For each row needing confirmation: `card_id`, reason, current assumptions, and suggested questions for the user.
- For each error row: line number, `card_id`, reason, and how to correct it.

**4) `output/prompts/batch_chatgpt.md`** (for pasting into ChatGPT)
- Start with the source, generation time, style_lock and negative_lock versions, and instructions to copy one block per card into ChatGPT.
- Give each card a section headed `## <card_id>（<asset_type>）`, followed by a ` ```text ` block containing `chatgpt_prompt`.
- For a card with `needs_confirmation: true`, add `⚠ 需確認：<notes>` under the heading. This is a literal Traditional Chinese output string meaning “confirmation needed.”
- Preserve CSV order; omit error rows.

**Overwrite rule:** Before writing new output files, rename existing files to `batch_prompts.<YYYYMMDD-HHMMSS>.json` or `.csv` respectively; do the same for the report and `batch_chatgpt.md`.

**Completion report (Traditional Chinese):** Provide the four output paths, statistics, and list of rows requiring confirmation, and ask whether the user wants to correct or supplement those rows.

**Prohibited:** Do not call any image generation API or train LoRA; generate prompts only.

## 7. Example input and output

The examples below are translated into English for documentation. Actual input supports Traditional Chinese, and generated `notes` must be in Traditional Chinese. The same scenarios and expected behavior apply.

**Input `data/batches/batch.csv`:**
```csv
card_id,asset_type,name,extra_description
C001,character,Grum,"orc shaman holding a magic wand, fire element"
W001,weapon,Frost Staff,"ancient wood shaft, ice crystal set at the top"
S001,scene,Frozen Ruins,"ancient ruins covered in ice and snow, at dusk"
M001,magic,Flame Burst,flames erupting outward from a magic circle on the ground
M002,magic,Mysterious Power,
```

**Output (excerpt of `items` in `batch_prompts.json`):**
```json
[
  {
    "card_id": "C001",
    "asset_type": "character",
    "final_prompt": "a muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, … , game-ready card illustration",
    "negative_prompt": "extra limbs, extra fingers, … , photorealistic, 3d render, chibi, super deformed",
    "chatgpt_prompt": "Create an image from this prompt, used as written:\n\na muscular orc tribal shaman, … , game-ready card illustration\n\nAvoid: extra limbs, extra fingers, … , chibi, super deformed\n\nAspect ratio: 2:3 portrait (1024x1536)",
    "aspect_ratio": "2:3",
    "suggested_loras": { "style": { "file": "", "weight": null, "status": "reserved" }, "character": { "file": "", "weight": null, "status": "reserved" }, "assets": [] },
    "needs_confirmation": false,
    "notes": "The name Grum is a proper noun not registered in assets.json and was omitted from the prompt; appearance was completed from extra_description. Register it in assets.json to fix its appearance."
  },
  {
    "card_id": "M002",
    "asset_type": "magic",
    "final_prompt": "an arcane vortex spell effect, … , game-ready card illustration",
    "negative_prompt": "…, character portrait focus, face close-up, indistinct effect silhouette",
    "aspect_ratio": "2:3",
    "suggested_loras": { "style": { "file": "", "weight": null, "status": "reserved" }, "magic": { "file": "", "weight": null, "status": "reserved" }, "assets": [] },
    "needs_confirmation": true,
    "notes": "The core fields (element and effect form) cannot be determined from Mysterious Power; an arcane vortex is assumed temporarily. Please confirm the element and form."
  }
]
```

See `output/prompts/batch_prompts.json`, `output/prompts/batch_prompts.csv`, `output/prompts/batch_chatgpt.md`, and `output/reports/batch_report.md` for complete output.

## 8. Test examples

### Test 1: All four types, all explicit

**Input:**
```csv
card_id,asset_type,name,extra_description
T101,character,elf archer,"silver hair, full body, standing in moonlight"
T102,weapon,flame-rift greatsword,"held by a knight with the greatsword as subject, wrapped in black flames, cracks in the blade"
T103,scene,sky castle,castle floating above a sea of clouds
T104,magic,lightning spear,lightning spear descending from the sky and striking the ground
```

**Expected:**
- `generated: 4`, `needs_confirmation: 0`, `errors: 0`.
- Each `final_prompt` includes `style_lock.style`, the type's `style_append`, `framing_override`, and `style_lock.quality` verbatim.
- T103 uses the scene framing override (including `single dominant focal subject filling much of the frame`), not global `framing`.
- T102's `final_prompt` uses diagonal placement and thematic environment framing, with no weapon echo in the background; its `negative_prompt` includes the weapon append terms, including `double-exposure weapon echo, duplicate weapon silhouette, oversized weapon shadow in the background`.
- T102 retains the knight holding the sword, black flames, and cracks. The holder is supporting; holding the weapon triggers neither confirmation nor lock_exceptions, and the negative prompt does not exclude people or hands.
- Every `final_prompt` contains one `… color scheme with … accents` phrase.
- Each row has a `chatgpt_prompt` made from `final_prompt` + `Avoid: <negative_prompt>` + the aspect-ratio text, and all four appear in `batch_chatgpt.md`.
- The CSV opens correctly in Excel and Chinese `notes` are not corrupted.

### Test 2: Ambiguous and conflicting rows

**Input:**
```csv
card_id,asset_type,name,extra_description
T201,magic,Mysterious Power,
T202,scene,market,"bustling market, many people shopping"
T203,character,nameless hero,very strong
```

**Expected:**
- All three rows generate best-guess prompts with `needs_confirmation: true`.
- T202 uses option A (a market without people); `negative_prompt` retains `people, crowd` and no `lock_exceptions` are applied.
- T203's `notes` state that neither species nor profession can be determined.
- `batch_report.md` lists suggested questions for each of the three rows.

### Test 3: Error rows

**Input:**
```csv
card_id,asset_type,name,extra_description
T301,monster,slime,green slime creature
,weapon,shortsword,ordinary iron shortsword
T303,Weapon,shortsword,ordinary iron shortsword
T303,scene,cave,
T305,magic,,
```

**Expected:**
- T301: invalid `asset_type` → error (`notes` recommends changing it to `character`).
- Line 3: empty `card_id` → error.
- T303 (line 4): `Weapon` is valid after lowercasing → generate normally.
- T303 (line 5): duplicate `card_id` → error.
- T305: both `name` and `extra_description` are empty → error.
- `generated: 1`, `errors: 4`; error rows appear only in `errors` and the report, not in `items`.
