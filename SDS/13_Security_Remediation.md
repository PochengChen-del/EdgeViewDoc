# SDS-13 安全修復紀錄

本章記錄風險登記（SDS-11）中已完成修復項目的設計、部署需求與驗收方式。風險編號與 SDS-11 一致。

## 1. 修復狀態

| 風險 | 項目 | 狀態 | 摘要 |
|------|------|------|------|
| R-001 | Session secret 硬編碼 | Fixed | 改由 `SESSION_SECRET` 或 `aes_secret` 經 HKDF 派生；無硬編碼、無 legacy fallback |
| R-002 | 全域停用 TLS 憑證驗證 | Fixed | 移除 `http.DefaultTransport` 全域 `InsecureSkipVerify` |
| R-003 | `/record/queryrecord` 無認證 | Fixed | 路由停用，handler 不再標註 `@Router`，Swagger 目錄移除 |
| R-006 | WebSocket 部分 topic 未認證 | Fixed | 非 `login` topic 統一要求 `AlreadyLogin`；systeminfo 登入後才註冊 |

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

## 6. 部署需求

| 項目 | 需求 |
|------|------|
| `SESSION_SECRET` | 至少 32 bytes；建議由 K8s Secret 或安裝程式產生 |
| `SESSION_SECRET_PREVIOUS` | 選用；輪替期間使用 |
| `PG_SCHEMA` | 使用 `aes_secret` 時，所有共用服務需一致 |
| `aes_secret` | 由 KeyMigrator 建立；K8s 若未執行 KeyMigrator，必須提供 `SESSION_SECRET` |
| KeyMigrator | on-prem 由 xnginx 在啟動服務前執行 |

K8s 環境若未注入 `SESSION_SECRET` 且資料庫沒有 `aes_secret`，服務會依設計啟動失敗，這是 R-001 的 fail-closed 行為。

## 7. 驗收

1. 原始碼搜尋不得再出現固定 session secret 字串。
2. 各服務 `go build ./...` 成功。
3. 啟動 log 顯示 key 來源與 fingerprint，且共用 session 的服務 fingerprint 一致。
4. 未設定 `SESSION_SECRET` 且無 `aes_secret` 時，服務啟動失敗並給出明確訊息。
5. `POST /record/queryrecord` 回 404；Swagger 不再列出該路徑。
6. 設定 `SESSION_SECRET_PREVIOUS` 時，舊 cookie 在期限內仍可解碼。
7. 未登入 WS 送非 `login` topic 回 `-100`，且不觸發 `default` 廣播。
8. 未登入不進入 systeminfo polling；登入後才收到 systeminfo。

## 8. 後續追蹤

| 編號 | 項目 | 說明 |
|------|------|------|
| R-002-R | 個別 client `InsecureSkipVerify` | 逐步改為系統 CA/指定 CA |
| R-001-R | 機器金鑰輪替 | 需設計 `aes_secret` 輪替與資料重新加密流程 |
| R-003-R | Record 查詢復用 | 若恢復功能，需補認證、授權與速率限制 |
