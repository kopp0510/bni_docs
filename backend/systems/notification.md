# Notification 通知系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Notification |
| 開發階段 | Phase 5 |
| 複雜度 | 中 |
| 相依模組 | Auth, Member |
| 被依賴 | 無 |

---

## 功能概述

通知系統負責管理應用程式內的通知推送，包含系統公告、活動提醒、聊天訊息推播等，支援站內通知與 Firebase Cloud Messaging (FCM) 推播。

---

## 通知類型

| 代碼 | 名稱 | 說明 |
|------|------|------|
| SYSTEM | 系統通知 | 系統公告、維護通知 |
| EVENT | 活動通知 | 活動報名、開始提醒 |
| CHAT | 聊天通知 | 新訊息通知 |
| POINT | 點數通知 | 點數異動、收款通知 |
| MEMBER | 會員通知 | 會籍到期、審核結果 |
| SHOP | 店鋪通知 | 店鋪審核、推薦 |
| BILLING | 帳務通知 | 繳費提醒、付款成功 |
| DONATION | 捐贈通知 | 捐贈成功、專案更新 |
| MEETING | 交流預約通知 | 預約邀請、確認、提醒 |

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 通知列表 | 查看所有推送記錄 |
| 推送管理 | 建立群發通知 |
| 推送對象 | 指定推送對象（全部/商會/個人） |
| 排程推送 | 設定定時推送 |
| 統計報表 | 查看推送成功率 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 通知列表 | 查看我的通知 |
| 未讀提示 | 顯示未讀數量 |
| 通知詳情 | 查看通知內容 |
| 標記已讀 | 單則/全部已讀 |
| 通知設定 | 管理通知偏好 |
| 推播權限 | 開啟/關閉推播 |

---

## API 端點設計

### 後台 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/notifications | 通知列表 |
| GET | /api/v1/admin/notifications/:id | 通知詳情 |
| POST | /api/v1/admin/notifications | 建立通知 |
| PUT | /api/v1/admin/notifications/:id | 編輯通知 |
| DELETE | /api/v1/admin/notifications/:id | 刪除通知 |
| POST | /api/v1/admin/notifications/:id/send | 立即發送 |
| GET | /api/v1/admin/notifications/stats | 推送統計 |
| GET | /api/v1/admin/notifications/:id/stats | 單則通知統計 |

### 客端 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/notifications | 我的通知列表 |
| GET | /api/v1/notifications/unread-count | 未讀數量 |
| PUT | /api/v1/notifications/:id/read | 標記單則已讀 |
| PUT | /api/v1/notifications/read-all | 標記全部已讀 |
| DELETE | /api/v1/notifications/:id | 刪除通知 |
| POST | /api/v1/notifications/device | 註冊裝置 token |
| DELETE | /api/v1/notifications/device/:deviceId | 移除裝置 |
| GET | /api/v1/notifications/settings | 取得通知設定 |
| PUT | /api/v1/notifications/settings | 更新通知設定 |

---

## 資料庫設計

### 通知表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| type | 列舉 | 通知類型 |
| title | 字串 | 標題 |
| content | 長文字 | 內容 |
| actionType | 列舉 | 動作類型（選填） |
| actionData | JSON | 動作參數（選填） |
| targetType | 列舉 | 對象類型（預設 ALL） |
| targetIds | 字串陣列 | 指定對象 ID（預設空陣列） |
| scheduledAt | 時間 | 排程發送時間（選填） |
| sentAt | 時間 | 實際發送時間（選填） |
| status | 列舉 | 狀態（預設 DRAFT） |
| totalCount | 整數 | 總推送數（預設 0） |
| successCount | 整數 | 成功數（預設 0） |
| failedCount | 整數 | 失敗數（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |
| createdBy | 字串 | 建立者 |

**通知類型列舉值：**

