# SDS-01 簡介（Introduction）

## 1. 目的

本文件為 EdgeView 的軟體設計規格（Software Design Specification, SDS），描述三個子系統的內部架構、模組責任、資料設計、介面設計、安全設計與關鍵流程。目的：

1. 讓開發者能從文件快速定位程式碼與理解設計意圖。
2. 作為需求（SRS）與實作之間的橋樑。
3. 記錄設計取捨、已知限制與技術債，供後續演進。

## 2. 範圍

| 子系統 | 程式碼根目錄 | 本文件章節 |
|--------|--------------|------------|
| Client（Designer + Viewer） | `wax_hmi-designer-client\wa-hmi-designer` | SDS-03 |
| Metadata Service | `wax_metadata-service` | SDS-04 |
| Runtime Proxy | `wax_rutime_proxy` | SDS-05 |
| OPC UA 設定 UI | `wax_opcua_agent_ui` | SDS-14 §3 |
| OPC UA Data Agent | `wax_opcua_agent` | SDS-14 §4 |
| OPC UA Alarm Agent | `wax_opcua_alarm_agent` | SDS-14 §5 |
| 共用資料模型 | 後端 | SDS-06 |
| 介面（REST/WS） | 後端 | SDS-07、SDS-14 §6 |
| 安全 | 全系統 | SDS-08 |
| 關鍵流程 | 全系統 | SDS-09 |
| 部署維運 | 全系統 | SDS-10 |
| 設計取捨與風險 | 全系統 | SDS-11 |
| 追溯 | 全系統 | SDS-12 |
| OPC UA 子系統 | 三相關程式庫 | SDS-14 |

## 3. 設計目標與原則

### 3.1 設計目標

| 目標 | 說明 |
|------|------|
| 跨平台 | 同一 Client 支援桌面、行動與瀏覽器 |
| 設計/執行分離 | 同一程式的 Designer 與 Viewer 模式，共用 Widget 與渲染管線 |
| 設定與資料分離 | Metadata 管設定，Runtime 管資料查詢與即時資料 |
| 多資料源 | WebAccess、EdgeHub/BI、X、OPC UA、SQL/Influx/HTTP |
| 可擴充 | Widget、資料源、語系、外掛、3D 資產可擴充 |
| 安全 | Session + CSRF + 時間戳 + 加密設定 + 稽核 |
| 可維運 | 標準化組態、日誌、容器部署、Swagger |

### 3.2 設計原則

1. **分層**：後端 `routes → controller → service → model → repository`；Client `UI(QML) → Common 單例 → API/IO → C++ 控制器/渲染`。
2. **單一責任**：每個 controller 對應一個領域；每個 model 對應一組資料表。
3. **契約優先**：REST/WS 訊息格式統一，錯誤碼一致。
4. **設定外部化**：`config.yaml` + 環境變數/命令列。
5. **向後相容**：物件與 API 具版本欄位；匯入支援 v1/v2。
6. **失效隔離**：外部服務失敗不應拖垮核心功能；panic 回復與逾時控制。
7. **可追溯**：需求 ID、程式路徑與測試案例可對照。

## 4. 技術棧

### 4.1 Client

| 類別 | 技術 |
|------|------|
| 語言 | C++（C++20）、QML、JavaScript |
| 框架 | Qt 6（Core、Quick、QuickControls2、Svg、Xml、WebSockets、Multimedia、ShaderTools） |
| 渲染 | SCDraw2D（QRhi 批次 2D）、SCDraw3D（threepp/qt-threepp）、NanoCanvas（NanoVG/ANOVG） |
| 3D | threepp、glTF/OBJ/FBX/STL/USD 等載入器 |
| 建置 | CMake |
| 平台 | Windows、macOS、Linux、Android、iOS、WebAssembly |
| 其他 | Crashpad、ZXing、Firebase（選用）、Qt WebView（選用） |

### 4.2 Metadata Service

| 類別 | 技術 |
|------|------|
| 語言 | Go |
| Web | Gin |
| Session | gin-contrib/sessions + PostgreSQL store |
| 資料庫 | PostgreSQL（`lib/pq`） |
| 日誌 | klog |
| 設定 | Viper |
| 文件 | Swaggo / Swagger / Widdershins |
| 整合 | WebAccess、Edge365（etcd/GraphQL）、X、OPC UA Agent、Firebase、bun-imex、Balsam |

### 4.3 Runtime Proxy

| 類別 | 技術 |
|------|------|
| 語言 | Go 1.23 |
| Web | Gin |
| WebSocket | Gorilla WebSocket（自建 Hub） |
| 資料庫 | PostgreSQL、SQL Server、Oracle、MySQL、InfluxDB v1/v2、HTTP |
| Session | gin-contrib/sessions + PostgreSQL store |
| 日誌 | klog |
| 設定 | Viper |
| 部署 | Docker、K8s |

## 5. 名詞

與 SRS-01 §4 相同，補充設計用語：

| 名詞 | 說明 |
|------|------|
| Hub | Runtime Proxy 的 WebSocket 連線與 topic 管理中心 |
| Polling | 後端定期查詢資料源並推送的機制 |
| Binding | Widget 屬性與資料來源的對應 |
| openMode | Client 模式：`config=0`、`edit=1`、`preview=2`、`view=3` |
| rcc | Qt 資源包，可由伺服器遠端更新 |
| SQLManager | 後端資料庫存取抽象 |
| ltree | PostgreSQL 階層路徑型別 |
| ObjectPath | 專案物件樹資料表 |
| Record | 資料查詢定義與執行結果 |
| DataModel | 可重用變數集合 |
| SourceType | 資料來源類型（WA/BI/OPCUA/X） |

## 6. 文件結構

| 章節 | 內容 |
|------|------|
| SDS-02 | 系統架構與部署 |
| SDS-03 | Client 設計 |
| SDS-04 | Metadata Service 設計 |
| SDS-05 | Runtime Proxy 設計 |
| SDS-06 | 資料模型與字典 |
| SDS-07 | 介面設計（REST/WS） |
| SDS-08 | 安全設計 |
| SDS-09 | 關鍵流程 |
| SDS-10 | 部署與維運 |
| SDS-11 | 設計取捨與風險 |
| SDS-12 | 追溯 |
| SDS-13 | 安全修復（R-001/R-002/R-003） |
| SDS-14 | OPC UA 子系統設計（UI/Data Agent/Alarm Agent/直連資料源） |

## 7. 參考

- `SRS/` 全部章節。
- `wax_metadata-service\docs\swagger.yaml`、`wax_rutime_proxy\docs\swagger.yaml`。
- `wax_rutime_proxy\websocket 開發文件.md`。
- `wax_hmi-designer-client\wa-hmi-designer\docs\qdoc\SC.qdoc`。
- `D:\Document\EdgeViewDoc\docs`。
- `wax_opcua_agent\wax_opcua_agent\appsettings.json`、`Controllers/`。
- `wax_opcua_alarm_agent\README.md`、`api.md`、`impl.md`。
- `wax_opcua_agent_ui\src/`。

## 8. 閱讀建議

- 想了解「如何運作」：先讀 SDS-02，再依子系統讀 SDS-03/04/05；OPC UA 讀 SDS-14。
- 想查 API：直接看 SDS-07（含 OPC UA Agent/Alarm）。
- 想查資料表：看 SDS-06。
- 想了解安全：看 SDS-08。
- 想了解流程：看 SDS-09。
- 想了解限制：看 SDS-11。
