# Chat 聊天系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Chat |
| 開發階段 | Phase 5 |
| 複雜度 | 高 |
| 相依模組 | Auth, Member |
| 被依賴 | Notification |

---

## 技術方案評估

> ⚠️ **待決定**：聊天系統的實作方案尚在評估中。

### 方案比較

| 方案 | 說明 | 優點 | 缺點 |
|------|------|------|------|
| **方案 A：自建** | 使用 NestJS + Socket.io 自行開發 | 與現有架構整合簡單、維護成本可控 | 功能較基礎、擴展性有限 |
| **方案 B：OpenIMSDK** | 整合開源 IM 解決方案 | 功能完整、支援大規模、多平台 SDK | 架構複雜、需額外維護 |

### 方案 B：OpenIMSDK（考慮中）

**專案連結**：https://github.com/OpenIMSDK

**簡介**：
- 由前微信技術專家打造的開源 IM 解決方案
- 使用 Go 語言開發，GitHub 15k+ stars
- 支援私有化部署，資料完全自有

**核心功能**：
- 單聊、群聊（支援萬人大群）
- 訊息類型：文字、圖片、語音、影片、檔案
- 已讀回執、訊息撤回、轉發
- 離線推送（iOS/Android）
- 歷史訊息漫遊

**技術架構**：
```
┌─────────────────────────────────────────────────────────────┐
│                    OpenIM Server (Go)                        │
│                      微服務架構                              │
└─────────────────────────────────────────────────────────────┘
                              │
┌──────────┬──────────┬──────────┬──────────┬──────────┐
│  MySQL   │ MongoDB  │  Redis   │  Kafka   │   ETCD   │
│ 用戶資料  │ 訊息儲存  │ 快取/Token│ 訊息佇列  │ 服務發現  │
└──────────┴──────────┴──────────┴──────────┴──────────┘
```

**整合方式**：
| 時機 | 商脈會 Backend | OpenIM Server |
|------|----------------|---------------|
| 會員註冊 | 建立會員 | 同步建立 IM 帳號 |
| 修改資料 | 更新姓名/頭像 | 同步更新 IM 資料 |
| 停權/刪除 | 停用會員 | 停用/刪除 IM 帳號 |

**評估重點**：
- [ ] 授權方式（AGPL-3.0）對專案的影響
- [ ] 額外基礎服務（MongoDB、Kafka、ETCD）的維運成本
- [ ] SDK 整合複雜度
- [ ] 與現有推播系統的整合

---

## 功能概述

聊天系統提供會員間的即時通訊功能，支援 1:1 私人聊天與 1:N 群組聊天（會議概念），使用 WebSocket 實現即時訊息推送。

**重要規則：訊息保存期限為 3 天，超過後將自動刪除。**

> 📝 以下為方案 A（自建）的規格，若採用方案 B（OpenIMSDK）則需調整。

---

## 聊天類型

| 類型 | 說明 |
|------|------|
| PRIVATE | 1:1 私人聊天 |
| GROUP | 1:N 群組聊天（會議） |

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 聊天室列表 | 查看所有聊天室 |
| 訊息監控 | 查看聊天內容（管理用途） |
| 訊息刪除 | 刪除不當訊息 |
| 用戶禁言 | 暫停用戶發言權限 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 聊天列表 | 顯示所有聊天室列表 |
| 發起私聊 | 與指定會員開始私聊 |
| 建立群組 | 建立群組聊天室 |
| 群組管理 | 邀請成員、移除成員、更改名稱 |
| 即時訊息 | 發送/接收即時訊息 |
| 訊息類型 | 支援文字、圖片、檔案 |
| 訊息狀態 | 已讀/未讀狀態 |
| 歷史訊息 | 載入歷史聊天記錄 |
| 搜尋功能 | 搜尋聊天記錄 |

---

## API 端點設計

### 後台 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/chat/rooms | 聊天室列表 |
| GET | /api/v1/admin/chat/rooms/:id | 聊天室詳情 |
| GET | /api/v1/admin/chat/rooms/:id/messages | 聊天訊息記錄 |
| DELETE | /api/v1/admin/chat/messages/:id | 刪除訊息 |
| PUT | /api/v1/admin/chat/members/:id/mute | 禁言用戶 |
| PUT | /api/v1/admin/chat/members/:id/unmute | 解除禁言 |

