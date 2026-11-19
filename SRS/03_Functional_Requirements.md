# SRS-03 功能需求（Functional Requirements）

本章以子系統分組列出 EdgeView 的功能需求。每條需求包含 ID、名稱、優先級、描述與驗收條件；關鍵需求另附前置條件與流程。

優先級：**Must**（必要）、**Should**（建議）、**Could**（選項）。

---

## 3.1 Client — 啟動、連線與登入（FR-CLI-001 ~ FR-CLI-020）

| ID | 名稱 | 優先級 | 描述 | 驗收條件 |
|----|------|--------|------|----------|
| FR-CLI-001 | 應用啟動 | Must | Client 啟動時載入設定、快取與 QML 資源，註冊 Widget 與模組後顯示預設頁面。 | 冷啟動可進入 Tenant/Login/Project 頁；缺少設定時使用預設值並可繼續。 |
| FR-CLI-002 | 遠端資源載入 | Should | 啟動時可從伺服器下載應用資源包（rcc），並使用本機快取避免重複下載。 | 有網路時下載成功；無網路時使用快取；版本不符時重新下載。 |
| FR-CLI-003 | 租戶/組織選擇 | Must | 依 Cookie（`IFPTenant`、`RootOrgID`）或租戶清單選擇租戶與組織。 | Cookie 存在時直接進入專案頁；否則顯示租戶清單；失敗時導向登入。 |
| FR-CLI-004 | 伺服器連線 | Must | 使用者可輸入/選擇伺服器位址，系統檢查連線與圖形能力。 | 顯示最近連線清單；連線失敗顯示錯誤；OpenGL/渲染能力不足時提示。 |
| FR-CLI-005 | 帳密登入 | Must | 支援使用者名稱/密碼登入，取得 Session 與 CSRF Token。 | 正確帳密可進入專案頁；錯誤顯示訊息；連續失敗依後端政策鎖定。 |
| FR-CLI-006 | 多來源登入 | Must | 支援 WebAccess、BI/EdgeHub、OPC UA、X 等登入來源。 | 依伺服器設定顯示對應登入流程；成功後可取得專案清單。 |
| FR-CLI-007 | OPC UA 首次改密 | Should | OPC UA 使用者首次登入或密碼需變更時，強制顯示改密流程。 | 未完成改密前不得進入專案；改密成功後可登入。 |
| FR-CLI-008 | 記住連線 | Could | 可將伺服器連線資訊存入本機快取。 | 下次啟動可快速選取；可清除紀錄。 |
| FR-CLI-009 | 版本檢查 | Should | 登入/連線時檢查 Client 與伺服器版本相容性。 | 版本不支援時顯示警告或阻擋；支援時正常進入。 |
| FR-CLI-010 | Session 逾時處理 | Must | 收到未授權（`error = -100`）時，Viewer 顯示重新驗證或鎖定畫面，Designer 返回登入。 | 逾時後不可繼續操作；重新登入可恢復。 |
| FR-CLI-011 | 登出 | Must | 使用者可主動登出並清除 Session/Token。 | 登出後回到登入頁；舊 Session 失效。 |
| FR-CLI-012 | 語系切換 | Should | 支援 English、French、Japanese、Korean、Traditional Chinese、Simplified Chinese。 | 切換後 UI 與字型即時更新；設定可保留。 |
| FR-CLI-013 | 連線錯誤處理 | Must | 連線中斷、伺服器錯誤、憑證錯誤需顯示可理解的錯誤訊息。 | 顯示錯誤碼/原因；提供重試。 |
| FR-CLI-014 | 圖形能力檢查 | Should | 依平台與 GPU 能力選擇渲染器（OpenGL/ES/Vulkan/軟體）。 | 不支援時降級或提示；不崩潰。 |
| FR-CLI-015 | 瀏覽器模式 | Should | WebAssembly 版本可在瀏覽器執行，支援主要設計與檢視功能。 | 可登入、載入專案、檢視畫面與即時資料；原生專屬功能明確停用。 |
| FR-CLI-016 | 行動版面 | Must | Android/iOS 提供 Phone/Tablet 版面與觸控操作。 | 主要頁面依螢幕尺寸切換版面；可完成登入、瀏覽、操作。 |
| FR-CLI-017 | 當機回報 | Should | 桌面平台整合 Crashpad，產生當機報告。 | 模擬當機可產生 dump；不影響正常啟動。 |
| FR-CLI-018 | 多語系字型 | Should | 可從伺服器下載語系檔與字型。 | 缺少語系檔時回退預設語言；字型載入失敗不影響啟動。 |
| FR-CLI-019 | 安全性設定 | Should | 支援 HTTPS、Cookie 保存與 CSRF Token 傳遞。 | HTTPS 連線可用；CSRF 缺失時請求被拒。 |
| FR-CLI-020 | 離線快取 | Could | 快取常用資源、圖片與畫面，降低重複載入。 | 重複開啟同一畫面載入時間明顯降低。 |

