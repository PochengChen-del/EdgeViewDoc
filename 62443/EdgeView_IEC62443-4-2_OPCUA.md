# EdgeView 對照 IEC 62443-4-2（OPC UA 版）

> 評估範圍：EdgeView 完整系統，datasource = **OPC UA**。  
> 包含：Qt Designer/Viewer、xnginx、Metadata Service、Runtime Proxy、OPC UA Data Agent（Runtime/History）、OPC UA Alarm Agent、OPC UA 設定 UI、PostgreSQL。  
> 依據：`iec62443.csv`（IEC 62443-4-2 Requirements，148 列，其中 141 列為有效需求）。

## 1. 判定原則

| 狀態 | 意義 |
|------|------|
| 符合 | EdgeView 內建機制可滿足需求。 |
| 符合（HTTPS） | 由 HTTPS/TLS 連線即可滿足（憑證、加密、完整性、來源驗證等）。 |
| 部分符合 | 有部分機制，但需搭配 OS/網路/流程或仍有缺口。 |
| 不符合 | 目前無對應機制。 |
| 不適用 | 該需求不適用於 EdgeView OPC UA 版。 |
| 不適用（Web Server） | 屬於嵌入式/網路設備/硬體/實體介面需求，不適用純 Web Server/軟體系統。 |
| 不適用（OS/硬體） | 由作業系統、虛擬化或硬體負責，EdgeView 不實作。 |

**HTTPS 欄位**：`是` 表示以 HTTPS 連線即可解決或主要靠 HTTPS/TLS；`—` 表示非 HTTPS 範疇。

## 2. Common Component Security Constraints

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CCSC 1 | Support of essential functions | N/A | 部分符合 | — | 服務分離（Metadata/Runtime/Agent/Alarm）與 xnginx 監督；尚未以 IEC 62443-3-3 系統風險分析正式界定關鍵功能。 |
| CCSC 2 | Compensating countermeasures | N/A | 部分符合 | 是 | xnginx TLS/限流、loopback 綁定、Session/CSRF、稽核；補償措施未形成正式文件。 |
| CCSC 3 | Least privilege | N/A | 符合 | — | Admin/Power/General/Restricted 角色、專案/畫面權限、OPC UA UserFunc/UserProject、安全區/級別。 |
| CCSC 4 | Software development process | N/A | 部分符合 | — | 有 SDLC/文件/版本管理；未提供 IEC 62443-4-1 認證或完整安全開發證據。 |

