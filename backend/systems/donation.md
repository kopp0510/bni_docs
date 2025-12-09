# Donation 捐贈系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Donation |
| 開發階段 | Phase 3 |
| 複雜度 | 低 |
| 相依模組 | Auth, Member, Point |
| 被依賴 | 無 |

---

## 功能概述

捐贈系統管理公益捐贈專案，會員可使用愛心點數進行捐贈，並獲得獎勵積分。

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 捐贈專案列表 | 查看所有捐贈專案 |
| 新增專案 | 建立新捐贈專案 |
| 編輯專案 | 修改專案資訊 |
| 狀態切換 | 啟用/停用專案 |
| 捐贈詳情 | 查看專案捐贈明細 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 捐贈列表 | 瀏覽公益專案列表 |
| 專案詳情 | 查看專案詳細資訊與進度 |
| 捐贈執行 | 選擇點數進行捐贈 |
| 捐贈記錄 | 查看我的捐贈紀錄 |

---

## API 端點設計

### 後台 API

**專案管理**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/donations/projects | 專案列表 |
| GET | /api/v1/admin/donations/projects/:id | 專案詳情 |
| POST | /api/v1/admin/donations/projects | 新增專案 |
| PUT | /api/v1/admin/donations/projects/:id | 編輯專案 |
| PUT | /api/v1/admin/donations/projects/:id/status | 更新狀態 |

**捐贈明細**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/donations/projects/:id/records | 專案捐贈明細 |
| GET | /api/v1/admin/donations/records | 全部捐贈紀錄 |

### 客端 API

**捐贈專案**

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/donations/projects | 專案列表（啟用中） |
| GET | /api/v1/donations/projects/:id | 專案詳情 |

**捐贈操作**

| 方法 | 端點 | 說明 |
|------|------|------|
| POST | /api/v1/donations/projects/:id/donate | 執行捐贈 |
| GET | /api/v1/donations/records | 我的捐贈紀錄 |

---

## 資料庫設計

### 捐贈專案表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| name | 字串 | 專案名稱 |
| description | 長文字 | 專案描述（選填） |
| coverImage | 字串 | 封面圖片（選填） |
| targetAmount | 整數 | 目標金額（點數，選填） |
| currentAmount | 整數 | 目前累積（預設 0） |
| rewardPoints | 整數 | 獎勵積分（預設 0） |
| rewardRate | 浮點數 | 獎勵比例（預設 0.1） |
| status | 列舉 | 專案狀態（預設 ACTIVE） |
| startAt | 時間 | 開始時間（選填） |
| endAt | 時間 | 結束時間（選填） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |
| createdBy | 字串 | 建立者 |

**專案狀態列舉值：**

| 值 | 說明 |
|------|------|
| DRAFT | 草稿 |
| ACTIVE | 進行中 |
| COMPLETED | 已完成 |
| CLOSED | 已關閉 |

### 捐贈紀錄表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| projectId | 字串 | 專案 ID |
| memberId | 字串 | 會員 ID |
| amount | 整數 | 捐贈點數 |
| rewardPoints | 整數 | 獲得獎勵積分（預設 0） |
| note | 字串 | 捐贈留言（選填） |
| isAnonymous | 布林 | 是否匿名（預設 false） |
| createdAt | 時間 | 建立時間 |

---

## 業務邏輯

### 捐贈流程

| 步驟 | 說明 |
|------|------|
| 1 | 會員選擇捐贈專案 |
| 2 | 輸入捐贈點數 |
| 3 | 系統驗證（專案狀態為 ACTIVE、會員點數足夠） |
| 4 | 扣除愛心點數 |
| 5 | 增加專案累積金額 |
| 6 | 計算並發放獎勵積分 |
| 7 | 建立捐贈紀錄 |

### 獎勵積分計算

| 項目 | 說明 |
|------|------|
| 計算公式 | 獎勵積分 = 捐贈點數 × 獎勵比例（無條件捨去） |
| 範例 | 捐贈 1000 點，獎勵比例 0.1 → 獎勵積分 = 100 |

### 專案狀態規則

