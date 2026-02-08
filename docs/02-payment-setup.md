# 2. 金流設定

系統支援三種金流服務，可以同時啟用，也可以只啟用其中一種或多種。

### 進入方式

**路徑：** 設定 > 一般設定 > 往下捲動至「Discord」區塊 > 綠界科技 / 歐富寶 / PayPal

### 2.1 綠界科技（ECPay）

| 欄位 | 說明 |
|------|------|
| **測試模式** | 勾選後使用綠界的**測試環境**（用於開發測試，不會真正扣款） |
| **商店代號** | 綠界分配的 MerchantID |
| **HashKey** | API 驗證密鑰（密碼欄位） |
| **HashIV** | API 驗證初始向量（密碼欄位） |

**申請方式：** 前往 [綠界科技官網](https://www.ecpay.com.tw/) 註冊商店帳號，取得以上資訊。

### 2.2 歐富寶（OPay）

| 欄位 | 說明 |
|------|------|
| **測試模式** | 勾選後使用歐富寶的**測試環境** |
| **商店代號** | 歐富寶分配的 MerchantID |
| **HashKey** | API 驗證密鑰（密碼欄位） |
| **HashIV** | API 驗證初始向量（密碼欄位） |

**申請方式：** 前往 [歐富寶官網](https://www.opay.tw/) 註冊商店帳號，取得以上資訊。

### 2.3 PayPal

| 欄位 | 說明 |
|------|------|
| **沙盒模式** | 勾選後使用 PayPal 的**沙盒測試環境**（用於開發測試，不會真正扣款） |
| **Client ID** | PayPal REST API 的 Client ID |
| **Client Secret** | PayPal REST API 的 Client Secret（密碼欄位） |

**申請方式：**

1. 前往 [PayPal Developer](https://developer.paypal.com/) 註冊開發者帳號
2. 進入 Dashboard > Apps & Credentials
3. 選擇 **Sandbox**（測試）或 **Live**（正式）模式
4. 點擊 **Create App** 建立應用程式
5. 取得 **Client ID** 和 **Secret**

**測試環境設定：**
- 在 PayPal Developer Dashboard 的 Sandbox > Accounts 中可以建立測試帳號
- 使用測試帳號進行付款測試，不會產生真實交易

**正式環境設定：**
- 切換到 Live 模式建立正式環境的 App
- 在 Odoo 中**取消勾選**沙盒模式，並填入正式環境的 Client ID 和 Secret

### 注意事項

- **測試模式 / 沙盒模式** 僅供開發或測試時使用，正式上線前請務必**取消勾選**。
- HashKey、HashIV 和 Client Secret 為敏感資訊，系統以密碼形式儲存，輸入後不會明文顯示。
- 如果所有金流都沒有設定，用戶在付款頁面將無法選擇任何付款方式。
- 至少需要完成**一種**金流的設定，購買功能才能正常運作。
- PayPal 支援國際付款，適合有海外客戶的需求。
