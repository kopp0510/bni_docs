# 後台 CMS 系統規劃

## 系統概述

商脈會後台管理系統，提供管理員操作會員、活動、帳務等功能的 Web 介面。

---

## 技術選型

| 項目 | 技術 |
|------|------|
| 框架 | React 18 + TypeScript |
| UI 元件庫 | Ant Design 5.x |
| 狀態管理 | Zustand |
| 路由 | React Router v6 |
| HTTP 請求 | Axios |
| 表單處理 | React Hook Form |
| 圖表 | ECharts / Ant Design Charts |
| 建置工具 | Vite |

---

## 資料夾結構

```
shangmai-admin/
├── public/
│   ├── favicon.ico
│   └── logo.png
│
├── src/
│   ├── main.tsx                    # 應用程式入口
│   ├── App.tsx                     # 根元件
│   ├── vite-env.d.ts
│   │
│   ├── assets/                     # 靜態資源
│   │   ├── images/
│   │   └── styles/
│   │       ├── global.css
│   │       └── variables.css
│   │
│   ├── components/                 # 共用元件
│   │   ├── common/                 # 通用元件
│   │   │   ├── PageHeader/
│   │   │   ├── SearchForm/
│   │   │   ├── DataTable/
│   │   │   ├── StatusTag/
│   │   │   ├── ImageUpload/
│   │   │   ├── RichTextEditor/
│   │   │   ├── DateRangePicker/
│   │   │   └── ConfirmModal/
│   │   │
│   │   └── layout/                 # 版面元件
│   │       ├── MainLayout/
│   │       ├── Sidebar/
│   │       ├── Header/
│   │       └── Breadcrumb/
│   │
│   ├── pages/                      # 頁面元件
│   │   ├── login/                  # 登入
│   │   │   └── LoginPage.tsx
│   │   │
│   │   ├── dashboard/              # 儀表板
│   │   │   └── DashboardPage.tsx
│   │   │
│   │   ├── member/                 # 會員管理
│   │   │   ├── MemberListPage.tsx
│   │   │   ├── MemberDetailPage.tsx
│   │   │   ├── MemberPendingPage.tsx
│   │   │   └── components/
│   │   │       ├── MemberSearchForm.tsx
│   │   │       ├── MemberTable.tsx
│   │   │       ├── MemberTypeModal.tsx
│   │   │       ├── MemberPointModal.tsx
│   │   │       ├── MemberReferralList.tsx
│   │   │       └── MemberGuestList.tsx
│   │   │
│   │   ├── organization/           # 商會管理
│   │   │   ├── OrganizationPage.tsx
│   │   │   └── components/
│   │   │       ├── OrgTree.tsx
│   │   │       ├── OrgDetailDrawer.tsx
│   │   │       └── OrgFormModal.tsx
│   │   │
│   │   ├── point/                  # 點數管理
│   │   │   ├── PointRecordPage.tsx
│   │   │   ├── TransactionPage.tsx
│   │   │   └── components/
│   │   │       ├── PointRecordTable.tsx
│   │   │       └── TransactionTable.tsx
│   │   │
│   │   ├── shop/                   # 店鋪管理
│   │   │   ├── ShopListPage.tsx
│   │   │   ├── ShopDetailPage.tsx
│   │   │   └── components/
│   │   │       ├── ShopSearchForm.tsx
│   │   │       └── ShopTable.tsx
│   │   │
│   │   ├── event/                  # 活動管理
│   │   │   ├── EventListPage.tsx
│   │   │   ├── EventDetailPage.tsx
│   │   │   ├── EventFormPage.tsx
│   │   │   └── components/
│   │   │       ├── EventSearchForm.tsx
│   │   │       ├── EventTable.tsx
│   │   │       ├── EventBasicForm.tsx
│   │   │       └── RegistrationTable.tsx
│   │   │
│   │   ├── donation/               # 捐贈管理
│   │   │   ├── ProjectListPage.tsx
│   │   │   ├── ProjectDetailPage.tsx
│   │   │   ├── ProjectFormPage.tsx
│   │   │   └── components/
│   │   │       ├── ProjectTable.tsx
│   │   │       ├── ProjectForm.tsx
│   │   │       └── DonationRecordTable.tsx
│   │   │
│   │   ├── lottery/                # 抽獎管理
│   │   │   ├── LotteryRecordPage.tsx
│   │   │   ├── LotterySettingPage.tsx
│   │   │   └── components/
│   │   │       ├── LotteryRecordTable.tsx
│   │   │       └── PrizeSettingForm.tsx
│   │   │
│   │   ├── cms/                    # 內容管理
│   │   │   ├── BannerListPage.tsx
│   │   │   ├── BannerFormPage.tsx
│   │   │   ├── AnnouncementListPage.tsx
│   │   │   ├── AnnouncementFormPage.tsx
│   │   │   ├── AboutPage.tsx
│   │   │   └── components/
│   │   │       ├── BannerTable.tsx
│   │   │       ├── BannerForm.tsx
│   │   │       ├── AnnouncementTable.tsx
│   │   │       └── AnnouncementForm.tsx
│   │   │
│   │   ├── billing/                # 帳務管理
│   │   │   ├── OrderListPage.tsx
│   │   │   ├── OrderDetailPage.tsx
│   │   │   ├── StatsPage.tsx
│   │   │   └── components/
│   │   │       ├── OrderSearchForm.tsx
│   │   │       ├── OrderTable.tsx
│   │   │       ├── OrderDetail.tsx
│   │   │       └── RevenueChart.tsx
│   │   │
│   │   ├── chat/                   # 聊天管理
│   │   │   ├── ChatRoomListPage.tsx
│   │   │   └── components/
│   │   │       └── ChatRoomTable.tsx
│   │   │
│   │   ├── notification/           # 通知管理
│   │   │   ├── NotificationListPage.tsx
│   │   │   ├── NotificationFormPage.tsx
│   │   │   └── components/
│   │   │       ├── NotificationTable.tsx
│   │   │       └── NotificationForm.tsx
│   │   │
│   │   └── system/                 # 系統設定
│   │       ├── AdminListPage.tsx
│   │       ├── AdminFormPage.tsx
│   │       └── components/
│   │           ├── AdminTable.tsx
│   │           └── AdminForm.tsx
│   │
│   ├── hooks/                      # 自訂 Hooks
│   │   ├── useAuth.ts
│   │   ├── useTable.ts
│   │   ├── useModal.ts
│   │   └── useUpload.ts
│   │
│   ├── services/                   # API 服務
│   │   ├── api.ts                  # Axios 實例
│   │   ├── auth.service.ts
│   │   ├── member.service.ts
│   │   ├── organization.service.ts
│   │   ├── point.service.ts
│   │   ├── shop.service.ts
│   │   ├── event.service.ts
│   │   ├── donation.service.ts
│   │   ├── lottery.service.ts
│   │   ├── cms.service.ts
│   │   ├── billing.service.ts
│   │   ├── chat.service.ts
│   │   └── notification.service.ts
│   │
│   ├── stores/                     # 狀態管理
│   │   ├── authStore.ts
│   │   └── appStore.ts
│   │
│   ├── types/                      # TypeScript 型別
│   │   ├── api.types.ts
│   │   ├── member.types.ts
│   │   ├── organization.types.ts
│   │   ├── point.types.ts
│   │   ├── shop.types.ts
│   │   ├── event.types.ts
│   │   ├── donation.types.ts
│   │   ├── lottery.types.ts
│   │   ├── cms.types.ts
│   │   ├── billing.types.ts
│   │   ├── chat.types.ts
│   │   └── notification.types.ts
│   │
│   ├── utils/                      # 工具函數
│   │   ├── format.ts               # 格式化
│   │   ├── validation.ts           # 驗證
│   │   ├── storage.ts              # 本地儲存
│   │   └── constants.ts            # 常數定義
│   │
│   └── router/                     # 路由設定
│       ├── index.tsx
│       └── routes.tsx
│
├── .env.example
├── .eslintrc.cjs
├── .prettierrc
├── index.html
├── package.json
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```