### FR-CLI-003 租戶/組織選擇（流程）

- 前置條件：Client 已啟動，且已完成伺服器連線設定。
- 主要流程：
  1. 讀取 Cookie `IFPTenant`、`RootOrgID`。
  2. 兩者皆存在 → 進入專案頁（`Project{dirView:true}`）。
  3. 否則呼叫租戶清單 API，顯示租戶/組織選擇。
  4. 選擇後寫入 Cookie 並進入登入或專案頁。
- 例外：租戶清單失敗 → 導向登入頁；Cookie 過期 → 重新查詢。

---

## 3.2 Client — 專案與畫面設計（FR-CLI-021 ~ FR-CLI-045）

| ID | 名稱 | 優先級 | 描述 | 驗收條件 |
|----|------|--------|------|----------|
| FR-CLI-021 | 專案清單 | Must | 顯示使用者可存取的專案清單/樹，支援搜尋與排序。 | 只顯示有權限的專案；可開啟專案。 |
| FR-CLI-022 | 專案開啟 | Must | 依專案設定與使用者選擇進入 Designer 或 Viewer。 | 單一專案可自動進入 Viewer；多專案顯示清單。 |
| FR-CLI-023 | 專案主題 | Should | 支援 Light/Dark/Gray 等主題與專案自訂主題（JSON）。 | 切換主題後畫面與 Widget 樣式更新；可還原預設。 |
| FR-CLI-024 | 畫面建立 | Must | 可新增畫面（Drawing），設定名稱、尺寸、背景、Grid。 | 新畫面可儲存並出現在物件樹。 |
| FR-CLI-025 | 畫面開啟/儲存 | Must | 可開啟、儲存、另存新畫面，並保存版本。 | 儲存後重新開啟內容一致；版本遞增。 |
| FR-CLI-026 | 畫面刪除/改名 | Must | 可刪除或重新命名畫面。 | 刪除需確認；物件樹即時更新；後端同步。 |
| FR-CLI-027 | 物件樹 | Must | 以樹狀結構瀏覽資料夾、畫面、腳本、圖片、範本、3D 等物件。 | 可展開/收合、拖放移動物件。 |
| FR-CLI-028 | 資料夾管理 | Must | 新增、刪除、移動、複製、改名資料夾。 | 操作後樹狀結構與後端一致。 |
| FR-CLI-029 | 物件移動/複製 | Must | 支援跨資料夾移動與複製物件。 | 複製後名稱唯一；移動後路徑正確。 |
| FR-CLI-030 | 搜尋 | Should | 可在專案內搜尋物件名稱與內容。 | 顯示符合項目；可跳至物件。 |
| FR-CLI-031 | 搜尋與取代 | Should | 可搜尋 Widget 屬性/綁定並批次取代。 | 預覽變更清單；確認後套用；可復原。 |
| FR-CLI-032 | Grid 與 RWD | Should | 支援 24 欄 Grid、固定列高、768px 斷點與寬度百分比。 | 調整視窗寬度時 Widget 依規則重排；可關閉 Grid。 |
| FR-CLI-033 | 縮放與裁剪 | Must | Viewer 支援縮放、平移、裁剪與符合視窗。 | 縮放不變形；可重置檢視。 |
| FR-CLI-034 | 圖層管理 | Should | 可調整 Widget 圖層順序、鎖定、隱藏、群組。 | 圖層變更即時反映；可多選群組。 |
| FR-CLI-035 | 復原/重做 | Should | 設計操作支援復原與重做。 | 連續操作可逐步復原/重做。 |
| FR-CLI-036 | 剪貼簿 | Should | 支援 Widget 複製、剪下、貼上。 | 跨畫面貼上保留屬性與綁定。 |
| FR-CLI-037 | 畫面預覽 | Must | Designer 可切換 Preview 模式檢視執行期效果。 | 預覽模式可接收即時資料；可返回編輯。 |
| FR-CLI-038 | 切換 Viewer | Must | 可從 Designer 切換至 Viewer，重新建立執行期綁定。 | 切換時解除設計期綁定；Viewer 正常運作。 |
| FR-CLI-039 | 畫面版本 | Should | 顯示與還原畫面歷史版本。 | 版本清單可查詢；還原後內容正確。 |
| FR-CLI-040 | 範本 | Should | 可將畫面/Widget 存為範本並重用。 | 範本可插入新畫面；版本可管理。 |
| FR-CLI-041 | 匯入/匯出畫面 | Should | 可匯出/匯入畫面與相關資源。 | 匯出檔可於另一環境匯入；引用資源正確。 |
| FR-CLI-042 | 縮圖 | Should | 畫面與資料模型產生縮圖供清單預覽。 | 縮圖可顯示；更新後重新產生。 |
| FR-CLI-043 | 自動儲存 | Could | 依設定自動儲存設計內容。 | 異常關閉後可回復最近內容。 |
| FR-CLI-044 | 多語系文字 | Should | 畫面文字可對應多語系資源。 | 切換語系後文字更新。 |
| FR-CLI-045 | 鍵盤快捷鍵 | Could | 提供常用操作快捷鍵。 | 快捷鍵可執行對應功能。 |

