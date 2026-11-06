# 參考圖風格分析報告

- 分析日期：2026-11-06
- 樣本：`data/references/` 四類各 20 張，共 80 張（全部逐張看過）
- 方法：逐張目視判讀 7 個面向；表中「約 N/20」是目視統計的約略次數，不是程式量測
- 限制：參考圖解析度只有 460–578 px 寬，筆觸細節的判讀有限
- 分析時的 style_lock：v0.1.0（初始值）
- **狀態：使用者已於 2026-11-06 批准方案 A，`style_lock.json` 與 `negative_lock.json` 已更新為 v0.2.0**（第 7 節內容）。第 10 節三項皆已決定：方案 A；武器構圖跟參考圖；**預設比例維持 2:3 直向**（使用者 2026-11-06 決定，5:4／3:2 保留為可選比例）

---

## 總結

參考圖是**明亮、高飽和的日系 TCG 插畫風格**：主體邊緣清晰、漸層平滑、發光與星芒閃光很多、構圖動態且主體塞滿畫面、背景是柔化的主題環境。
這和現行 style_lock 的「暗黑奇幻、半寫實、低飽和暗紅／古金／炭灰、居中構圖、乾淨暗色背景」**在色彩、光線、構圖、背景四個面向幾乎相反**。

| 面向 | 現行 style_lock v0.1.0 | 參考圖實際情況 | 一致？ |
|---|---|---|---|
| 類型氛圍 | dark fantasy | 明亮奇幻，少數暗色題材也用飽和的紫或紅 | ✗ |
| 媒介 | semi-realistic digital painting, painterly brushwork | 角色是動漫賽璐璐；武器是半寫實光澤硬表面；場地是動畫背景式繪畫；魔法是動漫特效 | 部分 |
| 色彩 | muted crimson, antique gold and charcoal | 高飽和，每張以單一元素色系為主，搭配互補色的發光；金色裝飾與寶石常見 | ✗（只有金色一致） |
| 光線 | cinematic rim lighting, dramatic but readable | 高明度、強輪廓光、bloom 光暈、星芒閃光、自發光特效 | 部分 |
| 構圖 | centered composition | 對角線動態、主體塞滿畫面、常被畫框裁切 | ✗ |
| 背景 | clean dark atmospheric background | 柔焦的主題環境（舞台、天空、叢林、遺跡…）加光斑與粒子 | ✗ |
| 負面詞 | `cropped subject`、`blurry` | 參考圖刻意裁切主體邊緣，背景刻意模糊 | ✗（會互相抵觸） |
| 材質 | detailed materials | 高光澤：拋光金屬、多切面寶石、緞面布料、亮面髮絲 | ✓ |
| 輪廓 | clean readable silhouette | 主體輪廓清楚 | ✓ |

---

## 1. 角色（characters，20 張）

| 面向 | 共同特徵 | 頻率 | 例外 |
|---|---|---|---|
| 繪畫媒介與筆觸 | 動漫美少女插畫：乾淨線稿、賽璐璐加柔和漸層、髮絲多道高光、大眼多層反光 | 20/20 | #12 較復古的噴槍動畫風；#13 平塗電視動畫風；#14 筆觸較鬆 |
| 色彩板 | 高飽和、高明度；每位角色有一個招牌色系（髮色與服裝同色系），背景用互補或類比色 | 20/20 | #11、#18 較暗，但仍是飽和的紫或綠 |
| 光線方向與對比 | 柔和正面或斜前方主光，加強烈逆光或舞台光；有輪廓光、光斑、光芒；陰影淺且帶色彩 | 約 18/20 | — |
| 構圖與視角 | 胸上或腰上的近景；臉在畫面上三分之一；常有傾斜或對角動勢；手臂、頭髮被畫框裁切；約半數有伸向鏡頭的透視手 | 構圖 20/20、裁切約 18/20 | 沒有任何一張是全身 |
| 背景處理 | 柔焦的情境背景（舞台燈、藍天白雲、室內窗景、水中氣泡、星空、花草）加 bokeh 光斑與閃光粒子 | 約 17/20 | 沒有任何一張是純色或暗色背景 |
| 材質表現 | 緞面光澤布料、荷葉邊、緞帶；金色滾邊與肩章；多切面寶石；金屬髮飾與角 | 金飾或寶石約 17/20 | — |
| 整體氛圍 | 開朗、有活力、偶像或舞台感；張嘴笑為主，少數是自信或挑釁的表情 | 笑容約 16/20 | #11、#20 表情較成熟 |

