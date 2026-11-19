# SDS-15 使用手冊功能對照（Manual Mapping）

> 對應需求：`SRS/08_User_Manual_Requirements.md`  
> 依據：EdgeView 使用手冊 V4.2.x

## 1. 目的

本章把手冊中的使用者功能對照到實際設計與程式碼，並補記手冊明列的限制、部署方式與差異。目的：

1. 讓測試/支援能從手冊章節找到對應實作。
2. 讓開發知道手冊承諾的行為落在哪些模組。
3. 記錄手冊與程式碼的落差（見 §14）。

## 2. 章節對照總表

| 手冊章節 | 功能 | 設計/程式碼 |
|----------|------|-------------|
| 1.4~1.5 | 系統需求、安裝 | SDS-10 §13、安裝程式（repo 外） |
| 1.5.4 | 授權/啟用 | `license/`、`wxlic`、`graphix.lic`、SDS-04 `license.go` |
| 1.6 | 伺服器設定 | Metadata `SettingsController`、UI `/sc/opcua`、`/system` |
| 1.7 / 16 | 客戶端連線 | `ui/page/HttpConnect`、`Login`、`Project`、WASM/APP |
| 2.1~2.3 | 操作介面 | `main.qml`、`ui/page/Editor`、`ViewerFunction` |
| 3 | 圖紙/樣板/圖層/底圖/APP 圖紙/預覽 | `Display*`、`ObjectPath`、`ui/page/Editor/content`、`ViewerContent` |
| 4 | 控件 | `ui/component/widgets/*`、`SCDraw2D`、`Chart.js`、`NanoCanvas` |
| 5 | 變數 | `WAVariableManager`、`Variable`/`Variablebody` |
| 6 | 腳本 | `WAJavaScriptManager`、`JavaScriptEditorDialog`、`JSTable` |
| 7 | SVG 編輯 | `WASvgCanvas`、`Svg*`、Image/File |
| 8 | 記錄查詢 | `RecordManager`、`RecordController`、Runtime |
| 9 | 事件 | `BaseWidget`、`wabindings.js`、`WAJavaScriptManager` |
| 10 | 動畫 | `BaseWidget` 動畫管線、`SCDraw2D` |
| 11 | 顏色/效果 | `BaseWidget`、`SCDraw2D`、`MaskSource` |
| 12 | 模型/實例化 | `WACustom`、`WACustomRectangle`、`WADataModelManager`、DataModel API |
| 13 | 檔案管理 | `ImageTable`、`AudioTable`、`PluginFile`、`scene3d` |
| 14 | 工程配置 | `Project`/`Display`/`User`/`Recipe`/`Group`、Config 頁 |
| 15 | 伺服器設定 | Metadata `Settings`/`OPC UA`/`File`、服務管理 |
| 16 | 客戶端連接 | `HttpConnect`、`Project`、`GeneralSettings`、APP/OEM |
| 17 | 使用者/角色 | `UserInfo`、`UserAcc`、`UserScy`、`OPCUA_User*` |
| 18 | 插件 | `WebPlugin`、`PluginFile`、`_public/plugin`、`WALottieAnimation`、`WAWebPlugin` |

## 3. 操作介面

### 3.1 六大區塊

| 區塊 | 實作 |
|------|------|
| 主選單 | `ui/page/Editor/top/Header.qml`；File/Edit/View/App/Tool/Help/Language |
| 工具列 | `ui/page/Editor/top/*`；Undo/Redo/Save/Ortho/Batch/Preview/Browse/JS/Variable/SVG/Model/Audio/Image/3D/File/Record |
| 圖紙編輯 | `ui/page/Editor/content/EditorContent.qml`、`EditorAtPath.qml` |
| 編輯功能框 | `Editor` 右側/浮動工具（縮放/對齊/圖層） |
| 左側檢視 | `ui/page/Editor/left/Widgets.qml`、`DataSource.qml`、`VariExplorer.qml`、`DeviceExplorer.qml`、`Information.qml` |
| 右側檢視 | `ui/page/Editor/right/Properties.qml`、`LayerSettings.qml`、`DrawingTree.qml`、`BindingDataForm.qml`、`JSListForm.qml` |

