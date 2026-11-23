# EdgeView IEC 62443-4-2 證據索引（OPC UA 版）

> 本文件補充 `EdgeView_IEC62443-4-2_OPCUA.md` 的「證據」欄位，將 141 項需求對應到 SRS、SDS、程式碼、設定與 SBOM。  
> 評估範圍：**datasource = OPC UA 的 EdgeView 完整系統**。

## 1. 證據分類

| 證據 ID | 類別 | 內容 | 位置 |
|---------|------|------|------|
| EV-01 | 需求 | SRS-01~08（含 OPC UA、使用手冊） | `SRS/` |
| EV-02 | 設計 | SDS-01~15（含 OPC UA/手冊/追溯） | `SDS/` |
| EV-03 | 原始碼 | Client（Qt/QML） | `wax_hmi-designer-client/wa-hmi-designer` |
| EV-04 | 原始碼 | Metadata Service | `wax_metadata-service` |
| EV-05 | 原始碼 | Runtime Proxy | `wax_rutime_proxy` |
| EV-06 | 原始碼 | OPC UA Data Agent | `wax_opcua_agent/wax_opcua_agent` |
| EV-07 | 原始碼 | OPC UA Alarm Agent | `wax_opcua_alarm_agent` |
| EV-08 | 原始碼 | OPC UA 設定 UI | `wax_opcua_agent_ui` |
| EV-09 | 原始碼/設定 | xnginx 反向代理 | `wax_nginx_ingress` |
| EV-10 | 設定 | `xnginx*.yaml`、`config.yaml`、`appsettings.json` | 各服務目錄 |
| EV-11 | SBOM | CycloneDX 1.7 `edgeview.cdx.json` | Repo 根目錄 |
| EV-12 | 符合性 | IEC 62443-4-1／4-2 對照表 | `62443/EdgeView_IEC62443-4-1_OPCUA.md`、`62443/EdgeView_IEC62443-4-2_OPCUA.md` |
| EV-SS | 手冊截圖 | 14 張 OPC UA UI/設定截圖（SS-01~SS-14） | `62443/evidence/` |
| EV-13 | 測試 | 測試/驗證報告（目前缺） | 待補 |
| EV-14 | 流程 | SDLC/SSP/安全開發證據（目前缺） | 待補 |
| EV-15 | 授權 | 第三方授權/Notice（由 SBOM 授權欄位輔助） | `edgeview.cdx.json` |
| EV-16 | 流程 | 出貨軟體簽章（Windows Authenticode、Linux GPG） | 發行/出貨流程（待補文件） |
| EV-17 | CRA | CRA-01~CRA-04 與 B-03-F01 安全合規檢視表 | `CRA/` |

## 2. 需求群組 ↔ 證據

| IEC 群組 | 主要證據 | 證據強度 | 備註 |
|----------|----------|----------|------|
| CCSC | EV-01、EV-02、EV-06~09、EV-11、EV-14 | 部分 | 架構/元件清單有；IEC 62443-3-3 系統風險分析與 SDL 證據待補。 |
| FR1 認證 | EV-01（SRS-07）、EV-02（SDS-08/14）、EV-04/06/07、EV-13 | 部分 | 登入、Session、OPC UA User/LoginLock 有實作；MFA/密碼政策/測試報告缺。 |
| FR2 使用控制 | EV-01（SRS-03/06/08）、EV-02（SDS-04/08/14/15）、EV-04/06/07、EV-13 | 部分 | 角色/專案/安全區、稽核有；並行 Session/WORM/測試缺。 |
| FR3 系統完整性 | EV-01（SRS-05）、EV-02（SDS-08/14）、EV-03~09、EV-11、EV-13、EV-16 | 部分 | TLS/CSRF/輸入驗證/錯誤處理/出貨簽章有；自我測試/測試報告缺。 |
| FR4 資料機密性 | EV-01（SRS-05/07）、EV-02（SDS-08）、EV-04/06、EV-10/11 | 部分 | HTTPS/AES/OPC UA 加密有；密碼雜湊與抹除驗證缺。 |
| FR5 資料流限制 | EV-01（SRS-02/04）、EV-02（SDS-02/10）、EV-09/10 | 符合（設計） | xnginx 路由、loopback、TLS、限流有；網路分段由防火牆配合。 |
| FR6 事件回應 | EV-01（SRS-05/06）、EV-02（SDS-08/09）、EV-04/07、EV-13 | 部分 | 稽核查詢/匯出有；SIEM/持續監控/測試報告缺。 |
| FR7 資源可用性 | EV-01（SRS-05/06）、EV-02（SDS-10/11/12）、EV-04/05/09/11 | 部分 | 備份/還原/限流有；容量告警、SBOM 完整性與測試缺。 |

