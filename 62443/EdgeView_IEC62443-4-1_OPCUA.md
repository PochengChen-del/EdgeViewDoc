# EdgeView 對照 IEC 62443-4-1（OPC UA 版）

> 評估範圍：EdgeView 完整系統，datasource = **OPC UA**。  
> 包含：Qt Designer/Viewer、xnginx、Metadata Service、Runtime Proxy、OPC UA Data Agent（Runtime/History）、OPC UA Alarm Agent、OPC UA 設定 UI、PostgreSQL。  
> 依據：**IEC 62443-4-1:2018** 的 8 個 Practice、47 項要求（SM／SR／SD／SI／SVV／DM／SUM／SG）。本文件僅引用條款編號與摘要名稱，不重製標準全文。  
> 證據索引：`62443/Evidence.md`；4-2 對照：`62443/EdgeView_IEC62443-4-2_OPCUA.md`；安全開發流程證據：`CRA/CRA-01`~`CRA-04`。

> 本文件為**差距分析初稿**，用於整理 EdgeView 對 IEC 62443-4-1 的現況、證據與待補事項，不構成認證結論。正式認證仍需 CB 審查、成熟度（ML）評分、發布紀錄與可稽核證據。

## 1. 文件資訊

| 項目 | 內容 |
|------|------|
| 文件名稱 | EdgeView 對照 IEC 62443-4-1（OPC UA 版） |
| 文件版本 | 1.0 |
| 文件狀態 | 初稿（差距分析，含部分符合／不符合項目） |
| 日期 | 2026-11-20 |
| 產品名稱 | EdgeView（HMI/SCADA 圖控設計與執行平台） |
| 評估範圍 | EdgeView 完整系統與其安全開發生命週期（SDL） |
| 資料源基準 | OPC UA（`DataSource=3`） |
| 文件語言 | 繁體中文（技術名詞保留英文） |
| 關聯文件 | `SRS/`、`SDS/`、`CRA/CRA-01`~`CRA-04`、`62443/Evidence.md`、`62443/EdgeView_IEC62443-4-2_OPCUA.md`、`edgeview.cdx.json` |

## 2. 評估範圍與邊界

### 2.1 納入範圍

| 類別 | 內容 |
|------|------|
| 產品 | EdgeView 完整系統（Designer／Viewer／Server／OPC UA 子系統） |
| 元件 | Qt Client、xnginx、Metadata Service、Runtime Proxy、OPC UA Data Agent、OPC UA Alarm Agent、OPC UA 設定 UI、PostgreSQL |
| 介面 | REST/JSON、WebSocket、OPC UA、HTTPS/TLS、內部 Bearer、第三方資料源、Firebase FCM |
| 生命週期 | 需求、設計、實作、驗證、發布、更新、漏洞處理、文件與停用 |
| 證據類型 | SRS／SDS／CRA 文件、原始碼、設定、SBOM、手冊截圖、簽章流程 |

### 2.2 不在範圍（由外部承擔）

| 項目 | 責任方 |
|------|--------|
| 作業系統核心、安全開機、開機完整性 | OS／硬體 |
| 實體防拆、實體診斷介面、TPM／HSM | 硬體／機房 |
| 網路分段、防火牆、IDS/IPS、DDoS 清洗 | 網路設備／維運 |
| 第三方 OPC UA Server、第三方資料庫平台 | 外部系統維運 |
| 客戶自訂專案、外掛與腳本內容 | 客戶／系統整合商 |

> 上述項目在 EdgeView 文件中以「責任分工」與「環境假設」處理，並於 `CRA-04` 要求資產擁有者落實。  
> IEC 62443-4-1 的要求對象為產品開發者與維護者；本文件以 EdgeView 產品團隊為責任主體，系統整合商與資產擁有者的責任另見 §2.2 與 `CRA-04`。

## 3. 判定原則

| 狀態 | 說明 |
|------|------|
| 符合 | EdgeView 已有文件、機制或流程可佐證；正式認證仍需發布、簽核與紀錄。 |
| 部分符合 | 已有部分機制或文件初稿，但仍缺流程、紀錄、測試或獨立審查。 |
| 不符合 | 目前無對應機制或可佐證證據。 |
| 不適用 | 該要求不適用 EdgeView OPC UA 版。 |

**成熟度（ML）說明**：本表以「狀態」呈現差距，不進行 IEC 62443-4-1 的成熟度評分。正式認證時，ML 目標與實際評分應由 CB 依標準 Annex A 指標與證據認定。

## 4. 證據索引

