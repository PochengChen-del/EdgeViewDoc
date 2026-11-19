# SRS-04 外部介面需求（External Interface Requirements）

本章定義 EdgeView 與使用者、硬體、軟體及外部系統之間的介面需求。詳細 API 目錄與訊息格式見 `SDS/07_Interface_Design.md`。

---

## 4.1 使用者介面（User Interfaces）

### IF-UI-001 啟動與連線流程

- 使用者依序經過：租戶/組織選擇 → 伺服器連線 → 登入 → 專案清單 → Designer/Viewer。
- 若只有一個可用專案，系統可自動進入 Viewer（`FR-CLI-022`）。
- 連線或登入失敗時，需顯示可理解的錯誤訊息與重試選項。

### IF-UI-002 Designer 版面

| 區域 | 內容 |
|------|------|
| 頂部 | 選單列（File/Edit/View/Goto/Help）、縮放/縮放模組、模式切換 |
| 左側 | Widget 庫、資料來源、變數瀏覽、裝置瀏覽、資訊 |
| 中央 | 設計畫布、格線、尺規、選取控制點 |
| 右側 | 屬性、圖層、繪圖樹、綁定、圖表設定、腳本清單 |
| 底部 | 狀態列、頁籤、縮放比例 |

### IF-UI-003 Viewer 版面

- 全螢幕或視窗化顯示畫面；依 `projectdetail` 設定顯示/隱藏工具列、縮放模式。
- 提供：About、通訊狀態、資料來源狀態、變數監看、縮放、警報記錄、配方、導覽。
- 權限不足時隱藏或停用對應操作；Session 逾時顯示鎖定/重新登入。

### IF-UI-004 響應式版面

- 依寬度切換 Desktop / Tablet / Phone 版面（對應 `Index.qml`、`IndexTablet.qml`、`IndexPhone.qml`）。
- 支援 24 欄 Grid、固定列高、768px 斷點、Widget 寬度百分比（`2026 EdgeView Features`）。

### IF-UI-005 多語系

- 支援 `eng`、`fra`、`jpn`、`kr`、`twn`、`zh`，預設 `eng`。
- 語系檔可隨應用內建或自伺服器下載（`/i18n/i18n_<lan>.qm`）。
- 字型可由 `/api/langfont/font/:fontfamily` 取得。

### IF-UI-006 可用性與無障礙

- 主要操作需有鍵盤焦點與快捷鍵（`FR-CLI-045`）。
- 錯誤訊息需包含原因與建議動作；破壞性操作需二次確認。
- 支援高 DPI 與不同螢幕尺寸。

---

## 4.2 硬體介面（Hardware Interfaces）

| ID | 介面 | 需求 |
|----|------|------|
| IF-HW-001 | 顯示器 | 支援 1024×768 以上；3D 建議支援 OpenGL 3.3/ES 3.0 以上 |
| IF-HW-002 | 輸入裝置 | 滑鼠、鍵盤、觸控；支援多點觸控縮放/平移 |
| IF-HW-003 | GPU | 支援 OpenGL/OpenGL ES/Vulkan；不足時降級或提示 |
| IF-HW-004 | 儲存 | 本機可寫入目錄用於快取、日誌、當機報告與匯出檔 |
| IF-HW-005 | 網路 | 可連線至 Metadata/Runtime；建議低延遲穩定網路 |
| IF-HW-006 | 行動裝置 | 相機（二維碼掃描，選用）、音訊輸出（警報音） |
| IF-HW-007 | 伺服器 | CPU/記憶體/磁碟依部署規模；PostgreSQL 需持久化儲存 |

---

## 4.3 軟體介面（Software Interfaces）

### IF-SW-001 PostgreSQL（設定資料庫）

- 用途：Metadata/Runtime/OPC UA Data Agent/OPC UA Alarm Agent 共用設定、工作階段、稽核與警報 runtime 資料。
- 必要擴充：`ltree`、`uuid-ossp`。
- 連線參數：host、port、database、schema、user、password、SSL。
- 需求：服務啟動時可建立必要資料表與索引；連線池上限依設定。

### IF-SW-002 WebAccess

