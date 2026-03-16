# 預付卡管理系統 — 完整部署教學（零基礎適用）

---

## 🗺️ 總覽：你需要完成的 4 個步驟

1. **建立 Google 試算表 + Apps Script 專案**
2. **取得 LINE 開發者設定（Channel Token + LIFF ID）**
3. **部署 GAS 為 Web App，取得 URL**
4. **填入設定值並完成**

預計總時間：**30～45 分鐘**

---

## 第一步：建立 Google Apps Script 專案

### 1-1 建立試算表
1. 前往 [Google Sheets](https://sheets.google.com)
2. 點左上角「＋ 空白試算表」建立新檔案
3. 將檔案命名為「預付卡管理系統」

### 1-2 開啟 Apps Script
1. 在試算表上方選單點「擴充功能」
2. 選「Apps Script」
3. 會開啟 GAS 編輯器頁面

### 1-3 建立程式檔案
預設有一個 `Code.gs` 檔案，**全選刪除內容後**：

1. **將 `Code.gs` 的全部內容貼入 `Code.gs`**

2. 新增第二個檔案：
   - 點左側「＋」→「指令碼」
   - 命名為 `Trigger`
   - **將 `Trigger.gs` 的全部內容貼入**

3. 新增 HTML 檔案：
   - 點左側「＋」→「HTML 檔案」
   - 命名為 `index`（系統會自動加 .html）
   - **將 `index.html` 的全部內容貼入**

### 1-4 填入設定值
在 `Code.gs` 最上方找到這段，填入你的設定：

```javascript
const CONFIG = {
  LINE_CHANNEL_ACCESS_TOKEN: '請填入你的LINE Channel Access Token',  // 第二步取得
  ADMIN_ACCOUNT: 'lbdk',        // 管理員帳號（固定）
  ADMIN_PASSWORD: 'admin1234',  // ← 建議改成你自己的密碼
  SPREADSHEET_ID: '',           // 留空即可，會自動抓當前試算表
};
```

---

## 第二步：取得 LINE 設定

### 2-1 建立 LINE 官方帳號 & Messaging API
1. 前往 [LINE Developers Console](https://developers.line.biz/console/)
2. 用你的 LINE 帳號登入
3. 點「Create a new provider」→ 輸入名稱（例如：預付卡管理）
4. 點「Create a Messaging API channel」
5. 填入必要資訊後建立

### 2-2 取得 Channel Access Token
1. 進入剛建立的 channel
2. 點「Messaging API」頁籤
3. 滾到最下方「Channel access token」
4. 點「Issue」產生 token
5. **複製這串 token**，貼到 `Code.gs` 的 `LINE_CHANNEL_ACCESS_TOKEN`

### 2-3 建立 LIFF App
1. 在同一個 provider 下，點「Create a LIFF app」  
   （或在 channel 的「LIFF」頁籤點「Add」）
2. 填入設定：
   - **LIFF app name**：預付卡管理系統
   - **Size**：Full（全螢幕）
   - **Endpoint URL**：先填 `https://example.com`（第三步取得真正 URL 後再改）
   - **Scopes**：勾選 `profile`、`openid`
3. 點「Add」建立
4. **複製 LIFF ID**（格式類似 `1234567890-AbCdEfGh`）

---

## 第三步：部署 GAS 為 Web App

### 3-1 初始化資料表
1. 回到 GAS 編輯器
2. 在上方函式選單選擇「`initializeSheets`」
3. 點「▶ 執行」
4. 第一次會要求授權：點「審查權限」→「允許」
5. 看到執行結果顯示「初始化完成」即成功

### 3-2 部署為 Web App
1. 右上角點「部署」→「新增部署作業」
2. 點齒輪圖示選擇類型「網頁應用程式」
3. 填入設定：
   - **說明**：v1.0
   - **以以下身分執行**：我（你的 Google 帳號）
   - **有權存取的人員**：所有人
4. 點「部署」
5. 複製「網頁應用程式 URL」（類似 `https://script.google.com/macros/s/AKfyc...../exec`）

### 3-3 設定排程通知
1. 在 GAS 編輯器選擇「`setupDailyTrigger`」函式
2. 點「▶ 執行」
3. 完成！系統會每天早上 9 點自動掃描即將到期的門號並推播通知

---

## 第四步：填入 URL，完成設定

### 4-1 更新前端設定
打開 `index.html`，找到最底部的這兩行：

```javascript
const GAS_URL = ''; // ← 貼入步驟 3-2 取得的 Web App URL
const LIFF_ID = ''; // ← 貼入步驟 2-3 取得的 LIFF ID
```

改成：
```javascript
const GAS_URL = 'https://script.google.com/macros/s/你的ID/exec';
const LIFF_ID = '1234567890-AbCdEfGh';
```

### 4-2 重新部署（必要！每次改程式都需要重新部署）
1. 點「部署」→「管理部署作業」
2. 點鉛筆圖示（編輯）
3. 「版本」選「建立新版本」
4. 點「部署」→ 複製新的 URL（通常跟舊的一樣）

### 4-3 更新 LIFF Endpoint URL
1. 回到 LINE Developers Console
2. 找到你的 LIFF app
3. 將 Endpoint URL 改為 GAS Web App URL
4. 儲存

---

## 🎉 完成！如何使用？

### 使用者入口
- **LIFF 連結**：`https://liff.line.me/你的LIFF_ID`
- 分享此連結給使用者，在 LINE 中開啟即可

### 管理員登入
- 帳號：`lbdk`
- 密碼：你在 `Code.gs` 設定的密碼（預設 `admin1234`，建議修改）

---

## 常見問題

### Q: 執行時出現「授權」錯誤
→ 點「審查權限」→ 選你的 Google 帳號 → 點「進階」→「前往（不安全）」→「允許」

### Q: 部署後呼叫 API 沒反應
→ 確認 GAS_URL 是否正確，且有重新部署過

### Q: LINE 通知發不出去
→ 確認 LINE Channel Access Token 已填入 Code.gs，且使用者需先在系統中綁定 LINE（登入時會自動取得 LINE User ID）

### Q: 如何讓使用者綁定 LINE？
→ 使用者必須透過 LIFF 連結（在 LINE 中）開啟並登入，系統會自動取得 LINE User ID 並綁定

### Q: 每次修改程式後需要重新部署嗎？
→ **是的！** 每次修改 `.gs` 或 `.html` 後，必須到「管理部署作業」→ 建立新版本 → 重新部署，變更才會生效

---

## 系統架構示意

```
使用者（LINE APP）
    │
    ▼
LIFF 前端（index.html）
    │  HTTP POST
    ▼
GAS Web App（Code.gs）
    │
    ├── 讀寫 Google Sheets（8個工作表）
    │
    └── 呼叫 LINE Messaging API 推播通知

每日 09:00 自動觸發
    │
    ▼
Trigger.gs（掃描到期門號 → 推播）
```

---

## 工作表結構（自動建立）

| 工作表名稱 | 功能 |
|-----------|------|
| members | 會員帳號、密碼、LINE綁定 |
| sim_cards | 所有門號資料 |
| app_platforms | APP平台清單 |
| app_registrations | 門號×APP 的勾選狀態 |
| notifications | 通知發送記錄 |
| audit_logs | 操作日誌 |
| announcements | 系統公告 |
| blacklist | 黑名單 |

---

_如有問題，請截圖錯誤訊息詢問。_
