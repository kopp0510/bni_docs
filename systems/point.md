# Point 點數系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Point |
| 開發階段 | Phase 2 |
| 複雜度 | 中 |
| 相依模組 | Auth, Member |
| 被依賴 | Event, Donation, Lottery, Billing |

---

## 功能概述

點數系統管理會員的愛心點數與獎勵積分，提供 QRCode 收付款、點數兌換等功能。

---

## 點數類型說明

| 類型 | 說明 | 用途 |
|------|------|------|
| 愛心點數 | 主要交易貨幣 | 消費、捐贈、兌換 |
| 獎勵積分 | 活動獎勵 | 活動報名、抽獎 |

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 會員點數調整 | 手動調整會員點數/積分 |
| 點數明細查詢 | 查看點數變動紀錄 |
| 交易紀錄查詢 | 查看所有交易紀錄 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 點數記錄 | 查看點數變動明細 |
| QRCode 收款 | 商家生成收款 QRCode |
| 掃碼付款 | 掃描商家 QRCode 付款 |
| 加點充值 | 充值愛心點數 |
| 商家兌換 | 點數兌換現金（1000點=1000元） |

---

## API 端點設計

### 後台 API

**點數管理**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/points/records | 全部點數紀錄 |
| GET | /api/v1/admin/points/transactions | 交易紀錄 |
| POST | /api/v1/admin/members/:id/points | 調整會員點數 |

**統計**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/points/stats | 點數統計概覽 |

### 客端 API

**點數查詢**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/points/balance | 查詢點數餘額 |
| GET | /api/v1/points/records | 點數變動紀錄 |

**QRCode 收付款**

| 方法 | 端點 | 說明 |
|------|------|------|
| POST | /api/v1/points/qrcode/generate | 生成收款 QRCode |
| POST | /api/v1/points/qrcode/pay | 掃碼付款 |
| GET | /api/v1/points/qrcode/verify/:code | 驗證 QRCode |

**充值**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/points/recharge/plans | 充值方案列表 |
| POST | /api/v1/points/recharge | 發起充值 |

**兌換**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/points/exchange/rules | 兌換規則 |
| POST | /api/v1/points/exchange | 申請兌換 |
| GET | /api/v1/points/exchange/records | 兌換紀錄 |

---

## 資料庫設計

### 點數紀錄表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| memberId | 字串 | 會員 ID |
| type | 列舉 | 點數類型 |
| action | 列舉 | 動作類型 |
| amount | 整數 | 變動數量（正增負減） |
| balance | 整數 | 變動後餘額 |
| referenceType | 列舉 | 關聯類型（選填） |
| referenceId | 字串 | 關聯 ID（選填） |
| description | 字串 | 描述（選填） |
| operatorId | 字串 | 操作人（後台調整時，選填） |
| createdAt | 時間 | 建立時間 |

**點數類型列舉值：**

| 值 | 說明 |
|------|------|
| LOVE_POINT | 愛心點數 |
| REWARD_POINT | 獎勵積分 |

**點數動作列舉值：**

| 值 | 說明 |
|------|------|
| RECHARGE | 充值 |
| PAYMENT | 付款 |
| RECEIVE | 收款 |
| EXCHANGE | 兌換 |
| DONATION | 捐贈 |
| EVENT_COST | 活動消耗 |
| EVENT_REWARD | 活動獎勵 |
| LOTTERY | 抽獎獲得 |
| ADMIN_ADJUST | 後台調整 |
| REFERRAL_REWARD | 推薦獎勵 |
| EXPIRED | 過期 |

**關聯類型列舉值：**

| 值 | 說明 |
|------|------|
| TRANSACTION | 交易 |
| EVENT | 活動 |
| DONATION | 捐贈 |
| LOTTERY | 抽獎 |
| EXCHANGE | 兌換 |
| RECHARGE | 充值 |

### 點數交易表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| code | 字串 | 交易編號（唯一） |
| senderId | 字串 | 付款方 ID |
| receiverId | 字串 | 收款方 ID |
| amount | 整數 | 交易點數 |
| type | 列舉 | 交易類型 |
| status | 列舉 | 交易狀態（預設 PENDING） |
| qrcodeData | 字串 | QRCode 資料（選填） |
| description | 字串 | 描述（選填） |
| createdAt | 時間 | 建立時間 |
| completedAt | 時間 | 完成時間（選填） |

**交易類型列舉值：**

| 值 | 說明 |
|------|------|
| STORE_PAYMENT | 店鋪消費 |
| TRANSFER | 點數轉帳 |

**交易狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待處理 |
| COMPLETED | 已完成 |
| CANCELLED | 已取消 |
| FAILED | 失敗 |

