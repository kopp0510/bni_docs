# Event 活動系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Event |
| 開發階段 | Phase 3 |
| 複雜度 | 中 |
| 相依模組 | Auth, Member, Organization, Point |
| 被依賴 | 無 |

---

## 功能概述

活動系統管理各類型活動的建立、報名、參與等功能，支援 8 種活動類型。

---

## 活動類型

| 代碼 | 名稱 | 說明 |
|------|------|------|
| BUSINESS_LECTURE | 商務講堂 | 商務知識講座 |
| PUBLIC_LECTURE | 公益講座 | 公益主題講座 |
| MEMBER_MEETING | 會員例會 | 定期會員聚會 |
| TRAINING | 培訓課程 | 專業培訓 |
| NETWORKING | 交流活動 | 會員交流 |
| CHARITY | 公益活動 | 公益服務 |
| CELEBRATION | 慶典活動 | 節慶活動 |
| OTHER | 其他 | 其他類型 |

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 活動列表 | 查看所有活動列表 |
| 新增活動 | 建立新活動 |
| 編輯活動 | 修改活動資訊 |
| 刪除活動 | 刪除活動 |
| 報名管理 | 查看報名人員、管理報名狀態 |
| 搜尋篩選 | 依類型、狀態、時間篩選 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 活動列表 | 瀏覽活動列表 |
| 活動分類篩選 | 依類型篩選活動 |
| 活動搜尋 | 搜尋活動 |
| 活動詳情 | 查看活動詳細資訊 |
| 活動報名 | 報名參加活動 |
| 我的活動 | 查看已報名活動 |

---

## API 端點設計

### 後台 API

**活動管理**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/events | 活動列表 |
| GET | /api/v1/admin/events/:id | 活動詳情 |
| POST | /api/v1/admin/events | 新增活動 |
| PUT | /api/v1/admin/events/:id | 編輯活動 |
| DELETE | /api/v1/admin/events/:id | 刪除活動 |
| PUT | /api/v1/admin/events/:id/status | 更新活動狀態 |

**報名管理**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/events/:id/registrations | 報名列表 |
| PUT | /api/v1/admin/events/:id/registrations/:regId | 更新報名狀態 |

### 客端 API

**活動瀏覽**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/events | 活動列表 |
| GET | /api/v1/events/:id | 活動詳情 |
| GET | /api/v1/events/types | 活動類型列表 |

**活動報名**

| 方法 | 端點 | 說明 |
|------|------|------|
| POST | /api/v1/events/:id/register | 報名活動 |
| DELETE | /api/v1/events/:id/register | 取消報名 |
| GET | /api/v1/events/mine | 我的活動 |
| GET | /api/v1/events/mine/:id | 我的報名詳情 |

---

## 資料庫設計

### 活動表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| title | 字串 | 活動標題 |
| type | 列舉 | 活動類型 |
| coverImage | 字串 | 封面圖片（1:1，選填） |
| description | 長文字 | 活動介紹（富文本） |
| speaker | 字串 | 講師名稱（選填） |
| speakerTitle | 字串 | 講師頭銜（選填） |
| speakerAvatar | 字串 | 講師頭像（選填） |
| location | 字串 | 活動地點（選填） |
| address | 字串 | 詳細地址（選填） |
| startAt | 時間 | 開始時間 |
| endAt | 時間 | 結束時間 |
| maxAttendees | 整數 | 人數上限（null=不限，選填） |
| registrationStart | 時間 | 報名開始時間（選填） |
| registrationEnd | 時間 | 報名截止時間（選填） |
| pointCost | 整數 | 消耗積分（預設 0） |
| pointReward | 整數 | 獎勵積分（預設 0） |
| organizationId | 字串 | 指定商會（null=全部，選填） |
| status | 列舉 | 活動狀態（預設 DRAFT） |
| viewCount | 整數 | 瀏覽次數（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |
| createdBy | 字串 | 建立者 |

**活動類型列舉值：**

| 值 | 說明 |
|------|------|
| BUSINESS_LECTURE | 商務講堂 |
| PUBLIC_LECTURE | 公益講座 |
| MEMBER_MEETING | 會員例會 |
| TRAINING | 培訓課程 |
| NETWORKING | 交流活動 |
| CHARITY | 公益活動 |
| CELEBRATION | 慶典活動 |
| OTHER | 其他 |

**活動狀態列舉值：**

| 值 | 說明 |
|------|------|
| DRAFT | 草稿 |
| PUBLISHED | 已發布 |
| REGISTRATION_OPEN | 報名中 |
| REGISTRATION_CLOSED | 報名截止 |
| IN_PROGRESS | 進行中 |
| COMPLETED | 已結束 |
| CANCELLED | 已取消 |

### 活動報名表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| eventId | 字串 | 活動 ID |
| memberId | 字串 | 會員 ID |
| status | 列舉 | 報名狀態（預設 REGISTERED） |
| pointPaid | 整數 | 已付積分（預設 0） |
| pointRewarded | 整數 | 已獲獎勵（預設 0） |
| checkedInAt | 時間 | 簽到時間（選填） |
| note | 字串 | 備註（選填） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**報名狀態列舉值：**

| 值 | 說明 |
|------|------|
| REGISTERED | 已報名 |
| CONFIRMED | 已確認 |
| CHECKED_IN | 已簽到 |
| COMPLETED | 已完成 |
| CANCELLED | 已取消 |
| NO_SHOW | 未出席 |

