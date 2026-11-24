# SDS-13 安全修復紀錄

本章記錄風險登記（SDS-11）中已完成修復項目的設計、部署需求與驗收方式。風險編號與 SDS-11 一致。

## 1. 修復狀態

| 風險 | 項目 | 狀態 | 摘要 |
|------|------|------|------|
| R-001 | Session secret 硬編碼 | Fixed | 改由 `SESSION_SECRET` 或 `aes_secret` 經 HKDF 派生；無硬編碼、無 legacy fallback |
| R-002 | 全域停用 TLS 憑證驗證 | Fixed | 移除 `http.DefaultTransport` 全域 `InsecureSkipVerify` |
| R-003 | `/record/queryrecord` 無認證 | Fixed | 路由停用，handler 不再標註 `@Router`，Swagger 目錄移除 |
| R-006 | WebSocket 部分 topic 未認證 | Fixed | 非 `login` topic 統一要求 `AlreadyLogin`；systeminfo 登入後才註冊 |
| R-007 | WS 登入掃描所有 Session | Fixed | WS handshake 綁定 `graphicSession`；只比對該 Session 的 `csrfToken`，不掃描全表 |
| R-008 | EH/X 告警輪詢間隔未初始化 | Fixed | 預設 1000ms；`<=0` 用預設、低於 100ms 夾到 100ms |
| R-009 | Record 快取無 TTL | Fixed | 新增 idle TTL、全域/每 client 上限、reaper 與 `Stats()` |
| R-018 | 預設 DB 連線字串/密碼 | Fixed | 移除預設密碼；改用 `ENSAAS_SERVICES` / `LOG_ENV` / `VCAP_SERVICES`；新增 `RegisterDBEnv.reg` 與 machine env fallback |
| R-019 | 動態 SQL 組裝 | Fixed | 識別字集中跳脫/白名單；值參數化；UpdateValue 欄位驗證 |
| R-028 | OPC UA 預設 DB 帳密 | Fixed | OPC UA Agent 移除預設密碼；Alarm 使用 `waxalarm`（runtime）與 `waxconfig`（metadata） |
| R-004 | 密碼雜湊未加鹽 | Fixed | 改 Argon2id（隨機鹽 + `aes_secret`/`SESSION_SECRET` pepper）；舊 SHA-256 登入即升級 |

## 2. R-001 Session secret

### 2.1 設計

Session key 由各服務啟動時解析，**不得回退到原始碼中的固定值**。

取得順序：

1. `SESSION_SECRET` 環境變數（至少 32 bytes）。
2. `aes_secret` 資料表 `id = 1` 的 32-byte 機器金鑰，經 HKDF-SHA256 派生：

```text
sessionKey = HKDF-SHA256(
    IKM  = aes_secret.secret_key,
    salt = nil,
    info = "edgeview/session-cookie/v1",
    len  = 32
)
```

3. 兩者皆無：啟動失敗，並在錯誤訊息提示設定 `SESSION_SECRET` 或執行 KeyMigrator。

`info` 是公開的用途隔離標籤，不是密鑰；真正機密只有 `SESSION_SECRET` 或 `aes_secret`。

### 2.2 程式位置

| 服務 | 檔案 | 進入點 |
|------|------|--------|
| Metadata | `wax_metadata-service/service/session_secret.go` | `meta-agent.go` |
| Runtime Proxy | `wax_rutime_proxy/service/session_secret.go` | `wxproxy.go` |
| xnginx | `wax_nginx_ingress/service/session_secret.go` | `service/app.go` |
| OPC UA Alarm Agent | `wax_opcua_alarm_agent/service/session_secret.go` | `opcua_alarm.go` |

`LoadSessionKeys()` 回傳 `SessionKeys`，`KeyPairs()` 產生 pgstore 所需的 key pairs；`Previous` 只用於解碼舊 cookie。Alarm Agent 的 session store 使用 `MetadataConfigDB`/`MetadataSchema`，因此派生金鑰時也讀取同一組 metadata DB 設定。

### 2.3 輪替

1. 設定新的 `SESSION_SECRET`，舊值放入 `SESSION_SECRET_PREVIOUS`。
2. 同時部署到所有共用 session 的服務，順序需一致。
3. 保留 previous 至少一個 `sessiontimeout`（預設 7 天）後移除。

