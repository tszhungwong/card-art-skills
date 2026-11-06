---
name: scene-art
description: Generate consistent vibrant fantasy TCG location, environment and field card illustrations from short user descriptions, using the shared card-art style lock.
---

# scene-art：場地／環境／地點卡立繪

> 路徑皆相對於 `card-art-skills/`。本 skill 只決定「畫什麼」與「構圖」；「怎麼畫」一律來自 `shared/style_lock.json`。

## 1. 何時使用

**使用：** 要生成以「地點或環境」為主體的卡牌立繪，例如遺跡、城堡、森林、火山、海港、地下城、浮空島。
- 「我要生成一個被冰雪覆蓋的古代遺跡」
- 「一座漂浮在雲海上的空中城堡」

**不使用（改用其他 skill）：**
- 環境只是角色背景，角色才是主體 → `character-art`
- 主體是正在發生的法術效果（例如隕石雨本身）→ `magic-art`
- 一次處理 CSV 多列 → `batch-generate`

## 2. 必須讀取的共用檔案

每次執行前重新讀取：
1. `shared/style_lock.json`（唯讀）：`style`、`quality`、`by_type.scene`（`style_append` 與 `framing_override`，後者取代全域 `framing`）
2. `shared/negative_lock.json`（唯讀）：`negative`、`append_by_type.scene`（排除人物、人群、特寫、空白背景）
3. `shared/assets.json`：`scenes`、`loras.style`、`loras.scene`
4. `shared/prompt_rules.md`

## 3. 如何解析使用者輸入

1. 拆出使用者明確提供的地點、時間、天氣、地標、氛圍、顏色、生物，對應到 §4 欄位。
2. 比對 `assets.json → scenes` 的 `name`／`aliases`／`id`，命中時帶入資產設定。
3. 抽出排除需求，英譯後加入負面 prompt。
4. 判斷比例：使用者說「橫向」「全景」「寬景」→ `3:2`；否則用預設 `2:3`。
5. 檢查衝突：人物、人群在負面鎖定中，依 `prompt_rules.md` §6 處理。生物（鳥、蝙蝠、巨獸…）**不屬於衝突**。
6. 判斷是否需確認，然後補全並組裝。

## 4. 必須補全的欄位

| key | 欄位 | 核心 | 補全原則（英文寫入 prompt） |
|---|---|---|---|
| `location_type` | 地點類型 | ★ | 具體地點與狀態（ancient ruins covered in snow, volcanic fortress, sunken temple） |
| `landmark` | 主要地標 | | **單一主導地標**，佔畫面大面積（a towering broken archway, a giant ancient tree, a floating citadel） |
| `layers` | 前景／中景／背景 | | 三層都要寫：foreground…, midground…, background… |
| `time_weather` | 時間與天氣 | | 選能強化氛圍與色調的組合（sunset + drifting clouds, night + moonlight）；以環境光源照明，只有題材需要時才加入發光地標、符文或極光 |
| `mood` | 氛圍 | | 1–2 個形容詞，預設偏奇幻壯觀（wondrous, awe-inspiring, mysterious, ominous） |
| `color` | 色彩 | | 配色句：`[主導環境色調] color scheme with [互補色] accents`；每張只有一個主導色調 |
| `creatures` | 生物（選填） | | 使用者提到，或地點有代表性生物時才加；體型小於地標（flock of birds, distant sea serpent）。不加人類 |
| `framing` | 構圖 | | 直向 2:3 時地標放中上方；橫向 3:2 時地標放中央或三分線 |

**核心規則：** `location_type` 必須能判斷，否則需確認。

**組裝對應（`prompt_rules.md` §3）：**
- subject：`location_type` 加上 `landmark`
- details：`layers`（foreground → midground → background）→ `time_weather` → `mood` → `color` → `creatures`（若有）
- composition（固定模板，只替換 `[ ]`）：
  `[vertical|horizontal] environment illustration, [landmark noun] as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background`
- framing：使用 `style_lock.by_type.scene.framing_override`（逐字）
- 比例：預設 `2:3`；橫向或全景 `3:2`；配合參考圖框 `5:4`。

## 5. 不可擅自更改的欄位

- `style_lock.json`、`negative_lock.json` 的所有字串（包含 scene 的 framing override 與追加負面詞）
- composition 模板中的單一主導地標與前景／中景／背景三層結構
- 使用者明確指定的地點、時間、天氣、地標、色彩、生物
- 命中資產的 `description`、`locked_traits`、`trigger_word`

## 6. 輸出格式

依 `prompt_rules.md` §9。`fields` 使用 §4 的 key；`suggested_loras` 的鍵為 `style`、`scene`、`assets`。

## 7. 範例輸入與範例輸出

**輸入：**
```
我要生成一個被冰雪覆蓋的古代遺跡
```

**輸出：**

| 欄位 | 設定 | 來源 |
|---|---|---|
| 地點類型 | 被冰雪覆蓋的古代遺跡 | 使用者 |
| 主要地標 | 半埋在雪中、刻著風化符文的高大斷裂石拱門 | 自動補全 |
| 前景／中景／背景 | 前：覆雪斷柱與凍結碎石／中：掛滿冰柱的拱門／後：霧中冰封山脈 | 自動補全 |
| 時間與天氣 | 黃昏、細雪、帶著細雪的寒風 | 自動補全 |
| 氛圍 | 肅穆、壯觀 | 自動補全 |
| 色彩 | 冰藍與白主色系，暖金互補色點綴；金色暮光從拱門透出 | 自動補全 |
| 生物 | 無 | 預設 |
| 構圖 | 直向 2:3，拱門佔據中上方大面積 | 預設 |