### 充值紀錄表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| code | 字串 | 充值單號（唯一） |
| memberId | 字串 | 會員 ID |
| planId | 字串 | 充值方案 ID（選填） |
| amount | 整數 | 充值金額 |
| points | 整數 | 獲得點數 |
| bonusPoints | 整數 | 贈送點數（預設 0） |
| paymentMethod | 字串 | 付款方式（選填） |
| paymentData | JSON | 金流回傳資料（選填） |
| status | 列舉 | 充值狀態（預設 PENDING） |
| createdAt | 時間 | 建立時間 |
| paidAt | 時間 | 付款時間（選填） |

**充值狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待付款 |
| PAID | 已付款 |
| CANCELLED | 已取消 |
| FAILED | 失敗 |

### 充值方案表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| name | 字串 | 方案名稱 |
| amount | 整數 | 金額 |
| points | 整數 | 基本點數 |
| bonusPoints | 整數 | 贈送點數（預設 0） |
| isActive | 布林 | 是否啟用（預設 true） |
| sortOrder | 整數 | 排序順序（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

### 兌換紀錄表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| code | 字串 | 兌換單號（唯一） |
| memberId | 字串 | 會員 ID |
| points | 整數 | 兌換點數 |
| amount | 整數 | 兌換金額 |
| bankCode | 字串 | 銀行代碼（選填） |
| bankAccount | 字串 | 銀行帳號（選填） |
| accountName | 字串 | 戶名（選填） |
| status | 列舉 | 兌換狀態（預設 PENDING） |
| note | 字串 | 備註（選填） |
| createdAt | 時間 | 建立時間 |
| processedAt | 時間 | 處理時間（選填） |

**兌換狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待處理 |
| APPROVED | 已核准 |
| COMPLETED | 已完成 |
| REJECTED | 已拒絕 |

---

## 業務邏輯

### QRCode 收款流程

| 步驟 | 說明 |
|------|------|
| 1 | 商家設定收款金額 |
| 2 | 系統生成 QRCode（包含商家ID、金額、時間戳、簽名） |
| 3 | 付款方掃描 QRCode |
| 4 | 驗證 QRCode 有效性（時效、簽名） |
| 5 | 確認付款金額 |
| 6 | 扣除付款方點數 |
| 7 | 增加收款方點數 |
| 8 | 建立交易紀錄 |

### QRCode 資料內容

| 欄位 | 說明 |
|------|------|
| merchantId | 商家會員 ID |
| amount | 收款金額 |
| timestamp | 時間戳 |
| nonce | 隨機數 |
| signature | 簽名 |

### QRCode 有效期

| 項目 | 說明 |
|------|------|
| 有效時間 | 5 分鐘 |
| 使用次數 | 一次性 |

### 充值方案範例

| 方案名稱 | 金額 | 基本點數 | 贈送點數 |
|----------|------|----------|----------|
| 基本方案 | 1000 | 1000 | 0 |
| 超值方案 | 3000 | 3000 | 150 |
| 豪華方案 | 5000 | 5000 | 350 |
| 尊爵方案 | 10000 | 10000 | 1000 |

### 兌換規則

| 項目 | 說明 |
|------|------|
| 兌換比例 | 1000 點 = 1000 元 |
| 最低兌換 | 1000 點 |
| 兌換單位 | 1000 點為單位 |
| 處理時間 | 3-5 個工作天 |

---

## DTO 資料格式

### 生成收款 QRCode 請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| amount | 整數 | 是 | 收款金額 |

### 生成收款 QRCode 回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| qrcode | 字串 | QRCode 圖片（Base64） |
| data | 字串 | QRCode 資料 |
| expiresAt | 時間 | 過期時間 |

### 掃碼付款請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| qrcodeData | 字串 | 是 | QRCode 資料 |

### 付款結果回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| transactionId | 字串 | 交易 ID |
| amount | 整數 | 交易金額 |
| merchantName | 字串 | 商家名稱 |
| balance | 整數 | 付款後餘額 |

### 充值請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| planId | 字串 | 是 | 方案 ID |

### 充值結果回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| orderId | 字串 | 訂單 ID |
| paymentUrl | 字串 | 金流付款頁面 |

### 兌換申請請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| points | 整數 | 是 | 兌換點數 |
| bankCode | 字串 | 是 | 銀行代碼 |
| bankAccount | 字串 | 是 | 銀行帳號 |
| accountName | 字串 | 是 | 戶名 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| PNT_001 | 點數不足 |
| PNT_002 | QRCode 已過期 |
| PNT_003 | QRCode 無效 |
| PNT_004 | 交易失敗 |
| PNT_005 | 充值失敗 |
| PNT_006 | 兌換金額不符合規則 |
| PNT_007 | 兌換申請處理中 |
| PNT_008 | 不能給自己付款 |

---

## 相關模組

- [Member 會員系統](./member.md) - 會員點數餘額
- [Shop 店鋪系統](./shop.md) - 店鋪收款
- [Event 活動系統](./event.md) - 活動積分消耗
- [Donation 捐贈系統](./donation.md) - 點數捐贈
- [Lottery 抽獎系統](./lottery.md) - 抽獎獎勵
- [Billing 帳務系統](./billing.md) - 充值金流
