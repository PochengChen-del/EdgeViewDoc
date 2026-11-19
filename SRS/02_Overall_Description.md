# SRS-02 整體描述（Overall Description）

## 1. 產品定位（Product Perspective）

EdgeView 是 WebAccess/SCADA X 生態系中的圖控前端與執行平台。Client 以 Qt 6/QML 建構，可在桌面、行動裝置與瀏覽器（WebAssembly）執行；後端由 Metadata Service、Runtime Proxy 兩個 Go 服務，以及 OPC UA Data Agent（C#）、OPC UA Alarm Agent（Go）與 OPC UA 設定 UI（Vue）組成。設定資料存於 PostgreSQL，Client 透過 HTTP REST/WebSocket 取得設定與 Record 資料，並**直接**向 OPC UA Data Agent 取得即時/歷史 Tag 值。

```mermaid
flowchart TB
    subgraph Users["使用者"]
        Designer[畫面設計者]
        Operator[現場操作員]
        Admin[系統/專案管理者]
        Integrator[系統整合者]
    end

    subgraph EdgeView["EdgeView"]
        Client[Client<br/>Designer / Viewer]
        Meta[Metadata Service<br/>:28883]
        Proxy[Runtime Proxy<br/>:28884]
        Agent[OPC UA Data Agent<br/>Runtime :28886 / History :28887]
        Alarm[OPC UA Alarm Agent<br/>:28889]
        UI[OPC UA 設定 UI<br/>/sc/opcua]
        DB[(PostgreSQL<br/>waxconfig / waxalarm)]
        Client --> Meta
        Client --> Proxy
        Client -->|即時/歷史 Tag| Agent
        Client -->|警報清單/歷史/Ack| Alarm
        UI --> Meta
        Meta --> DB
        Proxy --> DB
        Agent --> DB
        Alarm --> DB
    end

    subgraph External["外部系統"]
        WA[WebAccess]
        EH[EdgeHub / Edge365]
        X[X Platform]
        OPC[OPC UA Server]
        SQL[(SQL Server / MySQL / Oracle / PostgreSQL)]
        Influx[(InfluxDB v1/v2)]
        FCM[Firebase FCM]
        IdP[BI / 企業登入]
    end

    Designer --> Client
    Operator --> Client
    Admin --> Client
    Integrator --> Client
    Meta --> WA
    Meta --> EH
    Meta --> X
    Meta --> IdP
    Proxy --> WA
    Proxy --> EH
    Proxy --> X
    Proxy --> SQL
    Proxy --> Influx
    Proxy --> FCM
    Agent --> OPC
    Alarm --> Agent
```

### 1.1 系統介面摘要

| 介面 | 方向 | 協定 | 說明 |
|------|------|------|------|
| Client ↔ Metadata Service | 雙向 | HTTP/HTTPS REST、Cookie Session | 設定、專案、使用者、授權、OPC UA、匯入匯出 |
| Client ↔ Runtime Proxy | 雙向 | HTTP/HTTPS REST、WebSocket | Record 查詢、Record 即時資料、告警摘要、系統資訊 |
| Client ↔ OPC UA Data Agent | 雙向 | HTTP/HTTPS REST | 即時 Tag 值、歷史 Tag 值、寫值、Tag 明細、瀏覽 |
| Client ↔ OPC UA Alarm Agent | 雙向 | HTTP/HTTPS REST | 警報清單、歷史、Ack/Process、歷程 |
| OPC UA 設定 UI ↔ Metadata | 雙向 | HTTP/HTTPS REST | OPC UA 專案/連線/Tag/警報/使用者設定 |
| Metadata ↔ PostgreSQL | 雙向 | PostgreSQL wire protocol | 設定資料、Session、稽核 |
| Runtime ↔ PostgreSQL | 雙向 | PostgreSQL wire protocol | Record 定義、資料來源設定、Session、FCM |
| Runtime ↔ 第三方 DB | 唯讀/讀寫 | 各 DB 原生協定 | Record 查詢、Datalog、配方資料 |
| OPC UA Data Agent ↔ OPC UA Server | 雙向 | OPC UA TCP | 讀值、寫值、歷史、瀏覽 |
| OPC UA Data Agent ↔ PostgreSQL | 雙向 | PostgreSQL | OPC UA 設定、Session |
| OPC UA Alarm Agent ↔ Data Agent | 雙向 | HTTP | 輪詢 Tag 值（Bearer） |
| OPC UA Alarm Agent ↔ PostgreSQL | 雙向 | PostgreSQL | `waxalarm` 狀態/歷史、`waxconfig` 設定/Session |
| Metadata/Runtime ↔ Edge365 | 雙向 | HTTPS / etcd / 反向代理 | 應用註冊、API Hub、DB Master、Alarm |
| Runtime → Firebase | 單向 | FCM HTTP v1 | 警報推播 |
| Client ↔ 檔案系統/瀏覽器儲存 | 雙向 | 本機檔案、QSettings、WASM 儲存 | 設定快取、Cookie、離線資源 |