- 用途：使用者登入、專案/Tag 下載、告警摘要。
- 介面：REST（`/waconfig/api`、`/waxapi` 等），Basic Auth。
- 需求：可設定主機、埠、協定、帳密；連線逾時預設 30 秒。

### IF-SW-003 EdgeHub / Edge365

- 用途：資料來源、API Hub、DB Master、Alarm 代理、應用註冊。
- 介面：HTTPS、GraphQL、etcd。
- 需求：可設定 namespace/endpoint/route；Token 可刷新。

### IF-SW-004 X Platform

- 用途：使用者驗證、專案/Tag、告警。
- 介面：`/waxapi` REST，Basic Auth。
- 需求：可設定主機、埠、協定、帳密。

### IF-SW-005 OPC UA Data Agent

- 用途：OPC UA 連線、即時值、寫值、歷史值、Tag 明細、瀏覽、憑證、常數/計算/系統測點。
- 介面：HTTP/HTTPS；對外 xnginx `/ua`（Runtime `28886`）、`/uadb`（History `28887`）。
- 認證：Metadata Session Cookie + `csrfToken`，或 loopback 內部 Bearer。
- 需求：可設定 URI、安全模式/政策、帳密、PFX；Runtime/History 角色分離。
- 端點：`/ua/GetTagValue*`、`/ua/SetTagValue*`、`/ua/TagDetail/{projid}`、`/ua/Browse/*`、`/uadb/GetDataLog/{projid}`、`/uadb/GetRawDataLog/{projid}`、`/ua/Certificate/create`。
- 詳細需求：`SRS/07` FR-OPC-013~027、IF-OPC-002/003。

### IF-SW-006 第三方資料庫

| 類型 | 驅動/協定 | 必要參數 |
|------|-----------|----------|
| PostgreSQL | `lib/pq` | host_url、database、schema、user、password、ssl |
| SQL Server | `go-mssqldb` | host_url、database、user、password |
| Oracle | `go-ora` | host_url、database、user、password |
| MySQL | `go-sql-driver/mysql` | host_url、database、user、password |
| InfluxDB v1 | InfluxDB v1 client | host_url、database、user、password |
| InfluxDB v2 | InfluxDB v2 client | host_url、bucket、org、token |
| HTTP | 標準 HTTP | method、url、headers、body |

### IF-SW-007 Firebase

- 用途：行動裝置警報推播。
- 介面：FCM HTTP v1，服務帳號 JSON。
- 需求：裝置需註冊 token 與專案訂閱。

### IF-SW-008 瀏覽器（WebAssembly）

- 需求：支援 WebSocket、Cookie、IndexedDB/local storage（`wasmsettings`）。
- 限制：無原生檔案系統與 Crashpad；部分原生 API 不可用。

### IF-SW-009 外部工具

| 工具 | 用途 | 需求 |
|------|------|------|
| `bun-imex` | v2 匯入/匯出引用解析 | 執行檔存在且可執行 |
| Balsam | 3D 模型轉換 | 平台對應執行檔存在 |
| `waxcert` | Windows 憑證處理 | 路徑可設定 |

### IF-SW-010 OPC UA Alarm Agent

- 用途：警報規則、清單/歷史、Ack/Process、Alarm 欄位。
- 介面：HTTP/HTTPS；對外 xnginx `/uaam`（上游 `28889`；程式預設 8090）。
- 認證：Session + CSRF、本機 Basic Auth、loopback Bearer。
- 端點：`/uaam/api/alarm/list`、`/uaam/api/alarm/log`、`/uaam/api/alarm/ack`、`/uaam/api/alarm/processhistory`、`/uaam/api/alarm/definition/list`、`/uaam/api/alarm/simulate`、`/uaam/api/alarm/reload`、`/uaam/GetTagValue/:projid`、`/uaam/ping/:id`。
- 詳細需求：`SRS/07` FR-OPC-028~045、IF-OPC-004。

### IF-SW-011 OPC UA 設定 UI

