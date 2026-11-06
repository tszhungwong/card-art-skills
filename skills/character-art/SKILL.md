---
name: character-art
description: Generate consistent vibrant fantasy TCG character illustrations from short user descriptions, using the shared card-art style lock.
---

# character-art：角色立繪

> 路徑皆相對於 `card-art-skills/`。本 skill 只決定「畫什麼」與「構圖」；「怎麼畫」一律來自 `shared/style_lock.json`。

## 1. 何時使用

**使用：** 要生成以「單一角色」為主體的卡牌立繪，例如人物、種族、職業、怪物、龍或召喚獸（作為單位卡）。
- 「我要生成一個獸人，拿著魔法棒」
- 「一位銀髮精靈弓箭手」

**不使用（改用其他 skill）：**
- 主體是武器、道具、裝備 → `weapon-art`
- 主體是地點、環境 → `scene-art`
- 主體是法術效果本身，施法者只是陪襯 → `magic-art`
- 一次處理 CSV 多列 → `batch-generate`
- 無法判斷 → 依 `prompt_rules.md` §7 詢問

## 2. 必須讀取的共用檔案

每次執行前重新讀取：
1. `shared/style_lock.json`（唯讀）：`style`、`quality`、`by_type.character`（`style_append` 與 `framing_override`）
2. `shared/negative_lock.json`（唯讀）：`negative`、`append_by_type.character`
3. `shared/assets.json`：`characters`、`loras.style`、`loras.character`
4. `shared/prompt_rules.md`：組裝順序、配色寫法、補全、衝突、確認、輸出格式

## 3. 如何解析使用者輸入

1. 拆出使用者明確提供的資訊，對應到 §4 欄位，來源標為「使用者」。
2. 比對 `assets.json → characters` 的 `name`／`aliases`／`id`。命中時帶入 `description`、`locked_traits`、`trigger_word`、`lora_file`，來源標為「資產庫」。
3. 抽出排除需求（「不要…」「別有…」），英譯後加入負面 prompt。
4. 抽出構圖需求：「全身」→ full-body；「半身」→ half-body；「橫向」或指定比例 → 依 `prompt_rules.md` §2。
5. 檢查衝突（`prompt_rules.md` §6），並判斷是否需確認（`prompt_rules.md` §7 加上本 skill 的核心欄位）。
6. 需確認時，輸出「預計補全設定」與問題後停止。否則補全其餘欄位，依 `prompt_rules.md` §3–§4 組裝。

## 4. 必須補全的欄位

| key | 欄位 | 核心 | 補全原則（英文寫入 prompt） |
|---|---|---|---|
| `race_type` | 種族／角色類型 | ★ | 只給了職業時預設 `human`；寫出體型與醒目特徵（skin, horns, tusks, ears, wings） |
| `role` | 職業或身份 | ★ | 依道具與種族推定，例如拿魔杖 → shaman／mage |
| `outfit` | 服裝 | | 符合職業與陣營；具體寫出材質與層次，加入金色滾邊或寶石細節（style lock 已有 gold trim，寫具體位置即可） |
| `weapon_prop` | 武器或道具 | | 依職業補全；使用者指定的道具保留原物，只補材質細節 |
| `pose` | 姿勢 | | 有動勢、剪影清楚；可讓一隻手伸向鏡頭；不遮臉 |
| `expression` | 表情 | | 依陣營與個性，預設偏有活力（confident grin, bright smile, fierce glare…） |
| `element_faction` | 元素或陣營 | | 依道具、服裝、用詞推定，並寫出配色句：`[主色系] color scheme with [互補色] accents`（`prompt_rules.md` §3） |
| `background_magic` | 背景魔法效果 | | 可為空字串；僅在角色正在施法或題材需要時補少量局部效果，不預設符文陣、星芒或發光粒子；普通角色用環境背景 |
| `framing` | 構圖 | | 預設 `half-body`；使用者說全身，或需要展示全套裝備／站姿時用 `full-body` |

**核心規則：** `race_type` 與 `role` 至少要能判斷一項，否則需確認。

