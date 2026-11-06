# ChatGPT 貼上用 prompt

- 來源：`data/batches/batch.csv`
- 生成時間：2026-11-06T17:39:20+08:00
- style_lock 版本：0.2.0；negative_lock 版本：0.2.0
- 用法：每張卡複製一個區塊，整段貼到 ChatGPT。生成的圖放進 `output/images/`，可再請 agent 比對、調整。
- 不要附上 `data/references/` 的參考圖。

## C001（character）

```text
Create an image from this prompt, used as written:

a muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones, holding a gnarled wooden magic wand topped with a glowing faceted fire crystal, raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy, fierce confident grin, bared tusks, fire element, blazing orange and crimson color scheme with teal accent glow, swirling embers and a glowing runic circle behind the shoulders, vertical half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, anime character art, clean lineart, cel shading with soft gradients, large expressive eyes with layered highlights, glossy hair highlights, gold trim and gemstone accents, dynamic diagonal composition, character fills the frame, softly blurred thematic background with bokeh and sparkles, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, chibi, super deformed

Aspect ratio: 2:3 portrait (1024x1536)
```

## W001（weapon）

```text
Create an image from this prompt, used as written:

a frost magic staff, pale ancient wood shaft wrapped in polished silver bands, gold fittings, crowned with a large faceted ice crystal, icy blue and white color scheme with warm gold accent glow, frost runes engraved in a spiral along the shaft, glowing bright cyan, swirling cold mist and sparkling ice shards around the crystal head, set against a frozen glacier valley under a clear sky, single staff as the main subject, staff large and dominant in the frame, crystal head and key details clearly visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, semi-realistic glossy hard-surface rendering, polished reflective metal, faceted gemstones with star glints, glowing aura outlining the weapon, weapon placed diagonally across the frame, translucent oversized double-exposure echo of the same weapon in the background, softly blurred thematic environment matching the weapon's element, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, person, character, hands, hand holding weapon, wielder, weapon pile, weapon rack

Aspect ratio: 2:3 portrait (1024x1536)
```

## S001（scene）

```text
Create an image from this prompt, used as written:

ancient ruins covered in snow and ice, a towering broken stone archway carved with glowing runes, half-buried in snow, foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains, dusk, light snowfall, cold wind carrying sparkling snow, solemn and awe-inspiring atmosphere, icy blue and white color scheme with warm gold accent glow, golden dusk light shining through the arch, vertical environment illustration, the archway as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast, single dominant landmark filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, people, crowd, human figure, close-up portrait, flat empty background

Aspect ratio: 2:3 portrait (1024x1536)
```

## M001（magic）

```text
Create an image from this prompt, used as written:

a fire burst spell effect, explosive radial eruption of flames bursting outward from a single point, blazing orange and crimson color scheme with electric blue accent glow, the eruption as the main light source illuminating the surrounding smoke, flying embers, molten droplets and spinning fire glyphs, erupting from a fractured glowing runic circle on scorched ground, smoky scorched battlefield in the background, vertical composition, spell effect as the central focal point, clear readable shape of the burst, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, anime-style magic VFX, crisp sharp-edged energy shapes, manga speed lines, white-hot overexposed core with intense bloom, explosive dynamic composition, effect fills most of the frame, dim colored background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, character portrait focus, face close-up, dull flat lighting

Aspect ratio: 2:3 portrait (1024x1536)
```

## M002（magic）

⚠ 需確認：核心欄位（元素、效果形態）無法從「神秘力量」判斷；暫以奧術漩渦（arcane vortex）推測，請確認元素與效果形態。

```text
Create an image from this prompt, used as written:

an arcane vortex spell effect, a swirling vortex of energy spiraling around a glowing core, deep violet and magenta color scheme with gold accent glow, the core as the main light source casting a glow on the surrounding mist, drifting motes of light and floating runic glyph rings, rising from a glowing runic circle below, misty void in the background, vertical composition, spell effect as the central focal point, clear readable shape of the vortex, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, anime-style magic VFX, crisp sharp-edged energy shapes, manga speed lines, white-hot overexposed core with intense bloom, explosive dynamic composition, effect fills most of the frame, dim colored background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, character portrait focus, face close-up, dull flat lighting

Aspect ratio: 2:3 portrait (1024x1536)
```