**題材性內容（不寫入 style lock）：** 20/20 都是女性角色；約 9/20 拿麥克風（偶像主題系列）；部分有角、翅膀、尖耳等異族特徵。這些屬於這一系列的題材，由使用者輸入決定，不應鎖進風格。

---

## 2. 武器（weapons，20 張）

| 面向 | 共同特徵 | 頻率 | 例外 |
|---|---|---|---|
| 繪畫媒介與筆觸 | 半寫實光澤硬表面渲染：平滑噴槍漸層、銳利邊緣、強烈金屬反光，幾乎沒有線稿 | 約 14/20 | #03、#09、#15 是柔和繪畫風；#07、#16 帶動漫感 |
| 色彩板 | 武器本體高飽和，配色依元素（綠＝自然、藍＝水或風、紅紫＝暗、金＝機械）；背景比武器淡、飽和度低 | 20/20 | — |
| 光線方向與對比 | 明亮的日光或主題光；刀尖與寶石上有星芒閃光；武器外圍有發光輪廓（aura） | 星芒或發光約 12/20 | #02 是暗色雷雨 |
| 構圖與視角 | **對角線擺放**（多為左下到右上）、武器佔滿畫面、常超出畫框；近景微透視 | 對角 19/20、超框約 16/20 | — |
| 背景處理 | 符合武器主題的實景環境（遺跡、叢林、花田、山脈、城堡夜景、城市、齒輪），柔化處理；**背景常有一個放大、半透明的同款武器殘影（雙重曝光）** | 主題背景 20/20、殘影約 17/20 | 沒有任何一張是乾淨純色背景 |
| 材質表現 | 拋光鋼與鉻、金色雕飾、圓形或多切面寶石、部落風雕紋、植物或昆蟲等有機材質、機械零件 | 20/20 | — |
| 整體氛圍 | 英雄感、收藏品展示感、鮮豔 | 約 17/20 | #02、#07、#16 偏暗黑，但色彩仍飽和 |

**沒有手或持有者：** 20/20，和現行 weapon 負面詞一致。
**與你在 weapon-art 指定的「武器居中、乾淨背景」不同：** 參考圖是對角擺放、主題背景、殘影。

---

## 3. 場地（scenes，20 張）

| 面向 | 共同特徵 | 頻率 | 例外 |
|---|---|---|---|
| 繪畫媒介與筆觸 | 混合三種：動畫背景式賽璐璐加線稿、概念美術式繪畫筆觸、柔和數位繪畫。共通點是漸層柔和、空氣遠近法、焦點處細節最多 | 動畫約 6、繪畫約 9、柔和約 5 | 風格差異是四類中最大的 |
| 色彩板 | 每張有一個主導的環境色調（粉紅夕陽、紫霧、青綠螢光、金黃沙漠、翠綠），飽和度中高；搭配互補色的發光 | 20/20 | — |
| 光線方向與對比 | 大氣光：夕陽或逆光、地標本身發光（金字塔、火山、培養槽）、生物螢光；多數中高明度 | 約 15/20 | #06、#09、#11、#12 較暗，但用飽和的紫或青 |
| 構圖與視角 | **單一主導地標或現象佔據大面積**（城塞、塔、巨樹、冰晶山、石壁、骨骸、火山、金字塔、方尖碑）；中景距離，不是超廣角小地標；平視或仰視為主 | 約 18/20 | #20 是鳥瞰；#03、#15 是群體地景 |
| 背景處理 | 漸層天空、雲、霧、塵土；以大氣遠近法表現景深 | 20/20 | — |
| 材質表現 | 岩石、冰晶、樹皮、水花、沙；材質比角色和武器寫意 | — | — |
| 整體氛圍 | 奇幻、壯觀、有冒險感，元素屬性鮮明 | 約 16/20 | #06 毒沼、#09 研究所是陰森感 |