---

## 3.3 Client — Widget、綁定與腳本（FR-CLI-046 ~ FR-CLI-075）

| ID | 名稱 | 優先級 | 描述 | 驗收條件 |
|----|------|--------|------|----------|
| FR-CLI-046 | Widget 庫 | Must | 提供按鈕、標籤、輸入、選擇、儀表、開關、圖片、群組、圖表、表格等 Widget。 | Widget 可拖放至畫面並正確顯示。 |
| FR-CLI-047 | 屬性編輯 | Must | 依 Widget 型別顯示可編輯屬性（位置、大小、顏色、字型、資料）。 | 修改即時反映；非法值被阻擋。 |
| FR-CLI-048 | 批次建立 | Should | 可依範本與數量批次建立 Widget（如陣列）。 | 批次建立後位置/命名正確。 |
| FR-CLI-049 | Widget JSON | Should | 可檢視/編輯/匯入 Widget JSON。 | JSON 合法時建立 Widget；非法時提示。 |
| FR-CLI-050 | 自訂 Widget | Should | 支援自訂/外掛 Widget 載入。 | 外掛 Widget 可註冊並使用。 |
| FR-CLI-051 | Tag 綁定 | Must | 可將 Widget 屬性綁定至 Tag，設定範圍、間隔、格式化與動畫。 | 執行期數值更新並反映至 Widget。 |
| FR-CLI-052 | Variable 綁定 | Must | 可綁定專案/畫面變數（常數、系統、運算式）。 | 變數變更時 Widget 更新。 |
| FR-CLI-053 | Record 綁定 | Should | 可綁定 Record 查詢結果至表格/圖表。 | 查詢結果正確顯示；可分頁。 |
| FR-CLI-054 | Data Source Group | Should | 可定義資料來源群組，Widget 於 View/Preview 依選取群組切換資料來源。 | 切換群組後 Widget 資料來源改變。 |
| FR-CLI-055 | 綁定屬性面板 | Must | 顯示綁定來源、目標屬性、輸出、動畫模式與篩選。 | 面板可新增/修改/移除綁定。 |
| FR-CLI-056 | 動畫 | Should | 支援顯示動畫（位置、大小、旋轉、透明度、顏色）與變更動畫。 | 數值變化時依設定播放動畫。 |
| FR-CLI-057 | 效果/遮罩 | Could | 支援透明度遮罩、陰影、漸層等視覺效果。 | 效果可預覽並儲存。 |
| FR-CLI-058 | 事件腳本 | Must | Widget/畫面可綁定 JavaScript 事件腳本（點擊、載入、變更等）。 | 事件觸發時腳本執行；錯誤不崩潰。 |
| FR-CLI-059 | 事件觸發器 | Must | 支援 Event 與 Continuous 觸發器，可設條件與參數。 | 條件成立時觸發；可啟停。 |
| FR-CLI-060 | JavaScript 編輯器 | Must | 提供語法高亮、錯誤提示、巨集插入與壓縮儲存。 | 可儲存/載入腳本；語法錯誤有提示。 |
| FR-CLI-061 | 巨集庫 | Should | 提供常用巨集（WebAccess、資料存取、UI 操作）。 | 巨集可插入並執行。 |
| FR-CLI-062 | 腳本非同步 | Should | 支援 async/promise/yield 腳本與逾時/取消。 | 非同步腳本不阻塞 UI；切換畫面可取消。 |
| FR-CLI-063 | 腳本安全性 | Must | 腳本儲存時加密/壓縮，載入時解密。 | 資料庫中不存明文；Client 可正確還原。 |
| FR-CLI-064 | 圖表 Widget | Must | 支援折線、柱狀、圓餅、雷達、散佈、泡泡、組合、狀態時間軸。 | 資料更新時圖表重繪；樣式可設定。 |
| FR-CLI-065 | 資料清單/表格 | Must | 支援多欄資料、分頁、排序、樣式與群組。 | 可正確顯示與操作。 |
| FR-CLI-066 | Datalog 表格 | Should | 顯示歷史資料查詢結果與時間範圍/間隔設定。 | 可查詢、分頁、匯出。 |
| FR-CLI-067 | 配方 Widget | Should | 顯示與套用配方（Recipe）與單位。 | 可選擇配方、下載/上傳值。 |
| FR-CLI-068 | 警報 Widget | Should | 顯示即時警報摘要、警報歷史、確認操作。 | 摘要即時更新；確認成功。 |
| FR-CLI-069 | 導覽 Widget | Should | 支援畫面切換、選單、導覽列。 | 點擊可切換畫面。 |
| FR-CLI-070 | 媒體 Widget | Could | 支援圖片、GIF、影片、Lottie、SVG、Web、二維碼。 | 可載入與播放；資源缺失顯示替代。 |
| FR-CLI-071 | 3D Viewer Widget | Should | 支援 3D 模型檢視、模型切換、相機/光源/材質設定。 | 模型可載入並互動；設定可儲存。 |
| FR-CLI-072 | 3D 場景套件 | Could | 可上傳/下載/版本化 3D 場景套件與資產。 | 套件可載入；版本可切換。 |
| FR-CLI-073 | 資料來源狀態 | Should | Viewer 可檢視資料來源連線狀態。 | 顯示連線/斷線與重試。 |
| FR-CLI-074 | 變數監看 | Should | Viewer 可檢視變數目前值與品質。 | 數值即時更新；可搜尋。 |
| FR-CLI-075 | Widget 生命週期 | Must | Widget 建立/銷毀時正確初始化與釋放腳本、綁定、計時器。 | 反覆切換畫面無記憶體持續成長或幽靈更新。 |