**組裝對應（`prompt_rules.md` §3）：**
- subject：`race_type` 加上 `role` 的主體句，例如 `a muscular orc tribal shaman, grey-green skin, prominent tusks`
- details：`outfit` → `weapon_prop` → `pose` → `expression` → `element_faction` → `background_magic`
- composition（固定模板，只替換 `[ ]`）：
  - half-body：`[vertical|horizontal] half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible`
  - full-body：`[vertical|horizontal] full-body character portrait, single character, character as the clear focal point with a readable silhouette, entire figure in frame from head to feet`
- framing：使用 `style_lock.by_type.character.framing_override`（逐字）
- 比例：預設 `2:3`（vertical）。若要配合參考圖框或橫向，用 `5:4`（horizontal）。

## 5. 不可擅自更改的欄位

- `style_lock.json`、`negative_lock.json` 的所有字串（逐字使用）
- composition 模板中的 `single character` 與 `face fully visible`
- 使用者明確指定的內容（種族、道具、顏色、髮型、性別…）
- 命中資產的 `description`、`locked_traits`、`trigger_word`
- 需要違反時，依 `prompt_rules.md` §6 處理

## 6. 輸出格式

依 `prompt_rules.md` §9：先輸出繁中「補全設定」表，再輸出 JSON。`fields` 使用 §4 的 key；`suggested_loras` 的鍵為 `style`、`character`、`assets`。

## 7. 範例輸入與範例輸出

**輸入：**
```
我要生成一個獸人，拿著魔法棒
```

**輸出：**

| 欄位 | 設定 | 來源 |
|---|---|---|
| 種族／角色類型 | 獸人（orc） | 使用者 |
| 職業或身份 | 部落薩滿（tribal shaman） | 自動補全 |
| 服裝 | 層疊獸皮披肩、骨珠項鍊、金邊深紅儀式腰帶、鑲橘色寶石的皮護腕 | 自動補全 |
| 武器或道具 | 魔法棒：扭曲木質魔杖，頂端鑲帶有克制內光的多切面火焰水晶 | 使用者＋細節補全 |
| 姿勢 | 單手高舉魔杖，另一手伸向鏡頭引導魔力 | 自動補全 |
| 表情 | 自信凶狠的咧嘴笑、獠牙外露 | 自動補全 |
| 元素或陣營 | 火元素；橘紅與深紅主色系，青綠互補色點綴 | 自動補全 |
| 背景魔法效果 | 杖尖附近少量火星，背景為柔焦岩地 | 自動補全 |
| 構圖 | 直向 2:3 半身立繪 | 預設 |

```json
{
  "skill": "character-art",
  "asset_type": "character",
  "user_input": "我要生成一個獸人，拿著魔法棒",
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
    "background_magic": "a few embers near the wand tip against a softly blurred rocky backdrop",
    "framing": "half-body"
  },
  "auto_filled": ["role", "outfit", "pose", "expression", "element_faction", "background_magic", "framing"],
  "assumptions": [
    "「獸人」解讀為 orc（奇幻常見譯法）；若你指的是獸耳族／獸族（beastfolk），請告訴我。",
    "「魔法棒」保留為小型 magic wand，未改成大型法杖。"
  ],
  "matched_assets": [],
  "positive_prompt": "a muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones, holding a gnarled wooden magic wand topped with a faceted fire crystal with a restrained inner glow, raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy, fierce confident grin, bared tusks, fire element, blazing orange and crimson color scheme with teal accents, a few embers near the wand tip against a softly blurred rocky backdrop, vertical half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime character art, clean lineart, cel shading with soft gradients, large expressive eyes with layered highlights, soft hair highlights, gold trim and gemstone accents, dynamic diagonal composition, character fills the frame, softly blurred thematic background with atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative_prompt": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, chibi, super deformed",
  "chatgpt_prompt": "Create an image from this prompt, used as written:\n\na muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones, holding a gnarled wooden magic wand topped with a faceted fire crystal with a restrained inner glow, raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy, fierce confident grin, bared tusks, fire element, blazing orange and crimson color scheme with teal accents, a few embers near the wand tip against a softly blurred rocky backdrop, vertical half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime character art, clean lineart, cel shading with soft gradients, large expressive eyes with layered highlights, soft hair highlights, gold trim and gemstone accents, dynamic diagonal composition, character fills the frame, softly blurred thematic background with atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration\n\nAvoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, chibi, super deformed\n\nAspect ratio: 2:3 portrait (1024x1536)",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": {
    "style": { "file": "", "weight": null, "status": "reserved" },
    "character": { "file": "", "weight": null, "status": "reserved" },
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

a muscular orc tribal shaman, grey-green skin, prominent tusks, layered fur mantle, bone-bead necklaces, crimson ritual sash with gold trim, leather bracers set with orange gemstones, holding a gnarled wooden magic wand topped with a faceted fire crystal with a restrained inner glow, raising the wand overhead with one hand, the other hand reaching toward the viewer and channeling energy, fierce confident grin, bared tusks, fire element, blazing orange and crimson color scheme with teal accents, a few embers near the wand tip against a softly blurred rocky backdrop, vertical half-body character portrait, single character, character as the clear focal point with a readable silhouette, face fully visible, vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary color accents, soft directional lighting, restrained material-specific highlights, balanced exposure, localized emission only from actual light sources, professional TCG card art, anime character art, clean lineart, cel shading with soft gradients, large expressive eyes with layered highlights, soft hair highlights, gold trim and gemstone accents, dynamic diagonal composition, character fills the frame, softly blurred thematic background with atmospheric depth, no card frame, no text, no watermark, no UI, highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration

Avoid: extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render, excessive bloom, blown-out highlights, excessive decorative sparkles, glowing outlines on non-emissive surfaces, chibi, super deformed

Aspect ratio: 2:3 portrait (1024x1536)
```

