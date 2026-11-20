## 附錄 A：部分符合項目明細（39 項）

> 本附錄逐項說明「部分符合」與「部分符合（HTTPS）」項目的現況、缺口與建議。關聯需求與設計章節以連結標示；風險編號對應 `SDS/11_Design_Decisions_and_Risks.md`。

| 群組 | 項目 | 數量 |
|------|------|------|
| Common Component | CCSC 1、CCSC 2、CCSC 4 | 3 |
| FR1 識別與認證 | CR 1.02、1.02 RE(1)、1.05、1.07、1.10 | 5 |
| FR2 使用控制 | CR 2.01 RE(1)、2.01 RE(3)、2.03、2.04、SAR 2.04、SAR 2.04 RE(1)、2.05、2.09、2.09 RE(1)、2.10、2.11 RE(1)、2.12、2.12 RE(1) | 13 |
| FR3 系統完整性 | CR 3.02、SAR 3.02、3.03、3.04、3.04 RE(1)、3.04 RE(2)、3.09、3.10 | 8 |
| FR4 資料機密性 | CR 4.02、4.02 RE(1)、4.03 | 3 |
| FR6 事件回應 | CR 6.02 | 1 |
| FR7 資源可用性 | CR 7.01、7.01 RE(1)、7.02、7.03 RE(1)、7.06 RE(1)、7.08 | 6 |

### A.1 Common Component Security Constraints

#### A.1.1 CCSC 1 Support of essential functions（部分符合）
- **現況**：Metadata、Runtime、Data Agent、Alarm Agent 分離部署，xnginx 負責入口與服務監督；核心功能可獨立運作。
- **部分符合原因**：尚未依 IEC 62443-3-3 進行系統風險分析，未以正式文件界定「必要功能」、其相依元件、失效影響與復原目標；目前僅有架構與部署文件。
- **建議**：建立必要功能清單與 RTO/RPO，對應 `SDS-02`、`SDS-10`；以 zone/conduit 模型標示資料流與相依；由產品/資安共同核可，並納入變更管理。
- **對應**：[NFR-REL-001、NFR-OPC-005](../SRS/05_Nonfunctional_Requirements.md)、[SDS-02](../SDS/02_System_Architecture.md)、[SDS-10](../SDS/10_Deployment_and_Operations.md)

#### A.1.2 CCSC 2 Compensating countermeasures（部分符合，HTTPS）
- **現況**：以 xnginx TLS、限流、後端 loopback、Session/CSRF、稽核等作為補償控制。
- **部分符合原因**：補償措施散見於設計與程式，未形成正式「補償控制清單」，缺少每項控制對應的原始需求、威脅、殘餘風險與有效期限。
- **建議**：建立補償控制登記表（控制、對應 CR、替代原因、驗證方式、殘餘風險、到期日）；與 `SDS-08`、`SDS-11` 連動；定期由資安覆核。
- **對應**：[NFR-SEC-004、NFR-SEC-005、NFR-SEC-015](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08](../SDS/08_Security_Design.md)、[SDS-13](../SDS/13_Security_Remediation.md)

#### A.1.3 CCSC 4 Software development process（部分符合）
- **現況**：具備 SRS/SDS、追溯表、CRA 文件、版本管理與弱點修復紀錄。
- **部分符合原因**：未取得 IEC 62443-4-1 認證，亦缺少完整安全開發證據（安全需求評審、威脅建模更新頻率、程式碼安全掃描、滲透測試報告、供應鏈與漏洞處理 SLA 的正式紀錄）。
- **建議**：以 IEC 62443-4-1 為框架進行差距分析；將威脅建模（`CRA-01`）、安全程式碼檢視（`CRA-03`）、SBOM 與漏洞修補 SLA 納入 CI/Release Gate；保存可稽核紀錄。
- **對應**：[NFR-MNT-005](../SRS/05_Nonfunctional_Requirements.md)、[SRS-06 §7](../SRS/06_Acceptance_and_Traceability.md)、[SDS-12](../SDS/12_Traceability.md)

### A.2 Identification and Authentication Control（FR1）

#### A.2.1 CR 1.02 Software process and device identification and authentication（部分符合，HTTPS）
- **現況**：服務間使用內部 Bearer/Session，OPC UA 連線使用應用憑證/PFX。
- **部分符合原因**：未對每個服務程序或裝置核發獨立且可輪替的身分；部分內部呼叫仍共用 Session/Bearer，缺乏服務身分盤點與生命週期管理。
- **建議**：建立服務/裝置身分清冊；內部呼叫改用獨立服務帳號或 mTLS；憑證/Secret 由密鑰管理派發並定期輪替；撤銷流程納入維運。
- **對應**：[NFR-SEC-001、NFR-OPC-007](../SRS/05_Nonfunctional_Requirements.md)、[IF-SW-005](../SRS/04_External_Interface_Requirements.md)、[SDS-08 §2、§8.1](../SDS/08_Security_Design.md)

#### A.2.2 CR 1.02 RE(1) Unique identification and authentication（部分符合，HTTPS）
- **現況**：OPC UA 每個連線可設定獨立應用憑證/PFX，Certificate/create 可產生憑證。
- **部分符合原因**：憑證機制未涵蓋所有服務程序；憑證輪替、撤銷、到期監控與信任清單管理仍依人工。
- **建議**：為 Metadata/Runtime/Alarm 等服務建立專屬憑證或金鑰；自動化到期提醒與輪替；建立憑證撤銷與信任清單程序；記錄憑證指紋於啟動日誌。
- **對應**：[FR-OPC-005、FR-OPC-021、FR-OPC-027](../SRS/07_OPCUA_Subsystem.md)、[NFR-OPC-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-14 §4.5](../SDS/14_OPCUA_Subsystem_Design.md)

