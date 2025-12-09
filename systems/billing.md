# Billing 帳務系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Billing |
| 開發階段 | Phase 4 |
| 複雜度 | 中 |
| 相依模組 | Auth, Member, Point |
| 被依賴 | 無 |

---

## 功能概述

帳務系統管理會員的會費繳納、點數充值、帳單核對等金流相關功能。

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 帳單列表 | 查看所有帳單紀錄 |
| 帳單詳情 | 查看帳單明細 |
| 狀態更新 | 更新帳單狀態（人工核對） |
| 搜尋篩選 | 依會員、狀態、時間篩選 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 會員續費 | 查看會期狀態、選擇續費方案 |
| 加點充值 | 選擇充值方案、金流付款 |
| 繳費紀錄 | 查看我的繳費紀錄 |

---

## API 端點設計

### 後台 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/billing/orders | 帳單列表 |
| GET | /api/v1/admin/billing/orders/:id | 帳單詳情 |
| PUT | /api/v1/admin/billing/orders/:id/status | 更新帳單狀態 |
| GET | /api/v1/admin/billing/stats | 收款統計 |
| GET | /api/v1/admin/billing/stats/daily | 每日收款統計 |

### 客端 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/billing/membership/status | 會籍狀態 |
| GET | /api/v1/billing/membership/plans | 續費方案 |
| POST | /api/v1/billing/membership/renew | 發起續費 |
| GET | /api/v1/billing/recharge/plans | 充值方案 |
| POST | /api/v1/billing/recharge | 發起充值 |
| GET | /api/v1/billing/orders | 我的帳單列表 |
| GET | /api/v1/billing/orders/:id | 帳單詳情 |
| POST | /api/v1/billing/callback/newebpay | 藍新背景通知 |
| GET | /api/v1/billing/return/newebpay | 藍新前景返回 |

---

## 資料庫設計

### 帳單訂單表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| orderNo | 字串 | 訂單編號（唯一） |
| memberId | 字串 | 會員 ID |
| type | 列舉 | 訂單類型 |
| amount | 整數 | 金額 |
| description | 字串 | 描述（選填） |
| membershipPlanId | 字串 | 續費方案 ID（選填） |
| membershipMonths | 整數 | 續費月數（選填） |
| rechargePlanId | 字串 | 充值方案 ID（選填） |
| rechargePoints | 整數 | 充值點數（選填） |
| bonusPoints | 整數 | 贈送點數（選填） |
| paymentMethod | 列舉 | 付款方式（選填） |
| paymentGateway | 列舉 | 金流平台（選填） |
| transactionId | 字串 | 金流交易編號（選填） |
| paymentData | JSON | 金流回傳資料（選填） |
| status | 列舉 | 訂單狀態（預設 PENDING） |
| createdAt | 時間 | 建立時間 |
| paidAt | 時間 | 付款時間（選填） |
| expiredAt | 時間 | 過期時間（選填） |
| updatedAt | 時間 | 更新時間 |

**訂單類型列舉值：**

| 值 | 說明 |
|------|------|
| MEMBERSHIP_RENEW | 會員續費 |
| POINT_RECHARGE | 點數充值 |

**付款方式列舉值：**

| 值 | 說明 |
|------|------|
| CREDIT_CARD | 信用卡 |
| ATM | ATM 轉帳 |
| CVS | 超商代碼 |
| WEBATM | 網路 ATM |
| BARCODE | 超商條碼 |

**金流平台列舉值：**

| 值 | 說明 |
|------|------|
| NEWEBPAY | 藍新金流（唯一金流平台） |

**訂單狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待付款 |
| PROCESSING | 處理中 |
| PAID | 已付款 |
| COMPLETED | 已完成 |
| FAILED | 失敗 |
| CANCELLED | 已取消 |
| EXPIRED | 已過期 |
| REFUNDED | 已退款 |

