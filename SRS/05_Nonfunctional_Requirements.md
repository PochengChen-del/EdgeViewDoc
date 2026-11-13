# SRS-05 非功能需求（Non-Functional Requirements）

本章定義 EdgeView 的品質屬性需求。數值若來自現行 `config.yaml` 或程式常數，於描述中標明，作為設計與測試基準。

---

## 5.1 效能（Performance）

| ID | 需求 | 優先級 | 目標值/說明 |
|----|------|--------|-------------|
| NFR-PERF-001 | 畫面載入時間 | Must | 一般畫面（≤200 Widget）於區網內 3 秒內完成首次顯示；縮圖/資源採非同步載入。 |
| NFR-PERF-002 | Viewer 即時更新 | Must | Record 輪詢週期 1 秒；Tag 批次更新間隔 33 ms；UI 不因資料更新阻塞。 |
| NFR-PERF-003 | WebSocket 延遲 | Should | 資料變更至 Client 顯示的端到端延遲 ≤2 秒（正常網路）。 |
| NFR-PERF-004 | 告警摘要更新 | Should | WA/OPC UA 告警摘要輪詢 1 秒；EH/X 依設定（預設應為 1 秒）。 |
| NFR-PERF-005 | 系統資訊 | Should | 每 3000 ms 更新一次。 |
| NFR-PERF-006 | 查詢資料量 | Must | 單次 Record 查詢 `max_data_size` 限制 1–1000 筆。 |
| NFR-PERF-007 | HTTP 併發 | Should | Client 最多 255 個並行 HTTP 連線、1024 佇列；Viewer 等待上限 10 秒。 |
| NFR-PERF-008 | API 回應時間 | Should | 一般設定 API（無外部查詢）P95 ≤500 ms；含第三方 DB 查詢依來源而定。 |
| NFR-PERF-009 | 啟動時間 | Should | Server 冷啟動至可服務 ≤30 秒（資料庫正常、資料量中等）。 |
| NFR-PERF-010 | 記憶體 | Should | Client 閒置記憶體 ≤500 MB（桌面）；切換畫面後不得持續成長。 |
| NFR-PERF-011 | 渲染 | Should | 2D 採批次繪製與管線快取；3D 可調 MSAA；低階裝置可降級。 |
| NFR-PERF-012 | 大量物件 | Could | 單一畫面支援 ≥1000 Widget（實際效能依裝置與綁定數量）。 |

---

## 5.2 安全（Security）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-SEC-001 | 認證 | Must | 所有非公開 API 需有效 Session 與 CSRF Token；公開端點需明確白名單。 |
| NFR-SEC-002 | 授權 | Must | 依專案與角色檢查權限；無權限回傳 `-101`。 |
| NFR-SEC-003 | 密碼儲存 | Must | 不得儲存明文密碼；現行使用 SHA-256，應逐步升級為加鹽雜湊（見 SDS-11）。 |
| NFR-SEC-004 | 傳輸安全 | Must | 支援 HTTPS；憑證可設定。全域停用 TLS 驗證已移除；個別外部呼叫仍停用者應逐步改為系統 CA/指定 CA（R-002-R）。 |
| NFR-SEC-005 | CSRF | Must | 狀態變更請求需驗證 `csrfToken`。 |
| NFR-SEC-006 | 防重放 | Must | 寫入操作需驗證 `packetTimestamp`（預設 300 秒）。 |
| NFR-SEC-007 | 敏感設定加密 | Must | 資料來源密碼以 AES-256-CBC 加密儲存；金鑰不得寫入版本庫。 |
| NFR-SEC-008 | Session 管理 | Must | Session 存於伺服器端；登出/逾時即失效；Session secret 不得硬編碼，需由 `SESSION_SECRET` 或 `aes_secret` 派生；Cookie 需 HttpOnly/Secure（依部署）。 |
| NFR-SEC-009 | WebSocket 認證 | Must | 連線後需於 10 秒內完成登入，否則斷線。 |
| NFR-SEC-010 | 上傳限制 | Must | 限制上傳大小與類型；避免路徑穿越。 |
| NFR-SEC-011 | 日誌安全 | Should | 日誌不得記錄密碼、Token、完整憑證。 |
| NFR-SEC-012 | 依賴弱點 | Should | 定期掃描 Go/NPM/Qt 依賴與 SBOM（參考 `D:\Document\EdgeView SBom`）。 |
| NFR-SEC-013 | 權限最小化 | Should | 服務以非 root 執行；資料庫帳號最小權限。 |
| NFR-SEC-014 | 腳本沙箱 | Should | Client 腳本不得任意存取檔案系統；僅能透過受控 API。 |

