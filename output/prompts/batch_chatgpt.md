# ChatGPT 貼上用 prompt

- 來源：`data/batches/batch.csv`
- 生成時間：2026-11-16T14:34:06+08:00
- style_lock 版本：0.4.0；negative_lock 版本：0.3.2
- 用法：每張卡複製一個區塊，整段貼到 ChatGPT。生成的圖放進 `output/images/`，可再請 agent 比對、調整。
- 不要附上 `data/references/` 的參考圖。

## C001（character）

```text
Create an image from this prompt, used as written:

a muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones, holding a gnarled wooden magic wand topped with a faceted fire crystal with a restrained inner glow, raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy, fierce confident grin, bared tusks, fire element, blazing orange and crimson color scheme with teal accents, a few embers near the wand tip, rocky canyon backdrop, vertical half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime character art, clean lineart, cel shading with soft gradients, expressive eyes with detailed highlights, soft hair highlights, dynamic diagonal composition, character fills the frame, detailed thematic environment background with atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, chibi, super deformed

Aspect ratio: 2:3 portrait (1024x1536)
```

## W001（weapon）

```text
Create an image from this prompt, used as written:

a frost magic staff, pale ancient wood shaft wrapped in polished silver bands, gold fittings, crowned with a large faceted ice crystal, icy blue and white color scheme with warm gold accents, frost runes engraved in a spiral along the shaft, inlaid with cyan enamel, a thin curl of cold mist close to the crystal head, set against a frozen glacier valley under a clear sky, single staff as the main subject, staff large and dominant in the frame, crystal head and key details clearly visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, detailed hard-surface rendering, material-appropriate reflections, faceted gemstones with restrained highlights, weapon placed diagonally across the frame, softly blurred thematic environment matching the weapon's element, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, weapon pile, weapon rack, double-exposure weapon echo, duplicate weapon silhouette, oversized weapon shadow in the background

Aspect ratio: 2:3 portrait (1024x1536)
```

## S001（scene）

```text
Create an image from this prompt, used as written:

ancient ruins covered in snow and ice, a towering broken stone archway carved with weathered runes, half-buried in snow, foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains, dusk, light snowfall, cold wind carrying fine snow, solemn and awe-inspiring atmosphere, icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch, vertical environment illustration, the archway as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast, single dominant focal subject filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, people, crowd, human figure, close-up portrait, flat empty background

Aspect ratio: 2:3 portrait (1024x1536)
```

## M001（magic）

```text
Create an image from this prompt, used as written:

a fire burst spell effect, explosive radial eruption of flames bursting outward from a single point, blazing orange and crimson color scheme with electric blue accents, the flames lighting nearby smoke with controlled brightness and visible inner flame layers, a few flying embers and molten droplets, erupting from a fractured runic circle on scorched ground, smoky scorched battlefield in the background, vertical composition, spell effect as the central focal point, clear readable shape of the burst, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime-style magic VFX, clearly defined effect shapes, layered color and texture, controlled luminance preserving core detail, emission appropriate to the spell's element, explosive dynamic composition, effect fills most of the frame, element-appropriate background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, character portrait focus, face close-up, indistinct effect silhouette

Aspect ratio: 2:3 portrait (1024x1536)
```

## M002（magic）

⚠ 需確認：核心欄位（元素、效果形態）無法從「神秘力量」判斷；暫以奧術漩渦（arcane vortex）推測，請確認元素與效果形態。

```text
Create an image from this prompt, used as written:

an arcane vortex spell effect, a swirling vortex of energy spiraling around a bright core, deep violet and magenta color scheme with gold accents, the core lighting the surrounding mist with controlled brightness and visible inner swirl layers, a few drifting fragments of shattered stone, rising from a runic circle on cracked ground, misty ruins in the background, vertical composition, spell effect as the central focal point, clear readable shape of the vortex, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime-style magic VFX, clearly defined effect shapes, layered color and texture, controlled luminance preserving core detail, emission appropriate to the spell's element, explosive dynamic composition, effect fills most of the frame, element-appropriate background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, character portrait focus, face close-up, indistinct effect silhouette

Aspect ratio: 2:3 portrait (1024x1536)
```