| 證據 ID | 類別 | 內容 | 位置 |
|---------|------|------|------|
| EV-01 | 需求 | SRS-01~09（含 OPC UA、使用手冊） | `SRS/` |
| EV-02 | 設計 | SDS-01~16（含 OPC UA／手冊／追溯） | `SDS/` |
| EV-03 | 原始碼 | Client（Qt/QML） | `wax_hmi-designer-client/wa-hmi-designer` |
| EV-04 | 原始碼 | Metadata Service | `wax_metadata-service` |
| EV-05 | 原始碼 | Runtime Proxy | `wax_rutime_proxy` |
| EV-06 | 原始碼 | OPC UA Data Agent | `wax_opcua_agent/wax_opcua_agent` |
| EV-07 | 原始碼 | OPC UA Alarm Agent | `wax_opcua_alarm_agent` |
| EV-08 | 原始碼 | OPC UA 設定 UI | `wax_opcua_agent_ui` |
| EV-09 | 原始碼／設定 | xnginx 反向代理 | `wax_nginx_ingress` |
| EV-10 | 設定 | `xnginx*.yaml`、`config.yaml`、`appsettings.json` | 各服務目錄 |
| EV-11 | SBOM | CycloneDX 1.7 `edgeview.cdx.json` | Repo 根目錄 |
| EV-12 | 符合性 | IEC 62443-4-2 對照表 | `62443/EdgeView_IEC62443-4-2_OPCUA.md` |
| EV-13 | 測試 | 安全測試／掃描／滲透報告（目前缺） | 待補 |
| EV-14 | 流程 | SDL／SSP／安全開發證據（`CRA-01`~`CRA-04` 部分具備；訓練、SSP 待補） | `CRA/` |
| EV-15 | 授權 | 第三方授權／Notice（由 SBOM 授權欄位輔助） | `edgeview.cdx.json` |
| EV-16 | 流程 | 出貨軟體簽章（Windows Authenticode、Linux GPG） | 發行／出貨流程（待補文件） |
| EV-17 | 符合性 | 本文件（IEC 62443-4-1 差距分析） | `62443/EdgeView_IEC62443-4-1_OPCUA.md` |
| EV-SS | 手冊截圖 | 14 張 OPC UA UI／設定截圖（SS-01~SS-14） | `62443/evidence/` |

## 5. Practice 1 – Security management（SM）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| SM-1 | Development process：定義並遵循涵蓋產品生命週期的開發流程。 | 部分符合 | [SRS-06 §7](../SRS/06_Acceptance_and_Traceability.md)、[SDS-12](../SDS/12_Traceability.md)、[CRA-02 §5~§6](../CRA/CRA-02_Security_Checkpoint.md) | 已有需求變更、追溯與檢查點；缺正式 SDL 流程書（階段交付物、責任、移交、紀錄保存）。 |
| SM-2 | Identification of responsibilities：指派安全工作角色與責任。 | 部分符合 | [CRA-02 §4、§10](../CRA/CRA-02_Security_Checkpoint.md) | 角色已定義，但簽核欄空白；未指派人員、代理人與備援。 |
| SM-3 | Identification of applicability：界定適用產品、版本、變體與元件。 | 部分符合 | [README §1.1](../README.md)、[SDS-01 §2](../SDS/01_Introduction.md)、[62443/README §1](README.md)、[CRA-02 CP0-04](../CRA/CRA-02_Security_Checkpoint.md) | 有 OPC UA 基準與元件清單；缺版本／變體納入排除矩陣與理由。 |
| SM-4 | Security expertise：安全工作人員具備並維持所需專業。 | 不符合 | [CRA-02 CP0-03、§4](../CRA/CRA-02_Security_Checkpoint.md)、EV-14 | 無能力矩陣、訓練計畫、訓練紀錄與年度複核。 |
| SM-5 | Process scoping：界定 SDL 範圍（地點、組織、供應商、開發環境）。 | 部分符合 | [CRA-01 §2](../CRA/CRA-01_Threat_Modeling.md)、[README §1.1](../README.md)、[SDS-02](../SDS/02_System_Architecture.md) | 產品／介面範圍已定義；地點、團隊、供應商與開發環境邊界未明。 |
| SM-6 | File integrity：交付項目具完整性驗證機制。 | 部分符合 | [CRA-04 §6.1、§14.1](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP5-02](../CRA/CRA-02_Security_Checkpoint.md) | 安裝包具簽章（Authenticode／GPG、SHA-256、RFC 3161）；缺公開雜湊、驗證範例與簽章紀錄。 |
| SM-7 | Development environment security：開發、建置、測試環境受保護。 | 不符合 | [CRA-02 CP3](../CRA/CRA-02_Security_Checkpoint.md)、[CRA-03 §9](../CRA/CRA-03_Coding_Standards_Review_Record.md) | 無 repo／build／test 存取控制、分支保護、開發與生產分離及定期存取複審紀錄。 |
| SM-8 | Controls for private keys：簽章私鑰受保護、使用受限。 | 部分符合 | [CRA-04 §9](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP5-02](../CRA/CRA-02_Security_Checkpoint.md) | 由 Advantech／DigiCert 管理；缺金鑰儲存、存取、輪替、撤銷與簽章操作紀錄。 |
| SM-9 | Security requirements for externally provided components：外部元件具安全要求與弱點管理。 | 部分符合 | [edgeview.cdx.json](../edgeview.cdx.json)、[Evidence §3](Evidence.md)、[CRA-03 §9](../CRA/CRA-03_Coding_Standards_Review_Record.md) | SBOM 已產出；缺 OPC UA Agent／Alarm 合併、SCA 報告、VEX、供應商安全要求。 |
| SM-10 | Custom developed components from third-party suppliers：第三方客製元件符合安全要求。 | 部分符合 | [CRA-03 §3](../CRA/CRA-03_Coding_Standards_Review_Record.md)、[CRA-02 CP3-08](../CRA/CRA-02_Security_Checkpoint.md) | 尚無委外客製元件清單與安全評估；若確認無此類元件，可改判不適用。 |
| SM-11 | Assessing and addressing security-related issues：接收、評估、追蹤並處理安全議題。 | 部分符合 | [SDS-11 §2](../SDS/11_Design_Decisions_and_Risks.md)、[SDS-13](../SDS/13_Security_Remediation.md)、[CRA-01 §7](../CRA/CRA-01_Threat_Modeling.md)、[CRA-03 §7](../CRA/CRA-03_Coding_Standards_Review_Record.md) | 有風險登記、修復與發現清單；缺正式 intake、triage、owner、due date、closure 與 VDP 紀錄。 |
| SM-12 | Process verification：發布前確認 SDL 步驟與紀錄已完成。 | 部分符合 | [CRA-02 §6~§8](../CRA/CRA-02_Security_Checkpoint.md)、[SDS-12](../SDS/12_Traceability.md) | 有檢查點與追溯；缺獨立查核與各階段完成證據。 |
| SM-13 | Continuous improvement：定期檢討並改善 SDL。 | 部分符合 | [CRA-02 CP-6](../CRA/CRA-02_Security_Checkpoint.md)、[SDS-11 §5](../SDS/11_Design_Decisions_and_Risks.md)、[CRA-01 §13](../CRA/CRA-01_Threat_Modeling.md) | 有改善路線；缺定期檢討會議、指標、事故與測試回饋紀錄。 |

