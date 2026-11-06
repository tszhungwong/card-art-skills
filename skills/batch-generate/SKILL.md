---
name: batch-generate
description: Batch-generate consistent vibrant fantasy TCG card art prompts from data/batches/batch.csv by applying the matching card-art skill rules to each row, using the shared card-art style lock.
---

# batch-generate：批量生成卡牌 prompt

> 路徑皆相對於 `card-art-skills/`。本 skill 不另定規則，每一列都套用對應 skill 的規則與共用的 style lock。

## 1. 何時使用

- 使用者要一次為多張卡產生 prompt：「跑批量」「批次生成」「用 batch.csv 產生 prompt」。
- 輸入一律是 `data/batches/batch.csv`（使用者指定其他 CSV 時，改讀該檔）。
- 單張生成不用本 skill，改用對應的單張 skill。

## 2. 必須讀取的檔案

每次執行前重新讀取：
1. `shared/style_lock.json`、`shared/negative_lock.json`、`shared/assets.json`、`shared/prompt_rules.md`
2. CSV 中出現的 `asset_type` 所對應的 `SKILL.md`：
   | asset_type | 規則來源 |
   |---|---|
   | `character` | `skills/character-art/SKILL.md` |
   | `weapon` | `skills/weapon-art/SKILL.md` |
   | `scene` | `skills/scene-art/SKILL.md` |
   | `magic` | `skills/magic-art/SKILL.md` |
3. `data/batches/batch.csv`

## 3. 如何解析輸入

**CSV 格式：**
- UTF-8（可含 BOM），第一列是標題列，以逗號分隔，含逗號的欄位用雙引號包住
- 必要欄位：`card_id,asset_type,name,extra_description`
- 選用欄位：`aspect_ratio`（覆寫預設比例，只接受 `prompt_rules.md` §2 列出的比例）
- 其他欄位忽略
- 標題列缺少任一必要欄位 → 整批停止，用繁體中文告知缺少哪個欄位

**逐列驗證（`row` 為 CSV 行號，標題列 = 1）：**

| 檢查 | 失敗時 |
|---|---|
| `card_id` 非空 | 錯誤，略過該列 |
| `card_id` 不重複 | 第二筆起列為錯誤並略過 |
| `asset_type` 去空白、轉小寫後必須是 `character`／`weapon`／`scene`／`magic` | 錯誤，略過該列 |
| `name` 與 `extra_description` 至少一個非空 | 錯誤，略過該列 |
| `aspect_ratio`（若有填）合法 | 警告，改用預設比例，寫入 `notes` |

**每列處理流程：**
1. 組合輸入：`name` + `extra_description`。
2. 處理 `name`：先比對 `assets.json` 對應陣列的 `name`／`aliases`／`id`。命中就使用資產設定；沒有命中時，只取 `name` 的字面語意（例如「冰霜法杖」→ frost staff）。專有名詞（人名、卡名音譯）**不放進 prompt**，並在 `notes` 建議登錄到 `assets.json`。
3. 依 `asset_type` 套用對應 SKILL.md 的 §3–§5（欄位、核心欄位、組裝模板、不可更改項目），以及 `prompt_rules.md` §3–§6。
4. **批量不中斷：** 符合 `prompt_rules.md` §7 的列仍產生「最佳推測」prompt，設 `needs_confirmation: true`，並在 `notes` 用繁體中文寫原因與假設。
5. **衝突列：** 一律採選項 A（保留鎖定），**不套用任何 `lock_exceptions`**，在 `notes` 記錄衝突。使用者事後在對話中針對特定 `card_id` 同意例外時，才重跑該列。
6. 同一批次內，同一資產（相同 assets `id`，或相同 `name`）的外觀描述必須一致。

## 4. 必須補全的欄位

與各類型 SKILL.md §4 相同。每列都要補齊該類型的所有欄位後再組裝。

## 5. 不可擅自更改

- 各類型 SKILL.md §5 的所有項目
- `data/batches/batch.csv` 本身（唯讀，不修正使用者資料；問題寫進報告）
- 批量模式不套用任何鎖定例外（見 §3 第 5 步）

## 6. 輸出格式

**1) `output/prompts/batch_prompts.json`**
```json
{
  "meta": {
    "source": "data/batches/batch.csv",
    "generated_at": "<ISO 8601 含時區>",
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
- `items` 的順序與 CSV 相同
- `final_prompt`／`negative_prompt` 依 `prompt_rules.md` §3／§4 組裝；`chatgpt_prompt` 依 `prompt_rules.md` §12 組成
- `suggested_loras` 依 `prompt_rules.md` §8，鍵為 `style`、`<asset_type>`、`assets`

**2) `output/prompts/batch_prompts.csv`**
- 欄位順序：`card_id,asset_type,final_prompt,negative_prompt,aspect_ratio,suggested_loras,needs_confirmation,notes,chatgpt_prompt`（`chatgpt_prompt` 含換行，用雙引號包住即可）
- UTF-8 **含 BOM**（Excel 才能正確顯示中文）；每個欄位都用雙引號包住，欄位內的雙引號寫成兩個雙引號
- `suggested_loras` 存成單行 JSON 字串
- **用程式從 JSON 轉出 CSV**（Node.js 或 Python），不要手寫，避免跳脫錯誤

**3) `output/reports/batch_report.md`**（繁體中文）
- 統計：總列數、成功、需確認、錯誤
- 需確認的列：`card_id`、原因、目前採用的假設、建議要問使用者的問題
- 錯誤的列：行號、`card_id`、原因、修正方式

**4) `output/prompts/batch_chatgpt.md`**（ChatGPT 貼上用）
- 開頭寫來源、生成時間、style_lock／negative_lock 版本、使用方式（每張複製一個區塊貼到 ChatGPT）
- 每張卡一節：標題 `## <card_id>（<asset_type>）`，下面一個 ` ```text ` 區塊放 `chatgpt_prompt`
- `needs_confirmation: true` 的卡在標題下加一行 `⚠ 需確認：<notes>`
- 順序與 CSV 相同；錯誤列不列出