### 3.2 模式與狀態

- `openMode`：`config/edit/preview/view`；預覽與瀏覽使用 Viewer 元件。
- 檢視面板顯示/隱藏由 `Global`/Editor 設定保存。
- 右鍵選單：`ui/page/Editor/component/menu/*`（群組、對齊、層級、複製、批量、事件、資訊）。

## 4. 圖紙

### 4.1 樣板

| 功能 | 設計 |
|------|------|
| 新建/編輯樣板 | 圖紙屬性 + `Display` 內容；樣板為可重用 JSON |
| 高度/寬度/固定比例 | `BaseWidget`/圖紙屬性 |
| 背景圖片/壓縮/填充/透明度 | `WAImage`/SCDraw2D 填充與遮罩 |
| 綁定/取消綁定 | 圖紙屬性 `template` 參照 |

### 4.2 更新率

| 設定 | 值 | 實作 |
|------|----|------|
| 基本更新 | 25/50/100 ms | Editor/Viewer 更新計時器；控件 update rate |
| 閃爍更新 | 基本週期倍數 | `BaseWidget` 閃爍 |
| 變數更新 | 25/50/100 ms | `WAVariableManager.updateInterval` |
| 外部資料更新 | 可設定 | `Settings.client_data_update_interval`、`Global.treetimer` |

> 手冊的 25/50/100 ms 為圖紙/控件繪製與變數計算；外部資料（Tag）仍受資料源輪詢/掃描週期限制（Client 最低 500 ms、預設 1000 ms），兩者不同層級。

### 4.3 圖層

- 16 層（0–15），圖層 0 為預設編輯層。
- `ui/page/Editor/right/LayerSettings.qml`；控件屬性 `layer`。
- 每層 `editable`/`visible`；切換時更新畫布。

### 4.4 底圖與優先層級

- 背景圖片 > 填充漸變 > 背景色。
- `WAImage` + `SCDraw2D` gradient；`BaseWidget` 圖形屬性。

### 4.5 APP 圖紙

- `DisplayMobile`（phone/tablet）與 `Display` 關聯；同一畫面 id + type。
- 新增/刪除由物件樹/分頁右鍵；控件複製後獨立；變數/腳本共用（`displayid` 關聯）。

### 4.6 預覽

- `EditorFunction._viewDrawing()`、`ViewerContent`；預覽不儲存，返回清除 Viewer 狀態。

## 5. 控件與屬性

### 5.1 目錄

| 分類 | 控件 |
|------|------|
| 形狀 | `WAShapePoly`、`WAShapeArc`、`WAShapePipe`、`WAShapeRectangle`、`WAShapePolygon`、`WAShapeEllipse`、`WAShapePie`、`WAShapeStar` |
| 顯示 | `WALabel`、`WALCDNumber`、`WAImage`、`WAGIF`、`WASvgCanvas`、`WACircularGauge`、`WAValueBar`、`WAWebView`、`WAVideo`、`WACircularGaugeColor`、`ThreedViewer` |
| 控制 | `WAButton`、`WAToggleButton`、`WASlideSwitch`、`WASelect`、`WASlider`、`WATextBox`、`WASelectVariable`、`WANavigation`、`WARecipe`、`WATimeController`、`WADataLogGroupSelector`、`WARealTimeGroupSelector` |
| 圖表 | `WA*` 圖表（pie/radar/scatter/bubble/bar/trend/XY/state timeline/table/progress） |
| 來源 | `WADrawing`、`WACustom`/`WACustomRectangle` |
| 插件 | `WAWebPlugin`、`WALottieAnimation`、第三方 plugin |
| 群組/批量 | `WAGroup`、batch 對話框 |