若使用 `aes_secret` 派生，輪替機器金鑰需另行規劃，不可直接刪除 `aes_secret` 重跑 KeyMigrator，以免影響已加密的資料源密碼。

### 2.4 一致性

- Metadata、Runtime、xnginx 共用 `waxconfig`；建議明確設定 `PG_SCHEMA`，確保讀到同一筆 `aes_secret`。
- OPC UA Alarm Agent 的 session store 使用 `metadata.dbname` / `metadata.dsn` / `metadata.schema`（預設 `waxconfig`）；若需與其他服務共用 `graphicSession`，必須使用相同 `SESSION_SECRET`，且 metadata DB/schema 需一致。
- 啟動 log 僅輸出 `[session] key source=... fingerprint=...`；部署時比對 fingerprint，不要記錄 key 本體。

## 3. R-002 TLS 憑證驗證

- 已移除 `wax_rutime_proxy/wxproxy.go` 的 `http.DefaultTransport` 全域 `InsecureSkipVerify`。


## 4. R-003 `/record/queryrecord`

- `controller/RecordController.go` 的路由註冊已註解，handler 不再標註 `@Router`。
- `wax_rutime_proxy/docs` 的 Swagger 路徑已移除，避免文件宣稱可用。
- 其他 `/record/*` 端點維持 `needAuth=true`（Session + CSRF）。
- 未來若要恢復，必須先加入認證、授權（專案/角色）與速率限制。

## 5. R-006 WebSocket 認證

### 5.1 設計

- `webSocket.go` 在 `handleRecvMessage` 分派前統一檢查：
  - `topic == "login"` 允許未登入。
  - 其他 topic 若 `AlreadyLogin() == false`，回 `-100` 並停止處理，不再落到 `default`。
- `SystemInfoPolling.OnClientAdd` 增加 `AlreadyLogin()` 檢查。
- `systemInfoController` 新增 `OnAuthenticated`：
  - 連線時 `OnClientAdd` 不再註冊。
  - WS login 成功後由 `SoClient.NotifyAuthenticated()` 呼叫，才加入 polling。
- `PingPongController.OnMessage` 再檢查一次 `AlreadyLogin`，避免未來繞過中央 gate。
- `loginController.login` 驗證 CSRF 成功後：
  - `SetAlreadyLogin()`
  - `NotifyAuthenticated()`

### 5.2 程式位置

| 功能 | 檔案 |
|------|------|
| 中央 topic 認證 | `wax_rutime_proxy/service/webSocket.go` |
| 登入後通知 | `wax_rutime_proxy/service/webSocket.go`、`controller/websocket/loginController.go` |
| systeminfo 註冊 | `controller/websocket/systemInfoController.go`、`service/SystemInfoPolling.go` |
| default 防護 | `controller/websocket/baseController.go` |

### 5.3 驗收

1. 未登入送 `recorddata`、`systeminfo`、未知 topic 或 `default`，回 `-100`。
2. 未登入訊息不會被 `default` 廣播。
3. 未登入 client 不出現在 `SystemInfoPolling.clients`。
4. WS login 成功後，systeminfo 才開始推送。
5. `CheckAuthAndCloseAfter` 10 秒後仍會關閉未登入連線。

### 5.4 R-007 WS 登入 Session 綁定

#### 設計

- `service/webSocket.go` 在 WS handshake 時透過 session store 取得 `graphicSession` 對應的 `sessionID` 與 `csrfToken`，保存在 `SoClient`。
- `loginController.verifyToken` 不再查詢 `http_sessions` 全表，只以 `subtle.ConstantTimeCompare` 比對該連線綁定 Session 的 `csrfToken`。
- 缺少、無效或過期的 session cookie 一律登入失敗（`-100`）。
- 原生 Qt `WaWebSocket` 在 handshake 加上 Cookie jar 內容，確保送出 `graphicSession`；WASM／瀏覽器由原生 WebSocket 自動帶同源 Cookie（HTML WebSocket 不允許自訂 `Cookie` header）。

#### 程式位置