---

## 頁面結構規劃

### 側邊欄選單結構

| 一級選單 | 二級選單 | 路由 | 說明 |
|----------|----------|------|------|
| 儀表板 | - | /dashboard | 數據總覽 |
| 會員管理 | 待審核會員 | /member/pending | 審核新會員 |
| | 個人會員 | /member/list?type=personal | 個人會員列表 |
| | 公益商家 | /member/list?type=public_merchant | 公益商家列表 |
| | 商務會員 | /member/list?type=business | 商務會員列表 |
| 商會管理 | 商會結構 | /organization | 樹狀結構管理 |
| 點數管理 | 點數紀錄 | /point/records | 全部點數紀錄 |
| | 交易紀錄 | /point/transactions | 交易紀錄 |
| 店鋪管理 | 店鋪列表 | /shop | 全部店鋪 |
| 活動管理 | 活動列表 | /event | 全部活動 |
| | 新增活動 | /event/create | 建立新活動 |
| 捐贈管理 | 專案列表 | /donation | 捐贈專案 |
| | 新增專案 | /donation/create | 建立新專案 |
| 抽獎管理 | 抽獎紀錄 | /lottery/records | 抽獎紀錄 |
| | 獎品設定 | /lottery/settings | 獎品機率設定 |
| 內容管理 | 廣告輪播 | /cms/banner | 輪播圖管理 |
| | 小喇叭公告 | /cms/announcement | 公告管理 |
| | 認識商脈 | /cms/about | 公司介紹 |
| 帳務管理 | 帳單列表 | /billing/orders | 全部帳單 |
| | 收款統計 | /billing/stats | 統計報表 |
| 聊天管理 | 聊天室列表 | /chat | 聊天室管理 |
| 通知管理 | 通知列表 | /notification | 推播通知 |
| | 發送通知 | /notification/create | 發送新通知 |
| 系統設定 | 管理員帳號 | /system/admin | 管理員管理 |

