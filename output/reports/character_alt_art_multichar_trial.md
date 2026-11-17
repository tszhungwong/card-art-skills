# 四角色異畫設計實測

## 範圍與進度

2026-11-17。依使用者同意的碎冰倒影方向，補一張岩羊敘事異畫；另外自行設計人類修補師、鳥類信使、瓷面機械守衛三個角色，各產生普通與異畫兩版，共七張新圖。三個新角色的外觀、場景與辨識標記均為本次自動補全。每張以自己的完整英文 TXT 呼叫內建 imagegen 一次，未傳入參考圖片。首輪生成、無標籤盲讀及檔案驗證皆已完成。

岩羊的普通版與動作異畫沿用 [前次實測](character_alt_art_trial.md) 原圖，本次未重新生成。以下保留本輪所有首輪結果，包含未達預期的案例。

## 可複製提示詞與原始成圖

| 案例 | 英文提示詞 | 完整欄位 | 首輪圖片 |
|---|---|---|---|
| 岩羊・碎冰倒影 | [TXT](../prompts/single/20261117-224901_character_alpine-sheep-ice-alt-art.txt) | [JSON](../prompts/single/20261117-224901_character_alpine-sheep-ice-alt-art.json) | [PNG](../images/20261117-224901_character_alpine-sheep-ice-alt-art-round1.png) |
| 風箏修補師・普通版 | [TXT](../prompts/single/20261117-224901_character_kite-mender-standard.txt) | [JSON](../prompts/single/20261117-224901_character_kite-mender-standard.json) | [PNG](../images/20261117-224901_character_kite-mender-standard-round1.png) |
| 風箏修補師・敘事異畫 | [TXT](../prompts/single/20261117-224901_character_kite-mender-alt-art.txt) | [JSON](../prompts/single/20261117-224901_character_kite-mender-alt-art.json) | [PNG](../images/20261117-224901_character_kite-mender-alt-art-round1.png) |
| 琵鷺信使・普通版 | [TXT](../prompts/single/20261117-224901_character_spoonbill-ferrier-standard.txt) | [JSON](../prompts/single/20261117-224901_character_spoonbill-ferrier-standard.json) | [PNG](../images/20261117-224901_character_spoonbill-ferrier-standard-round1.png) |
| 琵鷺信使・動作異畫 | [TXT](../prompts/single/20261117-224901_character_spoonbill-ferrier-alt-art.txt) | [JSON](../prompts/single/20261117-224901_character_spoonbill-ferrier-alt-art.json) | [PNG](../images/20261117-224901_character_spoonbill-ferrier-alt-art-round1.png) |
| 瓷面守衛・普通版 | [TXT](../prompts/single/20261117-224901_character_porcelain-warden-standard.txt) | [JSON](../prompts/single/20261117-224901_character_porcelain-warden-standard.json) | [PNG](../images/20261117-224901_character_porcelain-warden-standard-round1.png) |
| 瓷面守衛・特寫異畫 | [TXT](../prompts/single/20261117-224901_character_porcelain-warden-alt-art.txt) | [JSON](../prompts/single/20261117-224901_character_porcelain-warden-alt-art.json) | [PNG](../images/20261117-224901_character_porcelain-warden-alt-art-round1.png) |

## 補全設定與來源

| 角色 | 共通設定 | 普通版／異畫差異 | 來源 |
|---|---|---|---|
| 岩羊 | 赤褐毛、琥珀眼、黑鼻樑白菱斑、左肩上排一枚下排兩枚白斑、短黑角、平背、四隻分趾蹄，青綠只在湖水；沿用原英文 | 既有湖畔站姿與跳躍，加上雪岩上低頭看冰面倒影；指定裂紋穿過倒影白菱斑 | 本體為使用者既有設定；倒影構想已同意，鏡位與冰裂細節為補全 |
| 風箏修補師 | 黑短髮、左眉上白髮束、梅紫工作衣、左肩藏紅花黃三角補丁；一面黃底紫月風箏 | 站立展示風箏／俯身縫合穿過月紋的裂口，針前有開口、針後有縫線 | 全部為補全 |
| 琵鷺信使 | 象牙白羽、黑匙形喙、左眼黑月形羽紋、兩根長冠羽，朱紅繩領與方黃銅扣、紅環掛黃銅鑰匙 | 渡口木樁上持鑰匙／向水面俯衝，以嘴接住鑰匙紅環，鑰匙尖觸水 | 全部為補全 |
| 瓷面守衛 | 黃銅機體、拱形象牙瓷面、正中單枚鈷藍圓鏡片、鏡片至下巴的分叉銅補縫，炭黑高領與象牙方扣 | 檔案櫃間全身站立／俯身星圖，特寫鏡片中的彎月反射 | 全部為補全 |

所有新角色均未命中資產庫。空白欄位不補入額外道具或表情。比例採預設 2:3；普通版均取全身，修補師與守衛異畫取半身；圖像預期 1024×1536。身體、服裝、道具與配色的既有欄位在同組兩版逐字一致。新圖未指定人類年齡或性別。

## 首輪視覺核對