```json
{
  "skill": "scene-art",
  "asset_type": "scene",
  "user_input": "我要生成一個被冰雪覆蓋的古代遺跡",
  "needs_confirmation": false,
  "confirmation_reasons": [],
  "fields": {
    "location_type": "ancient ruins covered in snow and ice",
    "landmark": "a towering broken stone archway carved with weathered runes, half-buried in snow",
    "layers": "foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains",
    "time_weather": "dusk, light snowfall, cold wind carrying fine snow",
    "mood": "solemn and awe-inspiring atmosphere",
    "color": "icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch",
    "creatures": "",
    "framing": "vertical, archway dominant at upper-middle"
  },
  "auto_filled": ["landmark", "layers", "time_weather", "mood", "color", "creatures", "framing"],
  "assumptions": ["未指定是否有人物或生物：場地卡預設無人、無生物，以地標為焦點。"],
  "matched_assets": [],
  "positive_prompt": "ancient ruins covered in snow and ice, a towering broken stone archway carved with weathered runes, half-buried in snow, foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains, dusk, light snowfall, cold wind carrying fine snow, solemn and awe-inspiring atmosphere, icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch, vertical environment illustration, the archway as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast, single dominant landmark filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative_prompt": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, people, crowd, human figure, close-up portrait, flat empty background",
  "chatgpt_prompt": "Create an image from this prompt, used as written:\n\nancient ruins covered in snow and ice, a towering broken stone archway carved with weathered runes, half-buried in snow, foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains, dusk, light snowfall, cold wind carrying fine snow, solemn and awe-inspiring atmosphere, icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch, vertical environment illustration, the archway as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast, single dominant landmark filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration\n\nAvoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, people, crowd, human figure, close-up portrait, flat empty background\n\nAspect ratio: 2:3 portrait (1024x1536)",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": {
    "style": { "file": "", "weight": null, "status": "reserved" },
    "scene": { "file": "", "weight": null, "status": "reserved" },
    "assets": []
  },
  "lock_exceptions": [],
  "style_lock_version": "0.3.0",
  "negative_lock_version": "0.3.0"
}
```

**ChatGPT 貼上用（`prompt_rules.md` §12）：**
```text
Create an image from this prompt, used as written:

ancient ruins covered in snow and ice, a towering broken stone archway carved with weathered runes, half-buried in snow, foreground of snow-covered broken pillars and frozen rubble, midground archway hung with long icicles, background of misty frozen mountains, dusk, light snowfall, cold wind carrying fine snow, solemn and awe-inspiring atmosphere, icy blue and white color scheme with warm gold accents, golden dusk light shining through the arch, vertical environment illustration, the archway as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast, single dominant landmark filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, people, crowd, human figure, close-up portrait, flat empty background

Aspect ratio: 2:3 portrait (1024x1536)
```

## 8. 測試範例

### 測試 1：明確輸入＋橫向（直接輸出）

**輸入：** `一座漂浮在雲海上的空中城堡，橫向卡牌`

**預期：**
- `needs_confirmation: false`
- `aspect_ratio: "3:2"`、`resolution_hint: "1216x832"`（使用者指定橫向）
- composition 使用 `horizontal environment illustration …`
- 配色句只有一個主導色調
- 預期 prompt 前段：
  `a floating sky castle above a sea of clouds, a tall white spired citadel on a fractured floating rock with small waterfalls pouring off its edges, foreground of drifting cloud banks and broken floating stones, midground castle with chained smaller islets, background of distant sunlit cloud towers, sunrise, high winds, wondrous and adventurous atmosphere, bright sky blue and gold color scheme with warm pink accents, horizontal environment illustration, the castle as the dominant focal point filling much of the frame, strong sense of depth with distinct foreground, midground and background`

### 測試 2：模糊輸入（需確認）

**輸入：** `一個很有氣勢的地方`

**預期：**
- `needs_confirmation: true`；原因：`location_type` 無法判斷
- **不輸出最終 prompt**
- 輸出「預計補全設定」表，例如：火山要塞／夜晚、火山灰／熔岩河上的巨大黑石城門／三層景深／威壓感／熔岩橘紅主色系、青藍互補色點綴／2:3
- 問題（最多 3 個）：1) 地點類型（自然、建築、遺跡、異界）？ 2) 時間與天氣？ 3) 直向還是橫向？

### 測試 3：與鎖定衝突

**輸入：** `熱鬧的市集，很多人在逛街`

**預期：**
- `needs_confirmation: true`；衝突：「很多人」↔ 負面鎖定 `people, crowd, human figure, multiple characters`
- 選項：
  - A（建議）：無人市集，用掛滿燈籠的攤位、飄動的布幔、冒煙的爐火、飛過的鳥群（生物不衝突）來暗示熱鬧
  - B：本次例外移除 `people`、`crowd`、`human figure`、`multiple characters`，改成「遠景小型剪影人群」，並記錄在 `lock_exceptions`
- 不可默默刪除鎖定詞