## 6. Practice 2 – Specification of security requirements（SR）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| SR-1 | Product security context：定義產品安全脈絡（用途、資產、假設、威脅環境）。 | 符合 | [SRS-01](../SRS/01_Introduction.md)、[SRS-02](../SRS/02_Overall_Description.md)、[SDS-08 §1~§2](../SDS/08_Security_Design.md)、[CRA-01 §5](../CRA/CRA-01_Threat_Modeling.md)、[CRA-04 §3~§4](../CRA/CRA-04_Product_Security_User_Manual.md) | 已定義元件、信任邊界、攻擊面、部署假設與責任分工；正式認證仍需簽核版本。 |
| SR-2 | Threat model：建立並維護產品威脅模型。 | 部分符合 | [CRA-01 §4~§7](../CRA/CRA-01_Threat_Modeling.md)、[SDS-08 §13](../SDS/08_Security_Design.md) | 有 71 筆 RID-EV、STRIDE、PID/TID 與風險矩陣；缺正式審查簽核、更新頻率與變更觸發紀錄。 |
| SR-3 | Product security requirements：定義產品安全需求。 | 部分符合 | [SRS-05 §5.2](../SRS/05_Nonfunctional_Requirements.md)、[SRS-07](../SRS/07_OPCUA_Subsystem.md)、[EdgeView 4-2 對照](EdgeView_IEC62443-4-2_OPCUA.md) | 有安全 NFR 與 OPC UA 需求；缺完整安全需求規格與「威脅 → 需求」追溯。 |
| SR-4 | Product security requirements content：安全需求具備必要內容與驗收條件。 | 部分符合 | [SRS-05](../SRS/05_Nonfunctional_Requirements.md)、[SRS-06 §5](../SRS/06_Acceptance_and_Traceability.md)、[SRS-07](../SRS/07_OPCUA_Subsystem.md) | 有需求與驗收方法；缺逐項假設、安全等級目標與驗收證據。 |
| SR-5 | Security requirements review：審查安全需求。 | 部分符合 | [CRA-02 CP-1](../CRA/CRA-02_Security_Checkpoint.md)、[SRS-06 §7](../SRS/06_Acceptance_and_Traceability.md) | 有變更流程與檢查點；缺需求審查會議紀錄與跨部門簽核。 |

## 7. Practice 3 – Secure by design（SD）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| SD-1 | Secure design principles：套用安全設計原則。 | 部分符合 | [SDS-08 §1~§2](../SDS/08_Security_Design.md)、[SDS-11 §1](../SDS/11_Design_Decisions_and_Risks.md) | 有信任邊界、最小權限、縱深防禦；缺設計原則 → 控制 → 威脅的正式對照。 |
| SD-2 | Defense in depth design：設計具縱深防禦。 | 部分符合 | [SDS-02](../SDS/02_System_Architecture.md)、[SDS-08 §2、§7、§8](../SDS/08_Security_Design.md)、[SDS-10](../SDS/10_Deployment_and_Operations.md) | 有 xnginx／TLS／loopback／分層服務；缺 zone/conduit 與每層防護對應威脅的文件。 |
| SD-3 | Security design review：審查安全設計。 | 部分符合 | [CRA-02 CP-2](../CRA/CRA-02_Security_Checkpoint.md)、[SDS-08](../SDS/08_Security_Design.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md) | 有設計審查檢查點；缺獨立審查紀錄與未關閉風險的正式核准。 |
| SD-4 | Secure design best practices：採用安全設計最佳實務。 | 部分符合 | [SDS-08 §14](../SDS/08_Security_Design.md)、[CRA-04 §21](../CRA/CRA-04_Product_Security_User_Manual.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md) | 有加固清單；仍有 HTTP `8880`、預設 DB 帳密、CORS 過寬等不安全預設待改善。 |