#### A.2.3 CR 1.05 Authenticator management（部分符合，HTTPS）
- **現況**：支援使用者改密、管理員重設；OPC UA 首次登入可強制改密。
- **部分符合原因**：缺少密碼到期、歷史、複雜度與重用限制；重設流程與臨時密碼生命週期未標準化；未強制所有帳號定期更新。
- **建議**：導入可設定的密碼政策（長度、複雜度、歷史次數、到期、首次改密）；重設採一次性臨時密碼並強制變更；所有變更寫入稽核；遷移至高強度雜湊。
- **對應**：[FR-CLI-007](../SRS/03_Functional_Requirements.md)、[FR-META-029](../SRS/03_Functional_Requirements.md)、[NFR-SEC-003](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6.1](../SDS/08_Security_Design.md)、[SDS-11 R-004](../SDS/11_Design_Decisions_and_Risks.md)

#### A.2.4 CR 1.07 Strength of password-based authentication（部分符合，HTTPS）
- **現況**：密碼以 SHA-256 儲存，無加鹽；登入經 HTTPS 保護。
- **部分符合原因**：SHA-256 不具工作因子，無法抵抗離線暴力破解；無最小長度/複雜度/常見密碼阻擋；未加鹽導致相同密碼雜湊相同。
- **建議**：改用 Argon2id 或 bcrypt（含成本參數與每帳號鹽）；登入端加入速率限制與鎖定；提供舊雜湊登入時自動升級（rehash）；政策與教育文件化。
- **對應**：[NFR-SEC-003、NFR-SEC-004](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6.1](../SDS/08_Security_Design.md)、[SDS-11 R-004](../SDS/11_Design_Decisions_and_Risks.md)

#### A.2.5 CR 1.10 Authenticator feedback（部分符合，HTTPS）
- **現況**：登入失敗回傳一般錯誤，不顯示密碼；HTTPS 防止竊聽。
- **部分符合原因**：未明確保證回應時間與訊息一致（避免帳號列舉與時間側通道）；失敗原因與來源 IP 的稽核欄位未標準化；無逐步延遲或鎖定提示策略。
- **建議**：統一失敗訊息與回應時間；對未知帳號與錯誤密碼採相同處理；記錄來源 IP、User-Agent、失敗原因碼；必要時加入指數退避；提供管理員可查詢的鎖定狀態。
- **對應**：[FR-CLI-013](../SRS/03_Functional_Requirements.md)、[NFR-SEC-011](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §3.2、§11](../SDS/08_Security_Design.md)

### A.3 Use Control（FR2）

#### A.3.1 CR 2.01 RE(1) Authorization enforcement for all users（部分符合）
- **現況**：人類使用者具備專案/角色/畫面/安全區權限；服務間以 Bearer/OPC UA 憑證區分。
- **部分符合原因**：軟體程序與裝置的原則僅部分落實，未建立機器身分對應的角色與資源範圍；部分內部端點僅驗證來源（loopback）而未細分權限。
- **建議**：建立機器身分授權矩陣（服務 → API → 資源範圍）；內部端點加入服務角色檢查；定期審查機器帳號與權限；將授權決策寫入稽核。
- **對應**：[NFR-SEC-002、NFR-OPC-007](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §4.2、§8.1](../SDS/08_Security_Design.md)

#### A.3.2 CR 2.01 RE(3) Supervisor override（部分符合）
- **現況**：Admin 可管理專案、使用者與設定，並可執行必要的管理操作。
- **部分符合原因**：無獨立「監督者覆寫」角色與流程；緊急覆寫缺少申請、理由、核准與到期機制，難以與日常管理權限區隔。
- **建議**：新增監督者/破窗（break-glass）角色，採臨時提權與自動到期；要求填寫理由與工單編號；雙人核准高風險操作；全程稽核與事後審查。
- **對應**：[FR-CLI-076](../SRS/03_Functional_Requirements.md)、[NFR-AUD-001](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §4.3](../SDS/08_Security_Design.md)、[SDS-11 R-024](../SDS/11_Design_Decisions_and_Risks.md)

#### A.3.3 CR 2.03 Use control for portable and mobile devices（部分符合，HTTPS）
- **現況**：APP 可連線並接收推播；裝置以 FCM Token 註冊。
- **部分符合原因**：未限制可存取裝置類型、版本或合規狀態；無裝置核准、遠端撤銷、遺失處理與 Session 綁定的完整流程。
- **建議**：建立裝置註冊/核准清單；支援遠端撤銷 Token 與 Session；要求最低 OS/App 版本；可選用 MDM 或條件式存取；記錄裝置與推播稽核。
- **對應**：[FR-CLI-016](../SRS/03_Functional_Requirements.md)、[FR-RT-021、FR-RT-022](../SRS/03_Functional_Requirements.md)、[NFR-PRV-004](../SRS/05_Nonfunctional_Requirements.md)、[SDS-03 §14](../SDS/03_Client_Design.md)、[SDS-05 §8.5](../SDS/05_Runtime_Proxy_Design.md)

#### A.3.4 CR 2.04 Mobile code（部分符合）
- **現況**：支援 Widget、JavaScript、外掛與 WebPlugin；腳本可壓縮/加密儲存。
- **部分符合原因**：未限制行動程式碼來源與執行權限；外掛載入缺少簽章/雜湊白名單；腳本 API 雖受控，但無執行期沙箱與資源上限的完整描述。
- **建議**：建立外掛/腳本來源白名單與簽章驗證；限制檔案系統與網路 API；設定執行時間、記憶體與遞迴上限；停用未使用的外掛類型；保留載入與失敗稽核。
- **對應**：[FR-CLI-050](../SRS/03_Functional_Requirements.md)、[FR-CLI-058~063](../SRS/03_Functional_Requirements.md)、[NFR-SEC-014](../SRS/05_Nonfunctional_Requirements.md)、[SDS-03 §9](../SDS/03_Client_Design.md)、[SDS-08 §9](../SDS/08_Security_Design.md)

