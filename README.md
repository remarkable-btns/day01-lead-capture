# Day 01 — Lead Capture Automation System
### お問い合わせ自動応答システム

> Part of my **30-Day Automation Portfolio** — one production-ready automation per day,  
> targeting real business problems in the Japanese market.

<img width="1212" height="609" alt="lead_automation_workflow" src="https://github.com/user-attachments/assets/9b1e5263-628d-4476-9d16-e6dd783590a0" />

---

## What It Does / 概要

A contact form is submitted. Within **30 seconds**, three things happen automatically:

| # | Action | Tool |
|---|---|---|
| 1 | Lead logged to a Google Sheet (instant CRM) | Google Sheets |
| 2 | Personalized welcome email sent to the lead | Gmail |
| 3 | Slack alert sent to the business owner | Slack |

Zero human involvement. Runs 24/7. Works even at 2am.

---

## Target Clients / 対象クライアント

Any local business that receives web inquiries and loses leads due to slow response:

- **Salons** / 美容院・サロン
- **Clinics** / クリニック・医院
- **Real Estate** / 不動産
- **Restaurants** / レストラン・飲食店

---

## Business Impact / ビジネス効果

| Before / 導入前 | After / 導入後 |
|---|---|
| Owner checks email every few hours | Slack alert in under 30 seconds |
| Leads go cold waiting for a reply | Lead receives personalized reply instantly |
| No central record of inquiries | Auto-logged to Google Sheets CRM |
| 2–3 hrs/week managing inquiries | Fully automated, zero manual work |

> Studies show **9× higher conversion** when responding to leads in under 5 minutes.  
> Most small businesses respond in hours — or not at all.

---

## Architecture / システム構成

```
[Google Form Submission]
        │
        ▼
[Apps Script: POST to webhook]
        │
        ▼
[n8n Webhook Node]
        │
        ▼
[Code Node: Validate + Sanitize input]
        │
        ▼
[Wait Node: 2s — race condition guard]
        │
        ▼
[Google Sheets: Duplicate email check]
        │
        ├── DUPLICATE ──→ [Respond 200: silent skip]
        │
        └── NEW LEAD
                │
                ├──→ [Google Sheets: Append Row to CRM]
                │         ✗ fail → [Sheets: Log to Errors tab]
                │                → [Slack: Admin alert]
                │
                ├──→ [Gmail: Send welcome email]
                │         ✗ fail → [Sheets: Log to Errors tab]
                │
                ├──→ [Slack: Lead alert to #leads]
                │         ✗ fail → [Gmail: Admin alert]
                │
                └──→ [Respond 200: success]

[Error Trigger] ──→ [Gmail: Admin alert] + [Sheets: Error log]
```

---

## Tech Stack / 技術スタック

| Tool | Role |
|---|---|
| **n8n** | Workflow automation engine |
| **Google Forms** | Lead capture form |
| **Google Apps Script** | Webhook bridge (Form → n8n) |
| **Google Sheets** | CRM + Error log |
| **Gmail** | Welcome email sender |
| **Slack** | Real-time owner notifications |

---

## Error Handling / エラーハンドリング

Built for production reliability, not just demos:

- 〇 **Input validation** — rejects submissions missing name, email, or message
- 〇 **Email format check** — regex validation before any node runs
- 〇 **HTML sanitization** — strips `<script>` tags and injection attempts
- 〇 **Deduplication** — checks Sheets before appending; silently skips duplicates
- 〇 **Retry logic** — 3 retries with 5s delay on each node
- 〇 **Per-node fail branches** — Gmail fail ≠ Sheets fail; independent recovery paths
- 〇 **Rate limit guard** — tracks Gmail daily send count; alerts before hitting 500/day limit
- 〇 **Error log tab** — all failures written to `Errors` sheet with timestamp, node, and message
- 〇 **Global Error Trigger** — catch-all for any unhandled failures

---

## Files / ファイル構成

```
day01-lead-capture/
├── lead_capture.json     # n8n workflow — import directly
├── welcome_email.html    # Bilingual email template (JP/EN)
├── setup_guide.md        # Full setup instructions (日本語・English)
└── README.md             # This file
```

---

## Quick Start / クイックスタート

**Full setup guide → [setup_guide.md](./setup_guide.md)**

Short version:

```bash
# 1. Import lead_capture.json into n8n Cloud
# 2. Configure credentials: Google Sheets, Gmail, Slack
# 3. Set your Webhook URL in Apps Script
# 4. Run the test suite (5 tests)
# 5. Toggle workflow Active 
```

---

## Test Suite / テスト手順

| Test | Input | Expected Result |
|---|---|---|
| Happy path | Valid complete form | Row added, email sent, Slack pinged |
| Missing email | `name` + `message` only | 400 rejected at IF node |
| Invalid email | `notanemail` | Code node throws, error logged |
| Japanese name | `田中 花子` | Email renders `田中 花子 様` correctly |
| Duplicate | Same email twice | Second submission silently skipped |
| Burst | 10 submissions at once | All 10 logged, no crashes |
| Error trigger | Break Gmail credential | Admin alerted, error logged to Sheets |

---

## Pricing / 料金

| Item | Price |
|---|---|
| One-time setup / 初期設定費 | ¥25,000 – ¥40,000 ($120.00 - $250.00)* |
| Monthly retainer / 月額保守費 | ¥8,000 ($50.00) / month* |

*Prices are negotiable

**Deliverables / 納品物:**
- n8n workflow JSON (importable)
- Customized welcome email template
- Setup guide in Japanese
- 30-day post-setup support

---

## 30-Day Portfolio Progress / 30日間ポートフォリオ進捗

| Day | System | Status |
|---|---|---|
| **01** | **Lead Capture Automation** | Complete |
| 02 | Appointment Reminder System | 🔜 |
| 03 | Invoice Generation Automation | 🔜 |
| 04–30 | ... | 🔜 |

---

## About / プロフィール

Building a career in automation engineering targeting the Japanese market.  
日本市場をターゲットにした自動化エンジニアとしてのキャリアを構築中。

**Stack:** n8n · Playwright · UiPath · Python · Google Apps Script  
**Focus:** Freelance automation for local Japanese businesses → Enterprise RPA

---

## License

MIT — feel free to fork, adapt, and use for your own clients.

---

*If this helped you, consider starring the repo.*