---

## 各模組頁面詳細規劃

### 1. 登入頁面

| 元素 | 說明 |
|------|------|
| Logo | 商脈會 Logo |
| 帳號輸入框 | 管理員帳號 |
| 密碼輸入框 | 密碼 |
| 登入按鈕 | 送出登入 |
| 錯誤提示 | 登入失敗訊息 |

---

### 2. 儀表板

| 區塊 | 內容 |
|------|------|
| 統計卡片 | 會員總數、待審核數、本月新增、活躍會員數 |
| 統計卡片 | 本月營收、待處理訂單、本月活動數 |
| 圖表 | 會員成長趨勢（折線圖） |
| 圖表 | 收款統計（柱狀圖） |
| 列表 | 待審核會員（快速連結） |
| 列表 | 近期活動（快速連結） |

---

### 3. 會員管理

#### 3.1 待審核會員頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 手機號碼、姓名、申請日期範圍 |
| 列表欄位 | 姓名、手機、會員類型、商會、申請時間、操作 |
| 操作按鈕 | 查看詳情、審核通過、審核拒絕 |

#### 3.2 會員列表頁面

| 元素 | 說明 |
|------|------|
| 篩選標籤 | 個人會員 / 公益商家 / 商務會員 |
| 搜尋條件 | 會員編號、手機、姓名、商會、狀態 |
| 列表欄位 | 會員編號、姓名、手機、商會、狀態、入會日期、操作 |
| 操作按鈕 | 查看詳情 |

#### 3.3 會員詳情頁面

| 區塊 | 內容 |
|------|------|
| 基本資訊卡 | 頭像、姓名、手機、郵箱、會員編號、會員類型、狀態 |
| 會籍資訊 | 所屬商會、入會日期、到期日、年資 |
| 點數資訊 | 愛心點數、獎勵積分、調整按鈕 |
| 推薦資訊 | 推薦人、推薦下線列表 |
| 頁籤：點數明細 | 點數變動紀錄表格 |
| 頁籤：邀約來賓 | 來賓列表、新增來賓按鈕 |
| 頁籤：分享記錄 | 分享紀錄表格 |
| 頁籤：店鋪資訊 | 店鋪詳情（商務會員） |
| 頁籤：交易紀錄 | 店鋪消費紀錄（商務會員） |
| 操作按鈕 | 調整類型、調整狀態、調整點數 |

---

### 4. 商會管理

| 元素 | 說明 |
|------|------|
| 樹狀結構 | 顯示商會層級，可展開/折疊 |
| 節點資訊 | 商會名稱、代碼、會員數 |
| 節點操作 | 新增子商會、編輯、刪除 |
| 右側詳情 | 選中商會的完整資訊 |
| 詳情內容 | 基本資訊、負責人資訊、會員列表 |