| 狀態 | 說明 | 可捐贈 |
|------|------|--------|
| DRAFT | 草稿，未公開 | 否 |
| ACTIVE | 進行中 | 是 |
| COMPLETED | 已達標完成 | 否 |
| CLOSED | 手動關閉 | 否 |

### 專案進度計算

| 項目 | 說明 |
|------|------|
| 計算公式 | 進度百分比 = (目前累積 / 目標金額) × 100 |
| 最大值 | 100%（超過目標仍顯示 100%） |
| 無目標時 | 顯示 0% |

---

## DTO 資料格式

### 建立專案

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| name | 字串 | 是 | 專案名稱 |
| description | 字串 | 否 | 專案描述 |
| coverImage | 字串 | 否 | 封面圖片 |
| targetAmount | 整數 | 否 | 目標金額 |
| rewardRate | 浮點數 | 否 | 獎勵比例（預設 0.1） |
| startAt | 時間 | 否 | 開始時間 |
| endAt | 時間 | 否 | 結束時間 |
| status | 列舉 | 否 | 專案狀態 |

### 更新專案

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| name | 字串 | 否 | 專案名稱 |
| description | 字串 | 否 | 專案描述 |
| coverImage | 字串 | 否 | 封面圖片 |
| targetAmount | 整數 | 否 | 目標金額 |
| rewardRate | 浮點數 | 否 | 獎勵比例 |
| startAt | 時間 | 否 | 開始時間 |
| endAt | 時間 | 否 | 結束時間 |

### 執行捐贈請求

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| amount | 整數 | 是 | 捐贈點數 |
| note | 字串 | 否 | 捐贈留言 |
| isAnonymous | 布林 | 否 | 是否匿名 |

### 執行捐贈回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| donationId | 字串 | 捐贈記錄 ID |
| amount | 整數 | 捐贈點數 |
| rewardPoints | 整數 | 獲得獎勵積分 |
| projectProgress | 整數 | 專案進度百分比 |
| balance | 整數 | 剩餘點數 |

### 專案詳情回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 專案 ID |
| name | 字串 | 專案名稱 |
| description | 字串/null | 專案描述 |
| coverImage | 字串/null | 封面圖片 |
| targetAmount | 整數/null | 目標金額 |
| currentAmount | 整數 | 目前累積 |
| progress | 整數 | 進度百分比 |
| rewardRate | 浮點數 | 獎勵比例 |
| donorCount | 整數 | 捐贈人數 |
| status | 列舉 | 專案狀態 |
| startAt | 時間/null | 開始時間 |
| endAt | 時間/null | 結束時間 |
| recentDonors | 陣列 | 最近捐贈者列表 |

**最近捐贈者欄位：**

| 欄位 | 類型 | 說明 |
|------|------|------|
| memberId | 字串 | 會員 ID |
| memberName | 字串 | 會員姓名 |
| memberAvatar | 字串 | 會員頭像 |
| amount | 整數 | 捐贈金額 |
| isAnonymous | 布林 | 是否匿名 |
| createdAt | 時間 | 捐贈時間 |

### 捐贈紀錄回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 記錄 ID |
| project.id | 字串 | 專案 ID |
| project.name | 字串 | 專案名稱 |
| project.coverImage | 字串 | 專案封面圖片 |
| amount | 整數 | 捐贈點數 |
| rewardPoints | 整數 | 獲得獎勵積分 |
| note | 字串/null | 捐贈留言 |
| createdAt | 時間 | 捐贈時間 |

---

## 捐贈排行榜（可選功能）

| 欄位 | 類型 | 說明 |
|------|------|------|
| memberId | 字串 | 會員 ID |
| memberName | 字串 | 會員姓名 |
| memberAvatar | 字串 | 會員頭像 |
| totalAmount | 整數 | 總捐贈點數 |
| donationCount | 整數 | 捐贈次數 |
| rank | 整數 | 排名 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| DNT_001 | 捐贈專案不存在 |
| DNT_002 | 專案非進行中狀態 |
| DNT_003 | 點數不足 |
| DNT_004 | 捐贈金額必須大於 0 |
| DNT_005 | 專案已結束 |

---

## 相關模組

- [Member 會員系統](./member.md) - 捐贈會員
- [Point 點數系統](./point.md) - 點數扣除與獎勵