## 2. 產品功能摘要（Product Functions）

### 2.1 Client（Designer / Viewer）

| 群組 | 主要功能 |
|------|----------|
| 啟動與連線 | 租戶/組織選擇、伺服器連線、OpenGL/渲染能力檢查、版本檢查、遠端 rcc 載入 |
| 登入 | 帳密登入、BI/EdgeHub 登入、OPC UA 首次登入改密、記住連線、錯誤處理、Session 逾時 |
| 專案 | 專案清單/樹、開啟專案、專案主題、圖片更新檢查、單一專案自動進入 Viewer |
| 畫面設計 | 新增/開啟/儲存/另存畫面、畫面屬性、Grid/RWD、縮放、裁剪、圖層 |
| Widget | 內建 Widget 庫、屬性編輯、群組、批次建立、JSON 匯入匯出、自訂 Widget |
| 資料綁定 | Tag、Variable、Record、Data Source Group 綁定；範圍、間隔、格式化、動畫 |
| 腳本 | JavaScript 編輯器、事件腳本、事件觸發器、巨集、壓縮儲存、非同步觸發 |
| 圖表/資料清單 | 折線、柱狀、圓餅、雷達、散佈、泡泡、狀態時間軸、Datalog 表格 |
| 3D | 3D Viewer Widget、模型/材質/光源/相機設定、場景套件上傳與版本 |
| Viewer 執行 | 即時資料、警報摘要、縮放、巡邏、通訊狀態、資料來源狀態、變數監看、全螢幕 |
| 設定模組 | 使用者管理、群組/安全、專案設定、匯入匯出、配方管理、搜尋取代、警報通知 |
| 系統 | 多語系、字型管理、資料更新設定、日誌維護、Crashpad、離線/WASM |

### 2.2 Metadata Service

| 群組 | 主要功能 |
|------|----------|
| 認證/授權 | 多來源登入、Session、CSRF、Token 驗證、專案使用者安全、BI Token |
| 專案/物件樹 | 專案清單、資料夾、物件移動/複製/改名/刪除、路徑查詢、搜尋、匯入匯出 |
| 畫面/腳本/模板 | Display/DisplayMobile/DisplaySystem、JS、Template 的 CRUD 與版本 |
| 變數/資料模型 | Variable、Variablebody、DataModel、DataModel_Variable、rcmap |
| 資料來源/Tag | DataSource、DBDefine、下載 Tag/ConstTag/TagPage |
| Record/Recipe/Alarm | Record 定義、Recipe 與單位、Alarm/Area/Tag |
| 使用者/群組 | UserInfo、UserAcc、UserScy、Group、OPC UA User |
| OPC UA | Project、Connection、Tag、ConstTag、TagConfig、Alarm、User/權限 |
| 系統設定 | Settings、i18n/font、OEM、MQTT Broker、WebPlugin、Symbol 資源 |
| 檔案/資產 | Image、Audio、File、PluginFile、FileCache、3D Scene |
| 稽核/維護 | ActionLog、AuditLog、Maintenance、日誌匯出 |
| 平台整合 | WebAccess、EdgeHub/Edge365、X、BI、Firebase、授權 |

### 2.3 Runtime Proxy

| 群組 | 主要功能 |
|------|----------|
| Record 查詢 | preview、previewquery、previewhttp、schema、tableschema（`queryrecord` 已停用） |
| 分頁 | prevpage、nextpage、gotopage |
| Datalog | querydatalog（時間範圍、間隔、時間欄位） |
| 指令 | runrecordcmd（SQL/Influx/HTTP） |
| 即時資料 | WebSocket recorddata register/unregister/update/updatevalue |
| 告警摘要 | WebSocket wa/ehalarm/opcuaalarm/xalarm summary |
| 系統資訊 | WebSocket systeminfo |
| 代理 | Edge365 apihub/dbmaster/alarm 反向代理、`/fw` HTTP 代理 |
| 通知 | 警報通知輪詢、Firebase 推播 |

### 2.4 OPC UA 子系統

