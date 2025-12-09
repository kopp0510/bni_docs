# Referral 推薦系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Referral |
| 開發階段 | Phase 2 |
| 複雜度 | 中 |
| 相依模組 | Auth, Member |
| 被依賴 | Notification |

---

## 功能概述

推薦系統提供會員之間互相引薦人脈的功能，會員可以將潛在客戶或合作對象推薦給其他會員，並追蹤推薦的後續狀態。

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 推薦記錄列表 | 查看所有推薦記錄 |
| 搜尋篩選 | 依推薦人、被推薦人、狀態篩選 |
| 推薦統計 | 查看推薦數據統計 |
| 匯出報表 | 匯出推薦記錄報表 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 建立推薦 | 推薦人選給其他會員 |
| 我的推薦 | 查看我推薦出去的記錄 |
| 收到的推薦 | 查看別人推薦給我的人選 |
| 推薦詳情 | 查看推薦詳細資訊 |
| 更新狀態 | 更新推薦處理狀態 |
| 推薦統計 | 個人推薦數據統計 |

---

## API 端點設計

### 後台 API

**推薦管理**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/referrals | 推薦記錄列表 |
| GET | /api/v1/admin/referrals/:id | 推薦詳情 |
| GET | /api/v1/admin/referrals/stats | 推薦統計 |
| GET | /api/v1/admin/referrals/export | 匯出報表 |

### 客端 API

**推薦操作**

| 方法 | 端點 | 說明 |
|------|------|------|
| POST | /api/v1/referrals | 建立推薦 |
| GET | /api/v1/referrals/sent | 我推薦出去的 |
| GET | /api/v1/referrals/received | 我收到的推薦 |
| GET | /api/v1/referrals/:id | 推薦詳情 |
| PUT | /api/v1/referrals/:id/status | 更新推薦狀態 |
| GET | /api/v1/referrals/stats | 我的推薦統計 |

**會員搜尋**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/referrals/members | 搜尋可推薦的會員 |

---

## 資料庫設計

### 推薦記錄表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| referrerId | 字串 | 推薦人（會員 ID） |
| receiverId | 字串 | 接收人（會員 ID） |
| referredName | 字串 | 被推薦人姓名 |
| referredPhone | 字串 | 被推薦人電話（選填） |
| referredEmail | 字串 | 被推薦人 Email（選填） |
| referredCompany | 字串 | 被推薦人公司（選填） |
| referredTitle | 字串 | 被推薦人職稱（選填） |
| description | 長文字 | 推薦說明 |
| status | 列舉 | 推薦狀態（預設 PENDING） |
| feedback | 長文字 | 處理回饋（選填） |
| contactedAt | 時間 | 聯繫時間（選填） |
| closedAt | 時間 | 結案時間（選填） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**推薦狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待處理 |
| CONTACTED | 已聯繫 |
| IN_PROGRESS | 洽談中 |
| CLOSED_WON | 已成交 |
| CLOSED_LOST | 未成交 |
| REJECTED | 已拒絕 |

---

## 業務邏輯

### 推薦流程

| 步驟 | 說明 |
|------|------|
| 1 | 會員 A（推薦人）選擇會員 B（接收人） |
| 2 | 填寫被推薦人資訊（姓名、聯絡方式、說明） |
| 3 | 送出推薦，狀態為 PENDING |
| 4 | 系統發送通知給會員 B |
| 5 | 會員 B 查看推薦並更新狀態 |
| 6 | 推薦結案（成交/未成交/拒絕） |

### 推薦規則

| 規則 | 說明 |
|------|------|
| 1 | 推薦人和接收人必須是不同的會員 |
| 2 | 推薦人和接收人會員狀態必須為 ACTIVE |
| 3 | 被推薦人姓名為必填 |
| 4 | 電話或 Email 至少填一項 |

### 狀態流轉規則

| 原狀態 | 可變更為 | 操作者 |
|--------|----------|--------|
| PENDING | CONTACTED, REJECTED | 接收人 |
| CONTACTED | IN_PROGRESS, CLOSED_LOST, REJECTED | 接收人 |
| IN_PROGRESS | CLOSED_WON, CLOSED_LOST | 接收人 |
| CLOSED_WON | - | 不可變更 |
| CLOSED_LOST | - | 不可變更 |
| REJECTED | - | 不可變更 |

### 通知觸發