---

### 5. 點數管理

#### 5.1 點數紀錄頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 會員編號、手機、點數類型、動作類型、日期範圍 |
| 列表欄位 | 會員、點數類型、動作、變動數量、餘額、時間 |

#### 5.2 交易紀錄頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 交易編號、付款方、收款方、狀態、日期範圍 |
| 列表欄位 | 交易編號、付款方、收款方、金額、狀態、時間 |

---

### 6. 店鋪管理

#### 6.1 店鋪列表頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 店鋪名稱、分類、城市、狀態 |
| 列表欄位 | 店鋪名稱、分類、店主、城市、狀態、瀏覽數、操作 |
| 操作按鈕 | 查看詳情、啟用/停用 |

#### 6.2 店鋪詳情頁面

| 區塊 | 內容 |
|------|------|
| 基本資訊 | 店鋪名稱、分類、標語、介紹 |
| 聯絡資訊 | 電話、郵箱 |
| 地址資訊 | 地址、地圖顯示 |
| 營業時間 | 週一至週日營業時間 |
| 圖片 | 封面圖、店鋪圖片 |
| 店主資訊 | 店主會員資訊 |

---

### 7. 活動管理

#### 7.1 活動列表頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 活動名稱、類型、狀態、日期範圍 |
| 列表欄位 | 活動名稱、類型、狀態、報名人數、開始時間、操作 |
| 操作按鈕 | 查看詳情、編輯、刪除、更新狀態 |

#### 7.2 活動詳情頁面

| 區塊 | 內容 |
|------|------|
| 基本資訊 | 標題、類型、封面圖、介紹 |
| 講師資訊 | 講師姓名、頭銜、頭像 |
| 時間地點 | 開始時間、結束時間、地點、地址 |
| 報名設定 | 人數上限、報名時間、積分消耗/獎勵 |
| 狀態資訊 | 目前狀態、已報名人數 |
| 頁籤：報名列表 | 報名會員列表、狀態管理 |

#### 7.3 活動編輯頁面

| 區塊 | 欄位 |
|------|------|
| 基本資訊 | 活動標題、活動類型、封面圖上傳、活動介紹（富文本） |
| 講師資訊 | 講師姓名、講師頭銜、講師頭像上傳 |
| 時間地點 | 開始時間、結束時間、活動地點、詳細地址 |
| 報名設定 | 人數上限、報名開始時間、報名截止時間 |
| 積分設定 | 消耗積分、獎勵積分 |
| 發布設定 | 指定商會、活動狀態 |

---

### 8. 捐贈管理

#### 8.1 專案列表頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 專案名稱、狀態 |
| 列表欄位 | 專案名稱、目標金額、目前累積、進度、狀態、操作 |
| 操作按鈕 | 查看詳情、編輯、更新狀態 |

#### 8.2 專案詳情頁面

| 區塊 | 內容 |
|------|------|
| 基本資訊 | 專案名稱、描述、封面圖 |
| 進度資訊 | 目標金額、目前累積、進度條、捐贈人數 |
| 獎勵設定 | 獎勵比例 |
| 頁籤：捐贈明細 | 捐贈紀錄列表 |

#### 8.3 專案編輯頁面

| 欄位 | 說明 |
|------|------|
| 專案名稱 | 必填 |
| 專案描述 | 富文本編輯器 |
| 封面圖片 | 圖片上傳 |
| 目標金額 | 選填 |
| 獎勵比例 | 預設 0.1 |
| 開始時間 | 選填 |
| 結束時間 | 選填 |
| 專案狀態 | 草稿/進行中 |

---

### 9. 抽獎管理

#### 9.1 抽獎紀錄頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 會員、獎品類型、日期範圍 |
| 列表欄位 | 會員、獎品名稱、獎品類型、數值、抽獎時間 |
| 統計卡片 | 今日抽獎次數、各獎品發放數量 |

#### 9.2 獎品設定頁面

| 元素 | 說明 |
|------|------|
| 九宮格預覽 | 視覺化顯示獎品配置 |
| 設定表格 | 位置、獎品名稱、類型、數值、機率 |
| 操作按鈕 | 編輯、儲存設定 |