- 用途：OPC UA 專案/連線/Tag/警報/使用者設定。
- 介面：HTTP 靜態 SPA（`/sc/opcua/`），API 走 Metadata `/sc/api/*`，經 xnginx。
- 認證：Metadata Session Cookie + `settings-csrfToken`。
- 需求：`graphic` 角色不得進入；依 `permissions` 顯示模組。

### IF-SW-012 xnginx Reverse Proxy

- 用途：地端 Client 統一入口；TLS、路由、限流、WebSocket、服務監督。
- 介面：HTTP `8880`／HTTPS `8881`。
- 路由：`/sc`→Metadata、`/wxproxy`、`/ws`→Runtime、`/ua`→Data Agent、`/uadb`→History、`/uaam`→Alarm、`/system`→system。
- 設定：`xnginx.yaml`（主）、`xnginx.webaccess.yaml`、`xnginx.opcua.yaml`、`xnginx.x.yaml`。
- EdgeHub/BI 例外：使用 K8s Ingress，不使用地端 xnginx。

---

## 4.4 通訊介面（Communication Interfaces）

### IF-COM-001 HTTP REST

- 協定：HTTP/1.1 或 HTTPS。
- 編碼：JSON（`application/json`）或 `multipart/form-data`（上傳）。
- Metadata 預設埠：`28883`；Runtime 預設埠：`28884`；地端 xnginx HTTP/HTTPS：`8880`/`8881`（`/sc/`、`/wxproxy`、`/ws`、`/ua`、`/uadb`、`/uaam`、`/system`）。
- 共用回應格式：

```json
{
  "data": {
    "error": 0,
    "reason": "",
    "result": {}
  }
}
```

> 部分端點直接回傳列舉資料或二進位檔（圖片、字型、縮圖），不套用上述信封。

### IF-COM-002 認證與防護標頭/欄位

| 項目 | 位置 | 說明 |
|------|------|------|
| Session Cookie | Cookie | `graphicSession`（設定頁另有 setting session） |
| `csrfToken` | JSON body / form field | 與 Session 綁定的 CSRF 權杖 |
| `packetTimestamp` | JSON body / form field | 防重放時間戳，需在 `driftseconds` 內 |
| Basic Auth | `Authorization` | 伺服器對伺服器呼叫時可用 |
| `EIToken` / `EIName` | Cookie | BI/Edge365 模式使用 |

### IF-COM-003 錯誤碼

| 代碼 | 名稱 | 說明 |
|------|------|------|
| `0` | 成功 | 正常 |
| `-100` | Unauthorized | 未登入、Session 失效、CSRF 缺失/錯誤 |
| `-101` | Access Deny | 已登入但無權限 |
| `-102` | Packet Timeout | `packetTimestamp` 超出容許漂移 |
| `-500` | Unknown | 未預期錯誤 |
| `-501` | Specified | 已知錯誤，`reason` 提供原因 |

### IF-COM-004 WebSocket

- Runtime 端點：`/so`（BI 模式為 `/`）。
- 訊息信封：

```json
{ "topic": "recorddata", "action": "register", "request": { } }
```

```json
{ "topic": "recorddata", "action": "query", "error": 0, "reason": "", "result": { } }
```

- 登入：連線後傳送 `topic=login, action=login` 並附 `csrfToken`；逾時（預設 10 秒）斷線。
- 心跳：server ping 週期 54 秒，pong 等待 60 秒，寫入逾時 60 秒。
- 訊息大小上限：100 MB（需依安全政策調整）。

### IF-COM-005 支援的 WebSocket Topic

| Topic | Action | 說明 |
|-------|--------|------|
| `login` | `login` | WebSocket 認證 |
| `recorddata` | `register`/`unregister`/`update`/`updatevalue`/`prevpage`/`nextpage`/`gotopage` | 即時 Record 資料與分頁 |
| `waalarmsummary` | `register`/`unregister` | WebAccess 告警摘要 |
| `ehalarmsummary` | `register`/`unregister` | EdgeHub 告警摘要 |
| `opcuaalarmsummary` | `register`/`unregister` | OPC UA 告警摘要 |
| `xalarmsummary` | `register`/`unregister` | X 告警摘要 |
| `systeminfo` | 登入後啟動 | 系統資訊推送 |
| `default` | 無 | 登入後 ping/pong 與廣播（開發用） |

