# EdgeView × IEC 62443-4-2（OPC UA 版）對照

本目錄依 `iec62443.csv`（IEC 62443-4-2 Requirements）逐項說明 EdgeView 的實作對照。

> **通訊協定**：EdgeView 系統預設使用 HTTPS（xnginx `protocol: https`，TLS 入口 `8881`，後端僅綁 loopback）。HTTP `8880` 僅保留為相容性選項；使用者若自行改為 `protocol: http` 或 `both` 並以 HTTP 連線，其傳輸風險由使用者自行承擔，不納入本對照表的符合性判定。

| 文件 | 內容 |
|------|------|
| [EdgeView_IEC62443-4-2_OPCUA.md](EdgeView_IEC62443-4-2_OPCUA.md) | 141 項需求逐條對照（狀態、實作說明）；附錄 A 部分符合項目明細（38 項）、附錄 B 不符合項目明細（8 項，含原因與建議） |
| [Evidence.md](Evidence.md) | 證據索引、SBOM、手冊截圖、測試/流程缺口 |
| [evidence/](evidence/) | 14 張 OPC UA 手冊截圖（SS-01~SS-14） |
| [CRA-01](../CRA/CRA-01_Threat_Modeling.md) | EdgeView／OPC UA 威脅模型 v2.0（71 RID-EV／28 MID-EV、風險登錄、標準對照） |
| [B-03-F01](../CRA/B-03-F01_Product%20Compliance%20and%20Security%20Requirement%20Check%20List.xlsx) | 安全合規及安全需求檢視表（SC1~SC2、CRA Annex I、FR1~FR7、OPC-1~OPC-8） |

## 1. 評估範圍

- **產品**：EdgeView 完整系統
- **資料源版本**：**OPC UA**（`DataSource=3`）
- **元件**：
  - Qt Designer / Viewer（桌面、瀏覽器、APP）
  - xnginx（預設 HTTPS `8881`；`8880` 為相容性選項、TLS、路由、限流、服務監督）
  - Metadata Service
  - Runtime Proxy
  - OPC UA Data Agent（Runtime `28886`／History `28887`）
  - OPC UA Alarm Agent（`28889`）
  - OPC UA 設定 UI（`/sc/opcua`）
  - PostgreSQL（`waxconfig`／`waxalarm`／`waxlog`）
- **部署**：
  - 地端：Client → xnginx → Metadata/Runtime/Data Agent/Alarm

## 2. 判定原則

| 狀態 | 說明 |
|------|------|
| 符合 | EdgeView 內建機制可滿足。 |
| 部分符合 | 有部分機制，仍需 OS/網路/流程或功能補強。 |
| 不符合 | 目前無對應機制。 |
| 不適用 | 該需求不適用 OPC UA 版 EdgeView。 |
| 不適用（Web Server） | 屬嵌入式/網路設備/硬體/實體介面需求，不適用純 Web Server/軟體系統。 |
| 不適用（OS/硬體） | 由 OS、虛擬化、TPM、UPS 或硬體負責。 |
| 不適用（網路設備） | EDR/NDR 等網路設備專屬需求，EdgeView 非網路設備。 |

## 3. 通訊協定（預設 HTTPS）

系統預設以 **HTTPS/TLS** 通訊，以下需求之通訊保護由預設 HTTPS 提供：

- 使用者/服務的遠端登入與 Session（CR 1.01、1.07、1.10、1.12、1.13、1.14）
- 憑證/公鑰認證（CR 1.08、1.09）
- 通訊完整性與來源認證（CR 3.01、3.01 RE(1)、3.08）
- 資料機密性與密碼學（CR 4.01、4.03）
- 網路分段與邊界入口（CR 5.01）
- 稽核程式化存取（CR 6.01 RE(1)）
- DoS 限流與安全設定管理（CR 7.01、7.06）

> 使用者若自行將 `protocol` 改為 `http` 或 `both` 並以 HTTP 連線，傳輸風險由使用者自行承擔，不納入本對照表的符合性判定。憑證由 xnginx/Ingress 管理，後端僅綁 loopback。

## 4. Web Server 不適用項目

以下需求在 EdgeView OPC UA 版標示 **不適用（Web Server）** 或 **不適用（OS/硬體）**：

- 無線存取（CR 1.06、NDR 1.06）
- 硬體安全金鑰/HSM（CR 1.05 RE(1)、1.09 RE(1)、1.14 RE(1)）
- 實體診斷/測試介面（CR 2.13、EDR/HDR/NDR 2.13）
- 實體防拆/竄改偵測（CR 3.11、EDR/HDR/NDR 3.11）
- 硬體 root of trust / 安全開機（CR 3.12~3.14、EDR/HDR/NDR）
- 緊急電源（CR 7.05）
- 嵌入式/網路設備專屬（EDR/NDR 多數項目）

## 5. 統計摘要

| 類別 | 數量 |
|------|------|
| 符合 | 32 |
| 部分符合 | 38 |
| 不符合 | 8 |
| 不適用（含 Web Server/OS/硬體/網路設備） | 63 |
| **合計** | **141** |

## 6. 主要缺口

| 缺口 | 對應需求 | 對應 RID-EV | SL | 建議 |
|------|----------|-------------|----|------|
| 無 MFA | CR 1.01 RE(2) | RID-EV-008 | SL3 | 整合外部 IdP/反向代理 MFA。 |
| 密碼使用 SHA-256（未加鹽），無完整強度/到期/歷史政策 | CR 1.05、1.07 | RID-EV-002、009 | SL1、SL3、SL4 | Argon2id/bcrypt + 複雜度/到期/歷史。 |
| 無同時 Session 上限 | CR 2.07 | RID-EV-010 | SL3 | Session 上限與管理。 |
| 稽核無 WORM/容量門檻告警 | CR 2.09、3.09 | RID-EV-053、054 | SL1、SL2、SL3、SL4 | 外部 WORM/SIEM、容量告警。 |
| 更新流程缺 anti-rollback/版本固定 | CR 3.04、3.10 | RID-EV-047、048 | SL1、SL2 | 更新包驗簽、版本 pinning、失敗回復。 |
| 無執行期安全功能自我測試/完整性監控 | CR 3.03 | RID-EV-052 | SL1、SL4 | 啟動/執行期 self-test。 |
| 無共享記憶體抹除驗證 | CR 4.02 | RID-EV-063 | SL2、SL3 | 共享記憶體/暫存清除與驗證。 |

## 7. 使用建議

- 正式投標/認證：以本表為初稿，補齊 CB 要求的證據（設定截圖、測試報告、SBOM、SDLC 文件）。
- 將「部分符合」逐項轉為：功能補強、流程補強（OS/網路）或風險接受。
- 威脅與風險追溯以 `CRA/CRA-01` v2.0（RID-EV／MID-EV）為準；安全需求檢視以 `CRA/B-03-F01` 為準；流程檢查點見 `CRA/CRA-02`，編碼審查見 `CRA/CRA-03`，部署指引見 `CRA/CRA-04`。
- 每次版本更新後重跑本表與 `SDS/12_Traceability.md`。
