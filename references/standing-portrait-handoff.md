# 輸出與下游交接

## 建議輸出目錄

每次建立新版本，例如 `output/character-visual-spec/<character-id>/v001/`。檔名角色代號可使用來源檔名，但不等同正式姓名。

```text
v001/
  source/                         原始立繪副本
  masters/
    front.png                     正面全身獨立圖
    three-quarter.png             45度全身獨立圖
    profile.png                   90度側面獨立圖
    back.png                      背面全身獨立圖
  details/
    key-visual.png
    proportions.png
    eyes.png
    hair.png
    clothing.png
    accessories.png
    palette.png
  expressions/
    neutral.png
    smile.png
    laugh.png
    surprise.png
    anger.png
    sadness.png
    blush.png
    serious.png
    contact-sheet.png
  lineart/
    front.png
    three-quarter.png
    profile.png
    back.png
  sheets/
    01-views.png
    02-face-hair.png
    03-clothing-accessories.png
  character-visual-spec.json       最小素材索引與下游入口
  design-lock.md                  姓名、讀音、3～5個設計重點
  production-notes.md              可見／推定項目、生成紀錄與QA
```

圖像模組可以有多張細節圖或額外設定頁，索引列出實際檔案。僅建立已存在的交付物路徑，不以空白圖或待辦欄位冒充完成。

## 最小索引

JSON是製作與交接資料，不是額外印在設定集上的人物設定欄位。以下是欄位形式範例，不代表已有圖檔或已完成量測；實際執行時改填真實資訊。

```json
{
  "schema_version": "1.0",
  "character_id": "phebe-gun-set",
  "identity": {"name": null, "reading": null},
  "status": "partial",
  "source": {"path": "source/Phebe_gun_set.png", "sha256": null},
  "views": {
    "front": {"path": "masters/front.png", "camera_side": "front", "yaw_degrees": 0, "width": null, "height": null, "sha256": null},
    "three_quarter": {"path": "masters/three-quarter.png", "camera_side": "character_left", "yaw_degrees": 45, "width": null, "height": null, "sha256": null},
    "profile": {"path": "masters/profile.png", "camera_side": "character_left", "yaw_degrees": 90, "width": null, "height": null, "sha256": null},
    "back": {"path": "masters/back.png", "camera_side": "back", "yaw_degrees": 180, "width": null, "height": null, "sha256": null}
  },
  "detail_files": [],
  "expression_files": [],
  "lineart_files": [],
  "sheet_files": [],
  "design_lock": "design-lock.md",
  "production_notes": "production-notes.md",
  "inferred_features": [],
  "handoff": {
    "recommended_view": "three_quarter",
    "reference_role": "visual_reference",
    "runtime_canvas": null,
    "runtime_eye_mouth_coordinates": null
  },
  "qa": {"file_checks": "pending", "visual_review": "pending", "open_issues": []}
}
```

- 所有路徑相對索引所在目錄；尺寸與SHA-256根據實際文件計算。缺少的視圖省略並列入 `qa.open_issues`，不能保留不存在的完成路徑。
- `yaw_degrees` 代表離正面的角度量，左右由 `camera_side` 決定，避免不同工具的正負號約定衝突。
- `detail_files`、`expression_files`、`lineart_files`、`sheet_files` 各元素使用 `{ "id": "...", "path": "..." }`；表情ID可依使用者清單調整。
- `inferred_features` 使用 `{ "part": "...", "decision": "...", "basis": "inferred|user_specified" }`；原圖可見的外觀證據記在製作備註。
- `recommended_view` 是建議參考，不限制下游使用者選擇；下游若選擇另一角度，改用相應已檢查圖。
- `status=complete` 只用於必備模組齊全且檔案與目視檢查均通過的版本。未知姓名或讀音保持null，不因此阻擋圖像交付。

## 製作備註內容

保留來源與生成提示詞、實際工具／模型資訊（可取得時）、參考圖片順序、輸出尺寸、修正次數、基準版本、推定補完與QA結果。後續修訂外觀時，能追溯哪個決定影響了哪些圖。

不額外加入人物背景、個性、服裝組成規格表或配件尺寸表；這份備註只服務於圖像一致性與推定資訊的追溯。

## 與 standing-portrait-SKILL.md 的邊界

本次規劃已閱讀工作專案中的 `standing-portrait-SKILL.md`。其現有流程會先完成角色與構圖驗收，再凍結姿勢母圖、SHA、眼嘴遮罩與座標，接著製作表情與必要肢體版本。

建議下游銜接順序：

1. 讀取本技能輸出的索引、設計重點與推定備註。
2. 選用 `masters/front.png` 或 `masters/three-quarter.png` 為主要角度參考；需要時引用細節圖與表情圖。
3. 依下游實際畫布、構圖與姿勢建立自己的母圖。設定集全身圖不因檔名為masters就自動具有像素鎖定或插件相容資格。
4. 重新驗收母圖並量測眼嘴位置；設定集不提供或捏造這些執行期座標。
5. 後續表情與肢體圖持續遵循3～5個設計重點；改變角色朝向或頭部結構時使用下游既有的獨立姿勢契約。

本技能的8種表情用來規範臉部畫法，不能當作下游60表情已完成，也不能直接當作可貼回同一母圖的疊片。下游的720×720等預設尺寸不限制本技能的高解析視覺參考素材。

此文件規劃交接方式，不修改既有下游技能或其參考檔案。實際執行後續技能時仍須讀取它當前的完整指示與依賴檔案。
