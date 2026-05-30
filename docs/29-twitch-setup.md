# Twitch 開台通知（後台）

當監控的 Twitch 實況主開始直播時，自動發送通知到指定的 Discord 頻道。

---

## Twitch API 設定

### 進入方式

**路徑：** 設定 > 一般設定 > 往下捲動至「Discord」區塊 > Twitch

### 設定欄位

| 欄位 | 說明 | 預設值 |
|------|------|--------|
| **Client ID** | Twitch Developer Console 取得的 Client ID | （空，必須設定） |
| **Client Secret** | Twitch Developer Console 取得的 Client Secret | （空，必須設定） |
| **Webhook Secret** | 系統自動生成，用於驗證 Twitch 回調簽章 | （自動生成，唯讀） |

### 如何取得 Twitch Client ID / Client Secret

1. 前往 [Twitch Developer Console](https://dev.twitch.tv/console)，使用 Twitch 帳號登入。
2. 點擊「Register Your Application」建立新應用程式。
3. 填寫資料：
   - **Name：** 自訂名稱（例如「Discord Bot」）
   - **OAuth Redirect URLs：** 填入 `http://localhost`（此功能不需要 OAuth 重導向）
   - **Category：** 選擇「Chat Bot」
4. 建立完成後，點擊「Manage」進入應用程式詳情頁面。
5. 複製 **Client ID**。
6. 點擊「New Secret」產生 **Client Secret**（只會顯示一次，請妥善保存）。
7. 將 Client ID 與 Client Secret 填入 Odoo 後台設定。

### 注意事項

- **Webhook Secret** 會在第一次建立訂閱時自動生成，不需要手動填寫。
- 伺服器必須有**公開 IP 與 SSL 憑證**（HTTPS），Twitch 才能正常發送 webhook 回調。
- `web.base.url` 系統參數必須設定為可從外部存取的 HTTPS 網址。

---

## 頻道監控

### 進入方式

**路徑：** Discord > Twitch > 頻道監控

### 列表欄位

| 欄位 | 說明 |
|------|------|
| **Twitch 帳號** | 實況主的 Twitch 登入帳號（例如 `rourou156`） |
| **顯示名稱** | 自動從 Twitch API 取得的實況主名稱（唯讀） |
| **通知文字** | 自訂通知訊息，支援 `{{name}}` 變數代表實況主名稱 |
| **Discord 通知頻道 ID** | 要發送通知的 Discord 文字頻道 ID |
| **直播狀態** | 離線（灰色）/ 直播中（綠色） |

### 新增頻道

1. 在列表底部直接填入資料（列表可直接編輯）。
2. 填入 **Twitch 帳號**（必填）和 **Discord 通知頻道 ID**（必填）。
3. **通知文字**預設為 `{{name}} 開台了！`，可自訂。
4. 儲存後，系統會自動：
   - 呼叫 Twitch API 取得實況主的 User ID、顯示名稱、頭像。
   - 建立 EventSub 訂閱（stream.online + stream.offline）。

**通知文字範例：**
```
{{name}} 開台了！
{{name}} 正在直播！快來看！
你家 {{name}} 出現拉！快去報到！
```

### 如何取得 Discord 頻道 ID

1. 確認已開啟 Discord 開發者模式（使用者設定 > 進階 > 開發者模式）。
2. 對目標文字頻道**按右鍵** > 選擇「複製頻道 ID」。
3. 將 ID 貼入「Discord 通知頻道 ID」欄位。

---

## 通知效果

當實況主開始直播時，系統會在指定的 Discord 頻道依序發送兩則訊息：

### 第一則：一般訊息

```
古錐 開台了！
<https://www.twitch.tv/rourou156>
```

- 不會 tag `@everyone`，僅發送通知文字。
- 自訂通知文字中的 `{{name}}` 會替換為實況主名稱。
- Twitch 連結不會產生重複的預覽。

### 第二則：Embed 資訊卡

包含以下資訊：
- **標題：** 實況主名稱（可點擊前往 Twitch）
- **描述：** 直播標題
- **欄位：** 觀眾數
- **縮圖：** 實況主頭像
- **圖片：** 直播畫面預覽圖
- **頁尾：** Twitch + 開台時間
- **顏色：** Twitch 紫色

---

## 自動機制

### 下播偵測

當實況主結束直播時，Twitch 會自動發送 `stream.offline` 事件，系統會將狀態更新為「離線」。下次開台時可正常觸發新的通知。

### 自動重新訂閱

系統每 60 秒會自動檢查所有監控頻道的訂閱狀態。如果 EventSub 訂閱因任何原因失效（例如伺服器短暫離線導致 Twitch 撤銷訂閱），系統會自動重新建立訂閱，無需手動處理。

---

## 注意事項

- 每個 Twitch 帳號只能新增一筆記錄（不可重複）。
- 刪除記錄時會自動刪除對應的 Twitch EventSub 訂閱。
- 將記錄設為「封存」（取消勾選 Active）等同於暫停監控，會刪除訂閱；重新啟用時會自動重建。
- Twitch 開台事件通常有 1～2 分鐘的延遲，這是 Twitch EventSub 的正常行為。
- 直播預覽圖需要在開台後數秒才會生成，極少數情況下可能顯示為預設圖片。