---

## 5.3 可靠度與可用性（Reliability & Availability）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-REL-001 | 服務可用性 | Should | 正常維運下月可用性 ≥99.5%。 |
| NFR-REL-002 | 錯誤隔離 | Must | 單一 API/查詢失敗不得導致整個服務崩潰；使用 panic 回復。 |
| NFR-REL-003 | WebSocket 重連 | Must | Client 斷線後自動重連並重新登入/訂閱。 |
| NFR-REL-004 | 優雅關閉 | Must | 收到終止訊號時關閉連線與輪詢，避免資料毀損。 |
| NFR-REL-005 | 資料一致性 | Must | Record 更新值需交易式處理；失敗回滾。 |
| NFR-REL-006 | 逾時控制 | Must | 外部呼叫需設定逾時，避免執行緒耗盡。 |
| NFR-REL-007 | 冪等性 | Should | 重複的建立/更新請求需具備可預期的結果（如 `on conflict`）。 |
| NFR-REL-008 | 當機回報 | Should | 桌面 Client 產生 Crashpad 報告；Server 記錄 stack trace。 |
| NFR-REL-009 | 備份 | Should | 設定資料庫需定期備份；匯出功能可作為專案備份。 |
| NFR-REL-010 | 降級 | Should | 外部平台不可用時，核心專案編輯/檢視仍可運作（除相依資料外）。 |

---

## 5.4 可擴充性與容量（Scalability）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-SCL-001 | 水平擴充 | Should | 服務可多副本部署（需處理 Session 共享與 WebSocket Sticky）。 |
| NFR-SCL-002 | 連線池 | Must | 資料庫連線池可設定；避免單一查詢佔用過多連線。 |
| NFR-SCL-003 | 併發控制 | Must | Metadata 設定操作上限 50、操作日誌 50（semaphore）。 |
| NFR-SCL-004 | 資料量 | Should | 支援多專案、多使用者；單專案物件數依 DB 容量調整。 |
| NFR-SCL-005 | 3D 資產 | Could | 大型 3D 套件採分塊/串流與快取。 |

---

## 5.5 可維護性（Maintainability）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-MNT-001 | 分層架構 | Must | 後端維持 controller/service/model/repository 分層。 |
| NFR-MNT-002 | 日誌 | Must | 使用 klog；可設定檔案輸出與輪替（預設 15 MB）。 |
| NFR-MNT-003 | 組態 | Must | 以 `config.yaml` 為主，支援環境變數/命令列覆寫。 |
| NFR-MNT-004 | API 文件 | Should | 以 Swagger/OpenAPI 產生並維護。 |
| NFR-MNT-005 | 文件 | Should | QML 型別與 API 遵循 QDoc；SRS/SDS 保持更新。 |
| NFR-MNT-006 | 測試 | Should | 關鍵流程具備單元/整合測試；API 契約測試。 |
| NFR-MNT-007 | 版本 | Must | 畫面/JS/範本/資料模型具版本欄位，支援還原。 |
| NFR-MNT-008 | 可觀測性 | Should | 提供健康檢查、系統狀態、連線數與錯誤統計。 |

---

## 5.6 可攜性（Portability）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-PORT-001 | Client 平台 | Must | Windows、Linux、macOS、Android、iOS、WebAssembly。 |
| NFR-PORT-002 | Server 平台 | Must | Linux 容器（主要）與 Windows 執行檔。 |
| NFR-PORT-003 | 資料庫 | Must | PostgreSQL 為設定資料庫；第三方資料源依支援清單。 |
| NFR-PORT-004 | 容器化 | Should | 提供 Dockerfile 與 K8s manifests。 |
| NFR-PORT-005 | 無外部相依 | Should | 核心功能不依賴特定雲端；Edge365 為選用。 |

