# CMS 內容管理系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | CMS |
| 開發階段 | Phase 4 |
| 複雜度 | 低 |
| 相依模組 | Auth |
| 被依賴 | 無 |

---

## 功能概述

內容管理系統負責管理前台展示的各類內容，包含廣告輪播圖、小喇叭公告、公司介紹等。

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 廣告輪播圖管理 | 新增/修改/刪除廣告、設定投放時間 |
| 小喇叭管理 | 新增/修改/刪除公告 |
| 認識商脈編輯 | 編輯公司介紹內容 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 首頁廣告輪播 | 顯示輪播廣告、點擊查看詳情 |
| 小喇叭公告 | 跑馬燈形式顯示公告 |
| 認識商脈頁面 | 顯示公司介紹內容 |

---

## API 端點設計

### 後台 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/cms/banners | 廣告列表 |
| GET | /api/v1/admin/cms/banners/:id | 廣告詳情 |
| POST | /api/v1/admin/cms/banners | 新增廣告 |
| PUT | /api/v1/admin/cms/banners/:id | 修改廣告 |
| DELETE | /api/v1/admin/cms/banners/:id | 刪除廣告 |
| PUT | /api/v1/admin/cms/banners/:id/status | 切換狀態 |
| GET | /api/v1/admin/cms/announcements | 公告列表 |
| GET | /api/v1/admin/cms/announcements/:id | 公告詳情 |
| POST | /api/v1/admin/cms/announcements | 新增公告 |
| PUT | /api/v1/admin/cms/announcements/:id | 修改公告 |
| DELETE | /api/v1/admin/cms/announcements/:id | 刪除公告 |
| PUT | /api/v1/admin/cms/announcements/:id/status | 切換狀態 |
| GET | /api/v1/admin/cms/about | 取得內容 |
| PUT | /api/v1/admin/cms/about | 更新內容 |

### 客端 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/cms/banners | 取得輪播廣告 |
| GET | /api/v1/cms/announcements | 取得公告列表 |
| GET | /api/v1/cms/about | 取得公司介紹 |

---

## 資料庫設計

### 廣告輪播圖表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| title | 字串 | 標題 |
| image | 字串 | 圖片 URL (1200x600) |
| linkType | 列舉 | 連結類型（選填） |
| linkUrl | 字串 | 連結 URL（選填） |
| startAt | 時間 | 開始時間（選填） |
| endAt | 時間 | 結束時間（選填） |
| isActive | 布林 | 是否啟用（預設 true） |
| sortOrder | 整數 | 排序順序（預設 0） |
| viewCount | 整數 | 瀏覽次數（預設 0） |
| clickCount | 整數 | 點擊次數（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |
| createdBy | 字串 | 建立者 |

**連結類型列舉值：**

| 值 | 說明 |
|------|------|
| NONE | 無連結 |
| URL | 外部連結 |
| EVENT | 活動詳情 |
| SHOP | 店鋪詳情 |
| PAGE | 內部頁面 |

### 小喇叭公告表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| content | 字串 | 公告內容 |
| linkType | 列舉 | 連結類型（選填） |
| linkUrl | 字串 | 連結 URL（選填） |
| startAt | 時間 | 開始時間（選填） |
| endAt | 時間 | 結束時間（選填） |
| isActive | 布林 | 是否啟用（預設 true） |
| sortOrder | 整數 | 排序順序（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |
| createdBy | 字串 | 建立者 |

**公告連結類型列舉值：**

| 值 | 說明 |
|------|------|
| NONE | 無連結 |
| URL | 外部連結 |
| EVENT | 活動詳情 |
| PAGE | 內部頁面 |

### 系統設定表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| key | 字串 | 設定鍵值（唯一） |
| value | 長文字 | 設定值 |
| description | 字串 | 說明（選填） |
| updatedAt | 時間 | 更新時間 |
| updatedBy | 字串 | 更新者（選填） |

---

## 業務邏輯

### 廣告顯示規則

廣告需符合以下條件才會顯示：

| 條件 | 說明 |
|------|------|
| 1 | isActive = true |
| 2 | startAt 為空 或 startAt <= 現在時間 |
| 3 | endAt 為空 或 endAt >= 現在時間 |
| 4 | 依 sortOrder 升冪排序 |

### 公告顯示規則

同廣告顯示規則

### 圖片規格

| 類型 | 建議尺寸 | 格式 | 大小限制 |
|------|----------|------|----------|
| 廣告輪播圖 | 1200 x 600 | JPG/PNG | 2MB |

### 公司介紹內容

- 使用富文本編輯器（HTML 格式）
- 支援圖片插入
- 存儲在 SystemSetting 表，key = 'about_content'

---

## DTO 資料格式

### 建立廣告

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| title | 字串 | 是 | 標題 |
| image | 字串 | 是 | 圖片 URL |
| linkType | 列舉 | 否 | 連結類型 |
| linkUrl | 字串 | 否 | 連結 URL |
| startAt | 時間 | 否 | 開始時間 |
| endAt | 時間 | 否 | 結束時間 |
| isActive | 布林 | 否 | 是否啟用 |
| sortOrder | 整數 | 否 | 排序順序 |

### 更新廣告

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| title | 字串 | 否 | 標題 |
| image | 字串 | 否 | 圖片 URL |
| linkType | 列舉 | 否 | 連結類型 |
| linkUrl | 字串 | 否 | 連結 URL |
| startAt | 時間 | 否 | 開始時間 |
| endAt | 時間 | 否 | 結束時間 |
| sortOrder | 整數 | 否 | 排序順序 |

### 廣告回應（客端）

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 廣告 ID |
| title | 字串 | 標題 |
| image | 字串 | 圖片 URL |
| linkType | 列舉/null | 連結類型 |
| linkUrl | 字串/null | 連結 URL |

### 建立公告

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| content | 字串 | 是 | 公告內容 |
| linkType | 列舉 | 否 | 連結類型 |
| linkUrl | 字串 | 否 | 連結 URL |
| startAt | 時間 | 否 | 開始時間 |
| endAt | 時間 | 否 | 結束時間 |
| isActive | 布林 | 否 | 是否啟用 |
| sortOrder | 整數 | 否 | 排序順序 |

### 公告回應（客端）

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 公告 ID |
| content | 字串 | 公告內容 |
| linkType | 列舉/null | 連結類型 |
| linkUrl | 字串/null | 連結 URL |

### 更新公司介紹

| 欄位 | 類型 | 說明 |
|------|------|------|
| content | 字串 | HTML 富文本內容 |

### 公司介紹回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| content | 字串 | HTML 內容 |
| updatedAt | 時間 | 最後更新時間 |

---

## 統計功能

### 廣告統計

| 功能 | 說明 |
|------|------|
| 記錄瀏覽 | 每次廣告曝光時，viewCount +1 |
| 記錄點擊 | 每次點擊廣告時，clickCount +1 |

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| CMS_001 | 廣告不存在 |
| CMS_002 | 公告不存在 |
| CMS_003 | 圖片格式不支援 |
| CMS_004 | 圖片尺寸不符 |
| CMS_005 | 投放時間設定錯誤 |

---

## 相關模組

- [Auth 認證系統](./auth.md) - 後台權限驗證