---

## 3.4 Client — 設定、管理與維運（FR-CLI-076 ~ FR-CLI-095）

| ID | 名稱 | 優先級 | 描述 | 驗收條件 |
|----|------|--------|------|----------|
| FR-CLI-076 | 使用者管理 | Must | 新增/修改/刪除專案使用者，設定密碼、類型、首頁、區域。 | 變更同步後端；權限生效。 |
| FR-CLI-077 | 群組/安全設定 | Should | 管理使用者群組與安全範本。 | 群組成員與權限正確。 |
| FR-CLI-078 | 專案設定 | Must | 設定預設頁、縮放模式、語系、工具列、小數分隔、全螢幕等。 | 設定儲存並於 Viewer 生效。 |
| FR-CLI-079 | 匯入/匯出 | Must | 匯出/匯入專案或物件，支援密碼保護與衝突處理。 | 匯入後引用關係正確；密碼錯誤被拒。 |
| FR-CLI-080 | 配方管理 | Should | 新增/編輯/刪除配方、單位與值。 | 配方可套用至 Tag/Variable。 |
| FR-CLI-081 | 警報通知設定 | Should | 設定單一/多重警報音效、區域與 Tag 對應。 | 警報發生時播放對應音效。 |
| FR-CLI-082 | 一般設定 | Should | 設定資料更新頻率、語系、字型。 | 設定儲存並套用。 |
| FR-CLI-083 | 日誌維護 | Should | 設定操作/稽核日誌的備份時間、保留期限與刪除策略。 | 依排程執行；可查詢結果。 |
| FR-CLI-084 | 稽核日誌檢視 | Should | 查詢、篩選與匯出稽核日誌。 | 結果正確；匯出檔可開啟。 |
| FR-CLI-085 | 操作日誌 | Should | 查詢使用者操作與 Tag 寫入紀錄。 | 可依專案/使用者/時間查詢。 |
| FR-CLI-086 | OPC UA 使用者 | Should | 管理 OPC UA 使用者、功能權限、專案權限與登入鎖定。 | 權限生效；鎖定政策可設定。 |
| FR-CLI-087 | OPC UA 連線 | Should | 新增/修改/刪除 OPC UA 連線與憑證。 | 連線測試成功；憑證可下載。 |
| FR-CLI-088 | 物件匯入匯出 | Should | 單一物件或整個專案的匯入匯出。 | 匯出檔可匯入；版本相容。 |
| FR-CLI-089 | 資料模型 | Should | 建立/匯入資料模型與其變數。 | 模型可套用至畫面；版本管理。 |
| FR-CLI-090 | 資料來源設定 | Must | 設定 WebAccess、OPC UA、X、EdgeHub/BI 連線參數。 | 測試連線成功；設定加密儲存。 |
| FR-CLI-091 | MQTT Broker 設定 | Could | 檢視/更新 MQTT Broker 設定（Client MQTT 目前停用）。 | 設定可儲存；不影響現有功能。 |
| FR-CLI-092 | OEM 品牌 | Could | 檢視/更新 Logo、名稱等品牌設定。 | 更新後登入頁/關於頁顯示新品牌。 |
| FR-CLI-093 | 外掛檔案 | Could | 上傳/管理 Web Plugin 與 Plugin 檔案。 | 外掛可載入；版本正確。 |
| FR-CLI-094 | 語言/字型管理 | Should | 上傳字型、啟停語系。 | 字型可用於畫面；語系可切換。 |
| FR-CLI-095 | 系統狀態 | Should | 顯示系統/連線/版本狀態。 | 可查看伺服器與 Client 版本、連線狀態。 |