---

### 10. 內容管理

#### 10.1 廣告輪播頁面

| 元素 | 說明 |
|------|------|
| 列表欄位 | 縮圖、標題、連結類型、投放時間、狀態、排序、操作 |
| 操作按鈕 | 新增、編輯、刪除、啟用/停用、調整排序 |

#### 10.2 廣告編輯頁面

| 欄位 | 說明 |
|------|------|
| 標題 | 必填 |
| 圖片 | 上傳（1200x600） |
| 連結類型 | 無/外部連結/活動/店鋪/內部頁面 |
| 連結 URL | 依類型填寫 |
| 投放開始 | 選填 |
| 投放結束 | 選填 |
| 排序順序 | 數字 |
| 是否啟用 | 開關 |

#### 10.3 小喇叭公告頁面

| 元素 | 說明 |
|------|------|
| 列表欄位 | 公告內容、連結類型、投放時間、狀態、排序、操作 |
| 操作按鈕 | 新增、編輯、刪除、啟用/停用 |

#### 10.4 認識商脈頁面

| 元素 | 說明 |
|------|------|
| 富文本編輯器 | HTML 內容編輯 |
| 圖片上傳 | 編輯器內嵌圖片 |
| 儲存按鈕 | 儲存內容 |
| 最後更新時間 | 顯示更新時間 |

---

### 11. 帳務管理

#### 11.1 帳單列表頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 訂單編號、會員、訂單類型、狀態、日期範圍 |
| 列表欄位 | 訂單編號、會員、類型、金額、狀態、建立時間、操作 |
| 操作按鈕 | 查看詳情、更新狀態 |

#### 11.2 帳單詳情頁面

| 區塊 | 內容 |
|------|------|
| 訂單資訊 | 訂單編號、類型、金額、狀態、建立時間、付款時間 |
| 會員資訊 | 會員姓名、手機、會員編號 |
| 方案資訊 | 續費方案或充值方案詳情 |
| 金流資訊 | 付款方式、交易編號、金流回傳資料 |
| 操作按鈕 | 更新狀態（人工核對） |

#### 11.3 收款統計頁面

| 區塊 | 內容 |
|------|------|
| 統計卡片 | 今日收款、本週收款、本月收款、總收款 |
| 篩選條件 | 日期範圍、訂單類型 |
| 圖表 | 每日收款趨勢（折線圖） |
| 圖表 | 訂單類型佔比（圓餅圖） |
| 表格 | 每日收款明細 |

---

### 12. 聊天管理

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 聊天室名稱、類型、建立日期範圍 |
| 列表欄位 | 聊天室名稱、類型、參與人數、訊息數、最後活動時間 |
| 操作按鈕 | 查看詳情 |

---

### 13. 通知管理

#### 13.1 通知列表頁面

| 元素 | 說明 |
|------|------|
| 搜尋條件 | 標題、類型、狀態、日期範圍 |
| 列表欄位 | 標題、類型、目標對象、發送時間、狀態、操作 |
| 操作按鈕 | 查看詳情、刪除 |

#### 13.2 發送通知頁面

| 欄位 | 說明 |
|------|------|
| 通知標題 | 必填 |
| 通知內容 | 必填 |
| 通知類型 | 系統公告/活動通知/帳務通知 |
| 目標對象 | 全部會員/指定商會/指定會員類型/指定會員 |
| 連結設定 | 連結類型、連結目標 |
| 發送時間 | 立即發送/排程發送 |

---

### 14. 系統設定

#### 14.1 管理員列表頁面

| 元素 | 說明 |
|------|------|
| 列表欄位 | 帳號、姓名、郵箱、狀態、最後登入、操作 |
| 操作按鈕 | 新增、編輯、停用/啟用、重設密碼 |

#### 14.2 管理員編輯頁面

| 欄位 | 說明 |
|------|------|
| 帳號 | 必填（新增時） |
| 密碼 | 必填（新增時） |
| 姓名 | 必填 |
| 郵箱 | 選填 |
| 是否啟用 | 開關 |

---

## 共用元件規格

### PageHeader 頁面標頭