| 事件 | 通知對象 | 通知內容 |
|------|----------|----------|
| 建立推薦 | 接收人 | 您收到一筆新的推薦 |
| 狀態更新 | 推薦人 | 您的推薦狀態已更新 |
| 成交 | 推薦人 | 恭喜！您的推薦已成交 |

---

## DTO 資料格式

### 建立推薦

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| receiverId | 字串 | 是 | 接收人會員 ID |
| referredName | 字串 | 是 | 被推薦人姓名 |
| referredPhone | 字串 | 否 | 被推薦人電話 |
| referredEmail | 字串 | 否 | 被推薦人 Email |
| referredCompany | 字串 | 否 | 被推薦人公司 |
| referredTitle | 字串 | 否 | 被推薦人職稱 |
| description | 字串 | 否 | 推薦說明 |

### 更新推薦狀態

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| status | 列舉 | 是 | 新狀態 |
| feedback | 字串 | 否 | 處理回饋 |

### 推薦詳情回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 推薦 ID |
| referrer.id | 字串 | 推薦人 ID |
| referrer.name | 字串 | 推薦人姓名 |
| referrer.avatar | 字串 | 推薦人頭像 |
| referrer.company | 字串 | 推薦人公司 |
| receiver.id | 字串 | 接收人 ID |
| receiver.name | 字串 | 接收人姓名 |
| receiver.avatar | 字串 | 接收人頭像 |
| receiver.company | 字串 | 接收人公司 |
| referredName | 字串 | 被推薦人姓名 |
| referredPhone | 字串/null | 被推薦人電話 |
| referredEmail | 字串/null | 被推薦人 Email |
| referredCompany | 字串/null | 被推薦人公司 |
| referredTitle | 字串/null | 被推薦人職稱 |
| description | 字串/null | 推薦說明 |
| status | 列舉 | 推薦狀態 |
| statusName | 字串 | 狀態名稱 |
| feedback | 字串/null | 處理回饋 |
| contactedAt | 時間/null | 聯繫時間 |
| closedAt | 時間/null | 結案時間 |
| createdAt | 時間 | 建立時間 |

### 推薦列表查詢

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| keyword | 字串 | 否 | 搜尋關鍵字（被推薦人姓名、公司） |
| status | 列舉 | 否 | 推薦狀態 |
| startDate | 時間 | 否 | 開始日期 |
| endDate | 時間 | 否 | 結束日期 |
| page | 整數 | 否 | 頁碼 |
| limit | 整數 | 否 | 每頁數量 |

### 推薦統計回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| totalSent | 整數 | 總推薦出去數 |
| totalReceived | 整數 | 總收到推薦數 |
| pendingCount | 整數 | 待處理數 |
| inProgressCount | 整數 | 洽談中數 |
| closedWonCount | 整數 | 成交數 |
| closedLostCount | 整數 | 未成交數 |
| successRate | 浮點數 | 成功率（成交/已結案） |

### 後台推薦列表查詢

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| referrerId | 字串 | 否 | 推薦人 ID |
| receiverId | 字串 | 否 | 接收人 ID |
| referrerName | 字串 | 否 | 推薦人姓名 |
| receiverName | 字串 | 否 | 接收人姓名 |
| referredName | 字串 | 否 | 被推薦人姓名 |
| status | 列舉 | 否 | 推薦狀態 |
| organizationId | 字串 | 否 | 商會 ID |
| startDate | 時間 | 否 | 開始日期 |
| endDate | 時間 | 否 | 結束日期 |
| page | 整數 | 否 | 頁碼 |
| limit | 整數 | 否 | 每頁數量 |

### 後台統計回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| totalReferrals | 整數 | 總推薦數 |
| thisMonthReferrals | 整數 | 本月推薦數 |
| pendingCount | 整數 | 待處理數 |
| closedWonCount | 整數 | 成交數 |
| closedLostCount | 整數 | 未成交數 |
| overallSuccessRate | 浮點數 | 整體成功率 |
| topReferrers | 陣列 | 推薦排行榜 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| REF_001 | 推薦記錄不存在 |
| REF_002 | 無權限查看此推薦 |
| REF_003 | 無權限更新此推薦狀態 |
| REF_004 | 不可推薦給自己 |
| REF_005 | 接收人不存在或非活躍會員 |
| REF_006 | 推薦狀態不可變更 |
| REF_007 | 電話或 Email 至少填一項 |

---

## 相關模組

- [Member 會員系統](./member.md) - 推薦人與接收人
- [Notification 通知系統](./notification.md) - 推薦通知
