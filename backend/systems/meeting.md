# Meeting 會員交流預約系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Meeting |
| 開發階段 | Phase 2 |
| 複雜度 | 中 |
| 相依模組 | Auth, Member |
| 被依賴 | Notification |

---

## 功能概述

會員交流預約系統提供會員之間 1 對 1 約見面的功能，可指定見面對象、地點、日期時間與主題，支援預約邀請、確認、完成等流程管理。

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 預約列表 | 查看所有預約記錄 |
| 預約詳情 | 查看預約詳細資訊 |
| 預約統計 | 統計會員交流次數 |
| 搜尋篩選 | 依會員、狀態、時間篩選 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 發起預約 | 向指定會員發起交流預約 |
| 預約列表 | 查看我發起/收到的預約 |
| 預約詳情 | 查看預約詳細資訊 |
| 確認預約 | 接受或拒絕收到的預約 |
| 取消預約 | 取消已發起的預約 |
| 完成交流 | 標記交流已完成 |
| 預約提醒 | 交流前推播通知提醒 |

---

## API 端點設計

### 後台 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/meetings | 預約列表 |
| GET | /api/v1/admin/meetings/:id | 預約詳情 |
| GET | /api/v1/admin/meetings/stats | 預約統計 |
| GET | /api/v1/admin/members/:id/meetings | 指定會員的預約記錄 |

### 客端 API

**預約管理**

| 方法 | 端點 | 說明 |
|------|------|------|
| POST | /api/v1/meetings | 發起預約 |
| GET | /api/v1/meetings | 我的預約列表 |
| GET | /api/v1/meetings/:id | 預約詳情 |
| PUT | /api/v1/meetings/:id | 更新預約資訊 |
| DELETE | /api/v1/meetings/:id | 取消預約 |

**預約回應**

| 方法 | 端點 | 說明 |
|------|------|------|
| POST | /api/v1/meetings/:id/confirm | 確認預約 |
| POST | /api/v1/meetings/:id/reject | 拒絕預約 |
| POST | /api/v1/meetings/:id/complete | 標記完成 |
| POST | /api/v1/meetings/:id/reschedule | 提議改期 |

---

## 資料庫設計

### 預約表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| requesterId | UUID | 發起人 ID（NOT NULL） |
| targetId | UUID | 對象會員 ID（NOT NULL） |
| topic | 字串 | 主題 |
| description | 長文字 | 詳細說明（選填） |
| location | 字串 | 地點名稱 |
| address | 字串 | 詳細地址（選填） |
| meetingAt | 時間 | 見面日期時間 |
| duration | 整數 | 預計時長（分鐘，選填，預設 60） |
| status | 列舉 | 預約狀態（預設 PENDING） |
| note | 字串 | 備註（選填） |
| rejectedReason | 字串 | 拒絕原因（選填） |
| completedAt | 時間 | 完成時間（選填） |
| cancelledAt | 時間 | 取消時間（選填） |
| cancelledBy | UUID | 取消者 ID（選填） |
| cancelReason | 字串 | 取消原因（選填） |
| reminderSent | 布林 | 是否已發送提醒（預設 false） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**預約狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待確認 |
| CONFIRMED | 已確認 |
| COMPLETED | 已完成 |
| CANCELLED | 已取消 |
| REJECTED | 已拒絕 |
| RESCHEDULED | 改期中 |
| EXPIRED | 已過期 |

### 預約改期記錄表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| meetingId | UUID | 預約 ID |
| proposedBy | UUID | 提議者 ID |
| originalTime | 時間 | 原本時間 |
| proposedTime | 時間 | 提議新時間 |
| proposedLocation | 字串 | 提議新地點（選填） |
| status | 列舉 | 狀態（預設 PENDING） |
| createdAt | 時間 | 建立時間 |

**改期狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待確認 |
| ACCEPTED | 已接受 |
| REJECTED | 已拒絕 |

---

## 業務邏輯

### 預約狀態流程

| 起始狀態 | 動作 | 目標狀態 | 說明 |
|----------|------|----------|------|
| - | 發起預約 | PENDING | 新建預約 |
| PENDING | 對方確認 | CONFIRMED | 預約成立 |
| PENDING | 對方拒絕 | REJECTED | 預約被拒絕 |
| PENDING | 發起人取消 | CANCELLED | 取消預約 |
| PENDING | 提議改期 | RESCHEDULED | 進入改期流程 |
| RESCHEDULED | 接受改期 | CONFIRMED | 更新時間後確認 |
| RESCHEDULED | 拒絕改期 | PENDING | 回到待確認 |
| CONFIRMED | 任一方取消 | CANCELLED | 取消預約 |
| CONFIRMED | 標記完成 | COMPLETED | 交流完成 |
| CONFIRMED | 時間已過 | EXPIRED | 自動過期 |

### 發起預約規則

| 規則 | 說明 |
|------|------|
| 1 | 不能向自己發起預約 |
| 2 | 對象會員狀態必須為 ACTIVE |
| 3 | 預約時間必須是未來時間 |
| 4 | 預約時間至少在 1 小時後 |
| 5 | 與同一會員同時間不可重複預約 |
| 6 | 每人每日發起預約上限 5 筆 |

### 確認/拒絕規則

| 規則 | 說明 |
|------|------|
| 1 | 只有對象會員可以確認/拒絕 |
| 2 | 只有 PENDING 狀態可確認/拒絕 |
| 3 | 拒絕時建議填寫原因 |