| 群組 | 主要功能 |
|------|----------|
| 設定 UI | 專案、連線、節點瀏覽、Tag、常數/計算/系統測點、警報等級/規則、使用者/專案授權、匯入匯出、OEM、稽核日誌 |
| Data Agent（Runtime） | 即時值、寫值、Tag 明細、瀏覽、憑證、Alarm 欄位轉發、常數/計算/累計/系統測點 |
| Data Agent（History） | 聚合歷史（GetDataLog）、原始歷史（GetRawDataLog） |
| Alarm Agent | 規則編譯、條件評估、優先級、雙狀態機、Ack/Process/Lock、警報清單/歷史/歷程、模擬、重載 |
| 直連資料源 | Client Viewer/Preview 直接呼叫 Data Agent 取得即時/歷史 Tag 值，不經 Runtime Proxy |

> OPC UA 子系統的完整需求見 `SRS/07_OPCUA_Subsystem.md`。

## 3. 使用者角色（User Classes）

| 角色 | 能力 | 典型操作 |
|------|------|----------|
| 系統管理員（Admin） | 全域設定、使用者/群組、專案建立、OPC UA 管理、授權、稽核 | 管理伺服器、匯入匯出專案、設定資料來源 |
| 專案管理員 | 單一專案設定、專案使用者與權限、畫面/變數管理 | 指派專案成員、維護專案設定 |
| 畫面設計者（Designer） | 編輯畫面、Widget、綁定、腳本、動畫、3D | 建立 HMI 畫面、測試預覽 |
| 現場操作員（Operator） | Viewer 操作、警報確認、配方切換、資料查詢 | 監控、操作按鈕、查看趨勢 |
| 系統整合者（Integrator） | 資料來源、Record、OPC UA 連線、API 整合 | 設定第三方 DB、OPC UA 連線/Tag/安全模式、匯入資料模型 |
| OPC UA 設定者 | OPC UA 專案、連線、Tag、警報規則、使用者 | 使用 `/sc/opcua/` 設定 UI |
| 警報操作員 | 檢視/確認/處理 OPC UA 警報 | Alarm Agent 清單、Ack/Process |
| 唯讀/稽核者 | 檢視與匯出日誌、報表 | 查詢稽核與操作日誌 |

權限以專案為單位，並由 `UserInfo.usertype`、`UserAcc`、`UserScy.area`、OPC UA `OPCUA_UserFunc`/`OPCUA_UserProject` 等機制控制。

## 4. 營運環境（Operating Environment）

### 4.1 Client

| 平台 | 說明 |
|------|------|
| Windows 10 1809+/11 | 主要桌面環境，支援 Crashpad、Session 監控 |
| Linux Ubuntu 24.04 | 桌面環境 |
| Android | 手機/平板，RWD 版面 |
| iOS | 手機/平板，RWD 版面 |
| WebAssembly/HTML5 | 瀏覽器執行（Chrome/Edge/Firefox，WebGL 2.0），使用 `wasmsettings`、`wahttp_web`、Web AES 函式庫 |

需求：

- Qt 6（含 Qt Quick、QuickControls2、Svg、WebSockets、Multimedia；選用 WebView、Firebase、ZXing）。
- 圖形支援 OpenGL 3.3+/OpenGL ES/Vulkan（依平台與 `WaSettings.getRender()`）；Windows 可支援 DirectX 11。
- 網路可達 Metadata Service 與 Runtime Proxy；瀏覽器模式需允許 Cookie 與 WebSocket。

### 4.2 Server

| 元件 | 需求 |
|------|------|
| OS | Windows 10 1607+/11、Ubuntu 22.04/24.04、麒麟 V10 SP1（或容器/K8s） |
| 執行環境 | Go（Metadata Service）、Go 1.23（Runtime Proxy）、Go（Alarm Agent）、.NET（Data Agent）、Node/Vue build（UI） |
| 資料庫 | PostgreSQL（必要），需 `ltree`、`uuid-ossp` 擴充；`waxalarm` 供 Alarm runtime |
| 網路 | Metadata `127.0.0.1:28883`；Runtime `127.0.0.1:28884`；Data Agent `28886`/`28887`；Alarm Agent `28889`；EVS 對外 HTTP/HTTPS `8880`/`8881`；可透過 Ingress 對外 |
| 憑證 | TLS 憑證與私鑰（`cert.crt`/`cert.key`） |
| 資源 | 依使用者與輪詢連線數調整；建議獨立 PostgreSQL 與適當連線池 |

### 4.3 外部系統