| 屬性 | 說明 |
|------|------|
| title | 頁面標題 |
| breadcrumb | 麵包屑路徑 |
| extra | 右側操作按鈕 |

### SearchForm 搜尋表單

| 屬性 | 說明 |
|------|------|
| fields | 搜尋欄位配置 |
| onSearch | 搜尋回調 |
| onReset | 重置回調 |

### DataTable 資料表格

| 屬性 | 說明 |
|------|------|
| columns | 欄位配置 |
| dataSource | 資料來源 |
| pagination | 分頁配置 |
| loading | 載入狀態 |
| rowSelection | 多選配置 |

### StatusTag 狀態標籤

| 狀態 | 顏色 |
|------|------|
| 成功/啟用/正常 | 綠色 |
| 處理中/待審核 | 藍色 |
| 警告/即將到期 | 橘色 |
| 失敗/停用/過期 | 紅色 |
| 草稿/已取消 | 灰色 |

### ImageUpload 圖片上傳

| 屬性 | 說明 |
|------|------|
| maxCount | 最大數量 |
| maxSize | 最大檔案大小 |
| accept | 允許格式 |
| aspectRatio | 建議比例 |

### RichTextEditor 富文本編輯器

| 功能 | 說明 |
|------|------|
| 基本格式 | 粗體、斜體、底線、刪除線 |
| 標題 | H1-H6 |
| 列表 | 有序、無序 |
| 連結 | 插入連結 |
| 圖片 | 上傳插入圖片 |
| 對齊 | 左/中/右對齊 |

---

## 路由配置

| 路由 | 元件 | 權限 |
|------|------|------|
| /login | LoginPage | 公開 |
| /dashboard | DashboardPage | 登入 |
| /member/pending | MemberPendingPage | 登入 |
| /member/list | MemberListPage | 登入 |
| /member/:id | MemberDetailPage | 登入 |
| /organization | OrganizationPage | 登入 |
| /point/records | PointRecordPage | 登入 |
| /point/transactions | TransactionPage | 登入 |
| /shop | ShopListPage | 登入 |
| /shop/:id | ShopDetailPage | 登入 |
| /event | EventListPage | 登入 |
| /event/create | EventFormPage | 登入 |
| /event/:id | EventDetailPage | 登入 |
| /event/:id/edit | EventFormPage | 登入 |
| /donation | ProjectListPage | 登入 |
| /donation/create | ProjectFormPage | 登入 |
| /donation/:id | ProjectDetailPage | 登入 |
| /donation/:id/edit | ProjectFormPage | 登入 |
| /lottery/records | LotteryRecordPage | 登入 |
| /lottery/settings | LotterySettingPage | 登入 |
| /cms/banner | BannerListPage | 登入 |
| /cms/banner/create | BannerFormPage | 登入 |
| /cms/banner/:id/edit | BannerFormPage | 登入 |
| /cms/announcement | AnnouncementListPage | 登入 |
| /cms/announcement/create | AnnouncementFormPage | 登入 |
| /cms/announcement/:id/edit | AnnouncementFormPage | 登入 |
| /cms/about | AboutPage | 登入 |
| /billing/orders | OrderListPage | 登入 |
| /billing/orders/:id | OrderDetailPage | 登入 |
| /billing/stats | StatsPage | 登入 |
| /chat | ChatRoomListPage | 登入 |
| /notification | NotificationListPage | 登入 |
| /notification/create | NotificationFormPage | 登入 |
| /system/admin | AdminListPage | 登入 |
| /system/admin/create | AdminFormPage | 登入 |
| /system/admin/:id/edit | AdminFormPage | 登入 |

---

## 相關文件

- [Auth 認證系統](./systems/auth.md)
- [Member 會員系統](./systems/member.md)
- [Organization 商會管理](./systems/organization.md)
- [Point 點數系統](./systems/point.md)
- [Shop 店鋪系統](./systems/shop.md)
- [Event 活動系統](./systems/event.md)
- [Donation 捐贈系統](./systems/donation.md)
- [Lottery 抽獎系統](./systems/lottery.md)
- [CMS 內容管理](./systems/cms.md)
- [Billing 帳務系統](./systems/billing.md)
- [Chat 聊天系統](./systems/chat.md)
- [Notification 通知系統](./systems/notification.md)
