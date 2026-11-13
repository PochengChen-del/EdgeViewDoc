# SDS-02 系統架構（System Architecture）

## 1. 架構總覽

EdgeView 採三層式架構：**Client（Qt/QML）**、**EdgeView Server（Metadata + Runtime）**、**資料與整合層**。Client 同時與兩個後端通訊；後端共用 PostgreSQL 設定資料庫，但職責分離。

```mermaid
flowchart TB
    subgraph Presentation["Presentation Layer"]
        direction LR
        Designer["Designer<br/>openMode=config/edit/preview"]
        Viewer["Viewer<br/>openMode=view"]
    end

    subgraph Application["Application Layer"]
        direction LR
        Meta["Metadata Service<br/>Go / Gin<br/>:28883"]
        Proxy["Runtime Proxy<br/>Go / Gin / WS Hub<br/>:28884"]
    end

    subgraph Domain["Domain / Data Layer"]
        PGCfg[(PostgreSQL<br/>waxconfig)]
        ExtDB[(SQL Server / MySQL<br/>Oracle / PostgreSQL)]
        Influx[(InfluxDB v1/v2)]
    end

    subgraph Integration["Integration Layer"]
        WA[WebAccess]
        EH[EdgeHub / Edge365]
        X[X Platform]
        OPC[OPC UA Agent]
        FCM[Firebase FCM]
    end

    Designer -->|REST + WS| Meta
    Viewer -->|REST + WS| Meta
    Designer -->|REST + WS| Proxy
    Viewer -->|REST + WS| Proxy
    Meta --> PGCfg
    Proxy --> PGCfg
    Proxy --> ExtDB
    Proxy --> Influx
    Meta --> WA
    Meta --> EH
    Meta --> X
    Meta --> OPC
    Proxy --> WA
    Proxy --> EH
    Proxy --> X
    Proxy --> OPC
    Proxy --> FCM
```

## 2. 元件責任

| 元件 | 責任 | 不負責 |
|------|------|--------|
| Client UI（QML Pages） | 頁面流程、版面、使用者互動 | 直接存取資料庫 |
| Client Common 單例 | 全域狀態、資源、綁定、腳本、路徑、設定 | 後端商業邏輯 |
| Client API/WS 層 | REST/WS 呼叫、重試、錯誤處理 | 資料格式的長期儲存 |
| Client 渲染層 | 2D/3D/NanoCanvas 繪製、批次、快取 | 業務規則 |
| Client Controller | 檔案、HTTP、圖片、剪貼簿、Session、除錯 | UI 排版 |
| Metadata Controller | 請求解析、驗證、回應 | SQL 細節（交由 model） |
| Metadata Service | 應用流程、權限、整合 | HTTP 路由（routes） |
| Metadata Model | 資料表 CRUD、SQL 組裝 | 請求格式 |
| Metadata Repository | SQLManager、連線池、交易 | 業務規則 |
| Runtime Controller | REST/WS 請求處理 | 資料源細節 |
| Runtime Service | 查詢、輪詢、告警、代理、通知 | UI |
| Runtime Repository | 多資料源連線與方言 | 業務規則 |
| WebSocket Hub | 連線、topic/action、訂閱、廣播 | 資料查詢 |

## 3. 分層設計

### 3.1 Client 分層

```mermaid
flowchart TB
    UI["ui/page/*<br/>功能頁面"]
    COMP["ui/component/*<br/>共用元件與 Widget"]
    COMMON["Common/*<br/>Global、ResourceManager、WidgetManager、<br/>WADataSourceManager、WAVariableManager、<br/>WAJavaScriptManager、RecordManager"]
    API["Common/Api/*<br/>REST 包裝"]
    WS["Common/Api/ws/*<br/>WebSocket topic controllers"]
    IO["io/*<br/>http、websocket、file、cookie、image、media、video"]
    CTRL["controller/*<br/>cppcontroller、drawcontroller、clipboard、<br/>imagecache、sessionmonitor、debuglogger"]
    RENDER["SCDraw2D、SCDraw3D、NanoCanvas"]
    SET["settings/*、security/*"]
    PLAT["platform: android、ios、macos、Crashpad"]

    UI --> COMP
    COMP --> COMMON
    COMMON --> API
    COMMON --> WS
    API --> IO
    WS --> IO
    COMMON --> CTRL
    COMP --> RENDER
    COMMON --> SET
    CTRL --> PLAT
```

### 3.2 後端分層（兩個 Go 服務共用樣式）

```mermaid
flowchart LR
    REQ[HTTP/WS Request] --> ROUTES[routes<br/>註冊、驗證、時間戳]
    ROUTES --> CTRL[controller<br/>解析、回應]
    CTRL --> SVC[service<br/>Application、整合、輪詢]
    SVC --> MODEL[model<br/>資料結構、SQL]
    MODEL --> REPO[repository<br/>SQLManager、連線池]
    REPO --> DB[(PostgreSQL / 外部 DB)]
```

## 4. 執行期拓撲與連接埠