| 案例 | 可讀之處 | 偏差與判定 |
|---|---|---|
| 岩羊 | 真身肩斑保持上一下二，鼻樑菱斑、雙角和赤褐毛可認；倒影與大裂紋形成安靜場面，青綠未染毛皮 | 主要裂紋穿過倒影額頭與眼附近，未精準切中鼻樑白菱斑。倒影上另有多條細裂紋。頸毛較前次蓬厚。需分開判斷故事可讀度與精確交點。 |
| 修補師 | 黑短髮白髮束、紫衣黃三角補丁和紫月風箏一致；裂口、針、連接兩側的紅縫線均清楚 | 異畫新增圍裙，普通版有腰部工具袋；背景生成額外風箏。讀得出修補事件，但服裝與道具數量並非完全一致。 |
| 琵鷺 | 站姿與俯衝輪廓明顯不同；紅繩方扣、黑匙形喙、紅環黃銅鑰匙都有保留，異畫鑰匙尖與水紋接點清楚 | 展翼尖端超出畫面，違反完整入鏡。眼旁黑羽形狀變動、冠羽數量難精確確認，兩圖鑰匙頭形狀不同；普通版粉花與異畫紅葉形成季節漂移。 |
| 守衛 | 鏡片內彎月、臉部補縫、象牙方扣和星圖能辨認；近景與遠景取景差別清楚 | 異畫新增白髮與人類雙眼，面罩輪廓也改變，跨圖角色一致性失敗。普通版衣裙、地毯與異畫星圖、夜空都出現藍色，違反鈷藍只在鏡片的限制。分叉銅縫的分叉方向與造型也不同。 |

## 無標籤盲讀結果

檢查者只收到七個原始 PNG 的隨機識別檔名與 A–G 標記，未讀提示詞或預期故事。先描述事件，再比較同組兩張。

| 組別 | 檢查者自行讀出的畫面 | 同一角色與異畫辨識 |
|---|---|---|
| A／B 修補師 | A 用紅線縫補黃底紫月風箏的裂口；B 全身站立展示風箏 | 強烈像同一角色，A 為近景異畫、B 為展示；指出圍裙、衣袖及工具配置不同 |
| C／D 琵鷺 | C 木樁上喙懸鑰匙；D 倒頭展翼，鑰匙觸水形成漣漪 | 強烈像同一鳥，C 為展示、D 為動作異畫；指出鑰匙頭、羽冠、眼紋與季節變動 |
| E／F 守衛 | E 白髮面具機械人用圓規繪製星圖；F 無髮單鏡片機械人在檔案櫃間直立 | 能分近景異畫與全身展示，但角色連續性較弱；新增白髮與閉眼狀紋最明顯 |
| G 岩羊 | 獸低頭靠近結冰湖面，裂紋切過倒影；難斷言正在飲水 | 真身與倒影的菱斑、三斑、雙角可見；指出倒影身體與腿的幾何對應不完整 |

修補事件首輪即可獨立讀出。琵鷺的身體動作及觸水接點成立，但是否正在接住掉落鑰匙仍有時序歧義。岩羊的倒影與裂冰形成場面，僅憑圖片仍難區分觀察自己與準備飲水。守衛案例顯示醒目的特寫構圖仍可能失去角色身份。

## 已採用的技能調整

在英文 `character-art/SKILL.md` 的選用流程中加入動作與安靜敘事的撰寫方法，沿用原有欄位。敘事要求可見的物件關係和事件證據，驗證時先讓檢查者自行描述故事；特寫要核對完整頭部結構及模型新增的毛髮、眼睛和配件，點綴色要檢查衣物、道具與環境。倒影需核對指定標記和裂紋的交點。此次未將任何方向定義為固定稀有度等級。

## 檔案驗證

- 2026-11-17 22:55:23（Asia/Taipei），Node.js v24.16.0 執行聚焦斷言，實際執行約 0.04 秒；七份 JSON 解析、欄位集合、同組共通欄位逐字一致、原岩羊特徵沿用均通過。
- 依當前風格鎖 0.4.0、負面鎖 0.3.2 重新組裝，核對完整正向與負面詞、ChatGPT 機械貼上格式及 TXT，全部通過。七個 PNG 皆為 1024×1536。
- 新增技能段落為英文，報告相對連結均可解析。`git --git-dir=.git --work-tree=. diff --check` 通過，另有既有換行格式提示。
- 此次只增補 character-art 的選用指引與本輪提示詞、圖片、報告；以讀取當前內容後插入的方式保留其他任務的技能修改。
- 圖像品質判定採上方首輪偏差與盲讀結果；只有單次案例，尚未量測穩定成功率。

## 原始圖像來源

- alpine-sheep-ice-alt-art → `exec-f5fbe56a-abcd-478d-acc3-d696c68db2ed.png`
- kite-mender-standard → `exec-00282791-e71d-421e-a302-33d70fc36e65.png`
- kite-mender-alt-art → `exec-ce443c20-9b90-4d89-84fc-a6a77fbb8c8a.png`
- spoonbill-ferrier-standard → `exec-101b69e3-b372-4fa9-a5be-28ecb428f084.png`
- spoonbill-ferrier-alt-art → `exec-334f197c-6bb0-4ad5-9b96-313754c6a8c6.png`
- porcelain-warden-standard → `exec-d79f70d2-d739-41f2-bcd4-6953e14ec821.png`
- porcelain-warden-alt-art → `exec-a9b080b2-5745-4716-b8a5-b365ab55e58f.png`