### 客端 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/chat/rooms | 我的聊天室列表 |
| POST | /api/v1/chat/rooms | 建立聊天室 |
| GET | /api/v1/chat/rooms/:id | 聊天室詳情 |
| PUT | /api/v1/chat/rooms/:id | 更新聊天室資訊 |
| DELETE | /api/v1/chat/rooms/:id | 刪除/退出聊天室 |
| GET | /api/v1/chat/rooms/:id/members | 成員列表 |
| POST | /api/v1/chat/rooms/:id/members | 邀請成員 |
| DELETE | /api/v1/chat/rooms/:id/members/:memberId | 移除成員 |
| GET | /api/v1/chat/rooms/:id/messages | 取得訊息（分頁） |
| POST | /api/v1/chat/rooms/:id/messages | 發送訊息（HTTP fallback） |
| DELETE | /api/v1/chat/messages/:id | 刪除訊息 |
| PUT | /api/v1/chat/rooms/:id/read | 標記已讀 |
| POST | /api/v1/chat/private/:memberId | 發起/取得私聊聊天室 |

### WebSocket 事件

**連線管理事件：**

| 事件 | 說明 |
|------|------|
| connect | 建立連線 |
| disconnect | 斷開連線 |
| authenticate | 驗證身份 |

**訊息事件：**

| 事件 | 說明 |
|------|------|
| message:send | 發送訊息 |
| message:receive | 接收訊息 |
| message:read | 訊息已讀 |
| message:delete | 訊息刪除 |

**聊天室事件：**

| 事件 | 說明 |
|------|------|
| room:join | 加入聊天室 |
| room:leave | 離開聊天室 |
| room:typing | 輸入中狀態 |
| room:member_join | 成員加入通知 |
| room:member_leave | 成員離開通知 |

**狀態事件：**

| 事件 | 說明 |
|------|------|
| user:online | 用戶上線 |
| user:offline | 用戶離線 |

---

## 資料庫設計

### 聊天室表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| type | 列舉 | 聊天室類型 |
| name | 字串 | 群組名稱（私聊為 null） |
| avatar | 字串 | 群組頭像（選填） |
| description | 字串 | 群組描述（選填） |
| maxMembers | 整數 | 最大成員數（預設 100） |
| isPublic | 布林 | 是否公開群組（預設 false） |
| createdById | 字串 | 創建者 ID（選填） |
| isActive | 布林 | 是否啟用（預設 true） |
| lastMessageId | 字串 | 最後訊息 ID（選填） |
| lastMessageAt | 時間 | 最後訊息時間（選填） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**聊天室類型列舉值：**

| 值 | 說明 |
|------|------|
| PRIVATE | 1:1 私聊 |
| GROUP | 群組聊天 |

### 聊天室成員表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| roomId | 字串 | 聊天室 ID |
| memberId | 字串 | 會員 ID |
| role | 列舉 | 成員角色（預設 MEMBER） |
| nickname | 字串 | 群內暱稱（選填） |
| isMuted | 布林 | 是否被禁言（預設 false） |
| mutedUntil | 時間 | 禁言到期時間（選填） |
| notificationEnabled | 布林 | 是否接收通知（預設 true） |
| isPinned | 布林 | 是否置頂（預設 false） |
| lastReadAt | 時間 | 最後已讀時間 |
| lastReadMessageId | 字串 | 最後已讀訊息 ID（選填） |
| joinedAt | 時間 | 加入時間 |
| leftAt | 時間 | 離開時間（選填） |

**成員角色列舉值：**

| 值 | 說明 |
|------|------|
| OWNER | 群主 |
| ADMIN | 管理員 |
| MEMBER | 一般成員 |

### 聊天訊息表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| roomId | 字串 | 聊天室 ID |
| senderId | 字串 | 發送者 ID |
| type | 列舉 | 訊息類型（預設 TEXT） |
| content | 長文字 | 文字內容（選填） |
| mediaUrl | 字串 | 媒體檔案 URL（選填） |
| mediaType | 字串 | MIME type（選填） |
| mediaSize | 整數 | 檔案大小 bytes（選填） |
| mediaThumbnail | 字串 | 縮圖 URL（選填） |
| replyToId | 字串 | 回覆訊息 ID（選填） |
| isDeleted | 布林 | 是否已刪除（預設 false） |
| deletedAt | 時間 | 刪除時間（選填） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**訊息類型列舉值：**