## 8. Practice 4 – Secure implementation（SI）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| SI-1 | Security implementation review：審查安全實作。 | 部分符合 | [CRA-03 §5~§7](../CRA/CRA-03_Coding_Standards_Review_Record.md)、[SDS-13](../SDS/13_Security_Remediation.md) | 有人工審查與發現清單；缺 SAST／SCA 報告、審查者簽核與 CI 強制 gate。 |
| SI-2 | Secure coding standards：建立並遵循安全編碼標準。 | 部分符合 | [CRA-03 §3~§4、§8](../CRA/CRA-03_Coding_Standards_Review_Record.md) | 有 Go／C++／C#／JS／SQL 編碼標準與 CWE 清單；工具未全面接入，Open findings 仍多。 |

## 9. Practice 5 – Security verification and validation testing（SVV）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| SVV-1 | Security requirements testing：驗證安全需求。 | 不符合 | [CRA-02 CP-4](../CRA/CRA-02_Security_Checkpoint.md)、[SRS-06 §5](../SRS/06_Acceptance_and_Traceability.md)、EV-13 | 無「安全需求 → 測試案例 → 結果」報告；CP4-01~CP4-11 多數待補。 |
| SVV-2 | Threat mitigation testing：驗證威脅緩解措施。 | 不符合 | [CRA-01 §7](../CRA/CRA-01_Threat_Modeling.md)、[CRA-02 CP-4](../CRA/CRA-02_Security_Checkpoint.md)、EV-13 | 未針對 RID／MID 執行緩解驗證；目前僅有設計與程式碼審查。 |
| SVV-3 | Vulnerability testing：執行弱點測試。 | 部分符合 | [edgeview.cdx.json](../edgeview.cdx.json)、[CRA-03 §9](../CRA/CRA-03_Coding_Standards_Review_Record.md)、[Evidence §3](Evidence.md) | SBOM 已產出；缺 SAST／DAST／SCA、Trivy／Grype 報告與 VEX。 |
| SVV-4 | Penetration testing：執行滲透測試。 | 不符合 | [CRA-02 CP4-08](../CRA/CRA-02_Security_Checkpoint.md)、EV-13 | 無滲透測試範圍、報告與改善追蹤。 |
| SVV-5 | Independence of testers：測試人員具適當獨立性。 | 部分符合 | [CRA-02 §4、§10](../CRA/CRA-02_Security_Checkpoint.md) | 有獨立審查者角色；未指派人員、無測試獨立性與簽核紀錄。 |

## 10. Practice 6 – Management of security-related issues（DM）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| DM-1 | Receiving notifications of security-related issues：接收安全議題通知。 | 部分符合 | [CRA-04 §18.1](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP6-03](../CRA/CRA-02_Security_Checkpoint.md) | 有 VDP 草案；聯絡窗口與公開公告仍為 placeholder，無正式受理管道。 |
| DM-2 | Reviewing security-related issues：審查安全議題。 | 部分符合 | [SDS-11 §2](../SDS/11_Design_Decisions_and_Risks.md)、[CRA-03 §7](../CRA/CRA-03_Coding_Standards_Review_Record.md)、[SDS-13](../SDS/13_Security_Remediation.md) | 有風險／發現登錄；缺安全議題 triage 會議、分類與影響範圍紀錄。 |
| DM-3 | Assessing security-related issues：評估安全議題影響。 | 部分符合 | [CRA-01 §4](../CRA/CRA-01_Threat_Modeling.md)、[SDS-11 §2](../SDS/11_Design_Decisions_and_Risks.md) | 有可能性 × 影響評分；缺 CVSS、供應鏈／客戶影響評估與 SLA 判定。 |
| DM-4 | Addressing security-related issues：處理安全議題。 | 部分符合 | [SDS-13](../SDS/13_Security_Remediation.md)、[SDS-11 §5](../SDS/11_Design_Decisions_and_Risks.md) | 已修 R-001／R-002／R-003／R-006／R-008／R-009；其餘 Open 且缺 owner、due date 與風險接受紀錄。 |
| DM-5 | Disclosing security-related issues：揭露安全議題。 | 部分符合 | [CRA-04 §18.1、§19](../CRA/CRA-04_Product_Security_User_Manual.md) | 有揭露原則與殘餘風險；缺安全公告範本、CVE／CVSS、發布管道與時程。 |
| DM-6 | Periodic review of security defect management practice：週期性檢討缺陷管理流程。 | 不符合 | [CRA-02 CP-6](../CRA/CRA-02_Security_Checkpoint.md)、EV-14 | 無安全缺陷管理流程的週期性檢討紀錄與改善追蹤。 |

## 11. Practice 7 – Security update management（SUM）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| SUM-1 | Security update qualification：驗證安全更新。 | 部分符合 | [CRA-04 §14.2](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP-5](../CRA/CRA-02_Security_Checkpoint.md) | 有更新步驟與煙霧測試；缺更新資格驗證紀錄與回歸報告。 |
| SUM-2 | Security update documentation：提供安全更新說明。 | 部分符合 | [CRA-04 §14](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP5-03](../CRA/CRA-02_Security_Checkpoint.md) | 有更新說明要求；Release Note 範本與安全修正段落待補。 |
| SUM-3 | Dependent component or operating system security update documentation：提供相依元件／OS 更新說明。 | 部分符合 | [CRA-04 §3.1、§14.3、§19](../CRA/CRA-04_Product_Security_User_Manual.md) | 有 OS／第三方責任說明；缺相依元件與 OS 更新指引、相容性矩陣。 |
| SUM-4 | Security update delivery：安全更新交付具完整性與來源驗證。 | 部分符合 | [CRA-04 §6.1、§14.1](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP5-02](../CRA/CRA-02_Security_Checkpoint.md) | 有簽章、HTTPS、SHA-256 與時間戳；缺 anti-rollback 與版本固定。 |
| SUM-5 | Timely delivery of security patches：及時交付安全修補。 | 部分符合 | [CRA-04 §14.3](../CRA/CRA-04_Product_Security_User_Manual.md) | 有建議 SLA 表；未形成正式支援政策、合約承諾與實際度量。 |