| 值 | 說明 |
|------|------|
| SYSTEM | 系統通知 |
| EVENT | 活動通知 |
| CHAT | 聊天通知 |
| POINT | 點數通知 |
| MEMBER | 會員通知 |
| SHOP | 店鋪通知 |
| BILLING | 帳務通知 |
| DONATION | 捐贈通知 |
| MEETING | 交流預約通知 |

**動作類型列舉值：**

| 值 | 說明 |
|------|------|
| NONE | 無動作 |
| OPEN_URL | 開啟網址 |
| OPEN_EVENT | 開啟活動 |
| OPEN_SHOP | 開啟店鋪 |
| OPEN_CHAT | 開啟聊天 |
| OPEN_ORDER | 開啟訂單 |
| OPEN_PAGE | 開啟內部頁面 |
| OPEN_MEETING | 開啟交流預約 |

**對象類型列舉值：**

| 值 | 說明 |
|------|------|
| ALL | 全部會員 |
| ORGANIZATION | 指定商會 |
| MEMBER | 指定會員 |
| MEMBER_TYPE | 指定會員類型 |

**通知狀態列舉值：**

| 值 | 說明 |
|------|------|
| DRAFT | 草稿 |
| SCHEDULED | 已排程 |
| SENDING | 發送中 |
| SENT | 已發送 |
| CANCELLED | 已取消 |

### 通知接收者表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| notificationId | 字串 | 通知 ID |
| memberId | 字串 | 會員 ID |
| isRead | 布林 | 是否已讀（預設 false） |
| readAt | 時間 | 已讀時間（選填） |
| isDeleted | 布林 | 是否已刪除（預設 false） |
| deletedAt | 時間 | 刪除時間（選填） |
| pushStatus | 列舉 | 推播狀態（預設 PENDING） |
| pushError | 字串 | 推播失敗原因（選填） |
| pushedAt | 時間 | 推播時間（選填） |
| createdAt | 時間 | 建立時間 |

**推播狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待推送 |
| SENT | 已推送 |
| FAILED | 推送失敗 |
| SKIPPED | 跳過（用戶關閉推播） |

### 裝置 Token 表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| memberId | 字串 | 會員 ID |
| token | 字串 | FCM token（唯一） |
| platform | 列舉 | 平台 |
| deviceId | 字串 | 裝置識別碼（選填） |
| deviceModel | 字串 | 裝置型號（選填） |
| osVersion | 字串 | 系統版本（選填） |
| appVersion | 字串 | App 版本（選填） |
| isActive | 布林 | 是否啟用（預設 true） |
| lastUsedAt | 時間 | 最後使用時間 |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**裝置平台列舉值：**

| 值 | 說明 |
|------|------|
| IOS | iOS 裝置 |
| ANDROID | Android 裝置 |
| WEB | 網頁版 |

### 通知設定表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| memberId | 字串 | 會員 ID（唯一） |
| pushEnabled | 布林 | 總開關（預設 true） |
| systemEnabled | 布林 | 系統通知（預設 true） |
| eventEnabled | 布林 | 活動通知（預設 true） |
| chatEnabled | 布林 | 聊天通知（預設 true） |
| pointEnabled | 布林 | 點數通知（預設 true） |
| memberEnabled | 布林 | 會員通知（預設 true） |
| shopEnabled | 布林 | 店鋪通知（預設 true） |
| billingEnabled | 布林 | 帳務通知（預設 true） |
| donationEnabled | 布林 | 捐贈通知（預設 true） |
| meetingEnabled | 布林 | 交流預約通知（預設 true） |
| quietHoursEnabled | 布林 | 勿擾時段開關（預設 false） |
| quietHoursStart | 字串 | 勿擾開始時間（選填，如 "22:00"） |
| quietHoursEnd | 字串 | 勿擾結束時間（選填，如 "08:00"） |
| updatedAt | 時間 | 更新時間 |

---

## 業務邏輯

### 發送通知流程

| 步驟 | 說明 |
|------|------|
| 1 | 建立通知記錄 |
| 2 | 確定發送對象 |
| 3 | 建立接收者記錄 |
| 4 | 發送站內通知（即時） |
| 5 | 發送 FCM 推播（異步佇列） |
| 6 | 更新統計數據 |