## 3. Identification and Authentication Control（FR1）

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CR 1.01 | Human user identification and authentication | SL1 | 符合（HTTPS） | 是 | OPC UA 使用者（admin/OPCUA_User）登入；Session + CSRF；建議全程 HTTPS。 |
| CR 1.01 RE(1) | Unique identification and authentication | SL2 | 符合 | — | `OPCUA_User` 使用者名稱唯一；每請求綁定 Session。 |
| CR 1.01 RE(2) | Multifactor authentication for all interfaces | SL3 | 不符合 | — | 未內建 MFA；需外部 IdP/反向代理補充。 |
| CR 1.02 | Software process and device identification and authentication | SL2 | 部分符合 | 是 | 服務間以內部 Bearer/Session、OPC UA 憑證；未對每個軟體程序/裝置發獨立身分。 |
| CR 1.02 RE(1) | Unique identification and authentication | SL3 | 部分符合 | 是 | OPC UA 連線使用獨立應用憑證/PFX；未涵蓋所有服務程序。 |
| CR 1.03 | Account management | SL1 | 符合 | — | `UserAdmin`/`OPCUA_User` 新增/修改/刪除/停用；OPC UA 設定 UI 管理角色與專案授權。 |
| CR 1.04 | Identifier management | SL1 | 符合 | — | 使用者名稱唯一鍵；專案/角色/安全區識別。 |
| CR 1.05 | Authenticator management | SL1 | 部分符合 | 是 | 支援改密/重設；無完整密碼到期、歷史、複雜度政策。 |
| CR 1.05 RE(1) | Hardware security for authenticators | SL3 | 不適用（Web Server） | — | 無硬體 Token/TPM 綁定。 |
| CR 1.06 | Wireless access management | N/A | 不適用 | — | 無無線管理功能。 |
| NDR 1.06 | Wireless access management | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| NDR 1.06 RE(1) | Unique identification and authentication | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| CR 1.07 | Strength of password-based authentication | SL1 | 部分符合 | 是 | 密碼以 SHA-256 儲存（未加鹽），無強度政策；HTTPS 保護傳輸。 |
| CR 1.07 RE(1) | Password generation and lifetime restrictions for human users | SL3 | 不符合 | — | 無產生規則/到期/歷史限制。 |
| CR 1.07 RE(2) | Password lifetime restrictions for all users | SL4 | 不符合 | — | 未強制所有帳號生命週期。 |
| CR 1.08 | Public key infrastructure certificates | SL2 | 符合（HTTPS） | 是 | HTTPS/xnginx 憑證、OPC UA PFX、Certificate/create；支援憑證匯入/下載。 |
| CR 1.09 | Strength of public key-based authentication | SL2 | 符合（HTTPS） | 是 | TLS 1.2/1.3、OPC UA SecurityPolicy；憑證金鑰強度依設定。 |
| CR 1.09 RE(1) | Hardware security for public key-based authentication | SL3 | 不適用（Web Server） | — | 私鑰可由 OS 檔案/憑證存放區保護；無 HSM。 |
| CR 1.10 | Authenticator feedback | SL1 | 部分符合（HTTPS） | 是 | 登入失敗回一般錯誤，不顯示密碼；HTTPS 防止竊聽。 |
| CR 1.11 | Unsuccessful login attempts | SL1 | 符合（HTTPS） | 是 | OPC UA 以 `OPCUA_LoginLock` 管理失敗次數/鎖定；登入經 HTTPS。 |
| CR 1.12 | System use notification | SL1 | 符合（HTTPS） | 是 | 登入/連線頁 footer（`InfoFooterRow`/`InfoFooterColumn`）提供 Privacy Policy、Contact、Support 連結；以 `Global.openUrlExternally` 開啟 HTTPS 頁面。若 CB 要求登入前強制同意，需再加確認視窗。 |
| CR 1.13 | Access via untrusted networks | N/A | 符合（HTTPS） | 是 | 地端 xnginx `8881` TLS；後端僅 loopback。 |
| NDR 1.13 | Access via untrusted networks | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| NDR 1.13 RE(1) | Explicit access request approval | SL3 | 不適用（網路設備） | — | 非網路設備。 |
| CR 1.14 | Strength of symmetric key-based authentication | SL2 | 符合（HTTPS） | 是 | Session HMAC、CSRF、HTTPS；AES 保護設定。 |
| CR 1.14 RE(1) | Hardware security for symmetric key-based authentication | SL3 | 不適用（Web Server） | — | 無 HSM。 |