| 功能 | 檔案 |
|------|------|
| Session 綁定與 token 比對 | `wax_rutime_proxy/service/webSocket.go` |
| WS 登入驗證 | `wax_rutime_proxy/controller/websocket/loginController.go` |
| 原生 Qt handshake Cookie | `wax_hmi-designer-client/wa-hmi-designer/io/websocket/wawebsocket.cpp` |

#### 驗收

1. 原始碼搜尋 `verifyToken` 不再呼叫 `HttpSessionModel.Query()` 或掃描 `http_sessions`。
2. 有效 `graphicSession` + 相符 `csrfToken`：WS login 成功。
3. 缺少／錯誤／過期 `graphicSession`，或以其他 Session 的 token 登入：回 `-100`。
4. 原生 Qt 與同源 WASM 的 WS handshake 均可見 `Cookie: graphicSession=...` 或成功登入。

## 6. R-008 EH/X 告警輪詢間隔

### 6.1 設計

- `config.go` 新增預設：
  - `eh_alarm_summary_interval_ms = 1000`
  - `x_alarm_summary_interval_ms = 1000`
- 讀取後統一經過 `normalizeIntervalMs`：
  - `<= 0`：使用預設值。
  - `1 ~ 99ms`：夾到最低 100ms。
  - `>= 100ms`：使用設定值。
- 同一規則套用於 WA、OPC UA、SystemInfo，避免其他輪詢因 `0`/負值變成忙迴圈。
- 啟動 log 會輸出 `EHAlmSummaryIntervalMs`、`XAlmSummaryIntervalMs` 等實際值。

### 6.2 程式位置

| 功能 | 檔案 |
|------|------|
| 預設值與正規化 | `wax_rutime_proxy/config/config.go` |
| EH 輪詢 | `wax_rutime_proxy/service/EHAlarmPolling.go` |
| X 輪詢 | `wax_rutime_proxy/service/XAlarmPolling.go` |
| 設定檔範例 | `wax_rutime_proxy/config.yaml` |

### 6.3 驗收

1. `config.yaml` 未設定 EH/X 時，啟動 log 顯示 1000ms。
2. 設定 `-1` 或 `0` 時，仍使用 1000ms。
3. 設定低於 100ms 時，實際使用 100ms。
4. `time.Sleep` 不再收到 `-1ms` 或 `0ms`。

## 7. R-009 Record 訂閱生命週期

### 7.1 設計

- `config.go` 新增設定（皆可由 `config.yaml` 覆寫）：
  - `recorddata_idle_timeout_seconds`：預設 `1800` 秒，`0` 表示停用閒置 TTL。
  - `recorddata_max_registers`：全域最大 record 數，預設 `256`，`0` 表示不限。
  - `recorddata_max_subscriptions_per_client`：每 client 最大訂閱數，預設 `256`，`0` 表示不限。
  - `recorddata_reap_interval_seconds`：回收與統計週期，預設 `60` 秒，最低 `10` 秒。
- `RecordDataPolling.Register` 在持鎖狀態下檢查全域與每 client 上限；同 record 重新註冊不重複計數。
- `LastActivity` 由 client 主動訊息更新：`register`、`prevpage`、`nextpage`、`gotopage`、`update`、`updatevalue`。伺服器每秒推送不刷新 TTL。
- Reaper 每 `recorddata_reap_interval_seconds` 執行一次：
  - 移除沒有訂閱者的 record。
  - 移除超過閒置 TTL 的 record。
  - 移除初始化超過 5 分鐘的 record。
  - 清除已斷線 client，釋放 manager，並對仍在線的受影響 client 發送
    `{topic:"recorddata", action:"query", error:-1, reason:"subscription expired ..."}`。
- 新增 `Stats()` 與週期 klog，輸出 `records`、`subscriptions`、`clients`、`goroutines` 與目前上限。
- 併發保護：
  - `CreateInfo` 以 per-record mutex 序列化。
  - 初始化計數使用 atomic CAS；初始化期間 reaper 不回收。
  - 全域上限在插入新 record 的同一把鎖內檢查。
  - 註冊被取消或 client 已斷線時回傳錯誤，controller 不會誤回成功。
- `SoClient.chanClosed` 改為 `atomic.Bool`，新增 `IsClosed()` 供 polling 判斷。

### 7.2 程式位置