---

## 5.7 可用性與使用者體驗（Usability）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-USE-001 | 一致性 | Must | 相同操作在不同頁面有一致行為與用語。 |
| NFR-USE-002 | 回饋 | Must | 長操作顯示進度；錯誤顯示原因與下一步。 |
| NFR-USE-003 | 復原 | Should | 破壞性操作可取消或復原。 |
| NFR-USE-004 | 鍵盤/觸控 | Should | 支援鍵盤快捷鍵與觸控手勢。 |
| NFR-USE-005 | 主題 | Should | Light/Dark/Gray 與專案主題。 |
| NFR-USE-006 | 協助 | Could | 提供 About、版本、通訊狀態與線上說明連結。 |

---

## 5.8 國際化與在地化（Internationalization）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-I18N-001 | 語系 | Must | 支援 eng/fra/jpn/kr/twn/zh；可擴充。 |
| NFR-I18N-002 | 執行期切換 | Should | 切換語系不需重啟。 |
| NFR-I18N-003 | 字型 | Should | 支援自訂字型上傳與下載；缺字回退。 |
| NFR-I18N-004 | 時區 | Must | 可依系統時區轉換查詢時間。 |
| NFR-I18N-005 | 數字格式 | Should | 可設定小數分隔符號。 |
| NFR-I18N-006 | 文字編碼 | Must | 全面使用 UTF-8；匯入匯出保留非 ASCII。 |

---

## 5.9 稽核與法規（Audit & Compliance）

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-AUD-001 | 稽核範圍 | Must | 登入、使用者/權限異動、專案/物件異動、匯入匯出、設定變更。 |
| NFR-AUD-002 | 稽核內容 | Must | 時間、事件 ID、專案、類別、動作、使用者、來源、目標、變更、狀態、原因、checksum。 |
| NFR-AUD-003 | 完整性 | Must | 稽核記錄含 SHA3-256 checksum，可驗證未被竄改。 |
| NFR-AUD-004 | 保留 | Should | 可設定保留期限與自動清理。 |
| NFR-AUD-005 | 匯出 | Should | 稽核可匯出供外部查核。 |
| NFR-AUD-006 | 操作日誌 | Should | 記錄 Tag 寫入與使用者操作。 |

---

## 5.10 隱私與資料保護

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-PRV-001 | 個資最小化 | Should | 僅蒐集必要使用者資訊（名稱、角色、來源 ID）。 |
| NFR-PRV-002 | 傳輸 | Must | 敏感資料傳輸使用 HTTPS。 |
| NFR-PRV-003 | 儲存 | Must | 密碼/資料源密碼加密或雜湊；不得記錄於日誌。 |
| NFR-PRV-004 | 裝置 Token | Should | FCM Token 僅用於推播，移除裝置時刪除。 |

---

## 5.11 相容性與法規遵循

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-CMP-001 | 瀏覽器 | Should | 支援主流 Chromium/Firefox/Safari 最新兩個版本（WASM 模式）。 |
| NFR-CMP-002 | 資料庫版本 | Should | PostgreSQL 12+；第三方 DB 依驅動支援版本。 |
| NFR-CMP-003 | 標準 | Should | REST/JSON、WebSocket RFC 6455、OPC UA（透過 Agent）。 |
| NFR-CMP-004 | 授權 | Must | 遵守 Qt、Go 套件、字型與第三方元件授權；維護 SBOM。 |

---

## 5.12 非功能需求驗證方式

| 類別 | 驗證方式 |
|------|----------|
| 效能 | 負載測試、長時間 soak test、前端效能剖析 |
| 安全 | 弱點掃描、滲透測試、Session/CSRF 測試、TLS 組態檢查 |
| 可靠度 | 斷線重連、外部服務失效、優雅關閉測試 |
| 可維護性 | 程式碼審查、API 契約測試、文件檢查 |
| 可攜性 | 各平台建置與煙霧測試 |
| i18n | 語系切換、字型/時區/數字格式測試 |
| 稽核 | 稽核軌跡完整性與 checksum 驗證 |