## 4. Use Control（FR2）

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CR 2.01 | Authorization enforcement | SL1 | 符合 | — | 專案/角色/畫面/安全區/級別；OPC UA UserFunc/UserProject。 |
| CR 2.01 RE(1) | Authorization enforcement for all users | SL2 | 部分符合 | — | 人類使用者完整；軟體程序/裝置僅部分（內部 Bearer/OPC UA）。 |
| CR 2.01 RE(2) | Permission mapping to roles | SL2 | 符合 | — | Admin/Power/General/Restricted 與 OPC UA 角色映射。 |
| CR 2.01 RE(3) | Supervisor override | SL3 | 部分符合 | — | Admin 可管理專案/使用者；無獨立監督者覆寫流程。 |
| CR 2.01 RE(4) | Dual approval | SL4 | 不符合 | — | 無雙人核准。 |
| CR 2.02 | Wireless use control | SL1 | 不適用 | — | 無無線功能。 |
| CR 2.03 | Use control for portable and mobile devices | N/A | 部分符合 | 是 | APP 可連線/推播；未細緻控制行動裝置。 |
| CR 2.04 | Mobile code | N/A | 部分符合 | — | Widget/JS/Plugins 可執行；無行動程式碼下載控制。 |
| EDR 2.04 | Mobile code | SL1 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 2.04 | Mobile code | SL1 | 不適用（OS/硬體） | — | 由 Host OS 負責。 |
| NDR 2.04 | Mobile code | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| SAR 2.04 | Mobile code | SL1 | 部分符合 | — | EdgeView 屬軟體應用；JS/Plugin 需來源控管。 |
| EDR 2.04 RE(1) | Mobile code authenticity check | SL2 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 2.04 RE(1) | Mobile code authenticity check | SL2 | 不適用（OS/硬體） | — | 由 Host OS 負責。 |
| NDR 2.04 RE(1) | Mobile code authenticity check | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| SAR 2.04 RE(1) | Mobile code authenticity check | SL2 | 部分符合（HTTPS） | 是 | Plugin 經 HTTPS 下載；無簽章驗證。 |
| CR 2.05 | Session lock | SL1 | 部分符合 | — | `sessionmonitor`/Viewer 鎖定；無全介面强制鎖定政策。 |
| CR 2.06 | Remote session termination | SL2 | 符合 | — | 使用者登出、Session 清除；管理端可移除 Session。 |
| CR 2.07 | Concurrent session control | SL3 | 不符合 | — | 未限制同時 Session 數。 |
| CR 2.08 | Auditable events | SL1 | 符合 | — | ActionLog/AuditLog（登入、設定、Ack/Process、匯入匯出）。 |
| CR 2.09 | Audit storage capacity | SL1 | 部分符合 | — | DB/Maintenance 保留策略；容量告警有限。 |
| CR 2.09 RE(1) | Warn when audit storage threshold reached | SL3 | 部分符合 | — | 有維護/統計，未見明確門檻告警。 |
| CR 2.10 | Response to audit processing failures | SL1 | 部分符合 | — | 日誌失敗不阻斷核心；無明確告警流程。 |
| CR 2.11 | Timestamps | SL1 | 符合 | — | 稽核/事件使用 Unix ms/UTC；時間來源相依 OS。 |
| CR 2.11 RE(1) | Time synchronization | SL2 | 部分符合 | — | 依賴 NTP/OS；應用未內建時間源驗證。 |
| CR 2.11 RE(2) | Protection of time source integrity | SL4 | 不適用（OS/硬體） | — | 由 OS/NTP 安全負責。 |
| CR 2.12 | Non-repudiation | SL1 | 部分符合 | — | 使用者/時間/事件與 checksum；非法律級不可否認。 |
| CR 2.12 RE(1) | Non-repudiation for all users | SL4 | 部分符合 | — | 部分軟體/裝置事件未完整歸屬。 |
| CR 2.13 | Use of physical diagnostic and test interfaces | N/A | 不適用（Web Server） | — | 無實體診斷介面。 |
| EDR 2.13 | Use of physical diagnostic and test interfaces | SL2 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 2.13 | Use of physical diagnostic and test interfaces | SL2 | 不適用（OS/硬體） | — | 由 Host 負責。 |
| NDR 2.13 | Use of physical diagnostic and test interfaces | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| EDR 2.13 RE(1) | Active monitoring | SL3 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 2.13 RE(1) | Active monitoring | SL3 | 不適用（OS/硬體） | — | 由 Host/EDR 負責。 |
| NDR 2.13 RE(1) | Active monitoring | SL3 | 不適用（網路設備） | — | 非網路設備。 |

