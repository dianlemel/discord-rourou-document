# 17. 會員中心設定

「會員中心」是一個獨立的網頁，使用者透過 Discord 帳號授權登入後，可以查看自己的：

- 🎁 **禮物收藏圖鑑** — 已收集 / 未獲得的禮物
- 📦 **購買紀錄** — 點數購買訂單與付款狀態
- 💝 **贈送紀錄** — 送出與收到的禮物
- 🎰 **抽獎紀錄** — 抽獎結果與中獎獎品

要啟用此功能，管理員需完成兩件事：

1. 在 [Discord Developer Portal](https://discord.com/developers/applications) 建立 OAuth2 設定
2. 在 Odoo 後台填入 OAuth2 憑證並設定面板權限

---

## 17.1 在 Discord Developer Portal 設定 OAuth2

### 進入方式

1. 前往 [Discord Developer Portal](https://discord.com/developers/applications)。
2. 點選您**現有的 Bot 應用**（與 Bot Token 為同一個 Application，OAuth2 共用）。

### 設定步驟

1. 左側選單點選 **OAuth2 → General**。
2. 在 **Redirects** 區段點 **Add Redirect**，填入：

   ```
   https://你的網域/discord/portal/callback
   ```

   例如：`https://discord.example.com/discord/portal/callback`

3. 點頁面下方 **Save Changes**（這步很容易漏，沒按沒生效）。

### 取得憑證

| 欄位 | 位置 | 操作 |
|------|------|------|
| **Client ID** | 「Client information」區段 | 直接複製 |
| **Client Secret** | 「Client information」區段 | 點 **Reset Secret** 取得（**只顯示一次，請馬上複製**） |

> ⚠️ Client Secret 與 Bot Token 不同，這是 OAuth2 專用的密鑰。
> ⚠️ 如果 Client Secret 不慎遺失，可以重新 Reset 一次取得新的（舊的會立即失效）。

### 不需要設定的部分

- ❌ **OAuth2 URL Generator**：那是用來產生固定授權連結的，本系統會自行組裝
- ❌ **Bot scope / Permissions**：那是邀請 Bot 加入伺服器用的，與會員中心登入無關

---

## 17.2 在 Odoo 後台填入設定

### 進入方式

**路徑：** 設定 > 一般設定 > 往下捲動至「**會員中心 (Portal)**」區塊

### 設定欄位

| 欄位 | 說明 |
|------|------|
| **OAuth Client ID** | Discord Developer Portal 取得的 Client ID |
| **OAuth Client Secret** | Discord Developer Portal 取得的 Client Secret |
| **面板允許身分組 ID** | 允許使用 `!portal_setup` 指令的 Discord Role ID，多個以**半形逗號**分隔 |

> 只有擁有「面板允許身分組」的成員才能使用 `!portal_setup` 指令發送會員中心面板。
> `!portal_setup` 指令**不限頻道**，可在任何頻道中使用。

### 範例

| 欄位 | 範例值 |
|------|--------|
| OAuth Client ID | `1234567890123456789` |
| OAuth Client Secret | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| 面板允許身分組 ID | `123456789012345678,987654321098765432` |

設定完成後，按 **儲存**。

---

## 17.3 確認網域設定

會員中心需要 Odoo 網域可以從外部訪問，且必須是 **HTTPS**（Discord OAuth2 安全要求 + 我們的 cookie 機制需要）。

### 檢查 `web.base.url`

**路徑：** 設定 > 技術 > 系統參數（需開啟開發者模式）

| 參數名稱 | 必須的值 |
|---------|---------|
| `web.base.url` | `https://你的網域`（例如 `https://discord.example.com`） |
| `web.base.url.freeze` | `True` |

> ⚠️ **`web.base.url.freeze` 設為 `True` 非常重要**：否則 Odoo 會在使用者下次登入時自動把 `web.base.url` 覆寫回當前請求的 URL（很常會回到 http），導致 OAuth2 重導 URL 不一致而失敗。

### 與 Discord Developer Portal 的對應關係

| Odoo 端 `web.base.url` | Developer Portal 的 Redirect URI |
|------------------------|---------------------------------|
| `https://discord.example.com` | `https://discord.example.com/discord/portal/callback` |

兩邊 **scheme（http/https）和網域必須完全相同**，否則 Discord 會回傳「無效的 OAuth2 redirect_uri」錯誤。

---

## 17.4 自訂面板訊息

「會員中心面板」的標題、說明文字、Embed 顏色等可以在訊息模板中修改。

**路徑：** Discord > 設定 > 訊息模板 > 找到類型為「會員中心面板」的紀錄

可調整的欄位請參考 [6. 訊息模板](06-message-template.md)。

---

## 17.5 使用者初次登入會發生什麼？

當使用者第一次點擊面板按鈕進入會員中心時：

1. 系統重導至 Discord 授權頁面，請使用者同意授權「讀取基本資料（identify）」
2. 使用者同意後，系統取得 Discord ID、使用者名稱、頭像
3. **如果該使用者尚未綁定** → 系統會用 Discord 資料**自動建立聯絡人**（同時嘗試下載 Discord 頭像）
4. **如果該使用者已綁定** → 直接使用既有聯絡人
5. 建立會員中心 session（**有效期 7 天**），寫入瀏覽器 cookie
6. 自動跳轉到會員中心首頁

> 之後 7 天內使用者再次訪問會員中心，無須重新授權；若手動登出或 7 天過期則需重新授權。
> 系統**只取得最基本的個人資料（ID、名稱、頭像）**，不會讀取使用者的伺服器列表、訊息或 email。

---

## 17.6 安全性說明

| 機制 | 說明 |
|------|------|
| **OAuth2 標準流程** | 不直接接觸使用者密碼，授權流程在 Discord 官方頁面完成 |
| **CSRF 防護** | 授權流程使用 `state` 隨機 token 驗證，防止跨站攻擊 |
| **Cookie 安全屬性** | Session cookie 使用 HttpOnly + Secure + SameSite=Lax |
| **僅查看自己的資料** | 即使取得 session cookie，也只能看到自己對應的禮物、訂單、贈送、抽獎資料 |
| **登出機制** | 使用者點選右上角「登出」可立即撤銷 session |

---

## 17.7 常見問題

### Q1：使用者點選面板按鈕後跳到 Discord 授權頁，但顯示「無效的 OAuth2 redirect_uri」？

**原因**：Odoo 的 `web.base.url` 與 Developer Portal 中設定的 Redirect URI 不一致（最常見是 `http` vs `https`）。

**解法**：依 17.3 章節檢查 `web.base.url` 與 `web.base.url.freeze` 設定。

### Q2：使用者授權後，會員中心首頁的頭像顯示不出來？

**原因**：可能在系統升級前就已建立過聯絡人，但頭像欄位是空的。

**解法**：
- 使用者可透過 `!bind` 重新觸發頭像同步（如果系統有實作此邏輯）
- 或由管理員在 Odoo 後台 → 聯絡人 → 直接上傳頭像

### Q3：使用者顯示「找不到此 Discord 帳號」或無法登入？

**原因**：很少見，但可能是 OAuth2 設定錯誤或 Discord API 回傳異常。

**解法**：
- 檢查 Odoo log 中的錯誤訊息
- 確認 Client ID / Client Secret 沒有打錯
- 確認 Developer Portal 的 Redirect URI 已 **Save Changes**

### Q4：可以讓多人共用同一個瀏覽器登入嗎？

不建議。Session cookie 是綁定瀏覽器的，A 登入後 B 同瀏覽器訪問會看到 A 的資料。請使用者在共用裝置使用後**主動登出**。

---

## 17.8 後續擴充

會員中心是「入口」設計，未來如果新增功能（例如：抽獎排行榜、活動報名、客服中心等），會以「子頁面」的形式加入，**不需要重新發送面板訊息**，原本的面板按鈕會自動帶到首頁，使用者就能看到新增的卡片。