### 會員方案表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| name | 字串 | 方案名稱 |
| months | 整數 | 月數 |
| price | 整數 | 價格 |
| originalPrice | 整數 | 原價（選填） |
| description | 字串 | 描述（選填） |
| isActive | 布林 | 是否啟用（預設 true） |
| sortOrder | 整數 | 排序順序（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

### 充值方案表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| name | 字串 | 方案名稱 |
| amount | 整數 | 金額 |
| points | 整數 | 基本點數 |
| bonusPoints | 整數 | 贈送點數（預設 0） |
| description | 字串 | 描述（選填） |
| isActive | 布林 | 是否啟用（預設 true） |
| sortOrder | 整數 | 排序順序（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

---

## 業務邏輯

### 會員續費方案（範例）

| 方案名稱 | 月數 | 價格 | 原價 |
|----------|------|------|------|
| 季度會員 | 3 | 3,000 | 3,600 |
| 半年會員 | 6 | 5,400 | 7,200 |
| 年度會員 | 12 | 9,600 | 14,400 |

### 點數充值方案（範例）

| 方案名稱 | 金額 | 基本點數 | 贈送點數 |
|----------|------|----------|----------|
| 基本方案 | 1,000 | 1,000 | 0 |
| 超值方案 | 3,000 | 3,000 | 150 |
| 豪華方案 | 5,000 | 5,000 | 350 |
| 尊爵方案 | 10,000 | 10,000 | 1,000 |

### 訂單流程

| 步驟 | 說明 |
|------|------|
| 1 | 會員選擇方案 |
| 2 | 建立訂單（狀態：PENDING） |
| 3 | 跳轉金流付款頁面 |
| 4 | 金流回調 - 成功：狀態改為 PAID → 執行後續處理 → COMPLETED |
| 4 | 金流回調 - 失敗：狀態改為 FAILED |
| 5 | 後續處理 - 續費：延長會籍到期日 |
| 5 | 後續處理 - 充值：增加點數 |

### 訂單編號規則

| 類型 | 前綴 | 範例 |
|------|------|------|
| 會員續費 | MR | MR20231215001 |
| 點數充值 | PR | PR20231215001 |

格式：{類型代碼}{年月日}{流水號}

### 訂單過期機制

- 未付款訂單 30 分鐘後自動過期
- 排程任務定期檢查並更新狀態

---

## DTO 資料格式

### 會籍狀態回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| status | 列舉 | ACTIVE / EXPIRED / EXPIRING_SOON |
| expiredAt | 時間/null | 到期日 |
| daysRemaining | 整數/null | 剩餘天數 |

### 發起續費請求

| 欄位 | 類型 | 說明 |
|------|------|------|
| planId | 字串 | 方案 ID |
| paymentMethod | 列舉 | 付款方式 |

### 發起續費回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| orderId | 字串 | 訂單 ID |
| orderNo | 字串 | 訂單編號 |
| amount | 整數 | 金額 |
| paymentUrl | 字串 | 金流付款頁面 URL |
| expiredAt | 時間 | 訂單過期時間 |

### 發起充值請求

| 欄位 | 類型 | 說明 |
|------|------|------|
| planId | 字串 | 方案 ID |
| paymentMethod | 列舉 | 付款方式 |

### 發起充值回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| orderId | 字串 | 訂單 ID |
| orderNo | 字串 | 訂單編號 |
| amount | 整數 | 金額 |
| points | 整數 | 點數 |
| bonusPoints | 整數 | 贈送點數 |
| paymentUrl | 字串 | 金流付款頁面 URL |
| expiredAt | 時間 | 訂單過期時間 |

### 帳單列表查詢

| 欄位 | 類型 | 說明 |
|------|------|------|
| memberId | 字串 | 會員 ID（選填） |
| memberPhone | 字串 | 會員手機（選填） |
| type | 列舉 | 訂單類型（選填） |
| status | 列舉 | 狀態（選填） |
| startDate | 時間 | 開始日期（選填） |
| endDate | 時間 | 結束日期（選填） |
| page | 整數 | 頁碼（選填） |
| limit | 整數 | 每頁數量（選填） |