---

## 3.5 Metadata Service（FR-META-001 ~ FR-META-045）

| ID | 名稱 | 優先級 | 描述 | 驗收條件 |
|----|------|--------|------|----------|
| FR-META-001 | 服務啟動 | Must | 載入 `config.yaml`、建立/驗證資料庫、初始化 Session Store、執行 SQLInit、註冊路由並啟動 HTTP/HTTPS。 | 服務可啟動；資料表與擴充存在；`/ping/:id` 正常。 |
| FR-META-002 | 設定載入 | Must | 支援檔案、環境變數與命令列覆寫設定。 | 優先順序正確；預設值可用。 |
| FR-META-003 | 多來源登入 | Must | 支援 WebAccess、BI/EdgeHub、OPC UA、X 登入並建立 Session。 | 各來源登入成功可取得 Session/CSRF。 |
| FR-META-004 | Session 管理 | Must | 以 PostgreSQL 儲存 Session，支援逾時與登出。 | Session 過期後 API 回傳未授權；登出即失效。 |
| FR-META-005 | CSRF 驗證 | Must | 驗證 JSON/表單中的 `csrfToken`。 | 缺少或錯誤時回傳 `-100`。 |
| FR-META-006 | 封包時間戳 | Must | POST/PUT/DELETE 驗證 `packetTimestamp` 在容許漂移內。 | 超時回傳 `-102`；`driftseconds=0` 可停用。 |
| FR-META-007 | 專案清單 | Must | 依使用者權限回傳專案清單。 | 僅顯示可存取專案。 |
| FR-META-008 | 專案詳細/更新 | Must | 查詢與更新專案設定（預設頁、縮放、語系、主題）。 | 更新後 Client 取得新設定。 |
| FR-META-009 | 物件樹 | Must | 以 `ObjectPath`（ltree）管理資料夾與物件階層。 | 可查詢子節點、路徑、物件詳細。 |
| FR-META-010 | 資料夾 CRUD | Must | 新增、刪除、改名、移動、複製資料夾。 | 階層與物件關聯正確。 |
| FR-META-011 | 物件 CRUD | Must | 移動、複製、改名、刪除各類物件。 | 操作後引用與路徑一致。 |
| FR-META-012 | 路徑搜尋 | Should | 依完整路徑文字取得資料夾/物件 ID，支援子路徑搜尋。 | 查詢結果正確。 |
| FR-META-013 | Display CRUD | Must | 建立/刪除/查詢畫面與版本、縮圖。 | 版本遞增；縮圖可取得。 |
| FR-META-014 | Mobile/System Display | Should | 管理行動版與系統畫面內容。 | 可依 type 取得對應內容。 |
| FR-META-015 | JS CRUD/版本 | Must | 建立/查詢/刪除 JavaScript 與版本清單。 | 版本可查詢與還原。 |
| FR-META-016 | Template CRUD/版本 | Should | 管理範本與版本。 | 範本可建立/套用。 |
| FR-META-017 | Variable CRUD | Must | 管理專案/畫面變數與 Variablebody。 | 變數可查詢/新增/修改/刪除/匯入/匯出。 |
| FR-META-018 | DataModel CRUD | Must | 管理資料模型、變數、版本、匯入匯出與密碼。 | 模型與變數關聯正確；密碼驗證有效。 |
| FR-META-019 | DataModel 映射 | Should | 管理 `rcmap`（資料模型與物件對應）。 | 映射可查詢與更新。 |
| FR-META-020 | 資料來源設定 | Must | 管理 DataSource/DataSourceBody（WebAccess/OPC UA/X/EdgeHub）。 | 設定可讀寫；密碼加密。 |
| FR-META-021 | DB 定義 | Should | 管理資料庫類型定義與欄位。 | 可依 dbtype 查詢。 |
| FR-META-022 | Tag 下載 | Must | 依專案/節點/裝置下載 Tag、ConstTag、TagPage。 | 回傳資料與來源一致。 |
| FR-META-023 | Record CRUD | Must | 管理 Record 與 Recordbody。 | 可查詢/新增/修改/刪除/搜尋。 |
| FR-META-024 | Recipe CRUD | Should | 管理 Recipe、RecipeValue、RecipeUnit。 | 配方與單位正確。 |
| FR-META-025 | Alarm 設定 | Must | 管理 Alarm、AlarmArea、AlarmTag 與音效。 | 設定可查詢/更新。 |
| FR-META-026 | 使用者 CRUD | Must | 管理 UserInfo、UserAcc、UserScy。 | 使用者與區域權限正確。 |
| FR-META-027 | 群組 CRUD | Should | 管理群組與成員。 | 群組可查詢/新增/修改/刪除。 |
| FR-META-028 | 專案使用者安全 | Must | 依專案名稱回傳使用者安全設定。 | Client 取得正確權限。 |
| FR-META-029 | 密碼變更 | Must | 使用者可變更密碼；管理員可重設。 | 舊密碼驗證；新密碼生效。 |
| FR-META-030 | WA 使用者同步 | Should | 從 WebAccess 複製/同步使用者。 | 同步後帳號可用。 |
| FR-META-031 | OPC UA 專案 | Should | 管理 OPC UA 專案。 | CRUD 正常；名稱唯一。 |
| FR-META-032 | OPC UA 連線 | Should | 管理連線、連線內容與憑證下載。 | 可測試連線；憑證可下載。 |
| FR-META-033 | OPC UA Tag | Should | 管理 Tag、ConstTag、TagConfig、Tag 分頁與節點。 | 查詢/新增/修改/刪除正常。 |
| FR-META-034 | OPC UA 警報 | Should | 管理 OPC UA 警報與動作/等級。 | 設定可儲存。 |
| FR-META-035 | OPC UA 使用者 | Should | 管理使用者、功能、專案與登入鎖定。 | 權限與鎖定生效。 |
| FR-META-036 | 系統設定 | Must | 管理 Settings、about、sysstatus。 | 設定可讀寫；狀態可查詢。 |
| FR-META-037 | 語系/字型 | Should | 管理 i18n 啟停與字型上傳/下載。 | 語系與字型可用。 |
| FR-META-038 | OEM 設定 | Could | 管理品牌資訊與圖片。 | 公開端點可取得品牌。 |
| FR-META-039 | 檔案/圖片/音訊 | Must | 上傳/下載/刪除圖片、音訊、通用檔案與縮圖。 | 檔案可取得；大小限制生效。 |
| FR-META-040 | 3D 場景 | Could | 管理 3D 套件、網格、資產、版本與上傳。 | 套件可儲存/下載。 |
| FR-META-041 | 匯入/匯出 v1/v2 | Must | 支援專案/物件的 v1 與 v2 匯出匯入，v2 使用外部工具解析引用並加密。 | 匯出檔可匯入；引用正確；密碼保護有效。 |
| FR-META-042 | 稽核日誌 | Must | 寫入與查詢稽核日誌，含 checksum。 | 日誌可查詢/匯出；checksum 可驗證。 |
| FR-META-043 | 操作日誌 | Should | 寫入與查詢操作日誌。 | 可依專案/使用者/時間查詢。 |
| FR-META-044 | 日誌維護 | Should | 設定備份/保留/刪除策略。 | 排程生效；可查詢設定。 |
| FR-META-045 | 平台整合 | Should | Edge365 註冊、BI Token、Firebase、授權檢查。 | 各整合端點可用；失敗不影響核心功能。 |