**生物：** 約 9/20 有生物（蝙蝠、鳥、巨蟲、魔像、沙蛇、鷹…），人類只有 #17 的小剪影。
**特效疊加：** 約 11/20 有閃光、能量線、粒子、六角屏障或速度線。

---

## 4. 魔法（magic，20 張）

| 面向 | 共同特徵 | 頻率 | 例外 |
|---|---|---|---|
| 繪畫媒介與筆觸 | 動漫特效：邊緣銳利的能量形狀、鋸齒狀閃電線、漫畫速度線；部分是柔和繪畫 | 動漫特效約 10、繪畫約 6、柔和約 4 | — |
| 色彩板 | 依元素的高飽和色（火＝橘紅、暗＝紫、雷＝黃、水冰＝青、自然＝綠），常配互補色（紫配橘、青配金） | 20/20 | — |
| 光線方向與對比 | **特效本身是主光源**：bloom、光暈、過曝的白熱核心、鏡頭光、星芒；亮的特效和較暗的周圍形成強對比，但周圍仍帶色彩、不是純黑 | 自發光 20/20、白熱核心約 8/20 | — |
| 構圖與視角 | 爆發式動態構圖：對角、放射、速度線、強透視；特效從邊到邊填滿畫面；焦點常偏離中心 | 動態約 18/20、正中央約 6/20 | #02 傳送門、#17 漩渦、#18 雷網是置中 |
| 背景處理 | 暗色但帶顏色的環境（暴風雲、洞窟、海面、電子格線），做動態模糊；細節少 | 約 17/20 | — |
| 材質表現 | 能量、火焰、水花、閃電、羽毛、金屬球體 | — | — |
| 整體氛圍 | 爆發、高能量、動感；少數可愛或輕鬆（#06、#13、#14） | 約 16/20 | — |

**生物：** 約 15/20 畫面中有生物或角色（龍、鳳凰、海狸、青蛙、水獺、羊、史萊姆少女…），作為施法者、受術者或特效的化身。現行 magic-art 預設「不畫施法者」，與參考圖不同。

---

## 5. 四類合併結論（建議寫入 style lock 的共同風格）

| 面向 | 四類共同點 | 類型差異（建議放進 `by_type`） |
|---|---|---|
| 繪畫媒介與筆觸 | 日系 TCG 數位插畫；主體邊緣清晰、平滑漸層；背景較柔化 | 角色：線稿加賽璐璐。武器：光澤硬表面。場地：動畫背景式繪畫。魔法：銳利能量形狀加速度線 |
| 色彩板 | 高飽和、高明度；每張以單一元素色系為主，搭配互補色發光；金色裝飾與寶石常見 | 場地有強烈的整體環境色調 |
| 光線方向與對比 | 明亮、強輪廓光、bloom 光暈、星芒閃光；陰影淺且帶色彩、少用純黑 | 魔法：特效自發光、白熱核心 |
| 構圖與視角 | 對角線動態、主體塞滿畫面、允許被畫框裁切 | 角色：胸上近景。武器：對角擺放加殘影。場地：單一主導地標。魔法：爆發填滿畫面 |
| 背景處理 | 柔焦的主題環境、有景深與空氣感、光斑與粒子；**不用純色或乾淨暗色背景** | 魔法：暗色動態模糊背景 |
| 材質表現 | 高光澤：拋光金屬、多切面寶石、緞面布料、亮面髮絲 | — |
| 整體氛圍 | 明亮奇幻、有活力、英雄感、收藏卡質感 | — |