### 5.2 通用屬性

`waobjectProperties.js` 定義群組：

| 群組 | 代表屬性 |
|------|----------|
| 基本 | componentId、name、updateRate |
| 輔助座標 | refX/refY/refPoint |
| 佈局 | x/y/width/height/fixedAspect/layer/angle/flipH/flipV/visible/blink/padding |
| 圖形 | lineColor/lineGradient/lineWidth/fillColor/fillGradient |
| 內容 | font/size/bold/italic/colors/alignment/title/legend/tooltip/grid/tick/header/table |
| 數據來源 | style/columns/data |
| 事件 | eventScript/eventFunction |
| 動畫 | display/change/frame/action/path |
| 效果 | opacity mask |

### 5.3 漸變格式

手冊提供 `lineGradient`/`fillGradient` JSON 格式；實作由 `waobjectProperties.js` 驗證、`SCDraw2D` 繪製。型別 0=停用、1=左上→右下、2=左下→右上、3=上→下、4=左→右、5=雙點、6=放射、7=角度。

## 6. 資料綁定與變數

### 6.1 綁定語法

| 語法 | 意義 | 實作 |
|------|------|------|
| `$$var` | 工程變數 | `WAVariableManager`（`replaceCrossVari`） |
| `$var` | 圖紙變數 | `WAVariableManager`（`replaceVari`） |
| `{$$pump}:spd` | 間接定址 | `replaceToCross`/`replaceToConst` |
| `$audio`/`$image`/`$model`/`$drawio` | 資源目錄 | `Global.urlPrefix`、`PathManager` |

- 綁定資料結構見 `wabindings.js`（sourceType/sourceId/target/interval/animation/range）。
- 陣列/字段：`tag(0)`、`tag.field`；`getTagAry` 展開。

### 6.2 變數型別

| 範圍 | 型別 |
|------|------|
| 工程 `$$` | 常數（文字/數字/陣列）、系統清單（Node/Channel/Device/Tag/Point/Block）、表達式、環境、記錄 |
| 圖紙 `$` | 常數、表達式、資料轉換（控件屬性/測點/HTTP）、記錄 |

- 名稱限制：不含 `$$`/`&` 最長 32 字元；英數大小寫與 `_`。
- 安全區域/級別：`area`/`level`。
- `Enable change event` 觸發圖紙 Variables changed。

### 6.3 更新與匯入匯出

- 變數更新週期：25/50/100 ms（`WAVariableManager.updateInterval`）。
- 匯出/匯入：`/api/variable/export`、`/api/variable/import`；DataModel 另用 `/api/datamodel/*`。

## 7. 腳本、事件、動畫

### 7.1 腳本

| 功能 | 實作 |
|------|------|
| 編輯器 | `JavaScriptEditorDialog`、`ui/page/Editor/...` |
| 儲存 | `WAJavaScriptManager.jsContentReplace` + `cppcontroller.compressString`；`JSTable` |
| 函數 | `GETVAL/SETVAL/GETVAR/SETVAR/GETWIDGET/CURWIDGET/SLEEP/GOTO/SHOWPAGE/POPUPPAGE/CLOSEPOPUP/ALMACK/ALMACKS/REVERSE/REVERSEVAR/HISFORW/HISBACK/RUNSCR/VARIARY/GETIMG` |
| 限制 | 一行代碼 ≤ 1024 字元 |
| 執行 | `BaseWidget.eventTrigger` → `WAJavaScriptManager` generator |

### 7.2 事件

| 事件 | 參數 |
|------|------|
| 數值改變 | `arguments[0]` = 新值 |
| 變數改變 | `arguments[0]` = 名稱、`arguments[1]` = 新值 |
| 圖紙 | 進入/顯示/離開/變數改變 |
| 控件 | 滑鼠按下/彈上/游標進入/離開、開關打開/關閉、文字框 Enter/Return |