### 建立通知流程

| 步驟 | 說明 |
|------|------|
| 1 | 取得目標會員列表 |
| 2 | 建立通知記錄 |
| 3 | 建立所有接收者記錄 |
| 4 | 若有排程時間則設為 SCHEDULED，否則為 DRAFT |

### 發送推播流程

| 步驟 | 說明 |
|------|------|
| 1 | 取得通知及待推送的接收者 |
| 2 | 更新通知狀態為 SENDING |
| 3 | 遍歷每位接收者 |
| 4 | 檢查通知設定（是否開啟該類型通知） |
| 5 | 檢查勿擾時段 |
| 6 | 發送到用戶的所有裝置 |
| 7 | 更新接收者推播狀態 |
| 8 | 更新通知統計（成功/失敗數量） |

### 通知設定檢查規則

| 通知類型 | 對應設定 |
|----------|----------|
| SYSTEM | systemEnabled |
| EVENT | eventEnabled |
| CHAT | chatEnabled |
| POINT | pointEnabled |
| MEMBER | memberEnabled |
| SHOP | shopEnabled |
| BILLING | billingEnabled |
| DONATION | donationEnabled |
| MEETING | meetingEnabled |

若 pushEnabled = false，則所有推播都不發送。

### 勿擾時段檢查

- 支援跨日設定（如 22:00 - 08:00）
- 在勿擾時段內的推播會延遲發送

### 目標會員取得規則

| 對象類型 | 取得方式 |
|----------|----------|
| ALL | 所有 ACTIVE 狀態的會員 |
| ORGANIZATION | 指定商會的 ACTIVE 會員 |
| MEMBER | 直接使用指定的會員 ID |
| MEMBER_TYPE | 指定會員類型的 ACTIVE 會員 |

### 即時通知

透過 WebSocket 即時推送站內通知給在線用戶。

---

## 排程任務

### 定時推送處理

- 執行頻率：每分鐘
- 功能：檢查已到期的排程通知並發送

### 清理過期通知

- 執行頻率：每日凌晨
- 功能：刪除 30 天前已讀的通知接收記錄

---

## DTO 資料格式

### 建立通知請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| type | 列舉 | 是 | 通知類型 |
| title | 字串 | 是 | 標題 |
| content | 字串 | 是 | 內容 |
| actionType | 列舉 | 否 | 動作類型 |
| actionData.url | 字串 | 否 | 開啟網址 |
| actionData.eventId | 字串 | 否 | 活動 ID |
| actionData.shopId | 字串 | 否 | 店鋪 ID |
| actionData.chatRoomId | 字串 | 否 | 聊天室 ID |
| actionData.orderId | 字串 | 否 | 訂單 ID |
| actionData.page | 字串 | 否 | 內部頁面 |
| actionData.meetingId | 字串 | 否 | 交流預約 ID |
| targetType | 列舉 | 是 | 對象類型 |
| targetIds | 字串陣列 | 否 | 指定對象 ID |
| scheduledAt | 時間 | 否 | 排程時間 |

### 通知列表回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 通知 ID |
| type | 列舉 | 通知類型 |
| typeName | 字串 | 通知類型名稱 |
| title | 字串 | 標題 |
| content | 字串 | 內容 |
| actionType | 列舉/null | 動作類型 |
| isRead | 布林 | 是否已讀 |
| createdAt | 時間 | 建立時間 |

### 未讀數量回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| total | 整數 | 總未讀數 |
| byType.system | 整數 | 系統通知未讀數 |
| byType.event | 整數 | 活動通知未讀數 |
| byType.chat | 整數 | 聊天通知未讀數 |
| byType.point | 整數 | 點數通知未讀數 |
| byType.member | 整數 | 會員通知未讀數 |
| byType.shop | 整數 | 店鋪通知未讀數 |
| byType.billing | 整數 | 帳務通知未讀數 |
| byType.donation | 整數 | 捐贈通知未讀數 |
| byType.meeting | 整數 | 交流預約通知未讀數 |