| 功能 | 檔案 |
|------|------|
| 設定與預設值 | `wax_rutime_proxy/config/config.go`、`config.yaml` |
| TTL、上限、reaper、統計 | `wax_rutime_proxy/service/RecordDataPolling.go` |
| Client 關閉狀態 | `wax_rutime_proxy/service/webSocket.go` |
| WS register 錯誤回傳 | `wax_rutime_proxy/controller/websocket/RecordDataController.go` |

### 7.3 驗收

1. 同一 record 多 client 共用一個 `RecordRegister`；最後一個 client 取消或閒置逾時後移除。
2. 超過 `recorddata_max_registers` 或 `recorddata_max_subscriptions_per_client` 時，register 回傳錯誤。
3. 閒置超過 TTL 的 client 收到 expiry 訊息，之後不再收到該 record 的推送。
4. client 斷線後不需要依賴前端 `unregister`，reaper 會清除殘留訂閱。
5. 每分鐘 log 可看到 `[recorddata] stats ...`。
6. `recorddata_idle_timeout_seconds: 0` 時不因閒置回收。

## 8. R-018 預設 DB 連線字串/密碼

### 8.1 設計

- 各服務不再內建 `Postgre@sql1`；預設 DSN 的 `password=` 為空，未取得環境變數時連線失敗（fail-closed）。
- 連線來源優先序：
  1. `ENSAAS_SERVICES`（設定 DB）
  2. `LOG_ENV`（action log DB）
  3. `VCAP_SERVICES`（私有雲）
- Windows 單機部署以 `edgeview-little-tool/RegisterDBEnv.reg` 寫入
  `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Environment`。
- Go 服務以 `util.GetEnv()` 先讀 process env，再 fallback 至 Windows machine registry；
  OPC UA Agent 的 `ReadEnv()` 同樣支援 Process → Machine → User。
- 服務 log 中的 DSN 一律經 `maskDSN()` / `MaskSecrets()` 遮蔽密碼。

### 8.2 程式位置

| 功能 | 檔案 |
|------|------|
| 環境變數解析 | `wax_metadata-service/service/app.go`、`wax_nginx_ingress/service/app.go`、`wax_rutime_proxy/service/app.go`、`wax_opcua_alarm_agent/service/app.go`、`wax_hmi_log/config/config.go` |
| OPC UA Agent | `wax_opcua_agent/wax_opcua_agent/Program.cs` |
| Windows machine env | `*/util/env_windows.go`、`*/util/env_other.go` |
| 環境變數註冊 | `edgeview-little-tool/RegisterDBEnv.reg` |
| ResetPassword | `wax_metadata-service/_tool/ResetPassword/ResetPassword.go`、`edgeview-little-tool/ResetPassword/ResetPassword.go` |

### 8.3 驗收

1. 原始碼掃描無 `Postgre@sql1` / `waxpgP@ssw0rd`。
2. 未設定 `ENSAAS_SERVICES` / `LOG_ENV` 時，服務連線失敗並記錄可辨識錯誤。
3. 合併 `RegisterDBEnv.reg` 後，服務可取得 machine env（或重啟後生效）。
4. 服務啟動 log 只輸出遮罩後的 DSN。

## 9. R-019 動態 SQL 組裝

### 9.1 設計

- 識別字（table / column / alias / order / schema）集中轉換與跳脫：
  - Metadata：`model/*` 查詢前驗證欄位/排序，SQL 識別字以 `quoteIdent` / `pq.QuoteIdentifier` 處理。
  - Runtime Proxy：`service/databaseService.go` 以 `ConvertDBIdentifier()` 依 driver 跳脫 table/column/alias/order；`UpdateValue` 先驗證更新欄位存在。
- 資料值一律使用參數化查詢（`$1` / `?`），不拼接使用者輸入。
- 動態排序、欄位選擇與 update 欄位需存在於允許清單。

### 9.2 程式位置

| 功能 | 檔案 |
|------|------|
| 識別字跳脫與查詢組裝 | `wax_rutime_proxy/service/databaseService.go` |
| UpdateValue 欄位驗證 | `wax_rutime_proxy/service/databaseService.go` |
| Metadata 查詢/排序驗證 | `wax_metadata-service/model/device/ObjectPathModel.go`、`RecordModel.go`、`model/log/ActionLogModel.go`、`model/opcua/TagModel.go` |
| Session/schema 識別字 | `*/util/sqlident.go`、`service/session_secret.go` |