### IF-COM-006 資料庫查詢介面

- SQL 查詢模型：`max_data_size`（1–1000）、`table_name`、`columns`、`orders`、`filter_logic_and`、`filters`。
- Datalog：`table_name`、`time_columns`、`by_row`、`tag_column_name`、`value_column_name`、`columns`、`type`。
- Influx：`measurement_name`、`columns`、`group_by_interval`、`order_by_desc`、`start_time`、`end_time`。
- 時間範圍支援 Grafana 風格語法（如 `now-1h`），由後端轉換。
- 時區：`by_system_timezone: true` 時依系統時區轉換。

### IF-COM-007 檔案與資源介面

| 端點型態 | 說明 | 認證 |
|----------|------|------|
| `/getfile`, `/getfile/:pathid/:filename` | 通用檔案 | 否 |
| `/gettumb/:objectid`, `/gettumb/:objectid/:type` | 縮圖 | 否 |
| `/getdmtumb/:projid/:dmid` | 資料模型縮圖 | 否 |
| `/getpluginfile/:projid/:filename` | 外掛檔案 | 否 |
| `/api/langfont/font/:fontfamily` | 字型檔 | 否 |
| `/api/oem/image/:imagetype` | OEM 圖片 | 否 |
| `/api/uploadfile`、`/api/uploadimage/*`、`/api/uploadaudio/*` | 上傳 | 是（CSRF） |

- 上傳大小限制：Client 端 `g_maxUploadFileSize = 5242880`（5 MB）；Ingress 預設 75 MB。
- 靜態資源回應允許 iframe 嵌入（`X-Frame-Options: ALLOWALL`、CSP `frame-ancestors *`）。

### IF-COM-008 協定與逾時摘要

| 介面 | 逾時/限制 |
|------|-----------|
| xnginx | connect 300 秒、read/send 300 秒、keepalive 6000 秒、body 無限制 |
| Metadata HTTP server | read/write 5 分鐘、header 30 秒、idle 120 秒 |
| Runtime HTTP server | request 2 分鐘 |
| WebAccess/X HTTP client | 30 秒 |
| Client HTTP 佇列 | 255 連線、1024 佇列、等待 120 秒（Viewer 10 秒） |
| WebSocket | login 10 秒、ping 54 秒、pong 60 秒 |
| Session | 預設 604800 秒（7 天） |
| 封包時間戳 | 300 秒 |
| Record 輪詢 | 1 秒 |
| 系統資訊 | 3 秒 |
| 告警摘要 | WA/EH/X/OPC UA 預設 1 秒；可由設定調整（最低 100 ms） |
| OPC UA 即時值 | Client 掃描預設 1 秒（最低 500 ms）；Data Agent 快取 500 ms |
| OPC UA 歷史值 | 依查詢筆數/Server；History Agent 獨立 |
| OPC UA Alarm 輪詢 | 預設 500 ms（`config.yaml` 1000 ms） |

---

## 4.5 介面需求追溯

| 介面 | 對應功能需求 | 對應設計章節 |
|------|--------------|--------------|
| 使用者介面 | FR-CLI-001~095 | SDS-03 |
| REST 設定/授權 | FR-META-001~045 | SDS-04、SDS-07 |
| REST 資料查詢 | FR-RT-001~009 | SDS-05、SDS-07 |
| WebSocket | FR-RT-010~017 | SDS-05、SDS-07 |
| 資料庫 | FR-META-001、FR-RT-008 | SDS-06 |
| 外部平台 | FR-META-045、FR-RT-019~022 | SDS-04、SDS-05 |
| OPC UA 設定 UI | FR-OPC-001~012 | SDS-14 §3 |
| OPC UA Data Agent | FR-OPC-013~027 | SDS-14 §4、§8 |
| OPC UA Alarm Agent | FR-OPC-028~045 | SDS-14 §5、§6 |
| 使用手冊介面（URL/安裝/授權/插件/xnginx） | FR-UM-001~020、IF-UM-001~007 | SDS-10 §2.1、SDS-15 §10~12 |