#### A.3.5 SAR 2.04 Mobile code（部分符合）
- **現況**：應用層具備輸入驗證、腳本管理與外掛審核建議。
- **部分符合原因**：EdgeView 為軟體應用，行動程式碼風險主要落在 JS/Plugin/Widget 生態；目前無強制掃毒、簽章或供應鏈驗證，僅靠流程審核。
- **建議**：外掛上架前執行靜態/動態掃描與簽章；以雜湊清單封鎖未核准版本；部署端限制外掛來源；建立安全開發與外掛審核紀錄。
- **對應**：[FR-CLI-050、FR-CLI-093](../SRS/03_Functional_Requirements.md)、[NFR-SEC-010、NFR-SEC-014](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §9](../SDS/08_Security_Design.md)

#### A.3.6 SAR 2.04 RE(1) Mobile code authenticity check（部分符合，HTTPS）
- **現況**：Plugin 可經 HTTPS 下載，降低傳輸竄改風險。
- **部分符合原因**：未驗證程式簽章或發行者；僅依 HTTPS 與伺服器信任，無法防止來源端被替換或內部檔案竄改。
- **建議**：外掛採分離式簽章（如 Ed25519/GPG）並驗證憑證鏈；安裝/更新時比對雜湊；建立發行者信任清單與撤銷；拒絕未簽章或簽章失效的套件。
- **對應**：[FR-CLI-050、FR-CLI-093](../SRS/03_Functional_Requirements.md)、[NFR-SEC-004、NFR-SEC-010](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §7、§9](../SDS/08_Security_Design.md)

#### A.3.7 CR 2.05 Session lock（部分符合）
- **現況**：`sessionmonitor` 與 Viewer 具備逾時/鎖定行為；Session 存於伺服器端。
- **部分符合原因**：無全域可設定的閒置鎖定政策；Designer/管理頁與不同 Client 的行為未完全一致；鎖定後的再認證與稽核描述不足。
- **建議**：提供全域與角色層級的閒置逾時/鎖定設定；鎖定後要求重新認證或登出；所有 Client 套用一致政策；記錄鎖定/解鎖事件；管理員可強制鎖定。
- **對應**：[FR-CLI-010、FR-CLI-011](../SRS/03_Functional_Requirements.md)、[NFR-SEC-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §3.4](../SDS/08_Security_Design.md)

#### A.3.8 CR 2.09 Audit storage capacity（部分符合）
- **現況**：稽核/操作日誌存於 PostgreSQL，具備維護與保留設定；可備份。
- **部分符合原因**：容量監控與告警有限；未定義各日誌類型的保留上限、輪替與外部歸檔；高負載或儲存不足時的行為未明確。
- **建議**：定義保留政策與容量預算；定期量測成長率；達到門檻時輪替/歸檔至外部儲存；監控磁碟與資料表大小；容量不足時的降級與告警流程文件化。
- **對應**：[FR-CLI-083](../SRS/03_Functional_Requirements.md)、[FR-META-044](../SRS/03_Functional_Requirements.md)、[NFR-AUD-004](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §11](../SDS/08_Security_Design.md)、[SDS-10 §8](../SDS/10_Deployment_and_Operations.md)

#### A.3.9 CR 2.09 RE(1) Warn when audit storage threshold reached（部分符合）
- **現況**：有維護統計與日誌可查詢。
- **部分符合原因**：未見明確的容量門檻告警與通知管道；無分級水位（警告/嚴重）與自動處置。
- **建議**：新增可設定的水位（如 70%/85%/95%）；定期檢查並透過郵件/Webhook/SIEM 通知；達嚴重水位時觸發輪替或停止非必要寫入；提供 UI 狀態。
- **對應**：[FR-CLI-083](../SRS/03_Functional_Requirements.md)、[NFR-AUD-004、NFR-MNT-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §7](../SDS/10_Deployment_and_Operations.md)、[SDS-08 §11](../SDS/08_Security_Design.md)

#### A.3.10 CR 2.10 Response to audit processing failures（部分符合）
- **現況**：日誌失敗不阻斷核心功能；錯誤會記錄。
- **部分符合原因**：未依事件類型定義 fail-open/fail-closed；無本機緩衝、重試與明確告警流程；稽核失效時的可見性不足。
- **建議**：定義關鍵事件（登入、權限變更、匯入匯出）於稽核失敗時的行為；加入本機緩衝與重送；健康端點顯示稽核狀態；告警至 SIEM/維運；定期演練。
- **對應**：[NFR-REL-002](../SRS/05_Nonfunctional_Requirements.md)、[NFR-AUD-001](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §11](../SDS/08_Security_Design.md)、[SDS-10 §11](../SDS/10_Deployment_and_Operations.md)

#### A.3.11 CR 2.11 RE(1) Time synchronization（部分符合）
- **現況**：稽核與事件使用 UTC/Unix ms；應用可依系統時區轉換。
- **部分符合原因**：時間來源依賴 OS/NTP，應用未驗證偏移或來源真實性；未監控時間跳變對稽核順序與 Token 驗證的影響。
- **建議**：部署 authenticated NTP/NTS 或受信任時間源；監控偏移與跳變；超過門檻時告警並拒絕敏感操作；文件化時區與 UTC 儲存規則。
- **對應**：[NFR-I18N-004](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-014](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §12](../SDS/10_Deployment_and_Operations.md)、[SDS-14 §7.4](../SDS/14_OPCUA_Subsystem_Design.md)