## 5. System Integrity（FR3）

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CR 3.01 | Communication integrity | SL1 | 符合（HTTPS） | 是 | TLS 保護 REST/WS；OPC UA Sign/SignAndEncrypt。 |
| CR 3.01 RE(1) | Communication authentication | SL2 | 符合（HTTPS） | 是 | TLS 憑證、OPC UA 應用憑證、Session。 |
| CR 3.02 | Protection from malicious code | N/A | 部分符合 | — | 無內建 AV；依 Host/端點防護。 |
| EDR 3.02 | Protection from malicious code | SL1 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.02 | Protection from malicious code | SL1 | 不適用（OS/硬體） | — | 由 Host/AV 負責。 |
| NDR 3.02 | Protection from malicious code | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| SAR 3.02 | Protection from malicious code | SL1 | 部分符合 | — | 應用層輸入/腳本控管；無掃毒。 |
| HDR 3.02 RE(1) | Report version of code protection | SL2 | 不適用（OS/硬體） | — | 由 Host/AV 回報。 |
| CR 3.03 | Security functionality verification | SL1 | 部分符合 | — | 有健康檢查/API 測試；無內建完整性自我測試。 |
| CR 3.03 RE(1) | Security functionality verification during normal operation | SL4 | 不符合 | — | 未持續驗證安全功能。 |
| CR 3.04 | Software and information integrity | SL1 | 部分符合 | 是 | 套件/設定版本與 checksum；更新檔未強制簽章。 |
| CR 3.04 RE(1) | Authenticity of software and information | SL2 | 部分符合（HTTPS） | 是 | 透過 HTTPS 下載；未驗證程式簽章。 |
| CR 3.04 RE(2) | Automated notification of integrity violations | SL3 | 部分符合 | — | 稽核/日誌可記錄；無自動完整性告警。 |
| CR 3.05 | Input validation | SL1 | 符合 | — | Go binding/參數化查詢、QML `validate.js`、屬性 schema。 |
| CR 3.06 | Deterministic output | SL1 | 符合 | — | 固定 API 回應/錯誤碼/序列化格式。 |
| CR 3.07 | Error handling | SL1 | 符合 | — | 統一錯誤碼 `-100/-101/-102/-500/-501`；panic 攔截。 |
| CR 3.08 | Session integrity | SL2 | 符合（HTTPS） | 是 | Session + CSRF + `packetTimestamp`；HTTPS 防竄改。 |
| CR 3.09 | Protection of audit information | SL2 | 部分符合 | — | `auditlog` checksum、DB 權限；無 WORM。 |
| CR 3.09 RE(1) | Audit records on write-once media | SL4 | 不符合 | — | 未使用 WORM/一次性媒體。 |
| CR 3.10 | Support for updates | N/A | 部分符合（HTTPS） | 是 | 安裝/升版流程；更新檔可經 HTTPS 傳輸。 |
| EDR 3.10 | Support for updates | SL1 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.10 | Support for updates | SL1 | 不適用（OS/硬體） | — | 由 Host OS 負責。 |
| NDR 3.10 | Support for updates | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| EDR 3.10 RE(1) | Update authenticity and integrity | SL2 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.10 RE(1) | Update authenticity and integrity | SL2 | 不適用（OS/硬體） | — | 由 Host OS 負責。 |
| NDR 3.10 RE(1) | Update authenticity and integrity | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| CR 3.11 | Physical tamper resistance and detection | N/A | 不適用（Web Server） | — | 無實體外殼/防拆。 |
| EDR 3.11 | Physical tamper resistance and detection | SL2 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.11 | Physical tamper resistance and detection | SL2 | 不適用（OS/硬體） | — | 由 Host 負責。 |
| NDR 3.11 | Physical tamper resistance and detection | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| EDR 3.11 RE(1) | Notification of a tampering attempt | SL3 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.11 RE(1) | Notification of a tampering attempt | SL3 | 不適用（OS/硬體） | — | 由 Host 負責。 |
| NDR 3.11 RE(1) | Notification of a tampering attempt | SL3 | 不適用（網路設備） | — | 非網路設備。 |
| CR 3.12 | Provisioning product supplier roots of trust | N/A | 不適用（Web Server） | — | 無硬體 root of trust。 |
| EDR 3.12 | Provisioning product supplier roots of trust | SL2 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.12 | Provisioning product supplier roots of trust | SL2 | 不適用（OS/硬體） | — | 由 Host/TPM 負責。 |
| NDR 3.12 | Provisioning product supplier roots of trust | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| CR 3.13 | Provisioning asset owner roots of trust | N/A | 不適用（Web Server） | — | 無硬體 root of trust。 |
| EDR 3.13 | Provisioning asset owner roots of trust | SL2 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.13 | Provisioning asset owner roots of trust | SL2 | 不適用（OS/硬體） | — | 由 Host/TPM 負責。 |
| NDR 3.13 | Provisioning asset owner roots of trust | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| CR 3.14 | Integrity of the boot process | N/A | 不適用（OS/硬體） | — | 由 OS/硬體安全開機負責。 |
| EDR 3.14 | Integrity of the boot process | SL1 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.14 | Integrity of the boot process | SL1 | 不適用（OS/硬體） | — | 由 Host 負責。 |
| NDR 3.14 | Integrity of the boot process | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| EDR 3.14 RE(1) | Authenticity of the boot process | SL2 | 不適用（Web Server） | — | 非嵌入式裝置。 |
| HDR 3.14 RE(1) | Authenticity of the boot process | SL2 | 不適用（OS/硬體） | — | 由 Host 負責。 |
| NDR 3.14 RE(1) | Authenticity of the boot process | SL2 | 不適用（網路設備） | — | 非網路設備。 |

