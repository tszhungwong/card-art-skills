# 共用 Prompt 規則（prompt_rules.md）

所有 skill（character-art、weapon-art、scene-art、magic-art、batch-generate）都必須遵守本檔。
所有路徑皆相對於 `card-art-skills/` 根目錄。

> 分工原則：**style lock 決定「怎麼畫」**（風格、框架、品質、負面詞）；**各 skill 只決定「畫什麼」與「構圖」**。

---

## 1. 共用檔案與權限

| 檔案 | 用途 | Skill 權限 |
|---|---|---|
| `shared/style_lock.json` | 全域風格 `style`、框架 `framing`、品質 `quality`、類型補充 `by_type` | 唯讀（鎖定） |
| `shared/negative_lock.json` | 負面 prompt 的**唯一來源**：`negative` + `append_by_type` | 唯讀（鎖定） |
| `shared/assets.json` | 已登錄的角色／武器／場地／魔法資產與 LoRA 欄位 | 唯讀（新增資產依 AGENTS.md 流程） |
| `shared/prompt_rules.md` | 本檔：組裝、補全、確認、輸出規則 | 唯讀 |

每次執行 skill 都要**重新讀取**以上檔案，不可沿用記憶中的舊內容。

---

## 2. 類型對照與比例表

| asset_type | Skill | assets.json 陣列 | loras 鍵 | 預設比例 | 可選比例 |
|---|---|---|---|---|---|
| `character` | character-art | `characters` | `character` | 2:3 | 5:4 |
| `weapon` | weapon-art | `weapons` | `weapon` | 2:3 | 3:2、5:4 |
| `scene` | scene-art | `scenes` | `scene` | 2:3 | 3:2、5:4 |
| `magic` | magic-art | `magic_effects` | `magic` | 2:3 | 5:4 |

選擇規則：
1. 使用者明確指定比例、「橫向／直向」、「全景」→ 依使用者（全景／橫向 → 3:2）。
2. 否則用預設比例。
3. 5:4 用於配合參考圖的藝術框（參考圖約 1.19:1：554×465、460×386；武器參考圖約 1.54:1：578×375）。

| 比例 | resolution_hint |
|---|---|
| 2:3 | `832x1216` |
| 3:2 | `1216x832` |
| 5:4 | `1120x896` |
| 4:5 | `896x1120` |
| 1:1 | `1024x1024` |

---

## 3. 正向 prompt 組裝

依下列順序以 `, `（逗號＋空白）串接，略過空值：

| # | 區塊 | 來源 | 規則 |
|---|---|---|---|
| 1 | trigger_words | 命中資產的 `trigger_word` | 只有該資產 `lora_file` 非空時才加入 |
| 2 | subject | skill | 主體一句（各 skill §4 定義） |
| 3 | details | skill | 依 skill §4 欄位順序 |
| 4 | composition | skill | 各 skill 的固定構圖模板 |
| 5 | `style_lock.style` | 鎖定 | **逐字照抄** |
| 6 | `style_lock.by_type[type].style_append` | 鎖定 | 非空才加，逐字照抄 |
| 7 | framing | 鎖定 | `by_type[type].framing_override` 非空則用它，否則用 `style_lock.framing`；逐字照抄 |
| 8 | `style_lock.quality` | 鎖定 | **逐字照抄** |

撰寫 1–4（skill 負責的部分）時：
- 一律英文，使用逗號分隔的具體視覺短語；建議 25–70 個英文單字，避免稀釋風格。
- 不得與鎖定字串矛盾（例：`muted desaturated palette`、`dark gloomy`、`photorealistic`、`chibi`、`plain white background`、`text`）。
- **配色寫法**：每張寫一句 `[主色系] color scheme with [互補色] accents`。主色系依元素／陣營／題材決定；互補色可用於布料、塗裝、鑲嵌、材質或環境色，不代表自發光。例：冰元素 → `icy blue and white color scheme with warm gold accents`；火元素 → `blazing orange and crimson color scheme with teal accents`。
- 不要重複 style lock 已有的畫風與照明字串，把字數留給主體、材質及具體光源。
- **發光與反光分開處理**：金屬、寶石、冰雪的反光不等於自發光。僅在使用者要求或題材確有光源時寫入局部發光，並說明來源與照亮範圍；火焰、雷電、聖光可發光，普通角色、武器、建築不自動添加光暈、符文陣或星芒。土、風、水等魔法可用形狀、材質與運動表現，不強制發光。
- **柔和不等於灰暗**：保留鮮豔配色與清楚明暗；避免過曝與滿屏泛光，讓材質、輪廓與魔法核心仍可辨識。依地點與時段使用日光、月光或其他合理光源。
- 不在正向 prompt 寫否定句（`no people`、`without wings`）；排除項一律放負面 prompt。（鎖定的 framing 字串內的 `no text` 等照抄，屬例外。）
- 不使用真實藝術家姓名、既有遊戲／作品／品牌名稱、既有角色名稱。
- 卡名或專有名詞**不直接放進 prompt**（模型可能畫出文字或誤解）；只取其語意轉成視覺描述。
- 性別、年齡外觀：使用者有指定才寫入，未指定時不自行假設。

