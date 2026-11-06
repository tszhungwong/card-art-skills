---
name: magic-art
description: Generate consistent vibrant fantasy TCG magic effect, skill effect and spell card illustrations from short user descriptions, using the shared card-art style lock.
---

# magic-art：魔法效果／技能特效／法術卡立繪

> 路徑皆相對於 `card-art-skills/`。本 skill 只決定「畫什麼」與「構圖」；「怎麼畫」一律來自 `shared/style_lock.json`。

## 1. 何時使用

**使用：** 要生成以「法術或特效本身」為主體的卡牌立繪，例如爆發、漩渦、光束、護盾、詛咒、召喚陣、治癒光、隕石雨。
- 「我要生成一個火焰爆發魔法」
- 「從天而降的雷電長槍」

**不使用（改用其他 skill）：**
- 施法者才是主體，魔法只是陪襯 → `character-art`
- 被召喚出的生物是主體 → `character-art`
- 主體是法器本身 → `weapon-art`
- 主體是被魔法改變的地點（例如被詛咒的森林）→ `scene-art`
- 一次處理 CSV 多列 → `batch-generate`

## 2. 必須讀取的共用檔案

每次執行前重新讀取：
1. `shared/style_lock.json`（唯讀）：`style`、`quality`、`by_type.magic`（`style_append` 與 `framing_override`；白熱核心、速度線、動態模糊背景都由這裡固定提供）
2. `shared/negative_lock.json`（唯讀）：`negative`、`append_by_type.magic`（排除角色特寫、平光）
3. `shared/assets.json`：`magic_effects`、`loras.style`、`loras.magic`
4. `shared/prompt_rules.md`

## 3. 如何解析使用者輸入

1. 拆出使用者明確提供的元素、形態、顏色、施法來源、生物，對應到 §4 欄位。
2. 比對 `assets.json → magic_effects` 的 `name`／`aliases`／`id`，命中時帶入資產設定。
3. 抽出排除需求，英譯後加入負面 prompt。
4. 判斷是否加入生物：使用者提到、或元素有代表性的化身（火 → 火鳥、雷 → 雷獸）時可加，但特效仍是主體。人形施法者預設只露手或背光剪影；若使用者想強調施法者的臉，屬於 skill 歸屬不明（`prompt_rules.md` §7 第 4 點），需確認。
5. 檢查衝突，判斷是否需確認，然後補全並組裝。

## 4. 必須補全的欄位

| key | 欄位 | 核心 | 補全原則（英文寫入 prompt） |
|---|---|---|---|
| `element` | 魔法元素 | ★ | fire, frost, lightning, shadow, holy light, poison, arcane, earth, wind, water… |
| `form` | 效果形態 | ★ | burst, vortex, beam, spear, shield dome, chains, summoning circle, meteor rain… |
| `color` | 顏色 | | 配色句：`[元素主色系] color scheme with [互補色] accent glow` |
| `lighting` | 光線 | | 特效本身是主光源，寫出它照亮了什麼（surrounding smoke, cracked ground, the creature's silhouette） |
| `particles_runes` | 粒子或符文 | | embers, sparks, ice shards, glyph rings, runic circle…，數量適中 |
| `source` | 施法來源 | | 預設無可見施法者：魔法陣、地面、天空裂縫、法器尖端；要有施法者時只露手或背光剪影 |
| `creature` | 相關生物（選填） | | 施法者、受術者或特效化身的生物（phoenix, thunder beast, sea serpent）；體型可大，但要融入或位於特效中，不可成為特寫主角 |
| `background` | 背景 | | 一句環境暗示（storm clouds, ocean surface, cavern, battlefield）；暗色、動態模糊由 framing 提供 |
| `framing` | 構圖 | | 固定：特效為中心焦點、形狀可辨識 |

**核心規則：** `element` 與 `form` 至少要能判斷一項，否則需確認。

**組裝對應（`prompt_rules.md` §3）：**
- subject：`a [element] [form] spell effect`
- details：`form` 的細節 → `color` → `lighting` → `particles_runes` → `source` → `creature`（若有）→ `background`
- composition（固定模板，只替換 `[ ]`）：
  `[vertical|horizontal] composition, spell effect as the central focal point, clear readable shape of the [form noun], energy radiating outward from the center`
- framing：使用 `style_lock.by_type.magic.framing_override`（逐字）
- 比例：預設 `2:3`；配合參考圖框 `5:4`。

## 5. 不可擅自更改的欄位

- `style_lock.json`、`negative_lock.json` 的所有字串（包含 magic 的 framing override 與追加負面詞）
- composition 模板中的 `spell effect as the central focal point`
- 使用者明確指定的元素、形態、顏色、施法來源、生物
- 命中資產的 `description`、`locked_traits`、`trigger_word`

## 6. 輸出格式

依 `prompt_rules.md` §9。`fields` 使用 §4 的 key；`suggested_loras` 的鍵為 `style`、`magic`、`assets`。

## 7. 範例輸入與範例輸出

**輸入：**
```
我要生成一個火焰爆發魔法
```

**輸出：**

| 欄位 | 設定 | 來源 |
|---|---|---|
| 魔法元素 | 火焰 | 使用者 |
| 效果形態 | 爆發：從單一點向外的放射狀火焰噴發 | 使用者＋細節補全 |
| 顏色 | 熾橘與深紅主色系，電光藍互補發光 | 自動補全 |
| 光線 | 噴發是主光源，照亮周圍煙霧 | 自動補全 |
| 粒子或符文 | 飛散的火星、熔融液滴、旋轉的火焰符文 | 自動補全 |
| 施法來源 | 焦土上碎裂發光的符文陣 | 自動補全 |
| 相關生物 | 無 | 預設 |
| 背景 | 煙霧瀰漫的焦土戰場 | 自動補全 |
| 構圖 | 直向 2:3，效果居中 | 預設 |