## 12. Practice 8 – Security guidelines（SG）

| ID | 要求摘要 | 狀態 | 證據 | 缺口／建議 |
|----|----------|------|------|-------------|
| SG-1 | Product defense in depth：說明產品縱深防禦。 | 部分符合 | [CRA-04 §3、§8~§13](../CRA/CRA-04_Product_Security_User_Manual.md)、[SDS-08](../SDS/08_Security_Design.md) | 有縱深防禦敘述；缺「產品防護層 → 威脅 → 環境責任」的完整對照。 |
| SG-2 | Defense in depth measures expected in the environment：說明環境預期防護。 | 符合 | [CRA-04 §4、§8、§21](../CRA/CRA-04_Product_Security_User_Manual.md) | 已列網路分段、防火牆、VPN、OS 加固、NTP 等環境責任。 |
| SG-3 | Security hardening guidelines：提供安全加固指引。 | 符合 | [CRA-04 §6、§9、§10、§21](../CRA/CRA-04_Product_Security_User_Manual.md) | 有初始設定、TLS／憑證、OPC UA 安全與加固清單。 |
| SG-4 | Secure disposal guidelines：提供安全停用與資料處置指引。 | 符合 | [CRA-04 §20](../CRA/CRA-04_Product_Security_User_Manual.md) | 有停用、帳號／憑證撤銷、資料抹除與紀錄要求。 |
| SG-5 | Secure operation guidelines：提供安全操作指引。 | 符合 | [CRA-04 §11~§17、§21](../CRA/CRA-04_Product_Security_User_Manual.md) | 有 Session／CSRF、日誌、備份與操作建議。 |
| SG-6 | Account management guidelines：提供帳號管理指引。 | 符合 | [CRA-04 §6.2、§7](../CRA/CRA-04_Product_Security_User_Manual.md) | 有角色、密碼／鎖定、MFA 補償與帳號管理。 |
| SG-7 | Documentation review：審查並維護安全文件。 | 部分符合 | [CRA-04 §24](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP5-04、CP5-05](../CRA/CRA-02_Security_Checkpoint.md) | 有版本紀錄；缺文件審查／核准紀錄與定期更新機制。 |

## 13. 統計摘要

| 類別 | 數量 |
|------|------|
| 符合 | 6 |
| 部分符合 | 35 |
| 不符合 | 6 |
| 不適用 | 0 |
| **合計** | **47** |

### 13.1 符合項目

| ID | 要求摘要 |
|----|----------|
| SR-1 | Product security context |
| SG-2 | Defense in depth measures expected in the environment |
| SG-3 | Security hardening guidelines |
| SG-4 | Secure disposal guidelines |
| SG-5 | Secure operation guidelines |
| SG-6 | Account management guidelines |

> 「符合」表示文件或機制已存在；正式認證仍需納入版本發布、簽核與可稽核紀錄。

### 13.2 不符合項目

| ID | 要求摘要 | 主要缺口 |
|----|----------|----------|
| SM-4 | Security expertise | 無訓練計畫、能力矩陣與紀錄 |
| SM-7 | Development environment security | 無開發環境存取控制與分離證據 |
| SVV-1 | Security requirements testing | 無安全需求測試報告 |
| SVV-2 | Threat mitigation testing | 無威脅緩解驗證 |
| SVV-4 | Penetration testing | 無滲透測試報告 |
| DM-6 | Periodic review of security defect management practice | 無週期性檢討紀錄 |

## 14. 主要缺口與優先順序

| 優先 | 缺口 | 對應要求 | 建議 |
|------|------|----------|------|
| P0 | 安全測試與驗證報告 | SVV-1、SVV-2、SVV-3、SVV-4、SVV-5 | 建立安全需求→測試案例→結果追溯；完成 SAST／DAST／SCA、滲透測試與回歸報告。 |
| P0 | 安全開發流程與角色簽核 | SM-1、SM-2、SM-4、SM-7、SM-12 | 建立正式 SDL 流程書、角色指派與代理人、訓練紀錄、開發環境存取控制與發布前查核。 |
| P0 | 安全議題管理與 VDP | SM-11、DM-1~DM-6 | 建立 intake、triage、owner、due date、closure、公告與週期性檢討流程。 |
| P1 | SBOM 與供應鏈弱點管理 | SM-9、SM-10、SVV-3 | 合併 OPC UA Agent／Alarm SBOM；補 SCA、VEX、供應商安全要求與委外元件清單。 |
| P1 | 更新與簽章治理 | SM-6、SM-8、SUM-1~SUM-5 | 補公開雜湊／驗證範例、金鑰生命週期、更新資格紀錄、anti-rollback 與正式修補 SLA。 |
| P1 | 需求／設計／文件審查紀錄 | SR-5、SD-3、SG-7 | 建立跨部門審查、簽核、版本發布與定期複審紀錄。 |
| P2 | 持續改善與流程指標 | SM-13、SM-12 | 定義 SDL 指標（缺陷密度、修補時間、測試覆蓋）並定期檢討。 |