---

## 業務邏輯

### 活動狀態流程

| 起始狀態 | 觸發條件 | 目標狀態 |
|----------|----------|----------|
| DRAFT | 發布活動 | PUBLISHED |
| PUBLISHED | 報名開始時間到達 | REGISTRATION_OPEN |
| REGISTRATION_OPEN | 報名截止或人數額滿 | REGISTRATION_CLOSED |
| REGISTRATION_CLOSED | 活動開始時間到達 | IN_PROGRESS |
| IN_PROGRESS | 活動結束時間到達 | COMPLETED |
| 任何狀態 | 取消活動 | CANCELLED |

### 報名規則

| 規則 | 說明 |
|------|------|
| 1 | 活動狀態必須為 REGISTRATION_OPEN |
| 2 | 報名人數未達上限 |
| 3 | 在報名時間範圍內 |
| 4 | 會員積分足夠（若有消耗積分） |
| 5 | 會員狀態為 ACTIVE |
| 6 | 若活動指定商會，會員需屬於該商會 |

### 積分處理

| 時機 | 處理方式 |
|------|----------|
| 報名時 | 扣除 pointCost 積分 |
| 取消報名 | 退還 pointCost 積分（活動開始前 24 小時內取消不退） |
| 活動完成時 | 已簽到的會員獲得 pointReward 積分 |

### 自動狀態更新（排程任務）

系統每分鐘執行排程任務，自動更新活動狀態：

| 原狀態 | 條件 | 新狀態 |
|--------|------|--------|
| PUBLISHED | 報名開始時間已到 | REGISTRATION_OPEN |
| REGISTRATION_OPEN | 報名截止時間已到 | REGISTRATION_CLOSED |
| REGISTRATION_OPEN / REGISTRATION_CLOSED | 活動開始時間已到 | IN_PROGRESS |
| IN_PROGRESS | 活動結束時間已到 | COMPLETED |

---

## DTO 資料格式

### 建立活動

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| title | 字串 | 是 | 活動標題 |
| type | 列舉 | 是 | 活動類型 |
| coverImage | 字串 | 否 | 封面圖片 |
| description | 字串 | 是 | 活動介紹 |
| speaker | 字串 | 否 | 講師名稱 |
| speakerTitle | 字串 | 否 | 講師頭銜 |
| speakerAvatar | 字串 | 否 | 講師頭像 |
| location | 字串 | 否 | 活動地點 |
| address | 字串 | 否 | 詳細地址 |
| startAt | 時間 | 是 | 開始時間 |
| endAt | 時間 | 是 | 結束時間 |
| maxAttendees | 整數 | 否 | 人數上限 |
| registrationStart | 時間 | 否 | 報名開始時間 |
| registrationEnd | 時間 | 否 | 報名截止時間 |
| pointCost | 整數 | 否 | 消耗積分 |
| pointReward | 整數 | 否 | 獎勵積分 |
| organizationId | 字串 | 否 | 指定商會 |
| status | 列舉 | 否 | 活動狀態 |

### 活動列表查詢

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| keyword | 字串 | 否 | 搜尋關鍵字 |
| type | 列舉 | 否 | 活動類型 |
| status | 列舉 | 否 | 活動狀態 |
| organizationId | 字串 | 否 | 商會 ID |
| startFrom | 時間 | 否 | 開始日期起 |
| startTo | 時間 | 否 | 開始日期迄 |
| page | 整數 | 否 | 頁碼 |
| limit | 整數 | 否 | 每頁數量 |

### 活動詳情回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 活動 ID |
| title | 字串 | 活動標題 |
| type | 列舉 | 活動類型 |
| typeName | 字串 | 活動類型名稱 |
| coverImage | 字串/null | 封面圖片 |
| description | 字串 | 活動介紹 |
| speaker | 字串/null | 講師名稱 |
| speakerTitle | 字串/null | 講師頭銜 |
| speakerAvatar | 字串/null | 講師頭像 |
| location | 字串/null | 活動地點 |
| address | 字串/null | 詳細地址 |
| startAt | 時間 | 開始時間 |
| endAt | 時間 | 結束時間 |
| maxAttendees | 整數/null | 人數上限 |
| currentAttendees | 整數 | 目前報名人數 |
| registrationStart | 時間/null | 報名開始時間 |
| registrationEnd | 時間/null | 報名截止時間 |
| pointCost | 整數 | 消耗積分 |
| pointReward | 整數 | 獎勵積分 |
| status | 列舉 | 活動狀態 |
| organization.id | 字串/null | 商會 ID |
| organization.name | 字串/null | 商會名稱 |
| isRegistered | 布林 | 當前用戶是否已報名 |
| registrationStatus | 列舉/null | 報名狀態 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| EVT_001 | 活動不存在 |
| EVT_002 | 活動非報名中狀態 |
| EVT_003 | 報名人數已額滿 |
| EVT_004 | 已超過報名截止時間 |
| EVT_005 | 積分不足 |
| EVT_006 | 已報名此活動 |
| EVT_007 | 未報名此活動 |
| EVT_008 | 無法取消報名（時間限制） |
| EVT_009 | 無權參加此活動（商會限制） |
| EVT_010 | 活動已取消 |

---

## 相關模組

- [Member 會員系統](./member.md) - 報名會員
- [Organization 商會管理](./organization.md) - 活動商會限制
- [Point 點數系統](./point.md) - 積分消耗/獎勵