#### A.3.12 CR 2.12 Non-repudiation（部分符合）
- **現況**：稽核記錄含使用者、時間、事件與 SHA3-256 checksum，可偵測竄改。
- **部分符合原因**：checksum 由同一系統產生，具 DB 權限者仍可重算；缺少硬體/外部時間戳與不可否認簽章，非法律級不可否認。
- **建議**：對稽核批次產生數位簽章並錨定外部（WORM/SIEM/時間戳服務）；分離簽章金鑰權限；定期驗證鏈結；保存稽核政策與金鑰輪替紀錄。
- **對應**：[NFR-AUD-001~003](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6.3、§11](../SDS/08_Security_Design.md)

#### A.3.13 CR 2.12 RE(1) Non-repudiation for all users（部分符合）
- **現況**：人類使用者操作具備稽核；部分服務/裝置事件有日誌。
- **部分符合原因**：軟體程序與裝置事件的歸屬不完整；未全面使用機器身分、關聯 ID 與來源資訊，難以還原跨服務操作鏈。
- **建議**：所有服務呼叫帶入機器身分與 correlation ID；建立事件覆蓋矩陣（人類/服務/裝置）；補齊 Ack/Process、設定變更與內部呼叫稽核；定期抽查。
- **對應**：[NFR-AUD-001~003](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-009](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §11](../SDS/08_Security_Design.md)、[SDS-14 §5.8](../SDS/14_OPCUA_Subsystem_Design.md)

### A.4 System Integrity（FR3）

#### A.4.1 CR 3.02 Protection from malicious code（部分符合）
- **現況**：應用層具備輸入/腳本控管；部署端可搭配主機防護。
- **部分符合原因**：未內建防毒/EDR 或容器映像掃描；上傳檔案與外掛無強制掃毒；惡意程式防護高度依賴 Host 與維運流程。
- **建議**：部署 EDR/AV 並納入標準安裝；CI 掃描容器與依賴；上傳檔案掃毒與類型驗證；建立隔離區與事件處理流程。
- **對應**：[NFR-SEC-012、NFR-SEC-014](../SRS/05_Nonfunctional_Requirements.md)、[FR-CLI-063](../SRS/03_Functional_Requirements.md)、[SDS-08 §9](../SDS/08_Security_Design.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md)

#### A.4.2 SAR 3.02 Protection from malicious code（部分符合）
- **現況**：應用層對輸入、腳本與外掛有基本控管。
- **部分符合原因**：EdgeView 為軟體應用，未提供應用層惡意程式掃描；外掛/腳本的可信來源與執行限制仍不足。
- **建議**：外掛簽章與白名單、腳本 API 最小化、上傳掃描、CSP/iframe 限制；將惡意程式防護責任與 Host 控制寫入部署手冊。
- **對應**：[NFR-SEC-012、NFR-SEC-014](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §9、§10](../SDS/08_Security_Design.md)

#### A.4.3 CR 3.03 Security functionality verification（部分符合）
- **現況**：具備健康檢查、API 測試與例外攔截；可透過日誌觀察狀態。
- **部分符合原因**：缺少內建的安全功能自我測試（TLS/憑證、金鑰來源、CSRF、權限、時間同步）；驗證多為開發/維運流程，非產品執行期能力。
- **建議**：新增啟動自我檢查與定期檢查（憑證到期、Session key 指紋一致、DB 權限、TLS 組態）；將結果輸出健康端點與稽核；異常時告警；提供可關閉的設定以因應相容性。
- **對應**：[NFR-MNT-008](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-012](../SRS/05_Nonfunctional_Requirements.md)、[NFR-REL-002](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §7](../SDS/10_Deployment_and_Operations.md)、[SDS-13](../SDS/13_Security_Remediation.md)

#### A.4.4 CR 3.04 Software and information integrity（部分符合，HTTPS）
- **現況**：具備版本欄位、checksum 與備份；更新檔可經 HTTPS 傳輸。
- **部分符合原因**：更新/安裝檔未強制簽章驗證；無 anti-rollback 與更新來源固定；完整性檢查未涵蓋設定與外掛。
- **建議**：建立簽署與驗證流程（安裝檔/容器/外掛）；版本清單與雜湊基準；更新前驗簽、更新後驗證；支援回復；將 SBOM 與雜湊納入發佈。
- **對應**：[NFR-SEC-012](../SRS/05_Nonfunctional_Requirements.md)、[NFR-MNT-007](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-020](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §9](../SDS/10_Deployment_and_Operations.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md)

#### A.4.5 CR 3.04 RE(1) Authenticity of software and information（部分符合，HTTPS）
- **現況**：軟體與資源可經 HTTPS 下載，傳輸具機密性與完整性。
- **部分符合原因**：未驗證程式碼簽章與發行者；HTTPS 僅保證通道，若來源或伺服器被入侵仍可能取得惡意更新。
- **建議**：採用程式碼簽章（Windows Authenticode、Linux GPG/cosign）並驗證憑證鏈；容器映像簽章與 provenance；下載頁提供雜湊；拒絕未簽章更新。
- **對應**：[NFR-SEC-004、NFR-SEC-012](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §7](../SDS/08_Security_Design.md)、[SDS-10 §9](../SDS/10_Deployment_and_Operations.md)

#### A.4.6 CR 3.04 RE(2) Automated notification of integrity violations（部分符合）
- **現況**：稽核/日誌可記錄異常，維運可查詢。
- **部分符合原因**：無自動化完整性監控（FIM）與告警；設定/程式/資料庫變更未建立基準與即時比對。
- **建議**：部署 FIM 或定期基準比對（設定、二進位、DB schema）；異常時告警至 SIEM/維運；納入變更管理與例外清單；定期演練。
- **對應**：[NFR-SEC-012](../SRS/05_Nonfunctional_Requirements.md)、[NFR-MNT-002](../SRS/05_Nonfunctional_Requirements.md)、[NFR-AUD-001](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §11](../SDS/08_Security_Design.md)、[SDS-10 §7](../SDS/10_Deployment_and_Operations.md)