---

## 4. 負面 prompt 組裝

1. `negative_lock.negative`（逐字）
2. `negative_lock.append_by_type[type]`（非空才加）
3. user_exclusions：使用者明確說「不要／不能有 X」→ 英譯後附加
4. 以逗號切分、去除前後空白、不分大小寫去重、保留首次出現順序
5. 移除經使用者同意的 `lock_exceptions` 項目（見 §6）

一致性檢查：若 `style_lock.negative` ≠ `negative_lock.negative`，以 `negative_lock` 為準，並用繁體中文提醒使用者兩檔不一致（**不要自行修正**）。

---

## 5. 補全原則

1. **使用者明確給的內容優先**，不可替換（「魔法棒」不可改成「法杖」）；可在不改變原意的前提下補細節（材質、顏色、光效）。
2. 中文詞有多種常見英譯時，選奇幻 TCG 最常見的意思，並在 `assumptions` 註明備選（例：獸人 → `orc`，備選 `beastfolk`）。
3. 補全值彼此一致：職業 ↔ 服裝 ↔ 道具 ↔ 元素 ↔ 背景特效 ↔ 色彩。
4. 自動補全時不主動加入與 style lock 氛圍相衝的元素；使用者明確要求的題材（例如機械、科幻武器）照用，只以 style lock 的畫法呈現。
5. 命中 `assets.json` 資產時：其 `description` 併入 subject／details，`locked_traits` 全部保留，不得補全出矛盾設定。
6. 每個欄位都要標示來源：「使用者」「自動補全」「資產庫」「預設」。
7. 一律保持**單一主體、焦點清楚**。武器圖允許人物持握；「單一主體」指武器是視覺焦點，不代表不能出現手或持有者。人物成為主要焦點時改用 `character-art`。

---

## 6. 不可擅自更改的內容與衝突處理

不可擅自更改：
- `style_lock.json`、`negative_lock.json` 的所有字串
- 使用者明確指定的內容
- 命中資產的 `description`、`locked_traits`、`trigger_word`
- 各 skill 宣告的固定構圖規則

常見衝突：
- 風格：Q 版、照片寫實、水彩、像素風、3D 渲染…
- 鎖定負面詞：文字／卡名／卡框、多角色、場地出現人群…
- 資產：要求改變已登錄資產的 `locked_traits`

處理方式：
1. 不可默默忽略使用者要求，也不可默默刪除鎖定字詞。
2. 設 `needs_confirmation = true`，用繁體中文列出衝突與選項：
   - **A（預設建議）**：保留鎖定，調整需求（提出具體替代方案）。
   - **B（本次例外）**：只在這一張 prompt 移除指定的**負面詞**或放寬 skill 構圖規則，記錄在 `lock_exceptions`，**不修改任何檔案**。
   - **C（永久變更）**：使用者明確要求修改 `style_lock.json`／`negative_lock.json`（依 AGENTS.md 流程）。
3. 以下**不提供本次例外**：要不同畫風請走 C；卡名與卡框應在排版階段加入。
   - `style`、`style_append`、`quality`
   - framing 中的 `no card frame, no text, no watermark, no UI`
   - 屬於畫風的負面詞：`chibi`、`super deformed`、`photorealistic`、`3d render`、`dull muted colors`、`desaturated`、`muddy colors`

`lock_exceptions` 格式：
```json
[{ "term": "multiple characters", "action": "removed_from_negative", "approved_by_user": true }]
```

---

## 7. 何時需要向使用者確認

任一成立 → `needs_confirmation = true`：
1. 無法從輸入判斷該 skill 的**核心欄位**（各 skill §4 以 ★ 標示）。
2. 有 §6 所列衝突。
3. 輸入出現疑似專有名詞（人名、地名、招式名），`assets.json` 查無此資產，且無法從字面推出外觀。
4. 無法判斷該用哪個 skill（例：「冰龍」可能是角色，也可能是召喚魔法）。
5. 使用者要求解除鎖定或修改鎖定檔。

**單張模式，需確認時：**
- 輸出「預計補全設定」表（欄位／打算補全的值／理由）與**最多 3 個**具體問題。
- **不輸出最終 prompt。** 告訴使用者：回覆「確認」即採用此設定，或直接修改任一欄位。
- 使用者回覆後再產生完整輸出。

**不需確認時：** 直接輸出完整結果，並列出自動補全的欄位，讓使用者知道可指定修改。

**批量模式：** 見 `skills/batch-generate/SKILL.md`（不中斷，產生最佳推測並標記）。