## 8. 測試範例

### 測試 1：明確輸入（直接輸出）

**輸入：** `一位銀髮精靈弓箭手，全身，站在月光下`

**預期：**
- `needs_confirmation: false`
- `race_type` = elf（使用者）、`role` = archer（使用者）、`framing` = full-body（使用者）
- 「銀髮」保留為 `silver hair`；「月光」轉成配色句與光線描述，不改動鎖定的 framing 字串
- 未指定性別 → prompt 不寫性別
- composition 使用 full-body 模板；`aspect_ratio: "2:3"`
- 預期 prompt 前段（後面接固定的 style、style_append、framing、quality）：
  `a slender elf archer, long silver hair, pointed ears, fitted leather armor with gold trim and blue gemstone clasps, flowing hooded cloak, longbow of pale carved wood, quiver of fletched arrows, poised stance with an arrow half drawn, calm piercing gaze, moon faction, silver and moonlight blue color scheme with soft violet accents, cold moonlight from behind, softly lit woodland behind the figure, vertical full-body character portrait, single character, character as the clear focal point with a readable silhouette, entire figure in frame from head to feet`

### 測試 2：模糊輸入（需確認）

**輸入：** `幫我畫一個很強的角色`

**預期：**
- `needs_confirmation: true`；原因：`race_type` 與 `role` 都無法判斷
- **不輸出最終 prompt**
- 輸出「預計補全設定」表，例如：human／銀白板甲騎士（silver plate armor with gold trim, crimson cape）／鋼製巨劍／持劍前指的姿勢／自信表情／光元素（金白主色系、天藍互補色點綴）／柔和日光下的神殿背景／半身 2:3
- 最多問 3 個問題：1) 種族或角色類型？ 2) 職業或戰鬥風格？ 3) 偏好的元素或陣營？
- 使用者回覆「確認」或修改後，才產生完整輸出

### 測試 3：與鎖定衝突

**輸入：** `兩個騎士在對戰，畫成 Q 版，上面寫上卡名「炎之誓約」`

**預期：**
- `needs_confirmation: true`，列出 3 個衝突：
  1. 兩個角色 ↔ 負面鎖定 `multiple characters` 與模板 `single character` → A（建議）：單一騎士做揮劍攻擊動作；B：本次例外移除 `multiple characters` 並放寬單角色規則
  2. Q 版 ↔ 負面鎖定 `chibi, super deformed` 與 quality 的 `consistent proportions` → 屬畫風，不提供本次例外（`prompt_rules.md` §6）；保留鎖定，或明確要求修改 `style_lock.json`
  3. 卡名文字 ↔ framing `no text` → 不提供例外；卡名應在卡框排版階段加入
- 不可默默刪除 `multiple characters`，prompt 不可出現 `chibi`，不可把「炎之誓約」字樣放進 prompt
- 使用者選 A 後：輸出單一騎士 prompt，可取「炎之誓約」的語意補全為 fire oath knight