## 6. Data Confidentiality（FR4）

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CR 4.01 | Information confidentiality | SL1 | 符合（HTTPS） | 是 | xnginx HTTPS、OPC UA SignAndEncrypt；敏感設定 AES。 |
| CR 4.02 | Information persistence | SL2 | 部分符合 | — | Session/資料保留可設定；缺少統一清除驗證。 |
| CR 4.02 RE(1) | Erase of shared memory resources | SL3 | 部分符合 | — | 程序結束釋放；無保證抹除 shared memory。 |
| CR 4.02 RE(2) | Erase verification | SL3 | 不符合 | — | 無抹除驗證。 |
| CR 4.03 | Use of cryptography | SL1 | 部分符合 | 是 | TLS/AES/SHA-256；密碼雜湊未加鹽，需改善。 |

## 7. Restricted Data Flow（FR5）

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CR 5.01 | Network segmentation | SL1 | 符合 | 是 | xnginx 地端入口、後端 loopback；網路分段由防火牆/主機網路配合。 |
| CR 5.02 | Zone boundary protection | N/A | 不適用（Web Server） | — | 由網路設備/防火牆負責。 |
| NDR 5.02 | Zone boundary protection | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| NDR 5.02 RE(1) | Deny all, permit by exception | SL2 | 不適用（網路設備） | — | 非網路設備。 |
| NDR 5.02 RE(2) | Island mode | SL3 | 不適用（網路設備） | — | 非網路設備。 |
| NDR 5.02 RE(3) | Fail close | SL3 | 不適用（網路設備） | — | 非網路設備。 |
| CR 5.03 | General-purpose person-to-person communication restrictions | N/A | 不適用 | — | 無即時通訊功能。 |
| NDR 5.03 | General purpose, person-to-person communication restrictions | SL1 | 不適用（網路設備） | — | 非網路設備。 |
| CR 5.04 | Application partitioning | N/A | 符合 | — | Metadata/Runtime/Agent/Alarm 分離；權限與資源隔離。 |

## 8. Timely Response to Events（FR6）

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CR 6.01 | Audit log accessibility | SL1 | 符合 | — | UI 稽核/操作日誌查詢與匯出。 |
| CR 6.01 RE(1) | Programmatic access to audit logs | SL3 | 符合 | 是 | `/api/auditlog/query`、`/export`；HTTPS 保護。 |
| CR 6.02 | Continuous monitoring | SL2 | 部分符合 | 是 | `systeminfo`、健康端點、日誌；無 SIEM/IDS 整合。 |

