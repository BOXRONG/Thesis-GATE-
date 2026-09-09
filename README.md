# Thesis-GATE 論文檢查送件系統

> 基於 Hyperledger 聯盟鏈與 Stage-Gate 門徑管理模型之去中心化学術審查與送件系統

[![Hyperledger Composer](https://img.shields.io/badge/Blockchain-Hyperledger%20Composer-blue.svg)](https://hyperledger.github.io/)
[![Smart Contract](https://img.shields.io/badge/Smart%20Contract-JavaScript%20(ES6+)-yellow.svg)]()
[![Access Control](https://img.shields.io/badge/Security-ACL%20Enforced-green.svg)]()

---

## 📌 專案簡介 (Introduction)

**Thesis-GATE** 是一套基於聯盟鏈架構開發的學術論文審查與送件系統。

傳統論文審查常面臨進度不透明、審查紀錄零散於 Email、權責邊界不清導致越權操作，以及人工核驗易出錯等痛點。本專案借鑑新產品開發（NPD）的 **Stage-Gate 門徑管理模型**，將審查流程抽象化為「**六大工作階段（Stages）**」與「**五道審查關卡（Gates）**」。

透過智慧合約與鏈上存取控制（ACL），系統在底層強制規範參與角色的權限邊界，確保審查歷程具備**不可篡改性**、**時間公證性**與**全程留痕**，建立透明且具公信力的學術審查工作流。

<img width="2516" height="1417" alt="image" src="https://github.com/user-attachments/assets/d31fea33-c714-460a-844c-e3c1e028cffa" />
<img width="2518" height="1270" alt="image" src="https://github.com/user-attachments/assets/b98e02ec-a4f0-4958-9a9f-afa80a1c2d12" />
<img width="2525" height="1412" alt="螢幕擷取畫面 2026-09-10 014958" src="https://github.com/user-attachments/assets/5f915421-c578-4051-9dc3-bd374ebedc3e" />



---

## 🛠️ 技術堆疊 (Tech Stack)

| 領域 / 元件 | 技術選型 | 說明 |
| :--- | :--- | :--- |
| **底層區塊鏈架構** | Hyperledger Composer / Fabric | 具備高隱私與高效能的企業級聯盟鏈架構 |
| **資料模型定義** | Composer Modeling Language (`.cto`) | 定義 Participant、Asset、Transaction 與 Event 模型 |
| **智慧合約邏輯** | JavaScript (ES6+ / Async & Await) | 實作雙狀態交替機、防呆防弊檢驗及鏈上事件觸發 |
| **安全與存取控制** | Access Control Language (`.acl`) | 實作架構層 Role-Based 動態存取控制與 Deny-All 防禦 |

---

## ✨ 核心功能 (Key Features)

### 1. 雙狀態交替工作流（Stage-Gate 狀態機）
* **Stage 工作期 (`isGatePhase = false`)**：學生專屬作業階段。學生可呼叫 `UpdateThesisWork` 撰寫並更新進度報告；確認完成後呼叫 `SubmitToGate`，系統立即凍結修改權限並移交審查委員。
* **Gate 審查期 (`isGatePhase = true`)**：審查委員專屬階段。依關卡性質進行決議（`ReviewGate`）：
  * **通過 (`approved = true`)**：解凍論文並自動晉升至下一 Stage，將主控權交還學生。
  * **退回 (`approved = false`)**：論文凍結於當前 Gate，學生需依評語補件修正。

### 2. 動態角色權限綁定（Dynamic RBAC）
* **格式檢查員（Checker）**：僅限審查 **Gate 1**（格式規範與抄襲率檢核）。
* **指導教授（Professor）**：負責 **Gate 2**（口試資格審查）與 **Gate 4**（口試委員決議）。
* **系主任／行政端（Director）**：負責 **Gate 3**（行政排程）與 **Gate 5**（最終離校核可），並具備 `AdvanceThesis` 最高行政裁量權，於例外狀況強制推進流程。

### 3. 底層架構級防護（Strict ACL）
* **資料隱私隔離**：透過鏈上 ACL 限制學生僅能讀取與編輯自身持有的論文資產（`r.student == p`），防止研究構想外洩。
* **越權防禦**：非當前關卡管轄之角色，其交易請求直接在底層架構層拒絕，徹底杜絕代簽或越權審核。
* **預設拒絕原則（Deny-All）**：採白名單安全邊界，未明確定義放行的操作一律拒絕。

### 4. 鏈上公證與智慧防呆
* **防呆攔截機制**：合約內建空白內容驗證，直接攔截無效送件並執行 Rollback，杜絕行政空轉。
* **數位時間戳記（Timestamp）**：送件紀錄與審查意見均綁定數位簽章永久上鏈，為學術成果提供具備法律效力的數位智財權公證。

---

## 📊 效益對照表 (Traditional vs. Thesis-GATE)

| 評估維度 | 傳統人工 / Web 系統 | Thesis-GATE 區塊鏈系統 |
| :--- | :--- | :--- |
| **程序透明度** | 內部黑箱資料庫，進度難以即時追蹤 | **100% 全程留痕**，具備公開可驗證的審查履歷 |
| **權責歸屬** | 易發生口頭答應事後反悔、代簽爭議 | **數位簽章綁定**，審查決議與評語不可篡改 |
| **公文流轉耗時** | 依賴人工催交與紙本傳遞（需 3~7 天） | **事件驅動（Event-Driven）**，狀態秒級切換 |
| **防呆攔截率** | 需人工打開檔案比對，易漏失錯誤 | **智慧合約底層驗證**，無效檔案 100% 自動攔截 |

---

## 🖼️ 系統架構與展示 (Screenshots & Architecture)

### 系統架構圖 (UML Data Model)
> 系統資產（Thesis）、參與者（Participants）與交易（Transactions）關聯架構：
> <img width="3106" height="1571" alt="architecture" src="https://github.com/user-attachments/assets/081605d2-a5ce-43f8-a64d-44798d97031b" />

### 系統操作展示 (Demo)

* **Stage 3 行政送審交易測試：**
  <!-- 可在此處貼上終端機或 Web 介面的運行截圖 -->
  <!-- ![Demo Screenshot](./docs/demo-stage3.png) -->

* **完整實作流程展示影片：**
  👉[https://www.youtube.com/watch?v=HeX2dzU-xbM］

---

## 📂 專案目錄結構 (Repository Structure)

```text
├── models/
│   └── edu.thesis.review.cto    # 區塊鏈資料模型 (Asset, Participant, Transaction, Event)
├── lib/
│   └── logic.js                 # 智慧合約業務邏輯 (狀態機流轉、權限檢驗)
├── permissions.acl              # 鏈上存取控制規則 (ACL 白名單與防護)
└── README.md                    # 專案說明文件