## 15. 與 IEC 62443-4-2、CRA 的對照

| 4-1 Practice | 主要對應 4-2／CRA 證據 | 說明 |
|--------------|------------------------|------|
| SM-1~SM-13 | [CRA-02](../CRA/CRA-02_Security_Checkpoint.md)、[SDS-11](../SDS/11_Design_Decisions_and_Risks.md) | 流程、角色、風險、檢查點與持續改善。 |
| SR-1~SR-5 | [CRA-01](../CRA/CRA-01_Threat_Modeling.md)、[SRS-05](../SRS/05_Nonfunctional_Requirements.md)、[SRS-07](../SRS/07_OPCUA_Subsystem.md) | 安全脈絡、威脅模型與安全需求。 |
| SD-1~SD-4 | [SDS-08](../SDS/08_Security_Design.md)、[CRA-02 CP-2](../CRA/CRA-02_Security_Checkpoint.md) | 安全設計、縱深防禦與設計審查。 |
| SI-1~SI-2 | [CRA-03](../CRA/CRA-03_Coding_Standards_Review_Record.md)、[SDS-13](../SDS/13_Security_Remediation.md) | 實作審查、編碼標準與修復。 |
| SVV-1~SVV-5 | [CRA-02 CP-4](../CRA/CRA-02_Security_Checkpoint.md)、EV-13 | 安全測試、弱點測試、滲透測試與獨立性。 |
| DM-1~DM-6 | [SDS-11](../SDS/11_Design_Decisions_and_Risks.md)、[SDS-13](../SDS/13_Security_Remediation.md)、[CRA-04 §18](../CRA/CRA-04_Product_Security_User_Manual.md) | 安全議題接收、評估、處理、揭露與檢討。 |
| SUM-1~SUM-5 | [CRA-04 §14](../CRA/CRA-04_Product_Security_User_Manual.md)、[CRA-02 CP-5](../CRA/CRA-02_Security_Checkpoint.md) | 更新資格、文件、交付與修補時效。 |
| SG-1~SG-7 | [CRA-04](../CRA/CRA-04_Product_Security_User_Manual.md) | 產品與環境防護、加固、停用、操作、帳號與文件審查。 |

> IEC 62443-4-2 的 `CCSC 4 Software development process` 即對應本文件的整體 SDL 證據；4-2 的產品安全需求則由 SR／SD／SI 支撐。

## 16. 使用與維護建議

1. **定位為差距分析**：本文件不是證書或符合性宣告；投標／認證時應搭配測試報告、流程紀錄與 CB 要求。
2. **建立證據連結**：每項要求應連到具體版本的文件、測試報告、簽核紀錄或程式碼位置。
3. **每次版本更新重跑**：至少在每個主要版本、重大架構變更、重大漏洞修復後更新本表。
4. **優先關閉 P0**：SVV 測試、SDL 流程、安全議題管理是目前最主要缺口。
5. **與 4-2 同步**：4-2 的 `CCSC 4`、`CR 3.04`、`CR 3.10` 等項目應與本文件的 SM／SUM 證據一致。
6. **保留簽核與版本紀錄**：正式認證需可追溯的核准、發布與訓練紀錄。

---

## 附錄 A：不符合項目明細（6 項）

### A.1 SM-4 Security expertise（不符合）

- **現況**：`CRA-02` 已定義安全負責人、開發負責人、測試負責人等角色，但無人員能力矩陣、訓練計畫、訓練紀錄與年度複核。
- **缺口**：無法證明執行安全工作的人員具備對應專業，亦無代理人與備援安排。
- **建議**：建立角色能力矩陣（Go／C++／QML／C#／Web／OT 安全）、年度訓練計畫、訓練與測驗紀錄；對高風險工作（威脅建模、滲透測試、密碼學）指定具資格人員或外部專家。
- **證據**：[CRA-02 §4、§10](../CRA/CRA-02_Security_Checkpoint.md)、EV-14。

### A.2 SM-7 Development environment security（不符合）

- **現況**：有版本控制與 CI 工具規劃，但未見 repo、build server、test system 的存取控制、分支保護、開發／生產分離與定期存取複審紀錄。
- **缺口**：無法證明開發環境免於未授權存取、原始碼／建置工具／產物遭竄改的風險已受控。
- **建議**：定義開發環境存取政策；啟用 MFA、最小權限、分支保護與強制審查；分離開發／測試／生產憑證；每半年複審存取名單並留存紀錄。
- **證據**：[CRA-02 CP3](../CRA/CRA-02_Security_Checkpoint.md)、[CRA-03 §9](../CRA/CRA-03_Coding_Standards_Review_Record.md)。

### A.3 SVV-1 Security requirements testing（不符合）

