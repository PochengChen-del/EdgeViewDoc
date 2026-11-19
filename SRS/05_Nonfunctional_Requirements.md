# SRS-05 非功能需求（Non-Functional Requirements）

本章定義 EdgeView 的品質屬性需求。數值若來自現行 `config.yaml` 或程式常數，於描述中標明，作為設計與測試基準。

---

## 5.1 效能（Performance）

| ID | 需求 | 優先級 | 目標值/說明 |
|----|------|--------|-------------|
| NFR-PERF-001 | 畫面載入時間 | Must | 一般畫面（≤200 Widget）於區網內 3 秒內完成首次顯示；縮圖/資源採非同步載入。 |
| NFR-PERF-002 | Viewer 即時更新 | Must | Record 輪詢週期 1 秒；Tag 批次更新間隔 33 ms；UI 不因資料更新阻塞。 |
| NFR-PERF-003 | WebSocket 延遲 | Should | 資料變更至 Client 顯示的端到端延遲 ≤2 秒（正常網路）。 |
| NFR-PERF-004 | 告警摘要更新 | Should | WA/EH/X/OPC UA 告警摘要預設輪詢 1 秒；可由設定調整，低於 100 ms 會夾到 100 ms。 |
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
| NFR-SEC-015 | 速率限制與登入鎖定 | Should | 登入、查詢與 WebSocket 需有速率限制；非 OPC UA 帳號需有失敗鎖定或等效控制。nginx `limit_req` 可作為第一層，但門檻需能有效阻擋暴力破解。 |

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

## 5.11 OPC UA 子系統非功能需求

| ID | 類別 | 需求 | 優先級 | 驗證 |
|----|------|------|--------|------|
| NFR-OPC-001 | 效能 | 100 Tags 即時讀取 ≤ 1 秒（區網）。 | Must | 壓測 |
| NFR-OPC-002 | 效能 | 1000 Tags 批次讀取 ≤ 5 秒。 | Must | 壓測 |
| NFR-OPC-003 | 效能 | 歷史查詢 60 點 × 10 Tags ≤ 3 秒（依 Server）。 | Should | 壓測 |
| NFR-OPC-004 | 效能 | Alarm 評估延遲 ≤ 2 × 輪詢間隔。 | Must | 量測 |
| NFR-OPC-005 | 可靠 | Agent/Alarm 重啟後 30 秒內恢復。 | Must | 故障演練 |
| NFR-OPC-006 | 可靠 | OPC UA Server 斷線回 Bad Quality，恢復後重連。 | Must | 斷線測試 |
| NFR-OPC-007 | 安全 | 外部請求需 Session/Bearer；Bearer 僅 loopback。 | Must | 滲透 |
| NFR-OPC-008 | 安全 | 支援 HTTPS/PFX、OPC UA Sign/SignAndEncrypt。 | Should | 組態檢查 |
| NFR-OPC-009 | 稽核 | 登入、設定變更、Ack/Process 可追溯。 | Must | 日誌 |
| NFR-OPC-010 | i18n | UI 支援 en/tw/zh。 | Should | UAT |
| NFR-OPC-011 | 擴充 | 多專案/連線/Tag；History 可水平擴充。 | Should | 容量 |
| NFR-OPC-012 | 可觀測 | 結構化日誌、健康端點、錯誤碼。 | Should | 運維 |
| NFR-OPC-013 | 相容 | OPC UA 1.04+、常見 DataType。 | Must | 互通 |
| NFR-OPC-014 | 時間 | NTP；歷史時間戳標示 UTC/本地。 | Must | 時區 |
| NFR-OPC-015 | 資源 | Runtime 256–512 MB；History 依快取調整。 | Should | 監控 |
| NFR-OPC-016 | 備份 | 設定/警報 DB 可備份還原。 | Must | 還原 |
| NFR-OPC-017 | 可維護 | 分層、可測試、設定外部化。 | Should | Review |
| NFR-OPC-018 | 可測試 | 模擬值與 Swagger。 | Should | 測試 |
| NFR-OPC-019 | 易用 | 表單驗證、錯誤提示、載入狀態。 | Should | UAT |
| NFR-OPC-020 | 文件 | API/設定/部署/限制文件化。 | Must | 審查 |

---

## 5.12 使用手冊補充非功能需求

| ID | 類別 | 需求 | 優先級 | 驗證 |
|----|------|------|--------|------|
| NFR-UM-001 | 效能 | 圖紙基本更新 25/50/100 ms；大量控件可非同步顯示。 | Must | 效能測試 |
| NFR-UM-002 | 相容 | Client/Server 主次版相同；瀏覽器 WebGL 2.0。 | Must | 相容測試 |
| NFR-UM-003 | 可用性 | 主選單/工具列/右鍵/快捷鍵一致；錯誤可理解。 | Should | UAT |
| NFR-UM-004 | i18n | en/tw/zh/ja；字型可上傳；小數/千分位可選。 | Must | 語系測試 |
| NFR-UM-005 | 安全 | 角色/安全區/級別、憑證、HTTPS。 | Must | 權限/安全測試 |
| NFR-UM-006 | 可靠 | 版本移除不影響 PostgreSQL 專案資料；備份/還原。 | Must | 還原演練 |
| NFR-UM-007 | 可維護 | 插件可獨立部署；系統資源分類可管理。 | Should | 運維演練 |
| NFR-UM-008 | 可測試 | 預覽、偵錯工具、runDebug、LOG。 | Should | 測試 |

---

## 5.13 相容性與法規遵循

| ID | 需求 | 優先級 | 說明 |
|----|------|--------|------|
| NFR-CMP-001 | 瀏覽器 | Should | 支援主流 Chromium/Firefox/Safari 最新兩個版本（WASM 模式）。 |
| NFR-CMP-002 | 資料庫版本 | Should | PostgreSQL 12+；第三方 DB 依驅動支援版本。 |
| NFR-CMP-003 | 標準 | Should | REST/JSON、WebSocket RFC 6455、OPC UA（透過 Agent）。 |
| NFR-CMP-004 | 授權 | Must | 遵守 Qt、Go 套件、字型與第三方元件授權；維護 SBOM。 |

---

## 5.14 非功能需求驗證方式

| 類別 | 驗證方式 |
|------|----------|
| 效能 | 負載測試、長時間 soak test、前端效能剖析 |
| 安全 | 弱點掃描、滲透測試、Session/CSRF 測試、TLS 組態檢查 |
| 可靠度 | 斷線重連、外部服務失效、優雅關閉測試 |
| 可維護性 | 程式碼審查、API 契約測試、文件檢查 |
| 可攜性 | 各平台建置與煙霧測試 |
| i18n | 語系切換、字型/時區/數字格式測試 |
| 稽核 | 稽核軌跡完整性與 checksum 驗證 |
| OPC UA | 互通測試（1.04+）、大量 Tag 壓測、歷史查詢、Alarm 狀態機、重啟復原 |
| 使用手冊 | 安裝/授權、桌面/瀏覽器/APP 連線、繪圖/控件/腳本/事件/動畫/模型/插件 UAT |