---

## 6. 構圖比例觀察

| 類型 | 參考圖尺寸 | 比例 | 現行預設 |
|---|---|---|---|
| 角色 | 554×465（17 張）、555×466（3 張） | 約 1.19:1（橫向，接近 5:4） | 2:3 直向 |
| 武器 | 578×375（20 張） | 約 1.54:1（橫向，接近 3:2） | 2:3 直向 |
| 場地 | 460×386（15 張）、554×465（5 張） | 約 1.19:1 | 2:3 直向 |
| 魔法 | 460×386（18 張）、554×465（2 張） | 約 1.19:1 | 2:3 直向 |

如果你的卡框藝術區和參考圖相同，建議預設改為：角色、場地、魔法 `5:4`（1120×896），武器 `3:2`（1216×832）。比例寫在 `prompt_rules.md` §2，不屬於鎖定檔。

---

## 7. 修訂草案 v0.2.0：方案 A（貼近參考圖，建議）

> **尚未套用。** 你確認後才會寫入 `shared/style_lock.json` 與 `shared/negative_lock.json`。

### 7.1 `style_lock.json` 變更

| 欄位 | v0.1.0 | v0.2.0 提案 | 依據 |
|---|---|---|---|
| `style` | dark fantasy … muted crimson, antique gold and charcoal palette … | 見下方 JSON | 第 5 節：媒介、色彩、光線、材質 |
| `framing` | centered composition, clean dark atmospheric background, … | dynamic diagonal composition, subject fills the frame, softly blurred thematic background with atmospheric depth, … | 構圖與背景四類一致 |
| `quality` | （不變） | （不變） | 與參考圖不衝突 |
| `negative` | … blurry … cropped subject | blurry → `blurry subject`；移除 `cropped subject`，改為 `awkward cropping, cut-off head`；新增 `dull muted colors, desaturated, muddy colors, photorealistic, 3d render` | 參考圖刻意柔焦背景與裁切；加強高飽和 |
| `by_type.*` | 只有 scene 有 framing override | 四類都加 `style_append`；角色、武器、場地、魔法都加 `framing_override` | 第 1–4 節的類型差異 |

```json
{
  "style": "vibrant fantasy trading card game illustration, Japanese TCG art style, anime-influenced digital painting, smooth gradient shading with crisp clean edges, vivid saturated colors, element-themed color scheme with complementary accent glow, luminous bloom and glowing particles, sparkling star-shaped light glints, glossy specular highlights, bright high-key lighting with strong rim light, professional TCG card art",
  "framing": "dynamic diagonal composition, subject fills the frame, softly blurred thematic background with atmospheric depth, no card frame, no text, no watermark, no UI",
  "quality": "highly detailed, clean anatomy, consistent proportions, crisp edges, game-ready card illustration",
  "negative": "extra limbs, extra fingers, deformed hands, bad anatomy, distorted face, blurry subject, low detail, jpeg artifacts, text, letters, watermark, signature, UI, card frame, border, cluttered background, multiple characters, awkward cropping, cut-off head, dull muted colors, desaturated, muddy colors, photorealistic, 3d render",
  "by_type": {
    "character": {
      "style_append": "anime character art, clean lineart, cel shading with soft gradients, large expressive eyes with layered highlights, glossy hair highlights, gold trim and gemstone accents",
      "framing_override": "dynamic diagonal composition, character fills the frame, softly blurred thematic background with bokeh and sparkles, no card frame, no text, no watermark, no UI"
    },
    "weapon": {
      "style_append": "semi-realistic glossy hard-surface rendering, polished reflective metal, faceted gemstones with star glints, glowing aura outlining the weapon",
      "framing_override": "weapon placed diagonally across the frame, translucent oversized double-exposure echo of the same weapon in the background, softly blurred thematic environment matching the weapon's element, no card frame, no text, no watermark, no UI"
    },
    "scene": {
      "style_append": "anime fantasy background art, painterly environment, atmospheric haze and aerial perspective, strong ambient color cast",
      "framing_override": "single dominant landmark filling much of the frame, atmospheric depth, no card frame, no text, no watermark, no UI"
    },
    "magic": {
      "style_append": "anime-style magic VFX, crisp sharp-edged energy shapes, manga speed lines, white-hot overexposed core with intense bloom",
      "framing_override": "explosive dynamic composition, effect fills most of the frame, dim colored background with motion blur, no card frame, no text, no watermark, no UI"
    }
  }
}
```

