# Member 會員系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Member |
| 開發階段 | Phase 1 |
| 複雜度 | 高 |
| 相依模組 | Auth, Organization |
| 被依賴 | Point, Shop, Event, Chat, Notification |

---

## 功能概述

會員系統是整個平台的核心，管理三種會員類型（個人會員、公益商家、商務會員），處理會員審核、推薦關係、會員資料等功能。

---

## 會員類型說明

| 類型 | 說明 | 特殊功能 |
|------|------|----------|
| 個人會員 | 一般個人用戶 | 基本會員功能 |
| 公益商家 | 提供公益服務的商家 | 可被列入公益商家列表 |
| 商務會員 | 企業或商務用戶 | 可建立店鋪、收款功能 |

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 會員審核列表 | 待審核會員列表、審核通過/拒絕 |
| 個人會員列表 | 查看個人會員列表 |
| 公益商家列表 | 查看公益商家列表 |
| 商務會員列表 | 查看商務會員列表 |
| 會員詳情 | 查看會員完整資料 |
| 調整會員類型 | 變更會員類型 |
| 調整點數 | 手動調整會員點數/積分 |
| 推薦詳情 | 查看會員的推薦下線 |
| 點數明細 | 查看點數變動紀錄 |
| 分享記錄 | 發出/收到的分享紀錄 |
| 邀約來賓 | 查看/新增邀約來賓 |
| 店鋪交易 | 查看店鋪消費紀錄 |
| 店鋪信息 | 查看會員店鋪資訊 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 會員資訊卡 | 顯示會員基本資訊 |
| 會員專區 | 統計數據、功能入口 |
| 會員搜索 | 搜尋其他會員 |
| 轉介紹 | 生成推薦連結/QRCode |
| 我的榮譽 | 榮譽徽章與成就 |
| 邀約來賓 | 新增/查看邀約來賓 |
| 個人資料編輯 | 編輯個人資料 |
| 介紹人明細 | 推薦下線列表 |
| 會員當周排行榜 | 邀請最多來賓、引薦金額最多 |

---

## API 端點設計

### 後台 API

**會員審核**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/members/pending | 待審核會員列表 |
| POST | /api/v1/admin/members/:id/approve | 審核通過 |
| POST | /api/v1/admin/members/:id/reject | 審核拒絕 |

**會員列表**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/members | 會員列表（可篩選類型） |
| GET | /api/v1/admin/members/:id | 會員詳情 |
| PUT | /api/v1/admin/members/:id | 更新會員資料 |
| PUT | /api/v1/admin/members/:id/type | 調整會員類型 |
| PUT | /api/v1/admin/members/:id/status | 調整會員狀態 |

**會員相關資料**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/members/:id/referrals | 推薦下線列表 |
| GET | /api/v1/admin/members/:id/points | 點數明細 |
| POST | /api/v1/admin/members/:id/points | 調整點數 |
| GET | /api/v1/admin/members/:id/shares | 分享記錄 |
| GET | /api/v1/admin/members/:id/guests | 邀約來賓列表 |
| POST | /api/v1/admin/members/:id/guests | 新增邀約來賓 |
| GET | /api/v1/admin/members/:id/transactions | 店鋪交易紀錄 |
| GET | /api/v1/admin/members/:id/shop | 店鋪信息 |

### 客端 API

**個人資料**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/members/me | 取得我的資料 |
| PUT | /api/v1/members/me | 更新我的資料 |
| PUT | /api/v1/members/me/avatar | 更新頭像 |
| PUT | /api/v1/members/me/password | 修改密碼 |

**會員功能**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/members/search | 搜尋會員 |
| GET | /api/v1/members/:id | 查看會員資料 |
| GET | /api/v1/members/me/stats | 我的統計數據 |
| GET | /api/v1/members/me/referrals | 我的推薦下線 |
| GET | /api/v1/members/me/referral-code | 取得推薦碼/連結 |
| GET | /api/v1/members/me/honors | 我的榮譽 |
| GET | /api/v1/members/me/guests | 我的邀約來賓 |
| POST | /api/v1/members/me/guests | 新增邀約來賓 |

**排行榜**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/members/leaderboard/guests | 邀請來賓排行榜 |
| GET | /api/v1/members/leaderboard/referrals | 引薦金額排行榜 |

---

## 資料庫設計

### 會員表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| code | 字串 | 會員編號（唯一） |
| phone | 字串 | 手機號碼（唯一） |
| password | 字串 | 密碼（加密） |
| name | 字串 | 姓名 |
| avatar | 字串 | 頭像（選填） |
| email | 字串 | 電子郵件（選填） |
| birthday | 日期 | 出生日期（選填） |
| gender | 列舉 | 性別（選填） |
| type | 列舉 | 會員類型 |
| status | 列舉 | 會員狀態（預設 PENDING） |
| organizationId | 字串 | 所屬商會 |
| referrerId | 字串 | 推薦人 ID（選填） |
| referralCode | 字串 | 推薦碼（唯一） |
| joinedAt | 時間 | 入會日期（選填） |
| expiredAt | 時間 | 會籍到期日（選填） |
| tenure | 整數 | 年資（月，預設 0） |
| signature | 字串 | 電子簽名圖片 URL（選填） |
| lovePoints | 整數 | 愛心點數（預設 0） |
| rewardPoints | 整數 | 獎勵積分（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**會員類型列舉值：**

| 值 | 說明 |
|------|------|
| PERSONAL | 個人會員 |
| PUBLIC_MERCHANT | 公益商家 |
| BUSINESS | 商務會員 |

**會員狀態列舉值：**

