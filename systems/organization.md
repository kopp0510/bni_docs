# Organization 商會管理系統

## 模組資訊

| 項目 | 說明 |
|------|------|
| 模組名稱 | Organization |
| 開發階段 | Phase 1 |
| 複雜度 | 中 |
| 相依模組 | Auth |
| 被依賴 | Member, Event |

---

## 功能概述

商會管理系統負責管理商會的樹狀層級結構，包含根層、總會、分會等多層級組織架構。

---

## 功能清單

### 後台功能

| 功能 | 說明 |
|------|------|
| 商會樹狀結構 | 以樹狀圖顯示商會層級關係 |
| 新增商會 | 建立新商會，選擇上級商會 |
| 修改商會 | 修改商會資訊 |
| 刪除商會 | 刪除商會（僅子層可刪） |
| 展開/折疊 | 樹狀結構展開與折疊功能 |

### 客端功能

| 功能 | 說明 |
|------|------|
| 選擇商會 | 註冊時選擇所屬商會 |
| 查看商會資訊 | 查看商會基本資訊 |

---

## API 端點設計

### 後台 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/admin/organizations/tree | 取得商會樹狀結構 |
| GET | /api/v1/admin/organizations | 取得商會列表 |
| GET | /api/v1/admin/organizations/:id | 取得商會詳情 |
| POST | /api/v1/admin/organizations | 新增商會 |
| PUT | /api/v1/admin/organizations/:id | 修改商會 |
| DELETE | /api/v1/admin/organizations/:id | 刪除商會 |
| GET | /api/v1/admin/organizations/:id/children | 取得子商會列表 |
| GET | /api/v1/admin/organizations/:id/members | 取得商會會員列表 |

### 客端 API

| 方法 | 端點 | 說明 |
|------|------|------|
| GET | /api/v1/organizations/tree | 取得商會樹狀結構（註冊選擇用） |
| GET | /api/v1/organizations/:id | 取得商會詳情 |

---

## 資料庫設計

### 商會表

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | UUID | 主鍵 |
| code | 字串 | 商會代碼（唯一） |
| name | 字串 | 商會名稱 |
| level | 列舉 | 層級 |
| parentId | 字串 | 上級商會 ID（選填） |
| contactName | 字串 | 負責人姓名（選填） |
| contactPhone | 字串 | 聯絡電話（選填） |
| contactEmail | 字串 | 聯絡郵箱（選填） |
| isActive | 布林 | 是否啟用（預設 true） |
| sortOrder | 整數 | 排序順序（預設 0） |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

**商會層級列舉值：**

| 值 | 說明 |
|------|------|
| ROOT | 根層（最高層） |
| GENERAL | 總會 |
| BRANCH | 分會 |

---

## 業務邏輯

### 商會層級結構

| 層級 | 上級 | 範例 |
|------|------|------|
| ROOT | 無 | 根層（系統唯一） |
| GENERAL | ROOT | 總會 A、總會 B、總會 C |
| BRANCH | GENERAL | 分會 A-1、分會 A-2、分會 B-1 |

**結構示意：**

| 層級 | 名稱 |
|------|------|
| 根層 | ROOT |
| ├── 總會 A | GENERAL |
| │   ├── 分會 A-1 | BRANCH |
| │   ├── 分會 A-2 | BRANCH |
| │   └── 分會 A-3 | BRANCH |
| ├── 總會 B | GENERAL |
| │   ├── 分會 B-1 | BRANCH |
| │   └── 分會 B-2 | BRANCH |
| └── 總會 C | GENERAL |
| │   └── 分會 C-1 | BRANCH |

### 新增商會規則

| 規則 | 說明 |
|------|------|
| 1 | 必須選擇上級商會（ROOT 除外） |
| 2 | ROOT 層級只能有一個 |
| 3 | 商會代碼必須唯一 |
| 4 | 層級自動根據父層級決定 |

**層級自動判定規則：**

| 父層級 | 新商會層級 |
|--------|-----------|
| ROOT | GENERAL |
| GENERAL | BRANCH |

### 刪除商會規則

| 規則 | 說明 |
|------|------|
| 1 | 只能刪除沒有子商會的商會 |
| 2 | 只能刪除沒有會員的商會 |
| 3 | ROOT 層級不可刪除 |

---

## DTO 資料格式

### 建立商會

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| code | 字串 | 是 | 商會代碼 |
| name | 字串 | 是 | 商會名稱 |
| parentId | 字串 | 是 | 上級商會 ID |
| contactName | 字串 | 否 | 負責人姓名 |
| contactPhone | 字串 | 否 | 聯絡電話 |
| contactEmail | 字串 | 否 | 聯絡郵箱 |
| sortOrder | 整數 | 否 | 排序順序 |

### 更新商會

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| name | 字串 | 否 | 商會名稱 |
| contactName | 字串 | 否 | 負責人姓名 |
| contactPhone | 字串 | 否 | 聯絡電話 |
| contactEmail | 字串 | 否 | 聯絡郵箱 |
| isActive | 布林 | 否 | 是否啟用 |
| sortOrder | 整數 | 否 | 排序順序 |

### 商會樹狀結構回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 商會 ID |
| code | 字串 | 商會代碼 |
| name | 字串 | 商會名稱 |
| level | 列舉 | 商會層級 |
| isActive | 布林 | 是否啟用 |
| memberCount | 整數 | 會員數量 |
| children | 陣列 | 子商會列表（遞迴結構） |

### 商會詳情回應

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | 字串 | 商會 ID |
| code | 字串 | 商會代碼 |
| name | 字串 | 商會名稱 |
| level | 列舉 | 商會層級 |
| parentId | 字串/null | 上級商會 ID |
| parentName | 字串/null | 上級商會名稱 |
| contactName | 字串/null | 負責人姓名 |
| contactPhone | 字串/null | 聯絡電話 |
| contactEmail | 字串/null | 聯絡郵箱 |
| isActive | 布林 | 是否啟用 |
| memberCount | 整數 | 會員數量 |
| childrenCount | 整數 | 子商會數量 |
| createdAt | 時間 | 建立時間 |
| updatedAt | 時間 | 更新時間 |

---

## 查詢功能

### 樹狀結構查詢

遞迴查詢所有子商會，依排序順序排列，包含各商會的會員數量統計。

---

## 錯誤代碼

| 代碼 | 說明 |
|------|------|
| ORG_001 | 商會代碼已存在 |
| ORG_002 | 上級商會不存在 |
| ORG_003 | 商會不存在 |
| ORG_004 | 無法刪除有子商會的商會 |
| ORG_005 | 無法刪除有會員的商會 |
| ORG_006 | 無法刪除根層商會 |
| ORG_007 | 商會已停用 |

---

## 相關模組

- [Auth 認證系統](./auth.md) - 權限驗證
- [Member 會員系統](./member.md) - 會員歸屬商會
- [Event 活動系統](./event.md) - 活動指定商會