## 9. Resource Availability（FR7）

| ID | Name | SL | 狀態 | HTTPS | EdgeView（OPC UA 版）實作 |
|----|------|----|------|-------|---------------------------|
| CR 7.01 | Denial of service protection | SL1 | 部分符合 | 是 | xnginx `limit_req`、連線池/佇列、逾時；無全面 DDoS 防護。 |
| CR 7.01 RE(1) | Manage communication load from component | SL2 | 部分符合 | — | 限流、批次、背壓；未動態調節。 |
| CR 7.02 | Resource management | SL1 | 部分符合 | — | DB 連線池、WS 佇列、掃描間隔；無全域資源配額。 |
| CR 7.03 | Control System backup | SL1 | 符合 | — | PostgreSQL 備份（waxconfig/waxalarm/waxlog）、設定/憑證備份。 |
| CR 7.03 RE(1) | Backup integrity verification | SL2 | 部分符合 | — | 可 checksum/還原演練；未自動驗證。 |
| CR 7.04 | Control system recovery and reconstitution | SL1 | 符合 | — | 安裝/還原流程、DB dump、服務重啟。 |
| CR 7.05 | Emergency Power | N/A | 不適用（OS/硬體） | — | 由 UPS/機房負責。 |
| CR 7.06 | Network and security configuration settings | SL1 | 符合 | 是 | `xnginx*.yaml`、`config.yaml`、`appsettings.json`、UI 設定；HTTPS 保護管理。 |
| CR 7.06 RE(1) | Machine-readable reporting of current security settings | SL3 | 部分符合 | 是 | 設定檔/API 可讀；無完整機器可讀安全設定報表。 |
| CR 7.07 | Least functionality | SL1 | 符合 | — | 角色/模組啟停、MQTT 停用、服務分離。 |
| CR 7.08 | Control system component inventory | SL2 | 部分符合 | — | SDS/部署清單與服務清冊；無自動 SBOM/盤點。 |

## 10. 主要缺口與建議

| 缺口 | 影響項目 | 對應 SL | 建議 |
|------|----------|---------|------|
| 無 MFA | CR 1.01 RE(2) | SL3 | 整合外部 IdP/反向代理 MFA。 |
| 密碼政策/生命週期不足、SHA-256 未加鹽 | CR 1.05、1.07 | SL1、SL3、SL4 | Argon2id/bcrypt + 複雜度/到期/歷史。 |
| 無 Concurrent Session 限制 | CR 2.07 | SL3 | Session 上限/管理。 |
| 稽核無 WORM/容量告警 | CR 2.09、3.09 | SL1、SL2、SL3、SL4 | 外部 WORM/SIEM、容量告警。 |
| 無程式/更新簽章 | CR 3.04、3.10 | SL1、SL2 | 簽章驗證與供應鏈安全。 |
| 無自我測試/完整性監控 | CR 3.03 | SL1、SL4 | 啟動/執行期 self-test。 |
| 無抹除驗證 | CR 4.02 | SL2、SL3 | 共享記憶體/暫存清除與驗證。 |
| 遠端設定/管理需全程 HTTPS | CR 1.01、1.13、3.01、4.01、5.01、7.06 | SL1 | 關閉 HTTP `8880` 或僅導向 HTTPS `8881`；憑證管理。 |

## 11. 統計

> 狀態統計以本表為準；「部分符合」與「不適用」需在正式評估時由產品/資安確認。

| 類別 | 數量 |
|------|------|
| 符合 | 20 |
| 符合（HTTPS） | 11 |
| 部分符合 | 35 |
| 部分符合（HTTPS） | 4 |
| 不符合 | 8 |
| 不適用 | 3 |
| 不適用（Web Server） | 21 |
| 不適用（OS/硬體） | 17 |
| 不適用（網路設備） | 22 |
| **合計** | **141** |

> 註：上述統計為文件初稿分類，實際 IEC 62443 認證需以 CB 審查與證據為準。
