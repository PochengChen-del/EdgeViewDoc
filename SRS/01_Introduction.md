# SRS-01 簡介（Introduction）

## 1. 目的（Purpose）

本文件定義 EdgeView 平台的軟體需求規格（Software Requirements Specification, SRS）。目的在於：

1. 建立產品功能的共同理解基準，供產品、開發、測試、維運與資安團隊使用。
2. 將三個程式碼庫（Client、Metadata Service、Runtime Proxy）的實際能力轉換為可驗證的需求條目。
3. 作為後續設計（SDS）、測試計畫、驗收與需求追溯的依據。

## 2. 範圍（Scope）

### 2.1 產品範圍

EdgeView 是一套可跨平台執行的 HMI/SCADA 圖控平台，包含：

- **EdgeView Designer**：以 Qt/QML 實作的視覺化畫面設計工具。
- **EdgeView Viewer**：同一 Client 的執行期模式，載入設計結果並與後端即時互動。
- **EdgeView Metadata Service**：設定、專案、使用者、權限、OPC UA 組態與稽核後端。
- **EdgeView Runtime Proxy**：Record/第三方資料查詢、告警摘要與代理後端。
- **OPC UA 子系統**：設定 UI（Vue）、Data Agent（C#，Runtime/History）、Alarm Agent（Go）。

### 2.2 納入範圍（In Scope）

- 專案與畫面（Drawing/Display）的建立、編輯、版本與匯入匯出。
- Widget 庫、屬性系統、資料綁定（Tag/Variable/Record）、事件腳本、動畫與圖表。
- 2D 向量繪圖（SCDraw2D）、3D 場景（SCDraw3D/threepp）、NanoCanvas 文字/向量渲染。
- 使用者、群組、專案權限、登入來源（WebAccess、BI/EdgeHub、OPC UA、X）。
- OPC UA 子系統：設定 UI、Data Agent（即時/歷史/寫值/瀏覽/憑證/測點）、Alarm Agent（規則/評估/狀態機/Ack/歷史）。
- OPC UA 連線、Tag、專案、使用者與警報設定。
- Client 直連 OPC UA Data Agent 取得即時/歷史 Tag 值。
- 資料來源設定（WebAccess、OPC UA、X、EdgeHub/BI）。
- Record 查詢定義、預覽、分頁、Datalog 歷史查詢。
- WebSocket 即時資料（record data）、警報摘要（WA/EH/OPC UA/X）、系統資訊。
- 警報通知、Firebase 推播、稽核與操作日誌、資料維護。
- 多語系與字型管理、OEM 品牌設定。
- 部署於 Windows/Linux 容器與 Kubernetes 的基本維運需求。

### 2.3 排除範圍（Out of Scope）

- 第三方 PLC/SCADA 控制器本身的韌體行為。
- WebAccess、EdgeHub/Edge365、X 平台內部實作。
- OPC UA Server／裝置端的韌體與資訊模型實作（本文件描述 Agent/UI/Client 與其介接）。
- 行動裝置商店上架流程與憑證生命週期細節。
- 商業授權條款與計價。
- 已停用或未實作功能（例如 MQTT Client、ODBC 資料源）不在需求保證範圍，僅於 SDS-11 記錄現況。

## 3. 對象（Intended Audience）

| 角色 | 使用方式 |
|------|----------|
| 產品經理 | 確認功能範圍、優先級與驗收準則 |
| 系統分析師 | 拆解需求、維護追溯矩陣 |
| 前端工程師 | 實作 Designer/Viewer 功能 |
| 後端工程師 | 實作 Metadata/Runtime API 與資料模型 |
| 測試工程師 | 依 FR/NFR 設計測試案例 |
| DevOps/SRE | 依部署與非功能需求規劃環境 |
| 資安/稽核 | 檢視認證、授權、加密與稽核需求 |

## 4. 定義與縮寫

| 縮寫 | 全稱 | 說明 |
|------|------|------|
| SRS | Software Requirements Specification | 軟體需求規格書 |
| SDS | Software Design Specification | 軟體設計規格書 |
| HMI | Human Machine Interface | 人機介面 |
| SCADA | Supervisory Control and Data Acquisition | 監控與資料擷取 |
| API | Application Programming Interface | 應用程式介面 |
| REST | Representational State Transfer | 表徵狀態轉移 |
| WS | WebSocket | 全雙工通訊協定 |
| CSRF | Cross-Site Request Forgery | 跨站請求偽造 |
| RBAC | Role-Based Access Control | 角色為基礎的存取控制 |
| FCM | Firebase Cloud Messaging | Firebase 雲端訊息 |
| ltree | PostgreSQL ltree | 階層樹狀資料型別 |
| BI | Business Intelligence / EdgeHub 模式 | 資料來源類型 |
| X | EdgeView X 資料平台 | 資料來源類型 |
| WA | WebAccess | Advantech WebAccess |
| OEM | Original Equipment Manufacturer | 品牌客製 |
| NFR | Non-Functional Requirement | 非功能需求 |
| FR | Functional Requirement | 功能需求 |
| IF | Interface Requirement | 介面需求 |

## 5. 參考文件

1. IEEE 830 / ISO/IEC/IEEE 29148：需求規格實務。
2. IEEE 1016：軟體設計描述實務。
3. `D:\Document\2026 EdgeView Features.docx`。
4. `wax_metadata-service\docs\swagger.yaml`。
5. `wax_rutime_proxy\docs\swagger.yaml`。
6. `wax_rutime_proxy\websocket 開發文件.md`。
7. `wax_hmi-designer-client\wa-hmi-designer\docs\qdoc\SC.qdoc` 與 QDoc 產生文件。

## 6. 文件結構

| 章節 | 說明 |
|------|------|
| SRS-01 | 簡介、範圍、定義 |
| SRS-02 | 整體描述：產品定位、角色、環境、限制 |
| SRS-03 | 功能需求：以子系統與領域分組，含 ID、優先級、驗收條件 |
| SRS-04 | 外部介面需求：UI、硬體、軟體、通訊 |
| SRS-05 | 非功能需求：效能、安全、可靠度、可維護、可攜、i18n、稽核 |
| SRS-06 | 驗收準則與需求追溯 |
| SRS-07 | OPC UA 子系統需求：設定 UI、Data Agent、Alarm Agent、直連資料源 |

設計對應請參閱 `SDS/` 目錄；需求與設計的對照請參閱 `SDS/12_Traceability.md`，OPC UA 設計見 `SDS/14_OPCUA_Subsystem_Design.md`。

## 7. 需求表達慣例

- **Shall/Must**：必要需求。
- **Should**：建議需求。
- **May/Could**：選項需求。
- 每條功能需求包含：ID、名稱、優先級、描述、前置條件、主要流程、例外/替代流程、驗收條件。
- 需求若源自特定程式碼行為，於 SDS 對應章節標註檔案路徑。