### 9.3 驗收

1. 以惡意 `table`、`column`、`order`、`alias` 呼叫查詢/更新 API，不會改變 SQL 結構或回傳 DB 錯誤。
2. 合法查詢、排序、UpdateValue 功能回歸通過。
3. SAST/程式碼審查不再出現未跳脫的使用者輸入拼接。

## 10. R-028 OPC UA 預設 DB 帳密

### 10.1 設計

- OPC UA Data Agent 移除 `Program.cs` 的 `Password=Postgre@sql1` 預設值。
- Alarm Agent 的 `ENSAAS_SERVICES` / `VCAP_SERVICES` 僅提供 server credentials；
  runtime DB 固定 `waxalarm`，metadata DB 使用 `metadata.dbname`（預設 `waxconfig`）。
- Windows 以 `RegisterDBEnv.reg` 註冊 `ENSAAS_SERVICES` / `LOG_ENV`；部署時應替換為正式 Secret。

### 10.2 程式位置

| 功能 | 檔案 |
|------|------|
| OPC UA Data Agent | `wax_opcua_agent/wax_opcua_agent/Program.cs` |
| Alarm Agent DB | `wax_opcua_alarm_agent/service/app.go` |
| 環境變數註冊 | `edgeview-little-tool/RegisterDBEnv.reg` |

### 10.3 驗收

1. OPC UA Agent 未設定 `ENSAAS_SERVICES` 時不具預設密碼，連線失敗。
2. 設定環境變數後，Data Agent 連 `waxconfig`、Alarm Agent 連 `waxalarm` / `waxconfig`。
3. `RegisterDBEnv.reg` 的 JSON 可被 `tryGetConnEnv` 正確解析。

## 11. R-004 密碼雜湊（Argon2id）

### 11.1 設計

- 演算法：Argon2id，`m=19456` KiB、`t=2`、`p=1`，每帳號隨機 16-byte salt、32-byte key。
- 輸出：PHC `$argon2id$v=19$m=19456,t=2,p=1$<salt>$<hash>`（約 97 字元，長於舊 64-hex）。
- Pepper：`HKDF-SHA256(secret, info="edgeview/password-pepper/v1")`，`secret` 為 `SESSION_SECRET`，否則 `aes_secret`；Argon2 輸入為 `HMAC-SHA256(pepper, password)`。
- 相容：舊 64-hex SHA-256 以 constant-time 比對，命中後於登入成功時改寫為 Argon2id（UPDATE 帶入舊值作為 CAS 條件，避免覆蓋併發改密碼）。
- 空白密碼不受理；`userinfo` 空密碼以空字串儲存。

### 11.2 程式位置

| 服務 | 共用實作 | 驗證/寫入 |
|------|----------|-----------|
| Metadata | `security/password.go` | `model/device/UserModel.go`、`model/opcua/UserModel.go` |
| xnginx | `security/password.go` | `model/UserModel.go` |
| Runtime Proxy | `security/password.go` | `service/basicAuth.go` |
| OPC UA Alarm Agent | `security/password.go` | `service/basicAuth.go` |

各服務於 `LoadSessionKeys()` 後以 `SessionKeys.PasswordPepper` 呼叫 `security.SetPepper()`，須在開始接請求前完成。

### 11.3 部署需求

- 四個服務必須使用相同的 `SESSION_SECRET`，或同一 `aes_secret` 與 `PG_SCHEMA`，否則 pepper 不同而互不通過。
- 輪替/刪除 `aes_secret`、或變更 `SESSION_SECRET` 會使已升級 Argon2id 全部失效；輪替前先重設使用者密碼。舊 SHA-256 不受影響。
- `SESSION_SECRET_PREVIOUS` 僅用於 cookie 解碼，不參與密碼 pepper。
- 工廠 `admin` 種子與 `ResetPassword` 工具仍寫 legacy SHA-256；登入後自動升級。

### 11.4 驗收