#### A.4.7 CR 3.09 Protection of audit information（部分符合）
- **現況**：稽核記錄含 SHA3-256 checksum，DB 具權限控管。
- **部分符合原因**：未使用 WORM/一次性媒體；具 DB 管理權限者可刪除或重算；備份未強制不可變。
- **建議**：稽核表改為 append-only 角色；串流至外部 SIEM/WORM；備份採物件鎖定/不可變儲存；定期驗證 checksum 與簽章；分離管理權限。
- **對應**：[FR-META-042](../SRS/03_Functional_Requirements.md)、[NFR-AUD-003](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6.3](../SDS/08_Security_Design.md)、[SDS-11 R-017](../SDS/11_Design_Decisions_and_Risks.md)

#### A.4.8 CR 3.10 Support for updates（部分符合，HTTPS）
- **現況**：具備安裝/升版流程與回復說明；更新檔可經 HTTPS 傳輸。
- **部分符合原因**：更新真實性、完整性與 anti-rollback 未強制；維護窗口、備份與回滾演練未標準化。
- **建議**：更新包簽章與驗證、版本 pinning、更新前備份與檢查清單、失敗自動回復；維護窗口與通知流程；更新事件稽核。
- **對應**：[FR-UM-007](../SRS/08_User_Manual_Requirements.md)、[NFR-MNT-005](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §9](../SDS/10_Deployment_and_Operations.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md)

### A.5 Data Confidentiality（FR4）

#### A.5.1 CR 4.02 Information persistence（部分符合）
- **現況**：Session/資料保留可設定；具備登出與維護機制。
- **部分符合原因**：缺少統一的資料分類、保留期限與清除驗證；備份、快取、暫存與日誌的清除責任未完整定義。
- **建議**：建立資料分類與保留矩陣；定義清除/銷毀程序與證據；加密靜態資料並支援金鑰銷毀；定期抽查殘留資料。
- **對應**：[NFR-PRV-001~004](../SRS/05_Nonfunctional_Requirements.md)、[NFR-AUD-004](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §12](../SDS/08_Security_Design.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md)

#### A.5.2 CR 4.02 RE(1) Erase of shared memory resources（部分符合）
- **現況**：程序結束時由 OS 釋放記憶體；敏感資料以 AES 保護。
- **部分符合原因**：未保證共享記憶體/暫存緩衝在使用後立即抹除；Go/Qt 執行期與 OS 交換/核心 dump 可能留下敏感資料。
- **建議**：敏感緩衝使用後顯式清零；停用或保護 core dump/swap；使用記憶體鎖定（可行時）；建立清除測試與殘留掃描。
- **對應**：[NFR-PRV-003](../SRS/05_Nonfunctional_Requirements.md)、[NFR-SEC-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §12](../SDS/08_Security_Design.md)、[SDS-11 R-022](../SDS/11_Design_Decisions_and_Risks.md)

#### A.5.3 CR 4.03 Use of cryptography（部分符合，HTTPS）
- **現況**：TLS、AES-256-CBC、SHA-256 與 OPC UA 加密；敏感設定加密儲存。
- **部分符合原因**：密碼雜湊未加鹽且無工作因子；AES-CBC 缺少完整性保護（建議 GCM）；金鑰輪替與演算法盤點未完整。
- **建議**：密碼改 Argon2id/bcrypt；新資料採 AES-GCM 或 Encrypt-then-MAC；建立加密演算法/金鑰清冊與輪替政策；淘汰弱協定/演算法；可選 FIPS 模式。
- **對應**：[NFR-SEC-003、NFR-SEC-004、NFR-SEC-007](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6](../SDS/08_Security_Design.md)、[SDS-11 R-004](../SDS/11_Design_Decisions_and_Risks.md)

### A.6 Timely Response to Events（FR6）

#### A.6.1 CR 6.02 Continuous monitoring（部分符合，HTTPS）
- **現況**：具備 `systeminfo`、健康端點、日誌與系統狀態頁。
- **部分符合原因**：未整合 SIEM/IDS/IPS；告警規則、關聯分析與值班流程不完整；監控資料保留與可視化有限。
- **建議**：將日誌/指標匯出至 SIEM；建立登入失敗、權限變更、稽核異常、資源耗盡的告警規則；搭配 IDS/IPS 與網路流量監控；定義值班與升級流程。
- **對應**：[NFR-MNT-008](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-012](../SRS/05_Nonfunctional_Requirements.md)、[NFR-REL-001](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §7](../SDS/10_Deployment_and_Operations.md)、[SDS-08 §11](../SDS/08_Security_Design.md)

### A.7 Resource Availability（FR7）

#### A.7.1 CR 7.01 Denial of service protection（部分符合，HTTPS）
- **現況**：xnginx `limit_req`、連線池/佇列、逾時與服務監督。
- **部分符合原因**：限流門檻偏高且非依端點細分；無全面 DDoS 防護、來源信譽或上游清洗；WS 訊息與連線數限制不足。
- **建議**：依端點（登入、查詢、WS）設定更嚴格限流；連線數/訊息大小上限；來源 IP 信譽與封鎖；上游 DDoS 清洗；定期壓測與調校。
- **對應**：[NFR-SEC-015](../SRS/05_Nonfunctional_Requirements.md)、[NFR-PERF-007](../SRS/05_Nonfunctional_Requirements.md)、[IF-COM-008](../SRS/04_External_Interface_Requirements.md)、[SDS-08 §13](../SDS/08_Security_Design.md)、[SDS-11 R-016](../SDS/11_Design_Decisions_and_Risks.md)