**覆寫規則：** 輸出檔已存在時，先把舊檔改名為 `batch_prompts.<YYYYMMDD-HHMMSS>.json`／`.csv`（報告與 `batch_chatgpt.md` 同理），再寫入新檔。

**完成後回報（繁體中文）：** 四個輸出檔路徑、統計數字、需確認清單，並詢問使用者是否要修正或補充這些列。

**禁止：** 不呼叫任何圖像生成 API、不訓練 LoRA；只產生 prompt。

## 7. 範例輸入與範例輸出

**輸入 `data/batches/batch.csv`：**
```csv
card_id,asset_type,name,extra_description
C001,character,格魯姆,拿著魔法棒的獸人薩滿，火元素
W001,weapon,冰霜法杖,古木杖身，頂端鑲著冰晶
S001,scene,冰封遺跡,被冰雪覆蓋的古代遺跡，黃昏時分
M001,magic,火焰爆發,從地面魔法陣向外爆發的火焰
M002,magic,神秘力量,
```

**輸出（節錄 `batch_prompts.json` 的 `items`）：**
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
    "notes": "name「格魯姆」為專有名詞，assets.json 無登錄，未放入 prompt；外觀依 extra_description 補全。建議登錄到 assets.json 以固定外觀。"
  },
  {
    "card_id": "M002",
    "asset_type": "magic",
    "final_prompt": "an arcane vortex spell effect, … , game-ready card illustration",
    "negative_prompt": "…, character portrait focus, face close-up, indistinct effect silhouette",
    "aspect_ratio": "2:3",
    "suggested_loras": { "style": { "file": "", "weight": null, "status": "reserved" }, "magic": { "file": "", "weight": null, "status": "reserved" }, "assets": [] },
    "needs_confirmation": true,
    "notes": "核心欄位（元素、效果形態）無法從「神秘力量」判斷；暫以奧術漩渦推測，請確認元素與形態。"
  }
]
```
完整輸出見 `output/prompts/batch_prompts.json`、`output/prompts/batch_prompts.csv`、`output/prompts/batch_chatgpt.md`、`output/reports/batch_report.md`。

## 8. 測試範例

### 測試 1：四類混合、全部明確

**輸入：**
```csv
card_id,asset_type,name,extra_description
T101,character,精靈弓箭手,銀髮，全身，站在月光下
T102,weapon,裂焰巨劍,騎士持握，以巨劍為主體，纏繞黑色火焰的巨劍，劍身有裂痕
T103,scene,空中城堡,漂浮在雲海上的城堡
T104,magic,雷槍,從天而降的雷電長槍擊中地面
```
**預期：**
- `generated: 4`、`needs_confirmation: 0`、`errors: 0`
- 每列的 `final_prompt` 都逐字包含 `style_lock.style`、該類型的 `style_append`、`framing_override` 與 `style_lock.quality`
- T103 的 framing 用 scene override（含 `single dominant landmark filling much of the frame`），不含全域 `framing`
- T102 的 `final_prompt` 使用對角擺放與主題環境 framing，不含背景武器殘影；`negative_prompt` 含 weapon 追加詞（包括 `double-exposure weapon echo, duplicate weapon silhouette, oversized weapon shadow in the background`）
- T102 保留騎士持握、黑色火焰與裂痕；持有者是陪襯，持握不觸發確認或 lock_exceptions，負面 prompt 不排除人物或手。
- 每列 `final_prompt` 都有一句 `… color scheme with … accents`
- 每列都有 `chatgpt_prompt`，內容是 `final_prompt` + `Avoid: <negative_prompt>` + 比例文字，且 4 張都出現在 `batch_chatgpt.md`
- CSV 可被 Excel 正常開啟，中文 `notes` 不亂碼

### 測試 2：含模糊列與衝突列

**輸入：**
```csv
card_id,asset_type,name,extra_description
T201,magic,神秘力量,
T202,scene,市集,熱鬧的市集，很多人在逛街
T203,character,無名英雄,很強
```
**預期：**
- 3 列都產生最佳推測 prompt，且 `needs_confirmation: true`
- T202 採選項 A（無人市集），`negative_prompt` 仍保留 `people, crowd`，`lock_exceptions` 不套用
- T203 的 `notes` 註明種族與職業都無法判斷
- `batch_report.md` 為 3 列各列出建議要問使用者的問題

### 測試 3：錯誤列

**輸入：**
```csv
card_id,asset_type,name,extra_description
T301,monster,史萊姆,綠色黏液怪
,weapon,短劍,普通的鐵製短劍
T303,Weapon,短劍,普通的鐵製短劍
T303,scene,洞窟,
T305,magic,,
```
**預期：**
- T301：`asset_type` 不合法 → 錯誤（`notes` 建議改成 `character`）
- 第 3 行：`card_id` 空白 → 錯誤
- T303（第 4 行）：`Weapon` 轉小寫後合法 → 正常產生
- T303（第 5 行）：`card_id` 重複 → 錯誤
- T305：`name` 與 `extra_description` 都空 → 錯誤
- `generated: 1`、`errors: 4`；錯誤列不出現在 `items`，只出現在 `errors` 與報告