| 值 | 說明 |
|------|------|
| PENDING | 待審核 |
| ACTIVE | 正常 |
| SUSPENDED | 停權 |
| EXPIRED | 過期 |
| DELETED | 已刪除 |

**性別列舉值：**

| 值 | 說明 |
|------|------|
| MALE | 男性 |
| FEMALE | 女性 |
| OTHER | 其他 |

### 邀約來賓表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| memberId | 字串 | 邀請人 ID |
| name | 字串 | 來賓姓名 |
| phone | 字串 | 來賓電話（選填） |
| email | 字串 | 來賓郵箱（選填） |
| company | 字串 | 公司名稱（選填） |
| note | 字串 | 備註（選填） |
| visitedAt | 時間 | 到訪日期（選填） |
| status | 列舉 | 狀態（預設 INVITED） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**來賓狀態列舉值：**

| 值 | 說明 |
|------|------|
| INVITED | 已邀請 |
| VISITED | 已到訪 |
| JOINED | 已入會 |
| CANCELLED | 已取消 |

### 會員榮譽表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| memberId | 字串 | 會員 ID |
| honorType | 列舉 | 榮譽類型 |
| title | 字串 | 榮譽名稱 |
| description | 字串 | 描述（選填） |
| achievedAt | 時間 | 獲得時間 |

**榮譽類型列舉值：**

| 值 | 說明 |
|------|------|
| REFERRAL_BRONZE | 推薦銅牌 |
| REFERRAL_SILVER | 推薦銀牌 |
| REFERRAL_GOLD | 推薦金牌 |
| GUEST_CHAMPION | 邀約冠軍 |
| DONATION_HERO | 捐贈英雄 |
| ACTIVE_MEMBER | 活躍會員 |

### 分享記錄表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| senderId | 字串 | 分享者 ID |
| receiverId | 字串 | 接收者 ID（平台會員，選填） |
| receiverPhone | 字串 | 接收者電話（非平台會員，選填） |
| shareType | 列舉 | 分享類型 |
| status | 列舉 | 狀態（預設 SENT） |
| createdAt | 時間 | 建立時間 |

**分享類型列舉值：**

| 值 | 說明 |
|------|------|
| PLATFORM_MEMBER | 平台會員 |
| NON_PLATFORM | 非平台會員 |

**分享狀態列舉值：**

| 值 | 說明 |
|------|------|
| SENT | 已發送 |
| VIEWED | 已查看 |
| REGISTERED | 已註冊 |

---

## 業務邏輯

### 會員編號生成規則

| 項目 | 說明 |
|------|------|
| 格式 | {商會代碼}-{年份}{月份}-{流水號} |
| 範例 | TPE-202312-0001 |

### 推薦碼生成規則

| 項目 | 說明 |
|------|------|
| 格式 | 8位英數字混合 |
| 範例 | ABC12345 |

### 會員審核流程

| 步驟 | 說明 |
|------|------|
| 1 | 會員提交註冊申請 |
| 2 | 狀態設為 PENDING |
| 3 | 後台管理員審核 |
| 4 | 審核通過 → 狀態改為 ACTIVE，設定入會日期 |
| 5 | 審核拒絕 → 狀態維持 PENDING，可重新提交 |

### 會員統計數據

| 統計項目 | 說明 |
|----------|------|
| exchangeCount | 交流次數 |
| referralCount | 轉介紹次數 |
| memberJoinCount | 介紹入會次數 |
| guestCount | 邀約來賓數 |
| eventJoinCount | 活動參與次數 |
| donationAmount | 捐贈總額 |

### 排行榜規則

| 項目 | 說明 |
|------|------|
| 週期 | 每週一 00:00 重置 |
| 邀請來賓排行 | 統計當週邀請來賓數量 |
| 引薦金額排行 | 統計當週推薦會員的繳費金額 |

---

## DTO 資料格式

### 更新會員資料

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| name | 字串 | 否 | 姓名 |
| email | 字串 | 否 | 電子郵件 |
| birthday | 日期 | 否 | 出生日期 |
| gender | 列舉 | 否 | 性別 |
| avatar | 字串 | 否 | 頭像 URL |

### 調整會員類型

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| type | 列舉 | 是 | 會員類型 |

### 調整點數

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| lovePoints | 整數 | 否 | 愛心點數（正數增加，負數減少） |
| rewardPoints | 整數 | 否 | 獎勵積分（正數增加，負數減少） |
| reason | 字串 | 是 | 調整原因 |

### 新增邀約來賓

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| name | 字串 | 是 | 來賓姓名 |
| phone | 字串 | 否 | 來賓電話 |
| email | 字串 | 否 | 來賓郵箱 |
| company | 字串 | 否 | 公司名稱 |
| note | 字串 | 否 | 備註 |

### 會員搜索查詢

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| keyword | 字串 | 否 | 關鍵字（姓名、手機、編號） |
| type | 列舉 | 否 | 會員類型 |
| organizationId | 字串 | 否 | 商會 ID |
| page | 整數 | 否 | 頁碼 |
| limit | 整數 | 否 | 每頁數量 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| MBR_001 | 會員不存在 |
| MBR_002 | 會員已被停權 |
| MBR_003 | 會籍已過期 |
| MBR_004 | 推薦人不存在 |
| MBR_005 | 無法修改會員類型 |
| MBR_006 | 點數不足 |
| MBR_007 | 推薦碼無效 |
| MBR_008 | 會員審核中 |

---

## 相關模組

- [Auth 認證系統](./auth.md) - 登入認證
- [Organization 商會管理](./organization.md) - 商會歸屬
- [Point 點數系統](./point.md) - 點數管理
- [Shop 店鋪系統](./shop.md) - 會員店鋪
- [Chat 聊天系統](./chat.md) - 會員交流