---

## 8. LoRA 規則（預留欄位，現階段不訓練）

- 來源：`assets.json → loras.style`、`loras.<type>`；命中資產的 `lora_file`、`default_weight`、`trigger_word`。
- 空字串 = 尚未設定 → `status: "reserved"`、`weight: null`。
- 有值 → `status: "ready"`；全域 LoRA（style／type）建議權重 `0.8`；資產 LoRA 用其 `default_weight`。
- `trigger_word` 只在對應 `lora_file` 非空時放入正向 prompt 開頭。
- **不訓練 LoRA、不下載模型或 LoRA 檔**，除非使用者在對話中明確確認。

`suggested_loras` 格式（`<type>` 鍵依 skill 而定：`character`／`weapon`／`scene`／`magic`）：
```json
{
  "style":  { "file": "", "weight": null, "status": "reserved" },
  "<type>": { "file": "", "weight": null, "status": "reserved" },
  "assets": [ { "id": "", "file": "", "weight": 0.8, "trigger_word": "" } ]
}
```
未命中任何資產時 `assets` 為空陣列。

---

## 9. 單張輸出格式

**第一部分：繁中「補全設定」表**

| 欄位 | 設定 | 來源 |
|---|---|---|
| 中文欄位名 | 中文說明（英文值） | 使用者／自動補全／資產庫／預設 |

**第二部分：JSON 區塊**

```json
{
  "skill": "<skill name>",
  "asset_type": "character | weapon | scene | magic",
  "user_input": "<原始輸入>",
  "needs_confirmation": false,
  "confirmation_reasons": [],
  "fields": { "<field_key>": "<english value>" },
  "auto_filled": ["<field_key>"],
  "assumptions": ["<繁中說明>"],
  "matched_assets": [],
  "positive_prompt": "<完整英文正向 prompt>",
  "negative_prompt": "<完整英文負面 prompt>",
  "chatgpt_prompt": "<依 §12 組成的 ChatGPT 貼上文字>",
  "aspect_ratio": "2:3",
  "resolution_hint": "832x1216",
  "suggested_loras": { },
  "lock_exceptions": [],
  "style_lock_version": "<style_lock._meta.version>",
  "negative_lock_version": "<negative_lock._meta.version>"
}
```

**第三部分：ChatGPT 貼上區塊**

把 `chatgpt_prompt` 放在 ` ```text ` 區塊中輸出，讓使用者整段複製貼到 ChatGPT（格式見 §12）。

需確認時只輸出「預計補全設定」表與問題，不輸出 JSON，也不輸出 ChatGPT 區塊。

---

## 10. 存檔

- 單張：使用者要求存檔時 → `output/prompts/single/<YYYYMMDD-HHMMSS>_<asset_type>_<slug>.json`（slug 為英文小寫加連字號）。
- 批量：見 batch-generate。
- 報告：`output/reports/`（`style_analysis.md`、`batch_report.md`）。
- `output/images/`：只放使用者自行生成後放入的圖片。Agent 不呼叫付費圖像 API，除非使用者明確確認。

---

## 11. 禁止事項

- 未經使用者明確要求就修改鎖定檔。
- 在 SKILL.md 寫死特定角色、武器、場地、魔法名稱（這些放 `assets.json`）。
- 用英文以外的語言寫 prompt。
- 在 prompt 使用真實藝術家或既有 IP 名稱。
- 未經確認就訓練 LoRA、下載模型、呼叫付費圖像 API。

---

## 12. ChatGPT 貼上格式

ChatGPT 沒有負面 prompt 欄位與 LoRA，但能理解自然語言的否定，所以把負面詞改成一行 `Avoid:`。`chatgpt_prompt` 由 `positive_prompt`、`negative_prompt`、`aspect_ratio` 機械式組成，**不另外改寫內容**：

```text
Create an image from this prompt, used as written:

<positive_prompt>

Avoid: <negative_prompt>

Aspect ratio: <比例文字>
```

| aspect_ratio | 比例文字 |
|---|---|
| 2:3 | `2:3 portrait (1024x1536)` |
| 3:2 | `3:2 landscape (1536x1024)` |
| 1:1 | `1:1 square (1024x1024)` |
| 5:4 | `5:4 landscape`（ChatGPT 若無法輸出此比例，改用 3:2 後裁切） |
| 4:5 | `4:5 portrait`（ChatGPT 若無法輸出此比例，改用 2:3 後裁切） |

使用提醒（回報給使用者時可附上）：
- 不要把 `data/references/` 的參考圖上傳給 ChatGPT 當風格參考：那些是既有遊戲的官方卡圖，生成結果太接近會有商用風險。
- 生成後把圖放進 `output/images/`，可請 agent 與參考圖比對、調整 prompt。
- `suggested_loras` 在 ChatGPT 中無作用，忽略即可。