#### A.7.2 CR 7.01 RE(1) Manage communication load from component（部分符合）
- **現況**：具備限流、批次查詢、背壓與 Record 訂閱上限/TTL。
- **部分符合原因**：未依負載動態調節；缺少元件級配額與電路斷路；高負載下的降級策略未完全自動化。
- **建議**：加入動態限流/配額、佇列長度與延遲指標、circuit breaker；依租戶/專案限額；自動降級與告警；壓測驗證。
- **對應**：[NFR-PERF-006、NFR-PERF-007](../SRS/05_Nonfunctional_Requirements.md)、[NFR-SCL-003](../SRS/05_Nonfunctional_Requirements.md)、[NFR-SEC-015](../SRS/05_Nonfunctional_Requirements.md)、[SDS-05 §6.3](../SDS/05_Runtime_Proxy_Design.md)、[SDS-13 R-009](../SDS/13_Security_Remediation.md)

#### A.7.3 CR 7.02 Resource management（部分符合）
- **現況**：DB 連線池、WS 佇列、掃描間隔與 Record TTL/上限。
- **部分符合原因**：無全域資源配額與容器資源上限保證；缺少 per-tenant/per-project 的使用量統計與限制；資源告警不完整。
- **建議**：以 cgroups/K8s limits 設定 CPU/記憶體；建立配額與使用量儀表；連線池與佇列上限調校；資源飽和告警與擴縮容策略。
- **對應**：[NFR-SCL-002、NFR-SCL-003](../SRS/05_Nonfunctional_Requirements.md)、[NFR-PERF-010](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-015](../SRS/05_Nonfunctional_Requirements.md)、[SDS-05 §7、§10](../SDS/05_Runtime_Proxy_Design.md)、[SDS-13 R-009](../SDS/13_Security_Remediation.md)

#### A.7.4 CR 7.03 RE(1) Backup integrity verification（部分符合）
- **現況**：可執行 checksum 與還原演練；具備 DB dump 與設定/憑證備份。
- **部分符合原因**：未自動驗證備份完整性與可還原性；缺少排程驗證、失敗告警與保存期限管理。
- **建議**：備份後自動計算雜湊並記錄；定期自動測試還原至隔離環境；驗證 schema/關鍵資料；失敗告警；保存與銷毀政策。
- **對應**：[NFR-REL-009](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-016](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §8](../SDS/10_Deployment_and_Operations.md)、[SDS-06](../SDS/06_Data_Model.md)

#### A.7.5 CR 7.06 RE(1) Machine-readable reporting of current security settings（部分符合，HTTPS）
- **現況**：設定檔與部分 API 可讀取；管理介面可查詢。
- **部分符合原因**：無完整、版本化且可機器讀取的安全設定報表；缺少有效值（含預設/覆寫）與基準差異。
- **建議**：提供 JSON 匯出 API（TLS、Session、CSRF、限流、密碼政策、憑證、稽核）；標示來源與有效值；支援基準比對與簽章；定期產出報表。
- **對應**：[FR-META-036](../SRS/03_Functional_Requirements.md)、[NFR-MNT-004、NFR-MNT-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-07](../SDS/07_Interface_Design.md)、[SDS-10 §5](../SDS/10_Deployment_and_Operations.md)

#### A.7.6 CR 7.08 Control system component inventory（部分符合）
- **現況**：具備 SDS/部署清單、服務清冊與版本資訊。
- **部分符合原因**：無自動化 SBOM 與元件盤點；部署後變更、外掛與相依套件未持續對帳；缺少資產擁有者與生命週期欄位。
- **建議**：CI 產生 CycloneDX/SPDX SBOM；部署時回報元件清單；建立 CMDB/資產清冊（版本、擁有者、位置、支援期限）；定期對帳與漏洞掃描。
- **對應**：[NFR-SEC-012](../SRS/05_Nonfunctional_Requirements.md)、[NFR-CMP-004](../SRS/05_Nonfunctional_Requirements.md)、[SDS-12](../SDS/12_Traceability.md)、[SDS-10 §2](../SDS/10_Deployment_and_Operations.md)

---

## 附錄 B：不符合項目明細（8 項）

> 本附錄說明目前無對應機制項目的需求原意、現況、不符合原因、風險與建議。建議分為短期（流程/部署緩解）、中期（產品功能）與長期（架構）三層。

| 項目 | 名稱 | SL | 主要缺口 |
|------|------|----|----------|
| CR 1.01 RE(2) | Multifactor authentication for all interfaces | SL3 | 無第二因子 |
| CR 1.07 RE(1) | Password generation and lifetime restrictions for human users | SL3 | 無產生/到期/歷史政策 |
| CR 1.07 RE(2) | Password lifetime restrictions for all users | SL4 | 未涵蓋所有帳號生命週期 |
| CR 2.01 RE(4) | Dual approval | SL4 | 無雙人核准 |
| CR 2.07 | Concurrent session control | SL3 | 無同時 Session 上限 |
| CR 3.03 RE(1) | Security functionality verification during normal operation | SL4 | 無執行期安全自我測試 |
| CR 3.09 RE(1) | Audit records on write-once media | SL4 | 無 WORM/不可變稽核 |
| CR 4.02 RE(2) | Erase verification | SL3 | 無抹除驗證 |

### B.1 CR 1.01 RE(2) Multifactor authentication for all interfaces（SL3｜不符合）
- **需求要求**：所有人類與軟體介面需採用多因子認證，降低單一密碼外洩造成的冒用風險。
- **現況**：目前為帳號/密碼 + Session/CSRF；OPC UA 使用者同為單一密碼因子。系統未整合 TOTP、WebAuthn、憑證或硬體金鑰等第二因子。
- **不符合原因**：
  1. 產品未實作第二因子註冊、驗證、復原與停用流程。
  2. 未提供標準 IdP 整合（OIDC/SAML），難以由外部身分提供者補足。
  3. OPC UA 設定介面與 Client 登入流程各自獨立，無統一 MFA 攔截點。
