---
name: evolution-art
description: Write independent English card-art prompts for a character's evolution, growth, aging, or mechanical modification. Use for base/evolved pairs, multi-period sequences, and inserting a missing period into an existing character timeline.
---

# Character evolution comparison

Default to two periods for a base/evolved comparison; follow requested counts or named periods chronologically, one character per image. For an existing timeline, deliver only the requested missing period. Standalone illustrations, outfit changes, and same-form alternate art use [character-art](../character-art/SKILL.md); transformation effects use [magic-art](../magic-art/SKILL.md) when the spell is the focal point.

## Establish identity and change

1. Read the [shared rules](../../shared/prompt_rules.md) and files in §1; use character-art §3–4 for parsing and fields. All periods use `asset_type: "character"` and its locked strings, composition, ratio, and LoRA rules. If a base form was supplied, start from it; retain matched asset descriptions and every `locked_traits` entry. Resolve conflicts under shared §6–7 before final output.
2. Write shared traits as drawable English phrases and reuse them verbatim in corresponding fields. Include necessary shape, location, and material. If unspecified, select recognizable traits from the existing description and label added details by source. Distinguish the character's own left/right from the viewer's using a consistent viewpoint.
   - **Markings:** state body location, total count, and body-relative arrangement, such as one dorsally and two side by side ventrally. Reuse this sentence across periods.
   - **Color:** bind accents to a body part, object, or scenery in the shared color-scheme sentence. Repeat any exclusive location restriction with the color name in every period; specify new parts' colors and materials to prevent accent spillover.
3. Describe each period's visible state along a continuous change. Middle periods must differ from both neighbors. Show growth/aging through facial structure, posture, and physique; show mechanical modification through a component's construction and retained repairs. Include only marks already acquired; old age may mean frailty. Put changes in character fields, not just stage names.
   - **Metamorphosis/reconstruction:** identify surviving visible traits. If a marking moves to a new structure, keep its design consistent, specify each period's location, and record inferred correspondence in `assumptions`. Resolve locked-location conflicts under shared rules. For covered identifiers, describe external clues and limits of identification.

## Make the changes visible

Frame both identifiers and changing parts. Include low markings; open hands to expose structures between fingers. Give notches an opening direction and visible background, with hair/clothing moved aside. For small identifiers, first adjust pose or prop position, then specify relative size and contrast against the background. Keep palette, clothing, viewpoint, and setting consistent unless history or the user requires changes.

For fully nonhuman stages prone to humanoid interpretation, describe body segments, limbs, and support/contact points, such as feet gripping a branch or the underside resting on it. Keep the character style lock and use:

`[vertical|horizontal] full-body creature illustration, single character, [creature or stage] as the clear focal point with a readable silhouette, [visible outer limits] fully in frame`

Define full-body limits by anatomy: a pupa's attachment stalk through abdominal tip; a winged insect's antennae, both wingtips, and abdominal tip. For a closed shell without exposed head/feet, use `entire closed shell in frame from attachment point to tip`. Do not add a face, limbs, or props before they appear. When silhouettes widen, fit the widest edges with background margin at both ends; camera distance may vary at the same ratio.

## Insert a period into an existing timeline

- Read the nearest established earlier/later cards and identify the exact versions continued. Keep written canon separate from visual evidence: retain prompt-defined traits even when hidden or misplaced in art, and report the discrepancy. Hidden traits with unspecified history remain unknown.
- Separate persistent identity from dated events: what exists earlier, first appears now, and persists later? If onset contradicts a card, cite that card/feature and follow shared §6–7. Offer established history or a separate continuity. Label approved branches and their divergence; reuse a compatible later endpoint only when both visible state and history agree. If a later card fixes a scar's shape, define its new-to-healed extent using anatomical landmarks.
- Compare the new period against both neighbors in the settings table, show its distinguishing evidence on the character, and record inferred transitions in `assumptions`. Save under a distinct slug; preserve existing IDs and filenames.

## Deliver independent prompts

First provide a Traditional Chinese table of shared traits, period changes, and field sources. For each requested period, in order, deliver complete shared §9 JSON plus the §12 paste-ready block. Use `skill: "evolution-art"`, `asset_type: "character"`, and character-art fields; assemble each independently under shared §3–4. Every prompt must identify the character, marking locations, and current appearance without another period's text.

| Request | `fields.form` | Saved slug suffix (§10) |
|---|---|---|
| Default pair | `base`, `evolved` | `-base`, `-evolved` |
| Named periods, including a single insertion | English period name | Sequence and stage, e.g. `-02-pupa` |

## Verify generated trials

When image testing is requested, generate an independent first-look comparison once per period from its complete text prompt, without reference images. Compare adjacent periods for clear progression and model-introduced differences. Check identifier side, shape, visibility, count/arrangement in each row, and the whole image for restricted accent colors. Record deviations and separate retries from first runs.

For repeatability tests:

1. Select the source record and declare visible checks before rendering. Keep the complete prompt, ratio, and reference-image settings identical for every independent run; retain every result.
2. Score each feature per image by side, count, and placement, not presence alone. Mark hidden/ambiguous details unverified. Separate inferred preferences from explicit restrictions, especially exclusive color placement.
3. Keep changed wording in a separate comparison. Report observed counts with sample size; do not extrapolate a success rate.
