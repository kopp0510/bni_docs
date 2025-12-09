# 商脈會 App 系統規劃文件

## 專案概述

商脈會 App 是一個商業會員管理平台，提供會員管理、點數交易、活動管理、即時通訊等功能。

---

## 文件結構

```
bni_docs/
├── README.md                 # 本文件（總覽）
├── backend/                  # 後端 API 系統文件
│   ├── README.md             # 後端系統總覽
│   ├── architecture.md       # 系統架構說明
│   └── systems/              # 各模組詳細規格
│       ├── auth.md           # 認證系統
│       ├── organization.md   # 商會管理
│       ├── member.md         # 會員系統
│       ├── point.md          # 點數系統
│       ├── shop.md           # 店鋪系統
│       ├── referral.md       # 推薦系統
│       ├── meeting.md        # 交流預約系統
│       ├── event.md          # 活動系統
│       ├── donation.md       # 捐贈系統
│       ├── lottery.md        # 抽獎系統
│       ├── cms.md            # 內容管理
│       ├── billing.md        # 帳務系統
│       ├── chat.md           # 聊天系統
│       └── notification.md   # 通知系統
├── admin/                    # 後台 CMS 系統文件
│   └── admin-cms.md          # 後台管理系統規劃
├── app/                      # 客端 App 文件
│   ├── README.md             # 客端 App 總覽
│   └── screens/              # 各頁面詳細規格
│       ├── home.md           # 首頁
│       ├── member-zone.md    # 會員專區
│       ├── business-lecture.md # 商學講堂
│       ├── points.md         # 使用點數
│       ├── about.md          # 認識愛心
│       ├── public-merchant.md # 公益商家
│       ├── events.md         # 最新活動
│       ├── charity.md        # 公益團體
│       ├── deals.md          # 好康優惠
│       ├── lottery.md        # 抽抽樂
│       ├── notification.md   # 通知中心
│       ├── profile.md        # 關於我
│       └── auth.md           # 登入註冊
└── images/                   # 參考示意圖
```

---

## 系統架構總覽

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              Client Layer                                    │
├─────────────────────────────────┬───────────────────────────────────────────┤
│         Mobile App (客端)        │           Admin CMS (後台)                │
│      iOS / Android / Web        │             Web Dashboard                 │
└─────────────────────────────────┴───────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              API Gateway                                     │
│                         (Nginx / Load Balancer)                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Backend Application                                  │
│                           (NestJS + TypeScript)                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Infrastructure Layer                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │  PostgreSQL  │  │    Redis     │  │  S3/MinIO    │  │   Firebase   │    │
│  │   主資料庫    │  │  快取/Pub-Sub │  │   檔案儲存    │  │   推播(選用)  │    │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 文件導覽

### 後端 API 系統

後端使用 NestJS + TypeScript 開發，包含 14 個核心模組。

| 文件 | 說明 |
|------|------|
| [後端系統總覽](./backend/README.md) | 後端架構、模組清單、開發流程 |
| [系統架構說明](./backend/architecture.md) | 詳細架構圖、相依關係 |

#### 核心模組

| 模組 | 文件 | 說明 |
|------|------|------|
| Auth | [auth.md](./backend/systems/auth.md) | 認證系統（登入、註冊、OTP） |
| Organization | [organization.md](./backend/systems/organization.md) | 商會管理 |
| Member | [member.md](./backend/systems/member.md) | 會員系統 |
| Point | [point.md](./backend/systems/point.md) | 點數系統 |
| Shop | [shop.md](./backend/systems/shop.md) | 店鋪系統 |
| Referral | [referral.md](./backend/systems/referral.md) | 推薦系統 |
| Meeting | [meeting.md](./backend/systems/meeting.md) | 交流預約系統 |
| Event | [event.md](./backend/systems/event.md) | 活動系統 |
| Donation | [donation.md](./backend/systems/donation.md) | 捐贈系統 |
| Lottery | [lottery.md](./backend/systems/lottery.md) | 抽獎系統 |
| CMS | [cms.md](./backend/systems/cms.md) | 內容管理 |
| Billing | [billing.md](./backend/systems/billing.md) | 帳務系統 |
| Chat | [chat.md](./backend/systems/chat.md) | 聊天系統 |
| Notification | [notification.md](./backend/systems/notification.md) | 通知系統 |

---

### 後台 CMS 系統

後台使用 React + Ant Design 開發。

| 文件 | 說明 |
|------|------|
| [後台管理系統](./admin/admin-cms.md) | 後台功能規劃、頁面結構 |

---

### 客端 App

客端使用 React Native / Flutter 開發。

| 文件 | 說明 |
|------|------|
| [客端 App 總覽](./app/README.md) | App 架構、導航結構、UI 規範 |

#### 頁面模組

| 頁面 | 文件 | 說明 |
|------|------|------|
| 首頁 | [home.md](./app/screens/home.md) | 主頁面與功能入口 |
| 會員專區 | [member-zone.md](./app/screens/member-zone.md) | 會員搜尋、交流、引薦 |
| 商學講堂 | [business-lecture.md](./app/screens/business-lecture.md) | 課程列表、報名 |
| 使用點數 | [points.md](./app/screens/points.md) | 點數管理、掃碼、轉點 |
| 認識愛心 | [about.md](./app/screens/about.md) | 組織介紹 |
| 公益商家 | [public-merchant.md](./app/screens/public-merchant.md) | 商家列表 |
| 最新活動 | [events.md](./app/screens/events.md) | 活動列表、報名 |
| 公益團體 | [charity.md](./app/screens/charity.md) | 公益團體、捐贈 |
| 好康優惠 | [deals.md](./app/screens/deals.md) | 優惠資訊 |
| 抽抽樂 | [lottery.md](./app/screens/lottery.md) | 小瑪莉抽獎 |
| 通知中心 | [notification.md](./app/screens/notification.md) | 推播通知 |
| 關於我 | [profile.md](./app/screens/profile.md) | 個人資料、設定 |
| 登入註冊 | [auth.md](./app/screens/auth.md) | 登入、註冊流程 |

---

## 技術棧

| 層級 | 技術選型 |
|------|----------|
| 後端框架 | NestJS (TypeScript) |
| 資料庫 | PostgreSQL |
| ORM | Prisma |
| 快取 | Redis |
| 即時通訊 | OpenIMSDK (評估中) |
| 檔案儲存 | S3 / MinIO |
| 容器化 | Docker + Docker Compose |
| API 文件 | Swagger |
| 後台前端 | React + Ant Design |
| 客端 App | Expo (React Native) |
| 推播通知 | Expo Notifications / FCM |

---

## 開發階段規劃

### Phase 1 - 核心服務
- Auth 認證系統
- Organization 商會管理
- Member 會員系統

### Phase 2 - 會員延伸
- Point 點數系統
- Shop 店鋪系統
- Referral 推薦系統
- Meeting 交流預約系統

### Phase 3 - 活動功能
- Event 活動系統
- Donation 捐贈系統
- Lottery 抽獎系統

### Phase 4 - 營運管理
- CMS 內容管理
- Billing 帳務系統

### Phase 5 - 通訊服務
- Chat 聊天系統
- Notification 通知系統

---

## 第三方服務整合

| 服務 | 用途 | 方案 |
|------|------|------|
| 金流付款 | 加點充值、會員續費 | 藍新 NewebPay |
| 簡訊驗證 | 手機驗證碼 | 自建 OTP 或第三方簡訊服務 |
| 推播通知 | App 推播 | 自建 WebSocket 或 FCM |
| 檔案儲存 | 圖片上傳 | 自建 MinIO 或 AWS S3 |