| 值 | 說明 |
|------|------|
| TEXT | 文字 |
| IMAGE | 圖片 |
| FILE | 檔案 |
| AUDIO | 語音 |
| VIDEO | 影片 |
| SYSTEM | 系統訊息 |

### 已讀回執表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| messageId | 字串 | 訊息 ID |
| memberId | 字串 | 會員 ID |
| readAt | 時間 | 已讀時間 |

### 用戶連線狀態表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| memberId | 字串 | 會員 ID |
| socketId | 字串 | Socket.io 連線 ID（唯一） |
| deviceType | 列舉 | 裝置類型 |
| deviceInfo | 字串 | 裝置資訊（選填） |
| isOnline | 布林 | 是否在線（預設 true） |
| lastActiveAt | 時間 | 最後活動時間 |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**裝置類型列舉值：**

| 值 | 說明 |
|------|------|
| IOS | iOS 裝置 |
| ANDROID | Android 裝置 |
| WEB | 網頁版 |

---

## 業務邏輯

### WebSocket 連線流程

| 步驟 | 說明 |
|------|------|
| 1 | 客戶端連線 WebSocket |
| 2 | 發送 authenticate 事件，附帶 JWT token |
| 3 | 伺服器驗證 token |
| 4 | 驗證成功：建立連線記錄、加入用戶專屬房間、加入所有聊天室房間、廣播用戶上線狀態 |
| 5 | 驗證失敗：斷開連線 |

### 發送訊息流程

| 步驟 | 說明 |
|------|------|
| 1 | 驗證聊天室成員資格 |
| 2 | 檢查是否被禁言 |
| 3 | 建立訊息記錄 |
| 4 | 更新聊天室最後訊息 |
| 5 | 透過 WebSocket 廣播訊息 |
| 6 | 發送推播通知給離線用戶 |

### 建立私聊流程

| 步驟 | 說明 |
|------|------|
| 1 | 查找是否存在現有私聊聊天室 |
| 2 | 若存在且成員數為 2，返回現有聊天室 |
| 3 | 若不存在，建立新私聊聊天室並加入兩位成員 |

### 建立群組流程

| 步驟 | 說明 |
|------|------|
| 1 | 建立新聊天室（類型為 GROUP） |
| 2 | 設定群組名稱、頭像、描述等資訊 |
| 3 | 將創建者設為 OWNER |
| 4 | 將邀請的成員設為 MEMBER |

### 已讀狀態處理流程

| 步驟 | 說明 |
|------|------|
| 1 | 取得聊天室最新訊息 |
| 2 | 更新成員已讀進度 |
| 3 | 建立已讀回執記錄 |
| 4 | 透過 WebSocket 廣播已讀狀態 |

### 未讀訊息計算

統計該成員最後已讀時間之後、非自己發送的訊息數量。

---

## DTO 資料格式

### 聊天室列表回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 聊天室 ID |
| type | 列舉 | 聊天室類型 |
| name | 字串/null | 聊天室名稱（私聊為對方名稱） |
| avatar | 字串/null | 聊天室頭像（私聊為對方頭像） |
| lastMessage.id | 字串 | 最後訊息 ID |
| lastMessage.type | 列舉 | 最後訊息類型 |
| lastMessage.content | 字串 | 最後訊息內容 |
| lastMessage.senderId | 字串 | 發送者 ID |
| lastMessage.senderName | 字串 | 發送者名稱 |
| lastMessage.createdAt | 時間 | 發送時間 |
| unreadCount | 整數 | 未讀數量 |
| isPinned | 布林 | 是否置頂 |
| memberCount | 整數 | 成員數量 |
| updatedAt | 時間 | 更新時間 |

### 發送訊息請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| roomId | 字串 | 是 | 聊天室 ID |
| type | 列舉 | 是 | 訊息類型 |
| content | 字串 | 否 | 文字內容 |
| mediaUrl | 字串 | 否 | 媒體檔案 URL |
| mediaType | 字串 | 否 | MIME type |
| mediaSize | 整數 | 否 | 檔案大小 |
| replyToId | 字串 | 否 | 回覆訊息 ID |