| 元件 | 預設位址 | 協定 | 備註 |
|------|----------|------|------|
| Client | 動態 | — | 桌面/行動/瀏覽器 |
| Metadata HTTP | `127.0.0.1:28883` | HTTP | `net.http` |
| Metadata HTTPS | 停用（`port: -1`） | HTTPS | 可設定 |
| Metadata Proxy 目標 | `127.0.0.1:28884` | HTTP | `proxy.net.http` |
| Runtime HTTP | `127.0.0.1:28884` | HTTP | `proxy.net.http` |
| Runtime WebSocket | `/so` | WS | BI 模式為 `/` |
| Runtime REST | `/record/*`、`/foo/*` | HTTP | 見 SDS-07 |
| PostgreSQL | 依設定 | PostgreSQL | 設定資料庫 |
| OPC UA Agent | `127.0.0.1:5123`（資料）、`8090`（告警） | HTTP | 依設定 |
| Edge365 Ingress | 依環境 | HTTPS | `/apihub`、`/dbmaster`、`/alarm` |

### 4.1 部署圖

```mermaid
flowchart TB
    subgraph Browser["使用者裝置"]
        C1[Desktop Client]
        C2[Mobile Client]
        C3[Browser WASM]
    end
    subgraph K8s["Kubernetes / 主機"]
        ING[Ingress / Nginx<br/>TLS、75M body]
        META[Meta Pod<br/>:28883]
        PROXY[Proxy Pod<br/>:28884]
        PG[(PostgreSQL)]
    end
    subgraph External["外部"]
        WA[WebAccess]
        EH[Edge365]
        OPC[OPC UA Agent]
        DB[(第三方 DB)]
        FCM[Firebase]
    end

    C1 --> ING
    C2 --> ING
    C3 --> ING
    ING --> META
    ING --> PROXY
    META --> PG
    PROXY --> PG
    META --> WA
    META --> EH
    META --> OPC
    PROXY --> WA
    PROXY --> EH
    PROXY --> DB
    PROXY --> OPC
    PROXY --> FCM
```

## 5. 通訊與資料流

### 5.1 設定資料流（Designer）

```mermaid
sequenceDiagram
    participant D as Designer
    participant M as Metadata Service
    participant P as PostgreSQL
    D->>M: POST /api/getdisplaylist/:projid (Session + CSRF)
    M->>P: SELECT Display / ObjectPath
    P-->>M: rows
    M-->>D: {data:{error,result}}
    D->>M: POST /api/getdisplaydetail/:displayid
    M-->>D: 畫面 JSON（含 Widget/綁定/腳本）
    D->>D: 解析並建立 Widget
```

### 5.2 即時資料流（Viewer）

```mermaid
sequenceDiagram
    participant V as Viewer
    participant R as Runtime Proxy
    participant WS as WS Hub
    participant DB as 外部資料源
    V->>R: WS connect /so
    V->>R: {topic:login, action:login, csrfToken}
    R-->>V: {error:0}
    V->>R: {topic:recorddata, action:register, request}
    R->>WS: 建立訂閱與輪詢
    loop 每 1 秒
        WS->>DB: 查詢
        DB-->>WS: rows
        WS-->>V: {topic:recorddata, action:query, result}
    end
    V->>R: {topic:recorddata, action:unregister}
```

### 5.3 告警資料流

```mermaid
sequenceDiagram
    participant V as Viewer
    participant R as Runtime Proxy
    participant A as Alarm Source
    participant F as Firebase
    V->>R: {topic:waalarmsummary, action:register}
    loop 每 1 秒
        R->>A: GetAlarmSummary
        A-->>R: 摘要
        R-->>V: {topic:waalarmsummary, action:query}
    end
    R->>F: 依 FCM 設定推送
```

## 6. 技術架構決策摘要

| 決策 | 選擇 | 理由 | 影響 |
|------|------|------|------|
| UI 技術 | Qt/QML | 跨平台、GPU 加速、豐富元件 | 需要 Qt 建置與平台打包 |
| 2D 渲染 | SCDraw2D（QRhi） | 批次繪製、管線快取、跨後端 | 學習曲線與自訂 shader |
| 3D 渲染 | threepp/qt-threepp | 成熟 3D 引擎與 glTF 支援 | 套件較大、需資產管理 |
| 設定後端 | Go + Gin | 高效、易部署、生態完整 | 需自行維護分層 |
| 即時通訊 | WebSocket | 雙向、低延遲 | 需處理重連與認證 |
| 設定儲存 | PostgreSQL + JSONB | 關聯 + 彈性 | 需管理 schema 與遷移 |
| 多資料源 | 每來源獨立 manager | 方言差異大 | 維護成本 |
| 部署 | Docker/K8s | 標準化、可擴充 | 需 Ingress/Session 設計 |

## 7. 架構限制與邊界

1. Metadata 與 Runtime 雖共用 PostgreSQL，但不共用程式碼；資料表契約需保持同步（例如 `Record`、`DataSource`）。
2. WebSocket Hub 為單程序記憶體狀態；多副本需 Sticky Session 或外部 Pub/Sub。
3. Client 的 Designer/Viewer 共用執行檔，模式切換需清理設計期狀態。
4. 外部平台中斷時，依功能降級（見 SRS-05 NFR-REL-010）。
5. 瀏覽器模式不支援原生檔案系統、Crashpad、Session 監控。

## 8. 與需求對照

| 架構元素 | 對應需求 |
|----------|----------|
| Client 分層與模式 | FR-CLI-001~095 |
| Metadata 分層與 API | FR-META-001~045 |
| Runtime REST/WS | FR-RT-001~025 |
| 部署與連線 | IF-COM-001~008、NFR-PORT-001~005 |
| 安全邊界 | NFR-SEC-001~014 |