### 帳單詳情回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 帳單 ID |
| orderNo | 字串 | 訂單編號 |
| type | 列舉 | 訂單類型 |
| typeName | 字串 | 訂單類型名稱 |
| amount | 整數 | 金額 |
| description | 字串/null | 描述 |
| status | 列舉 | 狀態 |
| statusName | 字串 | 狀態名稱 |
| paymentMethod | 列舉/null | 付款方式 |
| paymentMethodName | 字串/null | 付款方式名稱 |
| member.id | 字串 | 會員 ID |
| member.name | 字串 | 會員姓名 |
| member.phone | 字串 | 會員手機 |
| membershipPlan.name | 字串 | 續費方案名稱（選填） |
| membershipPlan.months | 整數 | 續費月數（選填） |
| rechargePlan.name | 字串 | 充值方案名稱（選填） |
| rechargePlan.points | 整數 | 充值點數（選填） |
| rechargePlan.bonusPoints | 整數 | 贈送點數（選填） |
| createdAt | 時間 | 建立時間 |
| paidAt | 時間/null | 付款時間 |

---

## 金流整合（藍新 NewebPay）

本系統統一使用 **藍新金流（NewebPay）** 作為金流服務平台。

### 環境設定

| 環境 | 付款 API URL |
|------|-------------|
| 測試環境 | https://ccore.newebpay.com/MPG/mpg_gateway |
| 正式環境 | https://core.newebpay.com/MPG/mpg_gateway |

### 金流配置項目

| 配置項 | 說明 |
|--------|------|
| merchantId | 商店代號 |
| hashKey | HashKey |
| hashIV | HashIV |
| version | API 版本（目前 2.0） |
| paymentUrl | 付款 API URL |
| returnUrl | 付款完成返回 URL |
| notifyUrl | 背景通知 URL |
| clientBackUrl | 用戶返回商店 URL |

### 付款流程

| 步驟 | 說明 |
|------|------|
| 1 | 產生交易資料（包含訂單編號、金額、商品說明等） |
| 2 | AES 加密交易資料 |
| 3 | 產生 SHA256 檢查碼 |
| 4 | 表單 POST 至藍新付款頁面 |
| 5 | 用戶完成付款 |
| 6 | 接收背景通知並處理訂單 |

### 回調通知處理

| 步驟 | 說明 |
|------|------|
| 1 | 解密回傳資料 |
| 2 | 驗證檢查碼 |
| 3 | 取得並驗證訂單 |
| 4 | 根據付款結果更新訂單狀態 |
| 5 | 執行後續處理（延長會籍或增加點數） |

### 藍新回傳狀態碼

| 狀態碼 | 說明 |
|--------|------|
| SUCCESS | 付款成功 |
| MPG03009 | 付款失敗 |
| MPG03002 | 訂單已存在 |
| MPG03004 | 訂單編號重複 |
| MPG03005 | 金額錯誤 |

### 支援的付款方式

| 付款方式 | 參數 | 說明 |
|----------|------|------|
| 信用卡 | CREDIT=1 | 一次付清 |
| ATM 轉帳 | VACC=1 | 虛擬帳號 |
| 超商代碼 | CVS=1 | 7-11/全家/萊爾富/OK |
| 超商條碼 | BARCODE=1 | 三段式條碼 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| BIL_001 | 訂單不存在 |
| BIL_002 | 訂單已過期 |
| BIL_003 | 訂單狀態錯誤 |
| BIL_004 | 方案不存在 |
| BIL_005 | 付款方式不支援 |
| BIL_006 | 金流處理失敗 |
| BIL_007 | 重複付款 |

---

## 相關模組

- [Member 會員系統](./member.md) - 會籍管理
- [Point 點數系統](./point.md) - 點數充值