- **現況**：`CRA-02 CP-4` 多數測試項目仍為待補；尚無「安全需求 → 測試案例 → 測試結果」的完整報告。
- **缺口**：無法證明 SRS-05／SRS-07 的安全需求在正常、異常與誤用情境下均被驗證。
- **建議**：建立安全需求測試矩陣，逐項對應測試案例與結果；優先完成認證／授權／Session／CSRF／HTTPS／OPC UA 加密與鎖定測試。
- **證據**：[CRA-02 CP-4](../CRA/CRA-02_Security_Checkpoint.md)、[SRS-06 §5](../SRS/06_Acceptance_and_Traceability.md)、EV-13。

### A.4 SVV-2 Threat mitigation testing（不符合）

- **現況**：`CRA-01` 已登錄 71 筆 RID-EV 與對應 MID-EV，但未針對威脅緩解措施執行驗證測試。
- **缺口**：無法證明威脅模型中的高風險項目在實作中有效降低。
- **建議**：從高風險 RID 建立測試案例，驗證 MID 的有效性；將結果回填 `CRA-01` 與 `SDS-11`，未通過者納入修復與回歸。
- **證據**：[CRA-01 §7](../CRA/CRA-01_Threat_Modeling.md)、[CRA-02 CP-4](../CRA/CRA-02_Security_Checkpoint.md)、EV-13。

### A.5 SVV-4 Penetration testing（不符合）

- **現況**：尚無滲透測試範圍、執行紀錄、報告與改善追蹤。
- **缺口**：無法證明產品在真實攻擊條件下可抵抗繞過防護機制的嘗試。
- **建議**：委由獨立測試者執行滲透測試，範圍涵蓋 Web／API／WebSocket／OPC UA／憑證與權限；弱點依風險修復並回歸，無法修復者登錄風險接受。
- **證據**：[CRA-02 CP4-08](../CRA/CRA-02_Security_Checkpoint.md)、EV-13。

### A.6 DM-6 Periodic review of security defect management practice（不符合）

- **現況**：有風險登記與修復紀錄，但無安全缺陷管理流程的週期性檢討。
- **缺口**：無法證明缺陷管理流程持續有效，亦無趨勢、根因與改善追蹤。
- **建議**：每季或每版本檢討安全缺陷的 intake、分級、修復時間、重複問題與流程缺口；留存會議紀錄與改善行動。
- **證據**：[CRA-02 CP-6](../CRA/CRA-02_Security_Checkpoint.md)、EV-14。

## 附錄 B：部分符合項目明細（35 項）

### B.1 Security management（SM）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| SM-1 | 有需求變更、追溯與檢查點。 | 缺正式 SDL 流程書與階段交付物。 | 建立階段、責任、交付物、移交與紀錄保存要求。 |
| SM-2 | 角色已定義。 | 簽核空白、未指派人員與代理人。 | 完成角色指派、代理人與簽核。 |
| SM-3 | 有 OPC UA 基準與元件清單。 | 缺版本／變體納入排除矩陣。 | 建立產品、版本、變體與元件的適用性清單與理由。 |
| SM-5 | 產品與介面範圍已定義。 | 地點、團隊、供應商、開發環境邊界未明。 | 補充 SDL 範圍聲明與外部介面圖。 |
| SM-6 | 安裝包具簽章與時間戳。 | 缺公開雜湊、驗證範例與簽章紀錄。 | 發布 SHA-256／簽章檔與客戶驗證步驟。 |
| SM-8 | 簽章由 Advantech／DigiCert 管理。 | 缺金鑰生命週期與操作紀錄。 | 建立金鑰儲存、存取、輪替、撤銷與簽章紀錄。 |
| SM-9 | SBOM 已產出。 | 缺 OPC UA Agent／Alarm 合併、SCA、VEX。 | 合併 SBOM 並附 Trivy／Grype 報告與 VEX。 |
| SM-10 | 有第三方元件盤點基礎。 | 缺委外客製元件清單與安全評估。 | 建立供應商安全要求與委外元件清單；若無則改判不適用。 |
| SM-11 | 有風險、修復與發現清單。 | 缺正式 intake、triage、owner、due date、closure。 | 建立安全議題追蹤流程與 VDP 紀錄。 |
| SM-12 | 有檢查點與追溯。 | 缺獨立查核與階段完成證據。 | 發布前由獨立審查者確認 SDL 紀錄。 |
| SM-13 | 有改善路線。 | 缺定期檢討、指標與回饋紀錄。 | 定義 SDL 指標並定期檢討。 |

### B.2 Specification of security requirements（SR）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| SR-2 | 有 71 筆 RID-EV、STRIDE 與風險矩陣。 | 缺審查簽核、更新頻率與變更觸發。 | 定義威脅模型更新時機並留存審查紀錄。 |
| SR-3 | 有安全 NFR 與 OPC UA 需求。 | 缺完整安全需求規格與威脅→需求追溯。 | 建立需求規格與 CRA-01 RID 的對照。 |
| SR-4 | 有需求與驗收方法。 | 缺逐項假設、安全等級目標與驗收證據。 | 補齊每項安全需求的內容與驗收準則。 |
| SR-5 | 有變更流程與檢查點。 | 缺需求審查會議紀錄與跨部門簽核。 | 建立需求審查紀錄與核准流程。 |

