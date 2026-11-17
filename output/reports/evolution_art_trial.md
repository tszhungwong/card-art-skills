# evolution-art 實測：沼澤斥候

2026-11-17。在專案內依 `skills/evolution-art/SKILL.md` 從中文描述生成基礎、進化兩份角色提示詞。每次生圖皆以獨立文字輸入，未傳入圖片或 LoRA。兩輪共四張。

| 檢查 | 首輪 | 修訂後第二輪 |
|---|---|---|
| 角色自身左耳的 V 形缺口 | 兩張都露出觀者右側的耳朵，耳緣缺口不夠明確 | 兩張同側皆可見向外敞開的三角缺口，進化型開口較寬 |
| 腰帶上的白色雙葉 | 兩張均完整入鏡，葉脈與扣件有差異 | 兩張仍清楚，葉形比例與葉脈細節有差異 |
| 角色本體進化 | 基礎型頸側平滑、手指分離；進化型有頸鰓和指蹼 | 頸側的鰓褶和張手時的薄膜都可直接辨認 |
| 短斗篷 | 兩張都是苔綠短斗篷，扣件不同 | 仍是苔綠短斗篷，繫繩及葉紋有差異 |

**修訂**。第一輪僅描述左耳有 V 形缺口。第二輪將兩份 prompt 的該句同時改為 `a broad sideways-opening V-shaped wedge missing halfway down the outer rim of the character’s left ear, two distinct skin edges framing a triangle of sunlit marsh in the gap`，並指明頭髮後梳、以明亮水面襯托耳朵輪廓。其餘提示詞保持一致於各自的首輪版本。此案例顯示小型剪影標記可以藉由明確的開口和可見背景表達；樣本數無法估計長期成功率。

**輸出核對**。獨立執行者產出的兩份 JSON 皆可解析，`skill` 為 `evolution-art`、`asset_type` 為 `character`、`fields.form` 分別為 `base` 與 `evolved`。核對兩份正向鎖定後綴、完整負面詞、ChatGPT 貼上文字與 2:3 比例。風格鎖為 0.4.0，負面鎖為 0.3.2。另以 `quick_validate.py skills/evolution-art` 驗證技能格式通過，三個技能內相對連結均可解析。

**原始成圖識別**（基礎／進化順序，保留於本次生圖紀錄）：

- 首輪：`exec-a4b7f895-639d-4ba5-a2fe-7ec24793f5e1.png`、`exec-94d10f6f-af02-4dd7-88fa-8ca8d2f34fd4.png`
- 第二輪：`exec-ec98514c-a5c9-4953-b1f8-6a1918723fc0.png`、`exec-2a45079e-455c-4450-92a7-477f5bdec907.png`