### 裝置註冊請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| token | 字串 | 是 | FCM token |
| platform | 列舉 | 是 | 平台 |
| deviceId | 字串 | 否 | 裝置識別碼 |
| deviceModel | 字串 | 否 | 裝置型號 |
| osVersion | 字串 | 否 | 系統版本 |
| appVersion | 字串 | 否 | App 版本 |

### 通知設定回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| pushEnabled | 布林 | 總開關 |
| systemEnabled | 布林 | 系統通知 |
| eventEnabled | 布林 | 活動通知 |
| chatEnabled | 布林 | 聊天通知 |
| pointEnabled | 布林 | 點數通知 |
| memberEnabled | 布林 | 會員通知 |
| shopEnabled | 布林 | 店鋪通知 |
| billingEnabled | 布林 | 帳務通知 |
| donationEnabled | 布林 | 捐贈通知 |
| meetingEnabled | 布林 | 交流預約通知 |
| quietHoursEnabled | 布林 | 勿擾時段開關 |
| quietHoursStart | 字串/null | 勿擾開始時間 |
| quietHoursEnd | 字串/null | 勿擾結束時間 |

### 更新通知設定請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| pushEnabled | 布林 | 否 | 總開關 |
| systemEnabled | 布林 | 否 | 系統通知 |
| eventEnabled | 布林 | 否 | 活動通知 |
| chatEnabled | 布林 | 否 | 聊天通知 |
| pointEnabled | 布林 | 否 | 點數通知 |
| memberEnabled | 布林 | 否 | 會員通知 |
| shopEnabled | 布林 | 否 | 店鋪通知 |
| billingEnabled | 布林 | 否 | 帳務通知 |
| donationEnabled | 布林 | 否 | 捐贈通知 |
| meetingEnabled | 布林 | 否 | 交流預約通知 |
| quietHoursEnabled | 布林 | 否 | 勿擾時段開關 |
| quietHoursStart | 字串 | 否 | 勿擾開始時間 |
| quietHoursEnd | 字串 | 否 | 勿擾結束時間 |

### 後台通知查詢請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| type | 列舉 | 否 | 通知類型 |
| status | 列舉 | 否 | 狀態 |
| startDate | 時間 | 否 | 開始日期 |
| endDate | 時間 | 否 | 結束日期 |
| page | 整數 | 否 | 頁碼 |
| limit | 整數 | 否 | 每頁數量 |

### 通知統計回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| total | 整數 | 總通知數 |
| byType | 物件 | 各類型數量 |
| byStatus | 物件 | 各狀態數量 |
| successRate | 數字 | 推送成功率 |
| todaySent | 整數 | 今日發送數 |

---

## FCM 整合

### Firebase 設定

| 設定項 | 說明 |
|--------|------|
| projectId | Firebase 專案 ID |
| clientEmail | 服務帳戶 Email |
| privateKey | 服務帳戶私鑰 |

### 推播訊息設定

| 平台 | 設定 |
|------|------|
| Android | priority: high, sound: default |
| iOS | sound: default, badge: 1 |

### Token 失效處理

當推播失敗且錯誤為 Token 無效時，自動將該 Token 標記為 isActive = false。

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| NTF_001 | 通知不存在 |
| NTF_002 | 無權限存取此通知 |
| NTF_003 | 裝置 Token 無效 |
| NTF_004 | 推播發送失敗 |
| NTF_005 | 排程時間必須是未來時間 |
| NTF_006 | 無效的推送對象 |
| NTF_007 | 通知已發送，無法編輯 |

---

## 相關模組

- [Auth 認證系統](./auth.md) - 身份驗證
- [Member 會員系統](./member.md) - 通知接收者
- [Chat 聊天系統](./chat.md) - WebSocket 即時通知、聊天訊息推播
- [Meeting 交流預約系統](./meeting.md) - 預約邀請、確認、提醒通知