```json
{
  "skill": "magic-art",
  "asset_type": "magic",
  "user_input": "我要生成一個火焰爆發魔法",
  "needs_confirmation": false,
  "confirmation_reasons": [],
  "fields": {
    "element": "fire",
    "form": "burst, explosive radial eruption of flames bursting outward from a single point",
    "color": "blazing orange and crimson color scheme with electric blue accent glow",
    "lighting": "the eruption as the main light source illuminating the surrounding smoke",
    "particles_runes": "flying embers, molten droplets and spinning fire glyphs",
    "source": "erupting from a fractured glowing runic circle on scorched ground",
    "creature": "",
    "background": "smoky scorched battlefield in the background",
    "framing": "vertical, effect centered"
  },
  "auto_filled": ["color", "lighting", "particles_runes", "source", "creature", "background", "framing"],
  "assumptions": ["未指定施法者或生物：以地面符文陣作為施法來源，不畫角色或生物。若想加入火鳥等化身，請告訴我。"],
  "matched_assets": [],
  "positive_prompt": "a fire burst spell effect, explosive radial eruption of flames bursting outward from a single point, blazing orange and crimson color scheme with electric blue accent glow, the eruption as the main light source illuminating the surrounding smoke, flying embers, molten droplets and spinning fire glyphs, erupting from a fractured glowing runic circle on scorched ground, smoky scorched battlefield in the background, vertical composition, spell effect as the central focal point, clear readable shape of the burst, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, anime-style magic VFX, crisp sharp-edged energy shapes, manga speed lines, white-hot overexposed core with intense bloom, explosive dynamic composition, effect fills most of the frame, dim colored background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative_prompt": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, character portrait focus, face close-up, dull flat lighting",
  "chatgpt_prompt": "Create an image from this prompt, used as written:\n\na fire burst spell effect, explosive radial eruption of flames bursting outward from a single point, blazing orange and crimson color scheme with electric blue accent glow, the eruption as the main light source illuminating the surrounding smoke, flying embers, molten droplets and spinning fire glyphs, erupting from a fractured glowing runic circle on scorched ground, smoky scorched battlefield in the background, vertical composition, spell effect as the central focal point, clear readable shape of the burst, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, anime-style magic VFX, crisp sharp-edged energy shapes, manga speed lines, white-hot overexposed core with intense bloom, explosive dynamic composition, effect fills most of the frame, dim colored background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration\n\nAvoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, character portrait focus, face close-up, dull flat lighting\n\nAspect ratio: 2:3 portrait (1024x1536)",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": {
    "style": { "file": "", "weight": null, "status": "reserved" },
    "magic": { "file": "", "weight": null, "status": "reserved" },
    "assets": []
  },
  "lock_exceptions": [],
  "style_lock_version": "0.2.0",
  "negative_lock_version": "0.2.0"
}
```

**ChatGPT 貼上用（`prompt_rules.md` §12）：**
```text
Create an image from this prompt, used as written:

a fire burst spell effect, explosive radial eruption of flames bursting outward from a single point, blazing orange and crimson color scheme with electric blue accent glow, the eruption as the main light source illuminating the surrounding smoke, flying embers, molten droplets and spinning fire glyphs, erupting from a fractured glowing runic circle on scorched ground, smoky scorched battlefield in the background, vertical composition, spell effect as the central focal point, clear readable shape of the burst, energy radiating outward from the center, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art, anime-style magic VFX, crisp sharp-edged energy shapes, manga speed lines, white-hot overexposed core with intense bloom, explosive dynamic composition, effect fills most of the frame, dim colored background with motion blur, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, character portrait focus, face close-up, dull flat lighting

Aspect ratio: 2:3 portrait (1024x1536)
```

## 8. 測試範例

### 測試 1：明確輸入（直接輸出）

**輸入：** `從天而降的雷電長槍，擊中地面`

**預期：**
- `needs_confirmation: false`
- `element` = lightning、`form` = 長槍形雷擊（使用者）；施法來源補為天空中裂開的雲層
- 直向 2:3 正好符合由上而下的動線
- 預期 prompt 前段：
  `a lightning spear spell effect, a massive spear-shaped bolt of lightning plunging from the sky and striking the ground, electric yellow and white color scheme with violet accent glow, the impact flash lighting up the cracked earth, scattering sparks and shattered rock fragments, glowing runes branching across the ground from the impact point, descending from a split in dark storm clouds, stormy sky in the background, vertical composition, spell effect as the central focal point, clear readable shape of the spear, energy radiating outward from the center`

### 測試 2：模糊輸入（需確認）

**輸入：** `一個很厲害的魔法`

**預期：**
- `needs_confirmation: true`；原因：`element` 與 `form` 都無法判斷
- **不輸出最終 prompt**
- 輸出「預計補全設定」表，例如：暗影元素／漩渦／深紫與洋紅主色系、金色互補發光／地面魔法陣／2:3
- 問題（最多 3 個）：1) 元素？ 2) 效果形態（爆發、光束、護盾、召喚陣…）？ 3) 要不要加入生物化身？

### 測試 3：skill 歸屬不明

**輸入：** `一位法師施放治癒之光，要看得到法師的臉`

**預期：**
- `needs_confirmation: true`；原因：主體可能是魔法也可能是角色（`prompt_rules.md` §7 第 4 點）
- 選項：
  - A（建議）：維持 `magic-art`，治癒光柱為主體，法師在下方呈背光半身剪影，臉部可見但佔畫面比例小（不違反 `face close-up`，不需要例外）
  - B：改用 `character-art`，法師為主體，治癒光作為 `background_magic`
- 不可默默把法師臉部特寫放進 magic prompt