> 威脅與風險追溯：`CRA/CRA-01` v2.0（71 RID-EV／28 MID-EV）；安全需求檢視：`CRA/B-03-F01`（SC1~SC2、CRA Annex I、FR1~FR7、OPC-1~OPC-8）；流程證據：`CRA/CRA-02`~`CRA-04`。

## 2.1 手冊截圖證據

截圖來源：`EdgeView User Manual.docx`，存放於 `62443/evidence/`。詳細需求對照見 `SRS/09_Manual_Screenshot_Evidence.md` 與 `SDS/16_Manual_Screenshot_Mapping.md`。

| 證據 | 截圖 | 手冊章節 | 可佐證 |
|------|------|----------|--------|
| SS-01 | `01_opcua_new_project.png` | 15.3.2 | OPC UA 專案建立（CR 1.03、2.01） |
| SS-02 | `02_opcua_display_range.png` | 15.3.2 | 專案顯示範圍設定 |
| SS-03 | `03_opcua_new_connection.png` | 15.3.2/1.6 | Sign and encrypt、Basic256、帳密、PFX（CR 1.08、1.09、3.01、4.01、4.03） |
| SS-04 | `04_opcua_tag_subscription.png` | 15.3.2 | Tag 訂閱/多選（CR 2.01） |
| SS-05 | `05_opcua_tag_scaling.png` | 15.3.2 | Span/顯示/Scaling（CR 3.05、3.06） |
| SS-06 | `06_opcua_const_points.png` | 15.3.2 | 常數點 Security area/level（CR 2.01、2.01 RE(2)） |
| SS-07 | `07_service_diagnostics.png` | 15.6 | 服務清單/開關（CR 3.10、7.07、7.08） |
| SS-08 | `08_certificate_service.png` | 15.6 | 憑證建立/上傳/內建（CR 1.08、1.09、4.03） |
| SS-09 | `09_edgeview_management_menu.png` | 16.2 | 管理選單/權限入口（CR 1.03、2.01） |
| SS-10 | `10_data_maintenance.png` | 16.2.2 | 備份/保留/刪除（CR 2.09、7.03） |
| SS-11 | `11_create_drawing.png` | 3.1 | 圖紙建立（功能證據） |
| SS-12 | `12_opcua_version_registry.png` | 1.5.2 | OPC UA 版本安裝識別（CR 3.10） |
| SS-13 | `13_database_export.png` | 15.1 | 工程 DB 匯出（CR 7.03） |
| SS-14 | `14_database_import.png` | 15.2 | 工程 DB 匯入（CR 7.03、7.04） |

> 截圖為 UI/設定證據；負向測試、鎖定、權限拒絕、憑證錯誤仍需測試報告。

## 3. SBOM 證據

`edgeview.cdx.json`：

| 項目 | 內容 |
|------|------|
| 格式 | CycloneDX `specVersion=1.7` |
| 版本 | `version=1` |
| Serial | `urn:uuid:ce8558fc-a814-4b1a-82fd-869b734612cd` |
| 產出時間 | `2026-11-12T03:28:54Z` |
| 工具 | Syft 1.55.0、Trivy 0.75.0、`merge_sbom.py`、`merge_edgeview_sbom.py` |
| 元件數 | 929 components、930 dependencies |
| 弱點 | `vulnerabilities=0`（未包含掃描結果欄位，不能視為無弱點） |
| 合併來源 | `edgeview-client`、`wax_hmi_log`、`wax_metadata-service`、`wax_nginx_ingress`、`wax_rutime_proxy` |

**證據用途**

