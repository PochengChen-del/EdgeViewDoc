# SRS-06 驗收準則與需求追溯（Acceptance & Traceability）

## 1. 驗收原則

1. 每條 **Must** 需求至少對應一個可執行測試案例。
2. 驗收需在指定的平台與環境組合完成（見 §3）。
3. 效能與安全需求需有量化證據（報告、日誌、掃描結果）。
4. 需求若因現況限制無法完全達成，需在 `SDS/11_Design_Decisions_and_Risks.md` 記錄並由產品核可。

## 2. 驗收階層

| 階層 | 內容 | 負責 |
|------|------|------|
| 單元測試 | 後端 service/model、Client 核心邏輯 | 開發 |
| 整合測試 | REST/WS、資料庫、外部服務模擬 | 開發/測試 |
| 系統測試 | 端到端流程（登入→設計→Viewer） | 測試 |
| 非功能測試 | 效能、安全、可靠度、可攜 | 測試/資安/SRE |
| 使用者驗收（UAT） | 依角色與實務情境 | 產品/使用者 |

## 3. 驗收環境矩陣

| 維度 | 組合 |
|------|------|
| Client 平台 | Windows 11、Ubuntu LTS、macOS、Android、iOS、Chromium（WASM） |
| Server | Linux 容器（K8s）、Windows 執行檔 |
| 資料庫 | PostgreSQL 12+、SQL Server、MySQL、Oracle、InfluxDB v1/v2 |
| 身分來源 | 本機/OPC UA、WebAccess、EdgeHub/Edge365、X |
| 網路 | 區網、延遲/斷線模擬、HTTPS |

## 4. 功能驗收準則（摘要）

| 需求群組 | 驗收準則 | 對應測試類型 |
|----------|----------|--------------|
| FR-CLI-001~020 啟動/登入 | 可完成租戶→連線→登入→專案；錯誤有提示；Session 逾時可恢復 | 系統/UAT |
| FR-CLI-021~045 專案/畫面 | 可建立、儲存、版本、匯入匯出；Grid/RWD 正確 | 系統/UAT |
| FR-CLI-046~075 Widget/綁定/腳本 | Widget 可建立與綁定；即時更新；腳本觸發與錯誤隔離；切換畫面無洩漏 | 系統/效能 |
| FR-CLI-076~095 設定/維運 | 使用者/群組/專案/配方/日誌/OPC UA/匯入匯出可操作 | 系統/UAT |
| FR-META-001~012 啟動/認證/物件樹 | 服務啟動、登入、CSRF、時間戳、物件樹 CRUD 正確 | 整合/API |
| FR-META-013~035 內容/裝置/OPC UA | Display/JS/Template/Variable/DataModel/Record/Recipe/Alarm/User/OPC UA CRUD 正確 | 整合/API |
| FR-META-036~045 設定/檔案/匯入匯出/稽核 | 設定可讀寫；檔案可上傳下載；v1/v2 匯入匯出正確；稽核可查 | 整合/系統 |
| FR-RT-001~009 查詢 | 各資料源可查詢；分頁/ schema/ datalog/ 指令正確；資料量限制生效 | 整合/API |
| FR-RT-010~017 即時 | WS 登入、訂閱、推送、取消、updatevalue 正確；逾時斷線 | 整合/系統 |
| FR-RT-018~025 代理/通知/關閉 | 代理轉發、FCM、統一錯誤、優雅關閉 | 整合/維運 |
| FR-OPC-001~012 設定 UI | 登入/權限、專案、連線、Tag、節點瀏覽、警報、使用者、匯入匯出 | 系統/UAT |
| FR-OPC-013~027 Data Agent | Runtime/History 角色、即時/歷史/寫值/瀏覽/憑證、Alarm 欄位 | 整合/API/效能 |
| FR-OPC-028~045 Alarm Agent | 規則、優先級、雙狀態、Ack/Process、清單/歷史、重啟復原 | 整合/系統 |
| 直連資料源 | Client 經 xnginx `/ua`、`/uadb`、`/uaam` 取得即時/歷史/警報；Proxy 不承載 OPC UA Tag 值 | 端到端/延遲量測 |
| xnginx 入口 | `8880/8881` 路由、TLS、WS、限流、四種 datasource 變體 | 整合/維運 |
| FR-UM-001~020 安裝/授權/連線 | 依手冊完成安裝、試用/正式授權、桌面/瀏覽器/APP 連線 | 系統/UAT |
| FR-UM-021~034 繪圖/控件/綁定 | 六大區塊、樣板、圖層、預覽、控件目錄、綁定語法、圖表限制 | 系統/UAT |
| FR-UM-035~046 變數/腳本/事件/動畫 | 變數型別/限制、腳本函數、事件參數、動畫組合 | 系統/UAT |
| FR-UM-047~064 模型/配置/權限/插件 | 模型/實例化、檔案、工程配置、角色、插件 | 系統/UAT |

## 5. 非功能驗收準則

| 需求 | 驗收方法 | 通過條件 |
|------|----------|----------|
| NFR-PERF-001/002/009 | 效能測試 | 符合章節目標值 |
| NFR-PERF-006 | API 測試 | 1–1000 以外被拒 |
| NFR-SEC-001~010 | 滲透/組態檢查 | 無高風險未修補項；CSRF/時間戳/Session 測試通過；DB 預設密碼與動態 SQL 已修（R-018/R-019/R-028） |
| NFR-REL-003/004 | 故障注入 | 斷線可重連；終止時正常關閉 |
| NFR-AUD-001~005 | 稽核測試 | 事件可追溯、checksum 可驗證、可匯出 |
| NFR-I18N-001~006 | 語系/時區測試 | 切換與格式正確 |
| NFR-PORT-001/002 | 建置/煙霧測試 | 各平台可啟動並完成登入 |
| NFR-OPC-001~004 | 效能測試 | 即時/批次/歷史/評估符合目標 |
| NFR-OPC-005/006 | 故障注入 | 重啟 30 秒恢復；斷線回 Bad Quality |
| NFR-OPC-007/008 | 安全測試 | 未認證被拒；TLS/OPC UA 加密可用 |
| NFR-UM-001~008 | 效能/相容/UAT | 更新率、版本相容、i18n、權限、備份 |
| NFR-SEC-004/015 | xnginx/TLS/限流 | 憑證、429、WS Upgrade |