### 7.2 `negative_lock.json` 變更

- `negative`：與 7.1 相同（兩檔保持一致）
- `append_by_type`：

| 類型 | v0.1.0 | v0.2.0 提案 | 依據 |
|---|---|---|---|
| character | （空） | `chibi, super deformed` | 參考圖是正常頭身比例 |
| weapon | … `multiple weapons`, weapon rack | 移除 `multiple weapons`，改為 `weapon pile, weapon rack` | `multiple weapons` 會抑制約 17/20 張都有的殘影 |
| scene | people, **character**, crowd, … | `people, crowd, human figure, close-up portrait, flat empty background` | 移除 `character`，讓約 9/20 張出現的生物可以出現 |
| magic | （不變） | `character portrait focus, face close-up, dull flat lighting` | — |

---

## 8. 方案 B（維持暗黑奇幻，只借用構圖與光效）

如果你要的是暗黑奇幻世界觀，參考圖只當構圖與光效參考：

| 欄位 | 方案 B 做法 |
|---|---|
| `style` | 保留現行色板（muted crimson, antique gold and charcoal），新增 `luminous glow effects, sparkling star-shaped light glints, glossy specular highlights` |
| `framing` | 改為 `dynamic diagonal composition, subject fills the frame, softly blurred dark thematic background, …` |
| `negative` | 同方案 A：`blurry` → `blurry subject`、`cropped subject` → `awkward cropping, cut-off head` |
| `by_type` | 只借用構圖：武器對角擺放加殘影、場地單一主導地標、魔法爆發填滿畫面；不加入動漫媒介詞 |

方案 B 生成的圖會和參考圖有明顯色調差異；混用參考圖時，同一副牌的視覺一致性會較低。

---

## 9. 套用後需要連動修改的檔案

以下檔案不是鎖定檔，但為了與新的 style lock 一致，需要一起更新：

| 檔案 | 修改內容 |
|---|---|
| `shared/prompt_rules.md` §3 | 「元素色只當點綴」改為「元素色是主色系，互補色作為點綴發光」；更新範例 |
| `shared/prompt_rules.md` §2 | 依你的卡框比例決定是否改成 5:4／3:2（見第 6 節） |
| 5 個 `SKILL.md` | `description` 拿掉 `dark fantasy`；§4 補全原則中與舊色板相關的描述；§7 範例 prompt 重新產生 |
| `character-art` | 構圖模板的 `head and hands fully in frame` 改為 `face fully visible`（參考圖常裁切手） |
| `weapon-art` | 構圖模板與「乾淨背景」改為對角擺放、主題背景、殘影（方案 A） |
| `magic-art` | 新增選填欄位「相關生物」，參考圖約 15/20 有生物 |
| `scene-art` | 構圖模板強調單一主導地標；允許選填生物 |
| `output/prompts/batch_prompts.*` | 用新的 lock 重跑 |

---

## 10. 待你決定

1. **風格方向**：方案 A（貼近參考圖，建議）或方案 B（維持暗黑奇幻）
2. **武器構圖**：跟參考圖（對角、主題背景、殘影），還是保留你原本指定的「居中、乾淨背景」
3. **預設比例**：維持 2:3 直向，還是配合參考圖改成 5:4／3:2