- CR 7.08 Control system component inventory：可作元件盤點初稿。
- CR 3.04 / 3.10：可佐證元件版本，但**不能**取代出貨簽章與完整性驗證紀錄。
- 授權/第三方元件：SBOM `licenses` 欄位可作授權清冊初稿。

**缺口**

1. `metadata.properties` 的 `sbom:merged-from` **未包含** `wax_opcua_agent`、`wax_opcua_alarm_agent`；OPC UA 認證需補合併。
2. `vulnerabilities` 為 0 不代表無弱點，需附 Trivy/Grype 掃描報告與 VEX。
3. 未見 SBOM 簽章/雜湊與版本綁定證據。
4. 未見授權 Notice 全文（僅 SBOM license 欄位）。

## 4. 各類證據可回答的需求

| 證據 | 可回答 |
|------|--------|
| EV-01 SRS | 需求是否存在、優先級、介面、NFR、驗收 |
| EV-02 SDS | 設計如何達成、模組/資料/流程/部署 |
| EV-03~08 原始碼 | 實作是否存在（登入、Session、權限、稽核、加密、錯誤處理） |
| EV-09~10 設定 | xnginx/TLS/路由、DB、OPC UA Agent、Alarm 設定 |
| EV-11 SBOM | 元件/版本/授權/盤點 |
| EV-12 對照表 | 每項 IEC 62443 狀態與實作說明 |
| EV-13 測試 | 功能/安全/效能/滲透測試結果（目前缺） |
| EV-14 流程 | SSP、SDL、威脅模型、漏洞管理、訓練（目前缺） |

## 5. 建議補齊的證據

| 優先 | 證據 | 對應需求 |
|------|------|----------|
| P0 | OPC UA Agent/Alarm 合併 SBOM + Trivy/Grype 報告 + VEX | CR 3.04、3.10、7.08 |
| P0 | 登入/權限/稽核/HTTPS/OPC UA 加密測試報告 | FR1、FR2、FR3、FR4 |
| P0 | 密碼政策/加鹽/鎖定/Lockout 測試 | CR 1.05、1.07、1.11 |
| P1 | 併發 Session、稽核容量/WORM、抹除驗證 | CR 2.07、2.09、3.09、4.02 |
| P1 | 更新完整性/anti-rollback 與出貨簽章驗證紀錄 | CR 3.04、3.10 |
| P1 | 備份/還原演練與容量告警 | CR 2.09、7.03 |
| P2 | SSP/SDL/威脅模型/漏洞管理流程 | CCSC、FR3、FR7 |
| P2 | 威脅模型複核與安全需求檢視（CRA-01、B-03-F01） | CRA Annex I、FR1~FR7 |
| P2 | 滲透測試與 SBOM 簽章/雜湊 | FR3、FR7 |

## 6. 認證證據清單（CB 常要求）

- [ ] System Security Plan（SSP）與範圍/邊界
- [ ] 威脅模型與風險評鑑（IEC 62443-3-2/3-3）
- [ ] 安全開發流程（IEC 62443-4-1）與訓練紀錄
- [ ] 安全需求規格與追溯（SRS/SDS/本對照表）
- [ ] CRA 技術文件（CRA-01~CRA-04、B-03-F01）
- [ ] 安全測試計畫、案例、結果（功能/負向/滲透）
- [ ] SBOM、VEX、弱點掃描與處置紀錄
- [ ] 安全設定/加固指南
- [ ] 更新/修補政策與簽章驗證
- [ ] 備份/還原與事件回應演練
- [ ] 稽核日誌樣本與完整性驗證
- [ ] 密碼學清單（TLS、AES、雜湊、憑證）
- [ ] 帳號/角色/權限設定證據

## 7. 與對照表的關係

- 逐條狀態與實作：`62443/EdgeView_IEC62443-4-2_OPCUA.md`
- 本文件：提供證據 ID、群組對應、SBOM 與缺口
- 需求/設計：`SRS/`、`SDS/`
- CRA 對照：`CRA/CRA-01`~`CRA-04`、`CRA/B-03-F01`
- 入口：Repo `README.md` 的「文件入口」

> 目前證據以 **設計/程式碼/設定/SBOM** 為主；正式 IEC 62443 認證仍需測試報告與流程證據。
