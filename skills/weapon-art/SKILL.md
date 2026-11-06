---
name: weapon-art
description: Generate consistent vibrant fantasy TCG weapon, item and equipment illustrations from short user descriptions, including weapons held by a supporting figure, using the shared card-art style lock.
---

# weapon-art：武器／道具／裝備立繪

> 路徑皆相對於 `card-art-skills/`。本 skill 只決定「畫什麼」與「構圖」；「怎麼畫」一律來自 `shared/style_lock.json`。

## 1. 何時使用

**使用：** 要生成以「單一武器、道具或裝備」為主體的卡牌立繪，例如劍、槍、弓、法杖、盾、護符、手甲、魔導書。
- 「我要生成一把冰霜法杖」
- 「一面刻著龍紋的盾牌」
- 「騎士握著一把長劍，以長劍為畫面主體」

允許手或人物持握、展示武器；持有者是陪襯，武器保持大而醒目，主要輪廓與關鍵細節清楚。未指定持有者時不強制加入人物；明確要求無人展示時，依使用者排除需求加入負面 prompt。

**不使用（改用其他 skill）：**
- 畫面要有角色握持或穿戴，而且角色是主體 → `character-art`
- 主體是武器釋放出的法術效果 → `magic-art`
- 一次處理 CSV 多列 → `batch-generate`

## 2. 必須讀取的共用檔案

每次執行前重新讀取：
1. `shared/style_lock.json`（唯讀）：`style`、`quality`、`by_type.weapon`（`style_append` 與 `framing_override`）。**對角擺放、主題環境背景都由 `framing_override` 固定提供，skill 不必重寫。**
2. `shared/negative_lock.json`（唯讀）：`negative`、`append_by_type.weapon`（排除武器堆、重複武器剪影與背景殘影；允許手與持有者）
3. `shared/assets.json`：`weapons`、`loras.style`、`loras.weapon`
4. `shared/prompt_rules.md`

## 3. 如何解析使用者輸入

1. 拆出使用者明確提供的資訊（類型、元素、材質、顏色、紋樣…），對應到 §4 欄位。
2. 比對 `assets.json → weapons` 的 `name`／`aliases`／`id`，命中時帶入資產設定。
3. 抽出排除需求，英譯後加入負面 prompt。
4. 判斷比例：預設 `2:3`；使用者要求橫向 → `3:2`。
5. 檢查衝突：「單一武器」是本 skill 的構圖規則；人物持握不構成衝突，不需因此確認或記錄 lock_exceptions。若人物是主體則改用 character-art；其他衝突依 `prompt_rules.md` §6 處理。
6. 判斷是否需確認（`prompt_rules.md` §7 加上本 skill 的核心欄位），然後補全並組裝。

## 4. 必須補全的欄位

| key | 欄位 | 核心 | 補全原則（英文寫入 prompt） |
|---|---|---|---|
| `weapon_type` | 武器類型 | ★ | 具體類型：longsword, greatsword, staff, wand, spear, halberd, bow, dagger, axe, gun-blade, shield, amulet, gauntlet, grimoire… |
| `material` | 材質 | | 主材質加次材質（polished steel, ancient wood, bone, crystal, gold ornament, leather-wrapped grip）；可加入多切面寶石 |
| `color` | 顏色 | | 配色句：`[主色系] color scheme with [互補色] accents`，主色系依元素或題材 |
| `engraving_runes` | 紋樣或符文 | | 與元素或陣營一致，寫出位置（along the blade, around the guard, spiraling along the shaft）；預設為雕刻或鑲嵌，符文不自動發光 |
| `vfx` | 特效 | | 可為空字串；普通武器不加特效。題材需要時才補局部 frost mist、crackling lightning 或 flame wreath，不遮住武器主要輪廓，依共用規則控制發光範圍 |
| `display` | 背景展示方式 | | 寫出**主題環境**：依元素或題材選一個具體場景（frozen glacier valley, jungle canopy, ancient ruins, stormy night sky）。需要持握時，在此描述持有者與握持方式，例如 `held by a knight with the wielder visually secondary`；手部自然握住握柄，避免遮住關鍵細節。對角擺放由 framing 提供 |
| `framing` | 構圖 | | 固定：單一武器為主體、大而醒目、關鍵部位清楚；可有持握者陪襯 |

**核心規則：** `weapon_type` 必須能判斷（「武器」「裝備」「神器」不算具體類型），否則需確認。

**組裝對應（`prompt_rules.md` §3）：**
- subject：`a [element/adjective] [weapon_type]`，例如 `a frost magic staff`
- details：`material` → `color` → `engraving_runes` → `vfx` → `display`
- composition（固定模板，只替換 `[ ]`）：
  `single [item noun] as the main subject, [item noun] large and dominant in the frame, [key part] and key details clearly visible`
  （`[key part]` 例：blade, crystal head, crossbow limbs, shield face）
- framing：使用 `style_lock.by_type.weapon.framing_override`（逐字）
- 比例：預設 `2:3`；橫向 `3:2`（配合參考圖框）。

## 5. 不可擅自更改的欄位

- `style_lock.json`、`negative_lock.json` 的所有字串（包含 weapon 的 framing 與追加負面詞）
- composition 模板中的 `single … as the main subject` 與 `key details clearly visible`
- 使用者明確指定的類型、材質、顏色、紋樣、損壞狀態（例如「劍身有裂痕」不可省略）
- 命中資產的 `description`、`locked_traits`、`trigger_word`

## 6. 輸出格式

依 `prompt_rules.md` §9。`fields` 使用 §4 的 key；`suggested_loras` 的鍵為 `style`、`weapon`、`assets`。