---

## 3.6 Runtime Proxy（FR-RT-001 ~ FR-RT-025）

| ID | 名稱 | 優先級 | 描述 | 驗收條件 |
|----|------|--------|------|----------|
| FR-RT-001 | 服務啟動 | Must | 載入設定、建立 DB/ Session Store、啟動 WebSocket Hub、註冊路由與輪詢服務。 | 服務可啟動；`/swagger` 可用；`/so` 可連線。 |
| FR-RT-002 | Record 查詢 | Must | 依 record id/version 回傳快取或重新載入的查詢結果。 | 版本未變回快取；變更後更新。 |
| FR-RT-003 | Record 預覽 | Must | 產生 SQL/InfluxQL 預覽並可執行預覽查詢（含 HTTP）。 | 預覽語法正確；結果可顯示。 |
| FR-RT-004 | Schema 查詢 | Should | 查詢資料表/measurement 與欄位。 | 回傳正確 schema。 |
| FR-RT-005 | 分頁 | Must | 支援上一頁/下一頁/跳頁。 | 頁碼與資料正確；邊界處理正確。 |
| FR-RT-006 | Datalog 查詢 | Must | 依時間範圍、間隔、時間欄位查詢歷史資料。 | 時間格式與時區正確；間隔聚合正確。 |
| FR-RT-007 | 指令執行 | Must | 執行 SQL/Influx/HTTP 指令（Function record）。 | 執行結果回傳；錯誤有原因。 |
| FR-RT-008 | 資料源支援 | Must | 支援 PostgreSQL、SQL Server、Oracle、MySQL、InfluxDB v1/v2、HTTP。 | 各來源可查詢；連線參數正確。 |
| FR-RT-009 | 資料量限制 | Must | `max_data_size` 限制 1–1000。 | 超出範圍被拒或截斷。 |
| FR-RT-010 | 即時資料 | Must | WebSocket `recorddata` 支援 register/unregister/update/updatevalue。 | 註冊後每秒推送；取消後停止。 |
| FR-RT-011 | 更新值 | Must | 支援 Data record 的交易式更新值。 | 更新成功且資料一致；失敗回滾。 |
| FR-RT-012 | 告警摘要 WA | Should | 提供 WebAccess 告警摘要 register/unregister。 | 摘要即時更新。 |
| FR-RT-013 | 告警摘要 EH | Should | 提供 EdgeHub 告警摘要。 | 摘要即時更新。 |
| FR-RT-014 | 告警摘要 OPC UA | Should | 提供 OPC UA 告警摘要。 | 摘要即時更新。 |
| FR-RT-015 | 告警摘要 X | Should | 提供 X 告警摘要。 | 摘要即時更新。 |
| FR-RT-016 | 系統資訊 | Should | WebSocket `systeminfo` 定期推送系統狀態。 | 連線後收到更新。 |
| FR-RT-017 | WebSocket 登入 | Must | 連線後需於時限內以 `csrfToken` 登入。 | 逾時斷線；登入後可訂閱。 |
| FR-RT-018 | 封包時間戳 | Must | REST 寫入操作驗證時間戳。 | 超時回傳 `-102`。 |
| FR-RT-019 | Edge365 代理 | Should | 反向代理 `/apihub`、`/dbmaster`、`/alarm`。 | 請求正確轉發；標頭處理正確。 |
| FR-RT-020 | HTTP 代理 | Could | 提供 `/fw` 通用 HTTP 代理供腳本使用。 | 可轉發 GET/POST。 |
| FR-RT-021 | Firebase 推播 | Should | 依裝置與專案設定推送警報通知。 | 裝置可收到通知；可更新語言。 |
| FR-RT-022 | 裝置註冊 | Should | 管理 FCM 裝置與專案訂閱。 | 註冊/刪除正常。 |
| FR-RT-023 | 會話共享 | Must | 與 Metadata Service 共用 Session/Cookie 與 CSRF 驗證。 | 同一 Session 可存取兩服務。 |
| FR-RT-024 | 錯誤格式 | Must | 統一回傳 `{error, reason, result}`。 | 錯誤碼符合定義（-100/-101/-102/-500/-501）。 |
| FR-RT-025 | 優雅關閉 | Should | 收到終止訊號時關閉 WebSocket、輪詢與 HTTP。 | 無資料遺失；連線正常結束。 |