1. 舊 `admin` 登入成功，`UserAdmin.password` 由 64-hex 變為 `$argon2id$…`。
2. 舊一般使用者登入成功並升級；新建立/修改的密碼直接為 Argon2id。
3. 同一帳號在 metadata、xnginx、runtime proxy、alarm agent 的驗證結果一致。
4. `go test ./security/` 通過（新/舊格式、錯誤密碼、pepper、惡意參數）。

## 12. 部署需求

| 項目 | 需求 |
|------|------|
| `SESSION_SECRET` | 至少 32 bytes；建議由 K8s Secret 或安裝程式產生 |
| `SESSION_SECRET_PREVIOUS` | 選用；輪替期間使用 |
| `ENSAAS_SERVICES` | 設定 DB 連線 JSON；Windows 可用 `RegisterDBEnv.reg` 註冊 |
| `LOG_ENV` | action log DB 連線 JSON |
| `VCAP_SERVICES` | 私有雲 DB 連線來源（`ENSAAS_SERVICES` 未提供時） |
| `PG_SCHEMA` | 使用 `aes_secret` 時，所有共用服務需一致 |
| `aes_secret` | 由 KeyMigrator 建立；K8s 若未執行 KeyMigrator，必須提供 `SESSION_SECRET` |
| KeyMigrator | on-prem 由 xnginx 在啟動服務前執行 |
| RecordData TTL | 預設 1800 秒；被動看板需在期限內重送 `register` 當 refresh，或設 `recorddata_idle_timeout_seconds: 0` 停用 |

K8s 環境若未注入 `SESSION_SECRET` 且資料庫沒有 `aes_secret`，服務會依設計啟動失敗，這是 R-001 的 fail-closed 行為。

## 13. 驗收

1. 原始碼搜尋不得再出現固定 session secret 字串。
2. 各服務 `go build ./...` 成功。
3. 啟動 log 顯示 key 來源與 fingerprint，且共用 session 的服務 fingerprint 一致。
4. 未設定 `SESSION_SECRET` 且無 `aes_secret` 時，服務啟動失敗並給出明確訊息。
5. `POST /record/queryrecord` 回 404；Swagger 不再列出該路徑。
6. 設定 `SESSION_SECRET_PREVIOUS` 時，舊 cookie 在期限內仍可解碼。
7. 未登入 WS 送非 `login` topic 回 `-100`，且不觸發 `default` 廣播。
8. 未登入不進入 systeminfo polling；登入後才收到 systeminfo。
9. EH/X 未設定、`-1` 或 `0` 時，實際 interval 為 1000ms。
10. EH/X 設定低於 100ms 時，實際 interval 為 100ms。
11. Record 閒置超過 TTL 後不再推送，並收到 `error=-1` 的 expiry 訊息。
12. 超過 record 或 per-client 上限時，register 回傳錯誤。
13. 啟動後每分鐘可看到 `[recorddata] stats ...`。
14. 將 `recorddata_idle_timeout_seconds` 設為 `0` 時，閒置 record 不會被 TTL 回收。
15. WS login 只驗證 handshake `graphicSession` 綁定的 Session，不再掃描全部 `http_sessions`。
16. 以其他 Session 的 `csrfToken` 登入回 `-100`；缺少／過期 cookie 亦回 `-100`。
17. 原始碼不得再出現 `Postgre@sql1` / `waxpgP@ssw0rd`；未設定 DB 環境變數時服務連線失敗。
18. 設定 DB 環境變數後，Metadata/Runtime/xnginx/OPC UA Agent/Alarm Agent/hmi_log 均可連線；Alarm runtime 使用 `waxalarm`、metadata 使用 `waxconfig`。
19. 以惡意 table/column/order/alias 呼叫查詢/更新 API，不會改變 SQL 結構或造成 SQL injection。

## 14. 後續追蹤

| 編號 | 項目 | 說明 |
|------|------|------|
| R-002-R | 個別 client `InsecureSkipVerify` | 逐步改為系統 CA/指定 CA |
| R-001-R | 機器金鑰輪替 | 需設計 `aes_secret` 輪替與資料重新加密流程 |
| R-003-R | Record 查詢復用 | 若恢復功能，需補認證、授權與速率限制 |
| R-009-R | SharedDB pool 縮減 | `SQLManager.Close()` 不關閉 process-wide pool；若需隨訂閱數縮減連線池，需另行設計 |