- **風險/影響**：帳密遭釣魚、重用或外洩即可完全冒用；對 SL3 要求不足；管理帳號風險最高。
- **建議**：
  1. **短期**：於 xnginx/Ingress 前配置 IdP 或 WAF 執行 MFA，優先套用管理帳號與 OPC UA 設定介面；限制來源網路。
  2. **中期**：產品支援 TOTP/WebAuthn，提供註冊、備用碼、復原與管理員重設；MFA 狀態納入稽核與使用者管理。
  3. **長期**：支援 OIDC/SSO 與條件式存取；服務間改 mTLS/憑證，降低共享密碼。
- **驗收建議**：未完成第二因子不得建立 Session；管理與 OPC UA 設定介面皆強制；失敗/成功/重設均留稽核；提供復原演練。
- **對應**：[NFR-SEC-001](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §14.2](../SDS/08_Security_Design.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md)

### B.2 CR 1.07 RE(1) Password generation and lifetime restrictions for human users（SL3｜不符合）
- **需求要求**：人類使用者密碼需具備產生規則、最短/最長使用期限與歷史限制，避免弱密碼與長期未更換。
- **現況**：支援改密與重設，但無產生規則、到期提醒/強制變更、歷史密碼比對與重用限制。
- **不符合原因**：
  1. 使用者模型未保存密碼變更時間與歷史雜湊。
  2. 登入流程未檢查 `must_change`/到期狀態（僅 OPC UA 首次改密）。
  3. 無集中式密碼政策設定與例外管理。
- **風險/影響**：弱密碼與長期未更新提高猜測/外洩風險；無法符合 SL3 帳號生命週期要求。
- **建議**：
  1. **短期**：以部署政策要求定期更換，管理員抽查；高權限帳號優先。
  2. **中期**：新增密碼政策（長度、複雜度、歷史 N 次、最長期限、首次改密）；到期前提示、逾期強制變更；記錄 `password_changed_at` 與歷史雜湊。
  3. **長期**：支援風險導向變更（外洩偵測）與 SSO 整合。
- **驗收建議**：到期帳號僅能進入改密頁；重用歷史密碼被拒；政策可設定並生效；變更與失敗均稽核。
- **對應**：[FR-CLI-007](../SRS/03_Functional_Requirements.md)、[FR-META-029](../SRS/03_Functional_Requirements.md)、[NFR-SEC-003](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6.1](../SDS/08_Security_Design.md)、[SDS-11 R-004](../SDS/11_Design_Decisions_and_Risks.md)

### B.3 CR 1.07 RE(2) Password lifetime restrictions for all users（SL4｜不符合）
- **需求要求**：所有帳號（含服務/裝置帳號）需有密碼或認證器生命週期限制與輪替。
- **現況**：僅人類使用者可改密；服務間 Bearer/Session、OPC UA 憑證與資料庫帳密未統一納入生命週期管理。
- **不符合原因**：
  1. 無統一的認證器清冊與到期欄位。
  2. 服務/裝置憑證與 Secret 輪替多依人工維運（部分已由 `SESSION_SECRET` 支援，但非全面）。
  3. 缺少到期前提醒、逾期停用與例外核准流程。
- **風險/影響**：長期未輪替的服務憑證/密鑰一旦外洩可長期使用；不符合 SL4。
- **建議**：
  1. **短期**：建立認證器清冊（類型、擁有者、到期日、用途）；優先輪替高權限與外部介面憑證。
  2. **中期**：導入 Secret/憑證管理（Vault/K8s Secret）與自動輪替；到期告警與逾期停用；支援雙金鑰重疊期。
  3. **長期**：全面改用短期憑證/mTLS 與自動化簽發。
- **驗收建議**：所有認證器具到期日與擁有者；逾期自動失效；輪替不中斷服務；輪替紀錄可稽核。
- **對應**：[NFR-SEC-003、NFR-SEC-008](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6](../SDS/08_Security_Design.md)、[SDS-13 §2.3](../SDS/13_Security_Remediation.md)

### B.4 CR 2.01 RE(4) Dual approval（SL4｜不符合）
- **需求要求**：高風險操作需雙人核准（four-eyes），避免單一管理員濫權或誤操作。
- **現況**：Admin 可獨立執行使用者/角色變更、專案匯入匯出、資料來源與系統設定；系統無待核准佇列與第二位核准者機制。
- **不符合原因**：
  1. 權限模型僅有角色檢查，無交易式核准流程。
  2. 無「申請者 ≠ 核准者」限制與核准紀錄。
  3. 高風險操作清單未正式界定。
- **風險/影響**：單一帳號遭冒用即可變更權限、匯出資料或改設定；不符合 SL4。
- **建議**：
  1. **短期**：以流程要求高風險操作需工單與第二人覆核，事後由資安抽查。
  2. **中期**：在 Metadata 新增核准工作流（pending action、申請理由、核准/駁回、到期）；禁止自我核准；通知與稽核。
  3. **長期**：依風險分級擴大至匯入匯出、金鑰/憑證、稽核設定與緊急覆寫。
- **驗收建議**：未經第二位核准的操作不得生效；申請/核准/執行三段稽核；可查詢待核准與歷史。
- **對應**：[FR-CLI-076](../SRS/03_Functional_Requirements.md)、[NFR-SEC-002](../SRS/05_Nonfunctional_Requirements.md)、[NFR-AUD-001](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §4](../SDS/08_Security_Design.md)