- 綁定方式：單行代碼、腳本、事件函數（連續型/事件型 + 條件）。
- 事件函數可選 `GETVAL` 等函數並填參數。

### 7.3 動畫

| 類型 | 實作 |
|------|------|
| 顯示 | 條件 → 屬性（值/文字/顏色/填充/邊框/可見/閃爍/圖紙來源） |
| 改變 | 綁定 Tag/Variable，View 點擊輸入 |
| 框架 | 多狀態/警報類型/狀態 → 圖片 |
| 動作 | 移動/旋轉/縮放，可組合 |
| 路徑 | 路徑/方向/參考點/速度 |
| 效果 | 透明度、遮片（文字/圖形/圖片） |

## 8. 模型與實例化

| 功能 | 實作 |
|------|------|
| 新建模型 | DataModel（`id uuid`、name、width/height、password/usePassword） |
| 模型編輯 | 獨立分頁（`WACustomRectangle` 載入模型內容） |
| 模型介面 | 開放屬性/變數（`waobjectProperties` + `DataModel_Variable`） |
| 保存/密碼 | DataModel `password`/`importpw`；匯入/刪除需密碼 |
| 匯入匯出 | `.wa` 檔（模型名稱+UUID）；`/api/datamodel/export`/`import` |
| 實例化 | `WACustom`（Customized Widget）+ `modelId`；`Reset size`；模型變數綁定外部 Tag/Variable |

## 9. 檔案與工程配置

| 功能 | 資料/API |
|------|----------|
| 音源 | `AudioTable`、`/api/uploadaudio`、`/api/getaudiolist`、`/api/deleteaudio` |
| 圖片 | `ImageTable`、`/api/uploadimage`、`/api/getimagelist`、`/api/deleteimage` |
| 檔案 | `PluginFile`/File API、`/api/uploadfile` |
| 模型 | `DataModel`、`/api/datamodel/*` |
| 3D | `scene3d*`、`/api/editor3d/*` |
| 工程設定 | `projectdetail`、`/api/project/update` |
| 使用者 | `UserInfo`/`UserAcc`/`UserScy`、`/api/user/*`、`/api/userscy/*` |
| 測點群組 | `TrendGroup`、`/api/group/*` |
| 配方 | `Recipe`/`RecipeValue`/`RecipeUnit`、`/api/recipe/*` |
| 匯入匯出 | `/api/setting/export`、`/api/setting/import`、`/api/v2/*` |
| 尋找取代 | 前端搜尋 + `Display`/`Variable` 更新；無專用 API |
| 警報區域 | `AlarmArea`/`Alarm`/`AlarmTag`、`/api/alarm/*` |

## 10. 伺服器設定與部署

| 功能 | 設計 |
|------|------|
| 設定入口 | `https://<host>:8881/SC/settings`（WA）、`/sc/opcua`（OPC UA）、`/system` |
| 資料來源 | `DataSource`/`DataSourceBody`、`/api/DataSource/*` |
| 工程 DB 匯出/匯入 | `/api/setting/export`、`/api/setting/import`、`/api/setting/projlist` |
| 使用者 | `/api/user/*`、`/api/opcua/user*` |
| 系統資源 | `assets`、`SymbolFactoryResource`、`/api/symbol/reload` |
| 服務管理 | 服務清單：EdgeView、Action log maintain、Record proxy、OPC UA client agent、OPC UA client agent (history) |
| 憑證 | Built-in/Create/Upload；`opcuaCert.go`、`Certificate/create`、`cert` |
| 備份 | `waxconfig`/`waxlog`/`waxalarm`、PostgreSQL dump |

## 11. 使用者角色與權限

| 角色 | 工程列表 | 繪圖 | 瀏覽 | 配置 | 使用者管理 |
|------|----------|------|------|------|------------|
| Admin | 全部 | 全部 | 全部 | 全部 | P/G/R |
| Power | 限定 | 限定 | 限定 | 限定 | G/R |
| General | 限定 | 不允許 | 限定 | 不允許 | 不允許 |
| Restricted | 限定 | 不允許 | 限定頁面 | 不允許 | 不允許 |