- WebAccess（REST API，Basic Auth）。
- EdgeHub/Edge365（反向代理、etcd 註冊、GraphQL 登入）。
- X Platform（`/waxapi`）。
- OPC UA Server（OPC UA TCP；選用 SecurityMode/Policy 與使用者認證）。
- OPC UA Data Agent（即時/歷史值與憑證 API）。
- OPC UA Alarm Agent（警報清單/歷史/Ack/Process API）。
- Firebase（FCM 服務帳號 JSON）。
- 第三方資料庫：PostgreSQL、SQL Server、Oracle、MySQL、InfluxDB v1/v2、HTTP API。

## 5. 限制與假設（Constraints and Assumptions）

### 5.1 限制

1. 伺服器端設定資料庫以 PostgreSQL 為唯一支援的設定儲存。
2. Metadata Service 與 Runtime Proxy 為獨立進程，預設僅綁定 loopback，需透過 Ingress/反向代理對外。
3. Client 的 Designer 與 Viewer 為同一執行檔（`gc-designer`），以 `openMode` 區分。
4. 資料來源若為 WebAccess/EdgeHub/X/OPC UA，需對應平台可用且網路可達。
5. OPC UA 即時/歷史 Tag 值由 Client 直連 Data Agent（`:28886`/`:28887`）；Client 需能直接連線該位址或經 Ingress `/ua`、`/uadb`。
6. OPC UA 歷史查詢需 OPC UA Server 支援 Historical Access。
7. OPC UA 設定 UI 需能連線 Metadata Service（`/sc/api`），且 `graphic` 角色不得進入設定頁。
8. 瀏覽器（WASM）模式部分原生能力（檔案系統、Session 監控、Crashpad）不可用；直連 Data Agent 需正確 CORS/Cookie。
9. 既有程式碼中 MQTT Client 功能停用；ODBC 資料源未實作。
10. 所有 REST/WS 請求需符合封包時間戳防重放（`packetTimestamp`，預設 300 秒容差）；Data Agent 目前未實際強制，見 SDS-14。

### 5.2 假設

1. 使用者具備瀏覽器或已安裝 Client 的裝置。
2. 系統管理員已完成 PostgreSQL、憑證與網路設定。
3. 外部系統（WebAccess、Edge365、OPC UA Server、Data/Alarm Agent）遵循既有 API 契約。
4. 時間同步（NTP）可用，以避免封包時間戳與 Session 驗證失敗。
5. 專案資料量與同時上線人數在產品設定容量內。
6. OPC UA 專案/Tag/警報設定由設定 UI 或 Metadata API 維護；Alarm Agent 只讀設定。
7. Data Agent Runtime 與 History 以兩個進程啟動，共用 `waxconfig`。

### 5.3 相依性

| 相依項目 | 影響 |
|----------|------|
| PostgreSQL | 設定、Session、稽核、Record 定義；不可用時後端無法啟動 |
| OPC UA Data Agent | OPC UA 即時/歷史值、寫值、Tag 明細、瀏覽 |
| OPC UA Alarm Agent | OPC UA 警報清單/歷史/Ack |
| OPC UA Server | 即時值與歷史資料來源 |
| 第三方資料庫 | Record/Datalog 查詢與配方 |
| Firebase 服務帳號 | 行動推播 |
| Edge365/etcd | 雲端代理與應用註冊 |
| 授權檔（`graphix.lic`） | 服務啟動與功能（含 3D/App） |
| 系統時間 | 封包時間戳、Session、Token、稽核 |

## 6. 設計與實作限制（Design Constraints）

- Client 以 QML 為主要 UI 技術，C++ 提供控制器、渲染與 I/O。
- 後端以 Go + Gin 實作（Metadata/Runtime/Alarm），採 controller/service/model/repository 分層；Data Agent 以 C# ASP.NET Core 實作。
- 設定資料以 PostgreSQL 關聯表 + JSONB 混合儲存；OPC UA 警報 runtime 使用獨立 `waxalarm` 資料庫。
- OPC UA 即時/歷史值由 Client 直連 Data Agent；Runtime Proxy 僅處理 Record、摘要與代理。
- 密碼與敏感資料處理需符合既有機制（詳見 SDS-08）。
- 國際化以 Qt `.ts/.qm` 與伺服器字型/i18n 設定為基礎。

## 7. 文件關係

本文件描述「要做什麼」；實作方式、模組責任、資料結構與介面細節請參閱 SDS。需求與設計的對應關係見 `SDS/12_Traceability.md`。