---

## 3.7 OPC UA 子系統

OPC UA 子系統（設定 UI、Data Agent、Alarm Agent）的需求另見 [`SRS/07_OPCUA_Subsystem.md`](07_OPCUA_Subsystem.md)，使用 `FR-OPC-nnn` 編號：

| 群組 | 需求範圍 | 說明 |
|------|----------|------|
| 設定 UI | FR-OPC-001~012 | 專案、連線、Tag、警報、使用者、匯入匯出 |
| Data Agent | FR-OPC-013~027 | 即時/歷史/寫值/瀏覽/憑證/測點 |
| Alarm Agent | FR-OPC-028~045 | 規則、評估、狀態機、Ack/Process、清單/歷史 |

### 3.7.1 Client 直連資料源（補充 FR-CLI）

Viewer/Preview 的 OPC UA 即時與歷史 Tag 值由 Client **邏輯直連** Data Agent（不經 Runtime Proxy）；地端實體路徑為 Client → xnginx `/ua`、`/uadb`、`/uaam` → Agent。此行為補充 FR-CLI-051、FR-CLI-066、FR-RT-002/006 的資料路徑說明：

| 資料 | 路徑 | 需求 |
|------|------|------|
| 即時 Tag | Client → xnginx `/ua` → Data Agent `GetTagValueText` | FR-OPC-015、FR-CLI-051 |
| 歷史 Tag | Client → xnginx `/uadb` → History Agent `GetDataLog`/`GetRawDataLog` | FR-OPC-018、FR-CLI-066 |
| 警報清單/歷史/Ack | Client → xnginx `/uaam` → Alarm Agent `/api/alarm/*` | FR-OPC-035~037 |

> Runtime Proxy 仍負責 Record/第三方 DB 查詢與 `opcuaalarmsummary` WebSocket 摘要。

### 3.7.2 使用手冊補充

安裝/授權、系統需求、伺服器設定、瀏覽器/APP 連線、繪圖介面、控件目錄、變數、腳本、事件、動畫、模型、工程配置、使用者角色與插件等手冊功能，另見 [`SRS/08_User_Manual_Requirements.md`](08_User_Manual_Requirements.md)（`FR-UM-nnn`）。

## 3.8 需求優先級統計

| 子系統 | Must | Should | Could | 合計 |
|--------|------|--------|-------|------|
| Client | 40 | 40 | 15 | 95 |
| Metadata | 24 | 18 | 3 | 45 |
| Runtime | 15 | 9 | 1 | 25 |
| OPC UA 子系統 | 38 | 7 | 0 | 45 |
| 使用手冊（SRS-08） | 44 | 16 | 4 | 64 |
| **總計** | **161** | **90** | **23** | **274** |

> 註：統計依本章、SRS-07、SRS-08 表格粗略分類，實際排程以產品規劃為準。
