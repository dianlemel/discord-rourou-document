# Discord 點數機器人 — 使用指南

---

## 第一部分：Odoo 後台設定

> 管理員操作指南，說明如何在 Odoo 後台進行模組的各項設定。

| # | 章節 | 說明 |
|---|------|------|
| 1 | [基本設定](docs/01-basic-setup.md) | Bot Token、點數單價、公告頻道、群發權限、禮物面板權限、點數面板權限、綁定面板權限 |
| 2 | [金流設定](docs/02-payment-setup.md) | 綠界科技 / 歐富寶 |
| 3 | [指令設定](docs/03-command-config.md) | 自訂指令名稱與別名 |
| 4 | [頻道設定](docs/04-channel-config.md) | 控制指令可用的頻道 |
| 5 | [自動刪除頻道](docs/05-auto-delete.md) | 訊息自動清除規則 |
| 6 | [訊息模板](docs/06-message-template.md) | Jinja2 模板、Embed 樣式、圖片設定 |
| 7 | [禮物管理](docs/07-gift-management.md) | 禮物項目、別名、圖片、Embed 自訂 |
| 8 | [購買訂單管理](docs/08-purchase-orders.md) | 查看購買訂單與狀態 |
| 9 | [贈送紀錄管理](docs/09-gift-records.md) | 查看贈送紀錄 |
| 10 | [禮物庫存管理](docs/10-gift-inventory.md) | 管理使用者的禮物庫存 |
| 11 | [聯絡人整合](docs/11-contact.md) | Discord ID、點數、贈送/接收紀錄、禮物庫存 |
| 12 | [工單設定與管理](docs/12-ticket-setup.md) | 工單設定、工單記錄管理 |
| 13 | [工單付款管理](docs/13-ticket-payment-setup.md) | 工單付款設定、付款記錄管理 |
| 14 | [派工設定與管理](docs/27-assign-setup.md) | 派工設定、派工記錄管理 |

## 第二部分：Discord 指令操作

> Discord 使用者的操作指南。

| # | 章節 | 說明 |
|---|------|------|
| 14 | [帳號綁定](docs/14-binding.md) | `!bind`、`!bind_setup` 綁定面板 |
| 15 | [查詢點數](docs/15-query-points.md) | `!points`、點數面板查詢按鈕 |
| 16 | [購買點數](docs/16-buy-points.md) | `!buy <數量>`、點數面板購買按鈕 |
| 17 | [點數面板](docs/17-points-panel.md) | `!points_setup` 點數面板（查詢 + 購買） |
| 18 | [贈送禮物](docs/18-gift.md) | `!gift @對方 禮物名稱 [數量]` |
| 19 | [查詢收到的禮物](docs/19-my-gifts.md) | `!mygifts` |
| 20 | [群發通知](docs/20-announce.md) | `!announce @身分組 訊息` |
| 21 | [工單系統](docs/21-ticket.md) | `!ticket_setup`、建立/關閉工單 |
| 22 | [工單付款](docs/22-ticket-payment.md) | `!pay 金額`、付款流程 |
| 23 | [禮物面板](docs/23-gift-panel.md) | `!gift_setup` 禮物面板（查詢 + 贈送） |
| 24 | [工單派工](docs/28-assign.md) | `!assign @使用者 頻道名稱` 派工指令 |

## 附錄

| # | 章節 | 說明 |
|---|------|------|
| 25 | [常見問題 FAQ](docs/24-faq.md) | 常見問題與排查步驟 |
| 26 | [注意事項](docs/25-notes.md) | 安全、規範、管理員須知 |
| 27 | [速查表](docs/26-quick-reference.md) | 指令速查、選單速查 |

---

*如有任何問題，請聯繫伺服器管理員取得協助。*