### B.3 Secure by design（SD）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| SD-1 | 有信任邊界、最小權限與縱深防禦。 | 缺設計原則→控制→威脅對照。 | 建立安全設計原則檢查表。 |
| SD-2 | 有 xnginx／TLS／loopback／分層服務。 | 缺 zone/conduit 與每層防護對應威脅。 | 補充分層防護與威脅對照圖。 |
| SD-3 | 有設計審查檢查點。 | 缺獨立審查與未關閉風險核准。 | 建立設計審查紀錄與風險接受簽核。 |
| SD-4 | 有加固清單。 | 仍有不安全預設。 | 關閉 HTTP 8880、移除預設 DB 帳密、收斂 CORS。 |

### B.4 Secure implementation（SI）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| SI-1 | 有人工審查與發現清單。 | 缺 SAST／SCA 報告、簽核與 CI gate。 | 接入工具並將高風險發現設為合併門檻。 |
| SI-2 | 有編碼標準與 CWE 清單。 | 工具未全面接入，Open findings 仍多。 | 完成工具鏈接入與發現修復。 |

### B.5 Security verification and validation testing（SVV）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| SVV-3 | SBOM 已產出。 | 缺 SAST／DAST／SCA、Trivy／Grype 與 VEX。 | 建立掃描流程並附報告。 |
| SVV-5 | 有獨立審查者角色。 | 未指派、無獨立性紀錄。 | 指派獨立測試者並留存測試簽核。 |

### B.6 Management of security-related issues（DM）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| DM-1 | 有 VDP 草案。 | 聯絡窗口與公告仍為 placeholder。 | 建立正式受理管道與公開窗口。 |
| DM-2 | 有風險與發現登錄。 | 缺 triage、分類與影響範圍紀錄。 | 建立安全議題審查會議與紀錄。 |
| DM-3 | 有可能性 × 影響評分。 | 缺 CVSS、供應鏈與客戶影響評估。 | 補 CVSS 與 SLA 判定。 |
| DM-4 | 已修多項高風險項目。 | 其餘 Open 且缺 owner、due date。 | 建立修復追蹤與風險接受紀錄。 |
| DM-5 | 有揭露原則與殘餘風險。 | 缺公告範本、CVE／CVSS 與時程。 | 建立安全公告流程與範本。 |

### B.7 Security update management（SUM）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| SUM-1 | 有更新步驟與煙霧測試。 | 缺更新資格驗證與回歸報告。 | 建立更新測試與核准紀錄。 |
| SUM-2 | 有更新說明要求。 | Release Note 範本與安全段落待補。 | 建立 Release Note 範本。 |
| SUM-3 | 有 OS／第三方責任說明。 | 缺相依元件與 OS 更新指引。 | 建立相容性矩陣與更新指引。 |
| SUM-4 | 有簽章、HTTPS 與時間戳。 | 缺 anti-rollback 與版本固定。 | 加入版本 pinning 與失敗回復。 |
| SUM-5 | 有建議 SLA 表。 | 未形成正式政策與度量。 | 發布正式修補 SLA 並定期統計。 |

### B.8 Security guidelines（SG）

| ID | 現況 | 缺口 | 建議 |
|----|------|------|------|
| SG-1 | 有縱深防禦敘述。 | 缺產品防護層→威脅→環境責任對照。 | 建立防禦層對照表。 |
| SG-7 | 有版本紀錄。 | 缺文件審查／核准與定期更新機制。 | 建立文件審查與複審週期。 |

## 附錄 C：證據與追溯對照

| 證據 | 可佐證要求 |
|------|------------|
| `CRA-01` 威脅模型 | SR-1、SR-2、SR-3、DM-2、DM-3、SM-11、SM-13 |
| `CRA-02` 安全檢查點 | SM-1、SM-2、SM-3、SM-5、SM-6、SM-7、SM-11、SM-12、SM-13、SD-3、SVV-1、SVV-4、SVV-5、SUM-1、SUM-5、SG-7 |
| `CRA-03` 編碼標準審查 | SM-9、SM-10、SI-1、SI-2、SVV-3、DM-2 |
| `CRA-04` 產品安全手冊 | SG-1~SG-7、SUM-1~SUM-5、DM-1、DM-5、SD-4、SM-6、SM-8 |
| `SRS-05` 非功能需求 | SR-1、SR-3、SR-4、SVV-1 |
| `SRS-06` 驗收與追溯 | SM-1、SR-4、SR-5、SVV-1 |
| `SRS-07` OPC UA 子系統 | SR-3、SR-4、SVV-1 |
| `SDS-08` 安全設計 | SR-1、SR-2、SD-1、SD-2、SD-3、SD-4、SG-1 |
| `SDS-11` 設計決策與風險 | SM-11、SM-13、SD-1、SD-3、DM-2、DM-3、DM-4 |
| `SDS-12` 需求追溯 | SM-1、SM-12 |
| `SDS-13` 安全修復 | SM-11、SI-1、DM-2、DM-4 |
| `edgeview.cdx.json` | SM-9、SVV-3 |
| `62443/Evidence.md` | 全部要求的證據索引 |
| EV-13 測試報告（待補） | SVV-1~SVV-5 |
| EV-16 出貨簽章（待補文件） | SM-6、SM-8、SUM-4 |

## 附錄 D：版本紀錄

| 版本 | 日期 | 變更 |
|------|------|------|
| 1.0 | 2026-11-20 | 初版，建立 8 個 Practice、47 項要求對照、統計、缺口與附錄。 |