### 取消規則

| 規則 | 說明 |
|------|------|
| 1 | 發起人和對象會員都可取消 |
| 2 | PENDING、CONFIRMED 狀態可取消 |
| 3 | 已完成或已取消的不可再取消 |
| 4 | 取消時建議填寫原因 |

### 完成規則

| 規則 | 說明 |
|------|------|
| 1 | 只有 CONFIRMED 狀態可標記完成 |
| 2 | 發起人或對象會員都可標記完成 |
| 3 | 完成後增加雙方的交流次數統計 |

### 自動狀態更新（排程任務）

| 原狀態 | 條件 | 新狀態 |
|--------|------|--------|
| PENDING | 預約時間已過 24 小時未確認 | EXPIRED |
| CONFIRMED | 預約時間已過 24 小時未標記完成 | EXPIRED |

### 提醒通知

| 時機 | 對象 | 通知內容 |
|------|------|----------|
| 預約發起時 | 對象會員 | 收到新的交流預約邀請 |
| 確認預約時 | 發起人 | 您的預約已被確認 |
| 拒絕預約時 | 發起人 | 您的預約已被拒絕 |
| 取消預約時 | 對方 | 交流預約已被取消 |
| 預約前 1 小時 | 雙方 | 交流預約即將開始 |
| 預約前 1 天 | 雙方 | 明天有交流預約提醒 |

---

## DTO 資料格式

### 發起預約

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| targetId | UUID | 是 | 對象會員 ID |
| topic | 字串 | 是 | 主題（最多 100 字） |
| description | 字串 | 否 | 詳細說明（最多 500 字） |
| location | 字串 | 是 | 地點名稱 |
| address | 字串 | 否 | 詳細地址 |
| meetingAt | 時間 | 是 | 見面日期時間 |
| duration | 整數 | 否 | 預計時長（分鐘，預設 60） |
| note | 字串 | 否 | 備註 |

### 更新預約

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| topic | 字串 | 否 | 主題 |
| description | 字串 | 否 | 詳細說明 |
| location | 字串 | 否 | 地點名稱 |
| address | 字串 | 否 | 詳細地址 |
| meetingAt | 時間 | 否 | 見面日期時間 |
| duration | 整數 | 否 | 預計時長 |
| note | 字串 | 否 | 備註 |

### 拒絕預約

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| reason | 字串 | 否 | 拒絕原因 |

### 取消預約

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| reason | 字串 | 否 | 取消原因 |

### 提議改期

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| proposedTime | 時間 | 是 | 提議新時間 |
| proposedLocation | 字串 | 否 | 提議新地點 |

### 預約列表查詢

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| type | 列舉 | 否 | sent（我發起的）/ received（我收到的）/ all |
| status | 列舉 | 否 | 預約狀態 |
| startFrom | 時間 | 否 | 開始日期起 |
| startTo | 時間 | 否 | 開始日期迄 |
| page | 整數 | 否 | 頁碼 |
| limit | 整數 | 否 | 每頁數量 |

### 預約詳情回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 預約 ID |
| topic | 字串 | 主題 |
| description | 字串/null | 詳細說明 |
| location | 字串 | 地點名稱 |
| address | 字串/null | 詳細地址 |
| meetingAt | 時間 | 見面日期時間 |
| duration | 整數 | 預計時長（分鐘） |
| status | 列舉 | 預約狀態 |
| note | 字串/null | 備註 |
| requester.id | UUID | 發起人 ID |
| requester.name | 字串 | 發起人姓名 |
| requester.avatar | 字串/null | 發起人頭像 |
| requester.phone | 字串 | 發起人電話 |
| target.id | UUID | 對象會員 ID |
| target.name | 字串 | 對象會員姓名 |
| target.avatar | 字串/null | 對象會員頭像 |
| target.phone | 字串 | 對象會員電話 |
| rejectedReason | 字串/null | 拒絕原因 |
| cancelReason | 字串/null | 取消原因 |
| cancelledBy | UUID/null | 取消者 ID |
| completedAt | 時間/null | 完成時間 |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

### 預約列表回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 預約 ID |
| topic | 字串 | 主題 |
| location | 字串 | 地點名稱 |
| meetingAt | 時間 | 見面日期時間 |
| status | 列舉 | 預約狀態 |
| isRequester | 布林 | 是否為發起人 |
| counterpart.id | UUID | 對方 ID |
| counterpart.name | 字串 | 對方姓名 |
| counterpart.avatar | 字串/null | 對方頭像 |
| createdAt | 時間 | 建立時間 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| MTG_001 | 預約不存在 |
| MTG_002 | 不能向自己發起預約 |
| MTG_003 | 對象會員不存在或已停用 |
| MTG_004 | 預約時間必須是未來時間 |
| MTG_005 | 預約時間至少需在 1 小時後 |
| MTG_006 | 與該會員已有進行中的預約 |
| MTG_007 | 今日發起預約次數已達上限 |
| MTG_008 | 無權限執行此操作 |
| MTG_009 | 目前狀態無法執行此操作 |
| MTG_010 | 只有對象會員可以確認/拒絕預約 |
| MTG_011 | 預約已過期 |
| MTG_012 | 改期提議不存在 |

---

## 相關模組

- [Auth 認證系統](./auth.md) - 身份驗證
- [Member 會員系統](./member.md) - 會員資料、交流次數統計
- [Notification 通知系統](./notification.md) - 預約通知推播