## 7. 範例輸入與範例輸出

**輸入：**
```
我要生成一把冰霜法杖
```

**輸出：**

| 欄位 | 設定 | 來源 |
|---|---|---|
| 武器類型 | 法杖（magic staff），冰霜屬性 | 使用者 |
| 材質 | 蒼白古木杖身、纏繞拋光銀環、金色配件、頂端大型多切面冰晶 | 自動補全 |
| 顏色 | 冰藍與白主色系，暖金互補色點綴 | 自動補全 |
| 紋樣或符文 | 沿杖身螺旋刻寫的冰霜符文，鑲有青色琺瑯 | 自動補全 |
| 特效 | 冰晶頭部附近一縷薄寒霧 | 自動補全 |
| 背景展示方式 | 晴空下的冰河谷（主題環境）＋對角擺放（鎖定 framing） | 自動補全＋鎖定 |
| 構圖 | 直向 2:3，法杖大而醒目、冰晶頭部清楚 | 預設 |

```json
{
  "skill": "weapon-art",
  "asset_type": "weapon",
  "user_input": "我要生成一把冰霜法杖",
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
  "assumptions": ["主題環境選用冰河谷以呼應冰霜屬性；對角擺放由 style lock 的 weapon framing 提供。"],
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
  "style_lock_version": "0.3.1",
  "negative_lock_version": "0.3.2"
}
```

**ChatGPT 貼上用（`prompt_rules.md` §12）：**
```text
Create an image from this prompt, used as written:

a frost magic staff, pale ancient wood shaft wrapped in polished silver bands, gold fittings, crowned with a large faceted ice crystal, icy blue and white color scheme with warm gold accents, frost runes engraved in a spiral along the shaft, inlaid with cyan enamel, a thin curl of cold mist close to the crystal head, set against a frozen glacier valley under a clear sky, single staff as the main subject, staff large and dominant in the frame, crystal head and key details clearly visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, detailed hard-surface rendering, material-appropriate reflections, faceted gemstones with restrained highlights, weapon placed diagonally across the frame, softly blurred thematic environment matching the weapon's element, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, weapon pile, weapon rack, double-exposure weapon echo, duplicate weapon silhouette, oversized weapon shadow in the background

Aspect ratio: 2:3 portrait (1024x1536)
```

## 8. 測試範例

### 測試 1：明確輸入（直接輸出）

**輸入：** `一把纏繞黑色火焰的巨劍，劍身有裂痕`

**預期：**
- `needs_confirmation: false`
- `weapon_type` = greatsword（使用者）；「黑色火焰」→ `vfx`；「劍身有裂痕」必須保留（負面鎖定沒有 broken 類詞，無衝突）
- 配色句：`black and crimson color scheme with violet accents`
- `display` 選一個與黑焰相符的主題環境（例如 stormy volcanic wasteland）
- `aspect_ratio: "2:3"`
- 預期 prompt 前段：
  `a cursed greatsword, broad blackened steel blade with deep cracks running along its length, gold crossguard set with a red gemstone, leather-wrapped grip, black and crimson color scheme with violet accents, jagged rune engravings near the guard, wreathed in swirling black flames with crimson edges, set against a stormy volcanic wasteland, single greatsword as the main subject, greatsword large and dominant in the frame, blade and key details clearly visible`

### 測試 2：模糊輸入（需確認）

**輸入：** `給我一個傳說級的裝備`

**預期：**
- `needs_confirmation: true`；原因：`weapon_type` 無法判斷（「裝備」不是具體類型）
- **不輸出最終 prompt**
- 輸出「預計補全設定」表，例如：長劍／拋光銀刃與金色護手、藍寶石／金與天藍主色系、紫色互補色點綴／劍身雕刻符文／晴空下的古代神殿遺跡／2:3
- 問題（最多 3 個）：1) 類型是劍、法杖、弓、盾還是飾品？ 2) 元素或屬性？ 3) 偏好的材質或主題環境？

### 測試 3：人物持握（直接輸出）

**輸入：** `一位騎士握著長劍，以長劍為畫面主體`

**預期：**
- `needs_confirmation: false`、`lock_exceptions: []`
- 使用 `weapon-art`；`weapon_type` = longsword，`display` 保留騎士持握的要求，持有者是陪襯。
- composition 保持單一長劍大而醒目，劍刃與關鍵細節清楚；握持自然。
- 負面 prompt 不含獨立排除詞 `person`、`character`、`hands`、`hand holding weapon`、`wielder`；仍保留 `deformed hands` 等解剖品質排除詞及背景殘影排除詞。
- 若使用者改為「以持劍騎士為主體」，改用 `character-art`。

### 測試 4：多武器構圖衝突

**輸入：** `一隻手握著雙刀，刀上有毒液滴落`

**預期：**
- `needs_confirmation: true`；只有「雙刀」與 `single … as the main subject` 的構圖衝突。持握本身不構成衝突。
- 選項：保留持握並改為單刀，或本次放寬為一組雙刀、只記錄放寬單一武器構圖的 `lock_exceptions`。
- 不要求移除手或改用角色圖；只有使用者要以人物為主體時才改用 `character-art`。
- 「毒液滴落」保留為 `vfx`（`dripping venom`），配色句例如 `toxic green and violet color scheme with yellow accents`。

### 測試 5：無人展示

**輸入：** `一把冰霜法杖，不要人物或手`

**預期：**
- `needs_confirmation: false`；保留無人展示，不自動補上持有者。
- 依使用者排除需求追加 `person, hands`，只影響本次輸出，不修改共用負面鎖定。