### 訊息回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 訊息 ID |
| roomId | 字串 | 聊天室 ID |
| type | 列舉 | 訊息類型 |
| content | 字串/null | 文字內容 |
| mediaUrl | 字串/null | 媒體檔案 URL |
| mediaType | 字串/null | MIME type |
| mediaThumbnail | 字串/null | 縮圖 URL |
| sender.id | 字串 | 發送者 ID |
| sender.name | 字串 | 發送者名稱 |
| sender.avatar | 字串 | 發送者頭像 |
| replyTo.id | 字串 | 回覆訊息 ID（選填） |
| replyTo.content | 字串 | 回覆訊息內容（選填） |
| replyTo.senderName | 字串 | 回覆訊息發送者名稱（選填） |
| isDeleted | 布林 | 是否已刪除 |
| createdAt | 時間 | 建立時間 |
| readBy | 字串陣列 | 已讀成員 ID 列表（群組） |

### 建立群組請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| name | 字串 | 是 | 群組名稱 |
| avatar | 字串 | 否 | 群組頭像 |
| description | 字串 | 否 | 群組描述 |
| memberIds | 字串陣列 | 是 | 邀請成員 ID 列表 |
| maxMembers | 整數 | 否 | 最大成員數（預設 100） |

### 群組詳情回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 群組 ID |
| name | 字串 | 群組名稱 |
| avatar | 字串/null | 群組頭像 |
| description | 字串/null | 群組描述 |
| memberCount | 整數 | 成員數量 |
| maxMembers | 整數 | 最大成員數 |
| createdBy.id | 字串 | 創建者 ID |
| createdBy.name | 字串 | 創建者名稱 |
| members | 陣列 | 成員列表 |
| members[].id | 字串 | 成員 ID |
| members[].name | 字串 | 成員名稱 |
| members[].avatar | 字串 | 成員頭像 |
| members[].role | 列舉 | 成員角色 |
| members[].joinedAt | 時間 | 加入時間 |
| createdAt | 時間 | 建立時間 |

### 訊息查詢請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| roomId | 字串 | 是 | 聊天室 ID |
| before | 字串 | 否 | 訊息 ID（分頁用） |
| limit | 整數 | 否 | 每頁數量（預設 50） |

---

## 效能優化

### 訊息分頁載入

使用游標分頁（cursor-based pagination）載入歷史訊息，以確保效能。

### Redis 快取策略

| 快取項目 | TTL | 說明 |
|----------|-----|------|
| 聊天室列表 | 5 分鐘 | 訊息發送時清除快取 |
| 用戶連線狀態 | 即時 | 連線/斷線時更新 |

### Redis Pub/Sub

使用 Redis Adapter 支援多伺服器實例的 WebSocket 訊息同步。

---

## 訊息保存規則

### 訊息自動清理

| 項目 | 說明 |
|------|------|
| 保存期限 | 3 天（72 小時） |
| 清理時間 | 每日凌晨 3:00 |
| 影響範圍 | 所有訊息（含圖片、檔案） |
| 媒體檔案 | 同步刪除雲端儲存檔案 |

---

## 檔案上傳限制

| 類型 | 大小限制 | 格式 |
|------|----------|------|
| 圖片 | 10MB | JPG, PNG, GIF, WebP |
| 檔案 | 50MB | PDF, DOC, DOCX, XLS, XLSX, PPT, PPTX |
| 語音 | 5MB | MP3, M4A, WAV |
| 影片 | 100MB | MP4, MOV |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| CHT_001 | 聊天室不存在 |
| CHT_002 | 非聊天室成員 |
| CHT_003 | 訊息不存在 |
| CHT_004 | 無權限執行此操作 |
| CHT_005 | 群組人數已達上限 |
| CHT_006 | 用戶已被禁言 |
| CHT_007 | 無法對自己發起私聊 |
| CHT_008 | WebSocket 連線驗證失敗 |
| CHT_009 | 訊息格式錯誤 |
| CHT_010 | 檔案大小超過限制 |

---

## 相關模組

- [Auth 認證系統](./auth.md) - WebSocket 身份驗證
- [Member 會員系統](./member.md) - 聊天成員
- [Notification 通知系統](./notification.md) - 離線推播通知
