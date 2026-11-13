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
| 身分來源 | 本機/OPC UA、WebAccess、BI/Edge365、X |
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

## 5. 非功能驗收準則

| 需求 | 驗收方法 | 通過條件 |
|------|----------|----------|
| NFR-PERF-001/002/009 | 效能測試 | 符合章節目標值 |
| NFR-PERF-006 | API 測試 | 1–1000 以外被拒 |
| NFR-SEC-001~010 | 滲透/組態檢查 | 無高風險未修補項；CSRF/時間戳/Session 測試通過 |
| NFR-REL-003/004 | 故障注入 | 斷線可重連；終止時正常關閉 |
| NFR-AUD-001~005 | 稽核測試 | 事件可追溯、checksum 可驗證、可匯出 |
| NFR-I18N-001~006 | 語系/時區測試 | 切換與格式正確 |
| NFR-PORT-001/002 | 建置/煙霧測試 | 各平台可啟動並完成登入 |

## 6. 需求追溯原則

- **正向追溯**：SRS 需求 → SDS 設計章節 → 程式碼位置 → 測試案例。
- **反向追溯**：程式碼模組 → SDS → SRS（確認無孤立功能）。
- 完整對照表見 `SDS/12_Traceability.md`。
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
| OI-006 | WebSocket `systeminfo`/`default` 認證不足 | NFR-SEC-009 | 已修（R-006）：非 `login` topic 需登入；systeminfo 登入後才訂閱 |
| OI-007 | Record 快取無明確閒置 TTL | NFR-PERF/REL | 依設計註解實作 10 分鐘回收或調整文件 |
| OI-008 | DataSource source type 編號不一致 | IF-SW-002/004 | 統一常數並加轉換層 |
| OI-009 | 非 OPC UA 登入鎖定與 K8s 限流未完整 | NFR-SEC-015 | nginx 已有 `limit_req`；補登入專用門檻、帳號鎖定、WS 訊息限流與 K8s ingress annotation |