- 安全區域 0–63、級別 0–127；低級別修改需切換使用者。
- 單一工程使用者登入可跳過工程列表。
- 來源差異：EdgeHub 全部 Power；WebAccess 於 WA/EV 管理；OPC UA 於 EVS 設定頁管理。

## 12. 插件

| 插件 | 設計 |
|------|------|
| Baidu/Google 地圖 | `WAWebPlugin`/iframe；API Key；標記/追蹤 |
| Barcode/QR | `ZXing`（選用）、`WAWebPlugin` |
| 日曆餅圖 | 圖表插件；6 測點 |
| 桑基/歷史桑基 | 插件；100 節點；無 cycle |
| WebAccess 查詢 | `WAWebPlugin`；不支援 APP |
| 漏斗/自訂表格 | 插件；8 測點/Excel 式表格 |
| Lottie | `WALottieAnimation`；JSON/dotLottie |

- 插件目錄：`SCServer\_public\plugin`；不支援主題色；顯示在最上層。

## 13. 限制落實

| 限制 | 落實位置 |
|------|----------|
| 變數 32 字元 | `validate.js`/變數編輯器 |
| 一行代碼 1024 | `JavaScriptEditorDialog`/後端 |
| 測點群組 60 | `TrendGroup`/UI |
| 圖層 16 | `LayerSettings`/`BaseWidget.layer` |
| 圖表 12/60/8 | 圖表設定/資料綁定驗證 |
| 安全區 64/級別 128 | `UserInfo.area`/`level`、`OPCUA_Tag` |
| 座標左上/角度 3 點鐘 | `BaseWidget`/SCDraw2D |

## 14. 差異與待改善

| 項目 | 手冊 | 程式碼/設計 | 建議 |
|------|------|-------------|------|
| 圖紙更新 25 ms | 可選 | Client 外部資料掃描最低 500 ms；25 ms 僅控件/變數 | 文件區分「繪製」與「資料」週期 |
| WebAccess 專屬功能 | 多處標示 | 依 `SourceType` 顯示/隱藏 | 保持來源差異測試 |
| APP 圖紙 | 手機/平板独立 | `DisplayMobile` | 確認共用變數/腳本行為 |
| 模型密碼 | 匯入/刪除需密碼 | DataModel `password`/`importpw` | 補密碼錯誤/暴力測試 |
| 插件主題 | 不支援主題 | `WAWebPlugin` | 文件標示 |
| 版本格式 | 四段 | Client 版本檢查 | 補主次版檢查測試 |

## 15. 追溯

| 手冊章節 | SRS | SDS |
|----------|-----|-----|
| 1 | FR-UM-001~014 | SDS-10、SDS-15 §10 |
| 2~3 | FR-UM-015~030 | SDS-03、SDS-15 §3~4 |
| 4 | FR-UM-031~034 | SDS-03 §7、SDS-15 §5 |
| 5~7 | FR-UM-035~046 | SDS-03 §8~9、SDS-15 §6~7 |
| 8 | FR-META-023、FR-RT-002~009 | SDS-04、SDS-05、SDS-15 §9 |
| 9~11 | FR-UM-043~046 | SDS-03 §7~10、SDS-15 §7 |
| 12 | FR-UM-047~048 | SDS-15 §8、SDS-04/06 |
| 13~15 | FR-UM-049~057 | SDS-03、SDS-04、SDS-10、SDS-15 §9~10 |
| 16 | FR-UM-015~020 | SDS-03 §5、SDS-15 §10 |
| 17 | FR-UM-058~060 | SDS-04、SDS-08、SDS-15 §11 |
| 18 | FR-UM-061~064 | SDS-03 §7、SDS-15 §12 |
