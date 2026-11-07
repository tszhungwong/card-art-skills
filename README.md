# Card Art Skills

[繁體中文](#繁體中文) · [简体中文](#简体中文) · [English](#english)

## 繁體中文

用一句卡牌描述，產生可交給圖像工具的英文提示詞。四種卡牌共用同一套奇幻集換式卡牌（TCG）畫風，各自有專屬構圖。你可以先試一張卡，再用 CSV 處理整批卡牌。實際出圖是下一步，由你在選用的圖像工具中完成。

### 從第一張卡開始

1. 取得完整專案。可以從 GitHub 下載並解壓縮 ZIP，或在終端機執行以下指令。

   ```sh
   git clone https://github.com/tszhungwong/card-art-skills.git
   cd card-art-skills
   ```

2. 在 Codex 等能讀取本機檔案的 AI 助理中，開啟含有 `AGENTS.md`、`skills/` 和 `shared/` 的專案根目錄。在對話框貼上這段需求。

   > 請依 AGENTS.md 和 shared/prompt_rules.md，使用 scene-art 產生一張場地卡的提示詞。黃昏的冰封港口，一座銅頂燈塔立在碎冰之間，是畫面的主體。橫向 3:2，請將結果存成 JSON。

3. 查看助理回傳的設定表。它會標出哪些細節來自你的描述、哪些是自動補全的。若主體不明或要求與規則衝突，助理會先提問；回覆「確認」或直接修正設定即可。要求存檔時，單卡 JSON 會寫入 `output/prompts/single/`。

4. 要在 ChatGPT 出圖，複製回覆中的整段 `chatgpt_prompt`，貼到支援圖像生成的對話。若工具提供正向與負面提示詞欄位，分別貼入 `positive_prompt` 和 `negative_prompt`，再依 `aspect_ratio` 設定比例。成圖可放入 `output/images/`。

### 選擇技能

先決定畫面要讓人一眼看到什麼。例如騎士手持法杖，若重點是杖頭的冰晶，就選 `weapon-art`；若重點是騎士的表情與姿勢，就選 `character-art`。單卡預設為直向 `2:3`。

| 畫面主體 | 技能 | 可直接試用的描述 |
| --- | --- | --- |
| 角色、怪物、召喚獸 | `character-art` | 銀髮精靈斥候，披深綠斗篷，蹲在樹枝上觀察谷地 |
| 武器、道具、裝備 | `weapon-art` | 古木法杖，杖頭鑲著冰晶，銀線纏繞握柄 |
| 地點、環境 | `scene-art` | 山崖上的廢棄天文台，破裂的圓頂朝向日出，橫向 3:2 |
| 法術、技能特效 | `magic-art` | 石板從地面翻起，圍成半圓形的土元素護盾 |

### 用 CSV 產生一批提示詞

先試用專案附的 `data/batches/batch.csv`，或另存自己的 UTF-8 CSV。必須保留以下四個欄名。每列的 `card_id` 須唯一且非空，`name` 和 `extra_description` 至少填一項。可另加 `aspect_ratio` 欄指定比例。

```csv
card_id,asset_type,name,extra_description
S101,scene,冰港燈塔,黃昏的銅頂燈塔立在碎冰之間
M101,magic,岩盾,石板從地面翻起形成半圓護盾
```

在助理對話框輸入：

> 請用 batch-generate 處理 data/batches/batch.csv，並列出需要我確認的 card_id。

如果另存了 CSV，把指令中的路徑換成你的檔案。完成後先看 `output/reports/batch_report.md`。隨附報告的 5 筆範例都產生了提示詞，其中 `M002`「神秘力量」因未指定元素與形態而標為 `needs_confirmation`。你可以回覆「M002 是水元素漩渦，請重新產生這一列」。缺少必要欄名會停止整批；重複 ID 等單列錯誤則會記入報告，其他有效列繼續處理。

| 批量輸出 | 用途 |
| --- | --- |
| `output/prompts/batch_chatgpt.md` | 按卡片複製可貼入 ChatGPT 的完整文字 |
| `output/prompts/batch_prompts.csv` | 用試算表檢閱整批結果 |
| `output/prompts/batch_prompts.json` | 供程式讀取每列的 `final_prompt`、`negative_prompt`、審閱標記與備註 |

要跨卡保留同一角色的外觀，可請助理將角色名稱、別名及固定特徵登錄到 `shared/assets.json`，下次直接用名稱引用。共用畫風與排除項分別由 `shared/style_lock.json` 和 `shared/negative_lock.json` 管理。初次使用可直接沿用這兩份檔案，參考圖與 LoRA 都是選配。

## 简体中文

输入一句卡牌描述，就能得到可直接用于图像生成工具的英文提示词。角色、武器、场地和法术卡各有自己的构图规则，但共用同一套奇幻集换式卡牌（TCG）画风。你可以先试一张卡，也可以用 CSV 批量生成提示词，再到自己选择的工具中出图。

### 从第一张卡开始

1. 从 GitHub 下载并解压项目 ZIP，或在终端执行以下命令。

   ```sh
   git clone https://github.com/tszhungwong/card-art-skills.git
   cd card-art-skills
   ```

2. 在 Codex 等能读取本地文件的 AI 助手中，打开包含 `AGENTS.md`、`skills/` 和 `shared/` 的项目根目录。把下面这段话粘贴到对话框。

   > 请按照 AGENTS.md 和 shared/prompt_rules.md，使用 scene-art 为一张场地卡生成提示词。黄昏的冰封港口，一座铜顶灯塔矗立在碎冰之间，是画面的主体。采用 3:2 横向构图，并将结果保存为 JSON。

3. 查看助手返回的设定表。它会用繁体中文标明哪些细节来自你的描述、哪些由它补全。如果主体不明确，或要求与规则冲突，助手会先提问。你可以确认它的建议，也可以直接修改。要求保存时，单张卡牌的 JSON 文件会写入 `output/prompts/single/`。

4. 在 ChatGPT 中出图时，把完整的 `chatgpt_prompt` 粘贴到支持图像生成的对话中。如果使用的工具分别提供正向和负面提示词输入框，就分别填入 `positive_prompt` 和 `negative_prompt`，再按 `aspect_ratio` 设置画布比例。生成的图片可放入 `output/images/`。

### 选择技能

按画面焦点选择技能。例如，同样画一位手持法杖的骑士，要突出杖头的冰晶，就用 `weapon-art`；要突出骑士的表情和姿势，就用 `character-art`。单张卡牌默认采用 `2:3` 纵向构图。

| 画面主体 | 技能 | 可直接试用的描述 |
| --- | --- | --- |
| 角色、怪物、召唤兽 | `character-art` | 银发精灵斥候披着深绿斗篷，蹲在树枝上观察山谷 |
| 武器、道具、装备 | `weapon-art` | 古木法杖的杖头镶着冰晶，握柄上缠着银线 |
| 地点、环境 | `scene-art` | 山崖上的废弃天文台，破裂的穹顶朝向初升的太阳，横向 3:2 |
| 法术、技能特效 | `magic-art` | 石板从地面翻起，围成半圆形的土元素护盾 |

### 用 CSV 批量生成提示词

可以直接试用项目自带的 `data/batches/batch.csv`，也可以创建自己的 UTF-8 编码 CSV 文件。保留以下四个列名。每行的 `card_id` 必须唯一且非空，`name` 和 `extra_description` 至少填写一项。需要指定画面比例时，可添加 `aspect_ratio` 列。

```csv
card_id,asset_type,name,extra_description
S101,scene,冰港灯塔,黄昏的铜顶灯塔矗立在碎冰之间
M101,magic,岩盾,石板从地面翻起形成半圆形护盾
```

在助手的对话框中输入以下请求。如果使用自己的 CSV，请替换文件路径。

> 请用 batch-generate 处理 data/batches/batch.csv，并列出需要我确认的 card_id。

完成后先看 `output/reports/batch_report.md`。项目自带的示例报告中，5 张卡都生成了提示词。其中 `M002`“神秘力量”没有指定元素和形态，因此被标记为 `needs_confirmation`。你可以回复“把 M002 改为水元素漩涡，重新生成这一行”。缺少必填列会导致整批停止。遇到重复 ID 等单行错误时，助手会将错误记入报告，继续处理其他有效行。

| 批量输出 | 用途 |
| --- | --- |
| `output/prompts/batch_chatgpt.md` | 按卡牌复制可直接粘贴到 ChatGPT 的完整提示词 |
| `output/prompts/batch_prompts.csv` | 在电子表格中查看整批结果 |
| `output/prompts/batch_prompts.json` | 用程序读取每张卡的 `final_prompt`、`negative_prompt`、待确认标记和备注 |

要让同一角色在多张卡中保持一致，可以请助手将角色名、别名和固定外貌特征登记到 `shared/assets.json`，之后按名称引用。共用画风由 `shared/style_lock.json` 管理，负面提示词由 `shared/negative_lock.json` 管理。首次使用可直接沿用这两个文件，参考图和 LoRA 均为可选项。

## English

Turn a short card description into English prompts for an image generator. The four card types share a fantasy trading card game (TCG) art style, each with its own composition rules. Start with one card or prepare a whole set from a CSV. You render the artwork in your chosen image tool afterward.

### Make your first card

1. Download and extract the repository ZIP from GitHub, or clone it in a terminal.

   ```sh
   git clone https://github.com/tszhungwong/card-art-skills.git
   cd card-art-skills
   ```

2. Open the project folder containing `AGENTS.md`, `skills/`, and `shared/` in a file-aware AI assistant such as Codex. Paste this request into the assistant chat.

   > Follow AGENTS.md and shared/prompt_rules.md. Use scene-art to prepare a prompt for a frozen harbor at dusk. A copper-roofed lighthouse rising above the broken ice is the focal point. Use a 3:2 landscape composition and save the result as JSON.

3. Review the settings table. The assistant explains its choices in Traditional Chinese and writes the visual prompts in English. If the subject is unclear or a request conflicts with a rule, reply to the clarification with your choice or revised details. Asking to save a single card writes its JSON to `output/prompts/single/`.

4. To render in ChatGPT, paste the entire `chatgpt_prompt` block into an image-enabled chat. For tools with separate prompt fields, paste `positive_prompt` and `negative_prompt` into the corresponding fields and set the canvas to `aspect_ratio`. You can keep the resulting art in `output/images/`.

### Choose a skill

Choose by focal point. For a knight holding a staff, use `weapon-art` to feature the crystal at its tip or `character-art` to feature the knight’s expression and pose. The default aspect ratio is `2:3` portrait.

| Focal point | Skill | Example request |
| --- | --- | --- |
| Character, monster, summoned creature | `character-art` | A silver-haired elven scout in a dark green cloak, crouching on a branch above a valley |
| Weapon, item, equipment | `weapon-art` | An ancient wooden staff with an ice crystal at its tip and silver wire around the grip |
| Location, environment | `scene-art` | An abandoned cliffside observatory, its broken dome facing the sunrise, 3:2 landscape |
| Spell, skill effect | `magic-art` | Stone slabs rise from the ground to form a semicircular earth shield |

### Generate a batch from CSV

Try the included `data/batches/batch.csv` or save your own UTF-8 CSV. Keep all four column headers below. Give each row a unique, nonempty `card_id` and fill in at least one of `name` and `extra_description`. Add an optional `aspect_ratio` column to override the default.

```csv
card_id,asset_type,name,extra_description
S101,scene,Ice Harbor Lighthouse,A copper-roofed lighthouse above broken ice at dusk
M101,magic,Stone Shield,Stone slabs rise from the ground to form a semicircular shield
```

In the assistant chat, enter:

> Use batch-generate to process data/batches/batch.csv and list the card IDs that need my review.

Substitute your own file path if needed. Open `output/reports/batch_report.md` first. In the included example report, all five rows produced prompts. One row, `M002` (“Mysterious Power”), is flagged with `needs_confirmation` because its element and shape are unclear. A follow-up could be “Make M002 a water vortex and regenerate that row.” Missing required headers stop the batch. Row errors such as duplicate IDs are recorded while valid rows continue.

| Batch output | Use |
| --- | --- |
| `output/prompts/batch_chatgpt.md` | Copy a complete ChatGPT prompt for an individual card |
| `output/prompts/batch_prompts.csv` | Review the set in a spreadsheet |
| `output/prompts/batch_prompts.json` | Read each card’s `final_prompt`, `negative_prompt`, review flag, and notes in code |

To keep a recurring character recognizable, ask the assistant to register its name, aliases, and fixed visual traits in `shared/assets.json`, then refer to it by name. The shared art direction lives in `shared/style_lock.json` and the exclusions in `shared/negative_lock.json`. The supplied files are enough to get started. Reference images and LoRAs are optional.