## 6. 需求追溯原則

- **正向追溯**：SRS 需求 → SDS 設計章節 → 程式碼位置 → 測試案例。
- **反向追溯**：程式碼模組 → SDS → SRS（確認無孤立功能）。
- 完整對照表見 `SDS/12_Traceability.md`；OPC UA 子系統見 `SRS/07` 與 `SDS/14`；使用手冊功能見 `SRS/08` 與 `SDS/15`。
- 需求 ID 一旦發佈不得重用；刪除需求需標記 `Deprecated` 並保留歷史。

## 7. 需求變更流程

1. 提出變更（CR），說明理由、影響範圍與優先級。
2. 更新 SRS 對應條目與版本。
3. 更新 SDS 設計與追溯表。
4. 更新測試案例並執行回歸。
5. 由產品/技術負責人核可後納入版本。

## 8. 已知待確認事項（Open Items）

| 編號 | 議題 | 影響需求 | 建議 |
|------|------|----------|------|
| OI-001 | EdgeHub/X 告警輪詢間隔未初始化（`-1`） | FR-RT-013、FR-RT-015、NFR-PERF-004 | 已修：預設 1000 ms，`<=0` 用預設、低於 100 ms 夾到 100 ms |
| OI-002 | MQTT Client 停用但仍有設定/UI | FR-CLI-091 | 明確標示停用或移除 |
| OI-003 | ODBC 未實作 | IF-SW-006 | 文件標示不支援，避免誤用 |
| OI-004 | 密碼雜湊未加鹽 | NFR-SEC-003 | 規劃遷移至 Argon2/bcrypt |
| OI-005 | 部分內部 HTTPS 呼叫停用憑證驗證 | NFR-SEC-004 | 正式環境改為驗證或指定 CA |
| OI-006 | WebSocket `systeminfo`/`default` 認證不足 | NFR-SEC-009 | 已修（R-006/R-007）：非 `login` topic 需登入；systeminfo 登入後才訂閱；WS 登入綁定 `graphicSession`，只比對該 Session 的 `csrfToken` |
| OI-007 | Record 快取無明確閒置 TTL | NFR-PERF/REL | 依設計註解實作 10 分鐘回收或調整文件 |
| OI-008 | Data Agent `packetTimestamp` 未強制 | NFR-OPC-007、IF-OPC-002 | 修正中介層短路；補時間戳測試 |
| OI-009 | Data Agent CORS 允許任意 Origin + Credentials | NFR-OPC-007 | 白名單化並限制憑證 |
| OI-010 | OPC UA 憑證自動接受不受信任 | NFR-OPC-008 | 正式環境改為信任清單 |
| OI-011 | Alarm 預設埠已統一為 28889 | FR-OPC-028、SDS-14 §11 | 已統一預設與部署文件 |
| OI-012 | Root path 直連參數由 Metadata `config.yaml` 提供（非 `DataSourceBody`） | FR-OPC-015/018、SDS-14 §8.3 | 設定 `opcua.agent.dbport`／`opcua.alarm.port` 或改用 `/uadb`、`/uaam` |
| OI-013 | OPC UA 設定 UI 與 Client 的 API 權限邊界 | FR-OPC-001、NFR-OPC-007 | 補權限測試與 CORS 驗證 |
| OI-014 | 手冊 25 ms 更新率與外部資料掃描最低 500 ms 的差異 | FR-UM-025/026、NFR-UM-001 | 文件區分繪製/資料週期 |
| OI-015 | 手冊插件/主題色/APP 查詢限制 | FR-UM-061/064 | 補來源與平台限制測試 |
| OI-016 | 手冊模型密碼/匯入刪除保護 | FR-UM-047/048 | 補密碼與錯誤測試 |
| OI-017 | xnginx 四個 datasource 變體設定漂移 | IF-SW-012、IF-OPC-011 | 已接受（R-035）：正式部署固定 `Proxy\xnginx.yaml` 單一設定與 `SCServer\config.yaml`／`appsettings.json`，變體僅供開發/建置；保留基準比對/啟動煙霧測試 |
| OI-018 | 地端 xnginx 與 EdgeHub K8s Ingress 路徑/認證差異 | IF-COM、NFR-OPC-007 | 分流測試（WebAccess/OPC UA/X vs EdgeHub） |
| OI-008 | DataSource source type 編號不一致 | IF-SW-002/004 | 統一常數並加轉換層 |
| OI-009 | 非 OPC UA 登入鎖定與 K8s 限流未完整 | NFR-SEC-015 | nginx 已有 `limit_req`；補登入專用門檻、帳號鎖定、WS 訊息限流與 K8s ingress annotation |
| OI-019 | 預設 DB 連線字串/密碼 | NFR-SEC-007、NFR-SEC-013 | 已修（R-018/R-028）：移除預設密碼，改用環境變數/Secret；Windows 以 `RegisterDBEnv.reg` 註冊 |
| OI-020 | 動態 SQL 組裝 | NFR-SEC-001、NFR-SEC-007 | 已修（R-019）：識別字跳脫/白名單、值參數化、UpdateValue 欄位驗證 |