### B.5 CR 2.07 Concurrent session control（SL3｜不符合）
- **需求要求**：可限制同一使用者同時存在的 Session 數量，並支援管理與終止。
- **現況**：Session 存於 PostgreSQL `http_sessions`，同一帳號可建立多個有效 Session；無上限、無互斥登入與管理端查詢/終止介面。
- **不符合原因**：
  1. Session 建立流程未檢查既有 Session 數量。
  2. 無 Session 管理 API/UI（列表、撤銷）。
  3. 未定義政策（上限、超過時淘汰最舊或拒絕新登入）。
- **風險/影響**：帳號共享或遭竊用時難以察覺；無法滿足 SL3 同時會話控制。
- **建議**：
  1. **短期**：以維運程序定期檢查異常 Session；高權限帳號加強監控。
  2. **中期**：新增可設定上限（如 1/3/5）；超過時拒絕或終止最舊；提供管理端 Session 列表與撤銷；登入/登出/撤銷稽核。
  3. **長期**：支援裝置綁定與風險導向會話政策。
- **驗收建議**：達上限時依政策拒絕或淘汰；管理端可即時撤銷；撤銷後 API/WS 立即失效；事件可稽核。
- **對應**：[NFR-SEC-008](../SRS/05_Nonfunctional_Requirements.md)、[FR-META-004](../SRS/03_Functional_Requirements.md)、[SDS-08 §3.3](../SDS/08_Security_Design.md)

### B.6 CR 3.03 RE(1) Security functionality verification during normal operation（SL4｜不符合）
- **需求要求**：系統需在正常運作期間持續驗證安全功能（如加密、稽核、存取控制）仍有效。
- **現況**：僅有健康檢查、API 測試與日誌；無執行期安全自我測試、定期組態驗證或完整性監控。
- **不符合原因**：
  1. 未實作安全功能自檢（憑證到期、金鑰來源、TLS 組態、CSRF、權限、稽核寫入）。
  2. 無基準與排程比對，無法偵測組態漂移。
  3. 缺少異常告警與修復指引。
- **風險/影響**：憑證過期、密鑰不一致或保護被關閉時可能無人察覺；不符合 SL4。
- **建議**：
  1. **短期**：以外部監控檢查 TLS 憑證、端點健康與關鍵組態；建立維運檢查表。
  2. **中期**：產品新增啟動/定期 self-test，輸出健康端點與稽核；異常告警；支援組態基準與漂移偵測。
  3. **長期**：整合 SIEM 與自動修復（重新載入憑證/金鑰）。
- **驗收建議**：可模擬憑證過期/設定錯誤並觸發告警；self-test 結果可查詢；不影響正常服務。
- **對應**：[NFR-MNT-008](../SRS/05_Nonfunctional_Requirements.md)、[NFR-OPC-012](../SRS/05_Nonfunctional_Requirements.md)、[NFR-REL-002](../SRS/05_Nonfunctional_Requirements.md)、[SDS-10 §7](../SDS/10_Deployment_and_Operations.md)

### B.7 CR 3.09 RE(1) Audit records on write-once media（SL4｜不符合）
- **需求要求**：稽核記錄需存放於一次性寫入（WORM）或等效不可變媒體，防止竄改與刪除。
- **現況**：稽核存於 PostgreSQL，具 checksum 與 DB 權限控管；無 WORM、不可變備份或外部錨定。具 DB 管理權限者仍可刪除/重算。
- **不符合原因**：
  1. 儲存層不支援 WORM/物件鎖定。
  2. checksum 與資料同庫，缺乏外部信任根。
  3. 未強制即時匯出至 SIEM/日誌保存庫。
- **風險/影響**：管理員或入侵者可掩蓋痕跡，影響事件調查與不可否認；不符合 SL4。
- **建議**：
  1. **短期**：即時轉送稽核至外部 SIEM 或不可變物件儲存（Object Lock）；限制 DB 刪除權限。
  2. **中期**：稽核表 append-only、批次簽章、每日雜湊錨定（外部時間戳/公證）。
  3. **長期**：WORM 儲存與金鑰分離管理；定期復原與驗證演練。
- **驗收建議**：常規帳號無法修改/刪除稽核；外部副本可獨立驗證；篡改可被偵測；保留期限符合政策。
- **對應**：[FR-META-042](../SRS/03_Functional_Requirements.md)、[NFR-AUD-003](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §6.3、§11](../SDS/08_Security_Design.md)、[SDS-11 R-017](../SDS/11_Design_Decisions_and_Risks.md)

### B.8 CR 4.02 RE(2) Erase verification（SL3｜不符合）
- **需求要求**：刪除/抹除敏感資料後，需能驗證資料已不可回復。
- **現況**：支援登出、資料維護與刪除；未提供抹除證明、殘留掃描或金鑰銷毀驗證。
- **不符合原因**：
  1. 一般刪除僅移除資料列，備份、WAL、快取與磁碟區塊可能仍留存。
  2. 無標準化的驗證程序與工具。
  3. 未定義需驗證的資料範圍（Session、憑證、日誌、暫存、備份）。
- **風險/影響**：敏感資料可能於退役/歸還/轉移後被復原；不符合 SL3 資料清除要求。
- **建議**：
  1. **短期**：建立資料清除程序與檢查表；退役前執行磁碟加密/銷毀；保留清除紀錄。
  2. **中期**：提供清除 API/工具並輸出證據（影響筆數、時間、執行者、雜湊）；支援金鑰銷毀（crypto-shredding）；備份生命週期納入清除。
  3. **長期**：自動化殘留掃描與合規報表。
- **驗收建議**：清除後查詢/備份/快取無資料；金鑰銷毀後舊資料不可解；產出可稽核的清除證明。
- **對應**：[NFR-PRV-003](../SRS/05_Nonfunctional_Requirements.md)、[NFR-SEC-008](../SRS/05_Nonfunctional_Requirements.md)、[SDS-08 §12](../SDS/08_Security_Design.md)、[SDS-11 R-022](../SDS/11_Design_Decisions_and_Risks.md)
