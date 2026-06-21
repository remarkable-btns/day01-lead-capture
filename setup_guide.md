# セットアップガイド / Setup Guide
## Lead Capture Automation System — Day 01

> **対応言語 / Language:** 日本語 · English  
> **所要時間 / Setup Time:** 約2〜4時間 / Approx. 2–4 hours  
> **難易度 / Difficulty:** 初級〜中級 / Beginner to Intermediate

---

## 目次 / Table of Contents

1. [前提条件 / Prerequisites](#1-前提条件--prerequisites)
2. [Googleフォームのセットアップ / Google Forms Setup](#2-googleフォームのセットアップ--google-forms-setup)
3. [Googleスプレッドシートのセットアップ / Google Sheets Setup](#3-googleスプレッドシートのセットアップ--google-sheets-setup)
4. [n8nワークフローのセットアップ / n8n Workflow Setup](#4-n8nワークフローのセットアップ--n8n-workflow-setup)
5. [Slackのセットアップ / Slack Setup](#5-slackのセットアップ--slack-setup)
6. [Gmailの設定 / Gmail Configuration](#6-gmailの設定--gmail-configuration)
7. [Google Apps Scriptの設定 / Apps Script Configuration](#7-google-apps-scriptの設定--apps-script-configuration)
8. [テスト手順 / Testing Protocol](#8-テスト手順--testing-protocol)
9. [エラーログの確認 / Checking Error Logs](#9-エラーログの確認--checking-error-logs)
10. [カスタマイズ方法 / Customization](#10-カスタマイズ方法--customization)
11. [よくあるご質問 / FAQ](#11-よくあるご質問--faq)
12. [サポート / Support](#12-サポート--support)

---

## 1. 前提条件 / Prerequisites

### 必要なアカウント / Required Accounts

| サービス / Service | 用途 / Purpose | 費用 / Cost |
|---|---|---|
| n8n Cloud | ワークフロー自動化 / Workflow automation | 無料プランあり / Free tier available |
| Google アカウント | Forms · Sheets · Gmail | 無料 / Free |
| Slack ワークスペース | オーナーへの通知 / Owner alerts | 無料プランあり / Free tier available |

### 事前に準備するもの / Prepare in Advance

- [ ] n8n Cloudアカウント作成済み / n8n Cloud account created
- [ ] Googleアカウントにログイン済み / Logged into Google account
- [ ] Slackワークスペースに参加済み / Joined Slack workspace
- [ ] `#leads` チャンネルを作成済み / `#leads` channel created in Slack

---

## 2. Googleフォームのセットアップ / Google Forms Setup

### フォームの作成 / Create the Form

1. [forms.google.com](https://forms.google.com) を開く / Open Google Forms
2. **「新しいフォーム」** をクリック / Click **"Blank form"**
3. タイトルを入力 / Enter the form title:
   ```
   お問い合わせフォーム
   ```
4. 説明文を入力 / Enter the description:
   ```
   以下のフォームにご記入ください。24時間以内にご連絡いたします。
   Please fill in the form below. We will contact you within 24 hours.
   ```

### 質問の追加 / Add Questions

以下の順番で質問を追加してください。  
Add the following questions in order.

**質問1 — お名前 / Name**
```
種類 / Type:    短文回答 / Short answer
ラベル / Label: お名前 / Full Name
必須 / Required: 〇 はい / Yes
```

**質問2 — メールアドレス / Email**
```
種類 / Type:    短文回答 / Short answer
ラベル / Label: メールアドレス / Email Address
必須 / Required: 〇 はい / Yes
入力規則 / Validation: テキスト → メールアドレス / Text → Email
```

**質問3 — 電話番号 / Phone**
```
種類 / Type:    短文回答 / Short answer
ラベル / Label: 電話番号（任意）/ Phone Number (Optional)
必須 / Required: × いいえ / No
```

**質問4 — お問い合わせ内容 / Message**
```
種類 / Type:    段落 / Paragraph
ラベル / Label: お問い合わせ内容 / Your Message
必須 / Required: 〇 はい / Yes
```

**質問5 — 情報源 / How did you find us?**
```
種類 / Type:    ラジオボタン / Multiple choice
ラベル / Label: どこでお知りになりましたか？/ How did you find us?
必須 / Required: × いいえ / No
選択肢 / Options:
  - Google検索 / Google Search
  - Instagram
  - 友人・知人の紹介 / Referral
  - チラシ・看板 / Flyer or Sign
  - その他 / Other
```

### 送信後のメッセージ / Confirmation Message

フォーム設定 → 回答 → 確認メッセージ:  
Form settings → Presentation → Confirmation message:
```
ありがとうございます！担当者より24時間以内にご連絡いたします。
Thank you! A team member will contact you within 24 hours.
```

---

## 3. Googleスプレッドシートのセットアップ / Google Sheets Setup

### スプレッドシートの作成 / Create the Spreadsheet

1. [sheets.google.com](https://sheets.google.com) を開く / Open Google Sheets
2. 新しいスプレッドシートを作成 / Create a new spreadsheet
3. 名前を変更 / Rename to:
   ```
   Lead Capture CRM
   ```

### シートの設定 / Sheet Configuration

**Sheet1（リード管理 / Lead Management）**

A1から順に以下のヘッダーを入力 / Enter these headers starting from A1:

| A | B | C | D | E | F |
|---|---|---|---|---|---|
| Name | Email | Phone | Message | Timestamp | Status |

Statusの列にドロップダウンを設定 / Add a dropdown to the Status column:
1. F列を選択 / Select column F
2. データ → データの入力規則 / Data → Data Validation
3. 条件: ドロップダウン / Criteria: Dropdown
4. 選択肢 / Options: `New`, `Contacted`, `Converted`, `Lost`

**Errorsシートの追加 / Add Errors Sheet**

1. 下の **「+」** をクリックして新しいシートを追加 / Click **"+"** to add a new sheet
2. 名前を変更 / Rename to: `Errors`
3. 以下のヘッダーを入力 / Enter these headers:

| A | B | C | D | E | F |
|---|---|---|---|---|---|
| Timestamp | Node | Error Message | Input Name | Input Email | Status |

Statusのドロップダウン / Status dropdown: `Unresolved`, `Investigating`, `Resolved`

### スプレッドシートIDの確認 / Find Your Spreadsheet ID

URLから確認できます / Find it in the URL:
```
https://docs.google.com/spreadsheets/d/[ここがID / THIS IS THE ID]/edit
```

このIDはn8nの設定で使用します / You will use this ID in n8n.

---

## 4. n8nワークフローのセットアップ / n8n Workflow Setup

### ワークフローのインポート / Import the Workflow

1. [cloud.n8n.io](https://cloud.n8n.io) にログイン / Log in to n8n Cloud
2. **「New Workflow」** をクリック / Click **"New Workflow"**
3. 右上メニュー（⋮）→ **「Import from File」** / Top right menu (⋮) → **"Import from File"**
4. `lead_capture.json` を選択 / Select `lead_capture.json`
5. ワークフロー名を変更 / Rename the workflow:
   ```
   Day 01 — Lead Capture Automation
   ```

### 認証情報の設定 / Configure Credentials

インポート後、以下のノードで認証情報を設定してください。  
After importing, configure credentials in these nodes.

**Google Sheets ノード / Node**
1. ノードをクリック / Click the node
2. **「Credential」** → **「Create New」**
3. Googleアカウントでログイン / Sign in with Google
4. Spreadsheet IDを貼り付け / Paste your Spreadsheet ID

**Gmail ノード / Node**
1. ノードをクリック / Click the node
2. **「Credential」** → **「Create New」**
3. 送信元のGoogleアカウントでログイン / Sign in with the sender Google account

**Slack ノード / Node**
1. Incoming Webhook URLを貼り付け / Paste your Incoming Webhook URL
2. （次のセクションで取得 / See next section）

### Webhook URLの確認 / Get Your Webhook URL

1. Webhookノードをクリック / Click the Webhook node
2. **「Production URL」** をコピー / Copy the **"Production URL"**:
   ```
   https://yourname.app.n8n.cloud/webhook/lead-capture
   ```
3. このURLをApps Scriptで使用します / Use this URL in Apps Script

---

## 5. Slackのセットアップ / Slack Setup

### Incoming Webhookの作成 / Create an Incoming Webhook

1. [api.slack.com/apps](https://api.slack.com/apps) を開く / Open Slack API
2. **「Create New App」** → **「From Scratch」**
3. アプリ名 / App name: `Lead Capture Bot`
4. ワークスペースを選択 / Select your workspace
5. 左サイドバー → **「Incoming Webhooks」** → **ON** に切り替え / Toggle **ON**
6. **「Add New Webhook to Workspace」** をクリック / Click **"Add New Webhook to Workspace"**
7. チャンネルを選択 / Select channel: `#leads`
8. Webhook URLをコピー / Copy the Webhook URL:
   ```
   https://hooks.slack.com/services/XXXXXXXXX/XXXXXXXXX/XXXXXXXXXXXXXXXX
   ```
9. このURLをn8nのSlackノードに貼り付け / Paste this into the Slack node in n8n

---

## 6. Gmailの設定 / Gmail Configuration

### 送信メールのカスタマイズ / Customize the Email

`welcome_email.html` を開き、以下の箇所を置き換えてください。  
Open `welcome_email.html` and replace the following placeholders:

| プレースホルダー / Placeholder | 置き換え内容 / Replace With |
|---|---|
| `{{BUSINESS_NAME}}` | 会社名 / Your business name |
| `{{STAFF_NAME}}` | 担当者名 / Staff member name |
| `{{BUSINESS_PHONE}}` | 電話番号 / Phone number |
| `{{WEBSITE_URL}}` | WebサイトURL / Website URL |

n8nが自動的に置き換える変数 / Variables replaced automatically by n8n:

| 変数 / Variable | 内容 / Content |
|---|---|
| `{{$json.name}}` | 送信者の名前 / Submitter's name |
| `{{$json.email}}` | 送信者のメール / Submitter's email |
| `{{$json.phone}}` | 送信者の電話番号 / Submitter's phone |
| `{{$json.message}}` | お問い合わせ内容 / Inquiry message |
| `{{$json.timestamp}}` | 受付日時 / Submission timestamp |

### Gmail送信制限について / Gmail Send Limits

| プラン / Plan | 1日の上限 / Daily Limit |
|---|---|
| Gmail (個人) / Personal | 500通 / 500 emails |
| Google Workspace | 2,000通 / 2,000 emails |

> 大量のお問い合わせが予想される場合はSendGridの利用をご検討ください。  
> For high-volume businesses, consider using SendGrid instead.

---

## 7. Google Apps Scriptの設定 / Apps Script Configuration

### スクリプトの追加 / Add the Script

1. Googleフォームを開く / Open Google Forms
2. 右上の **「⋮」** → **「スクリプトエディタ」** / Top right **"⋮"** → **"Script editor"**
3. 以下のコードを貼り付け / Paste the following code:

```javascript
function onFormSubmit(e) {
  const response = e.response;
  const items = response.getItemResponses();

  // フィールドの初期化 / Initialize fields
  const data = {
    name:      "",
    email:     "",
    phone:     "",
    message:   "",
    source:    "",
    timestamp: new Date().toISOString()
  };

  // フォームの回答をマッピング / Map form responses
  items.forEach(item => {
    const label = item.getItem().getTitle();
    const value = item.getResponse() || "";

    if (label === "お名前" || label === "Full Name")                          data.name    = value;
    if (label === "メールアドレス" || label === "Email Address")              data.email   = value;
    if (label === "電話番号（任意）" || label === "Phone Number (Optional)")  data.phone   = value;
    if (label === "お問い合わせ内容" || label === "Your Message")             data.message = value;
    if (label === "どこでお知りになりましたか？" || label === "How did you find us?") data.source = value;
  });

  // n8n Webhookに送信 / Send to n8n Webhook
  const webhookUrl = "https://yourname.app.n8n.cloud/webhook/lead-capture";

  try {
    UrlFetchApp.fetch(webhookUrl, {
      method: "post",
      contentType: "application/json",
      payload: JSON.stringify(data),
      muteHttpExceptions: true
    });
  } catch (error) {
    Logger.log("Webhook error: " + error.toString());
  }
}
```

4. `webhookUrl` をあなたのn8n URLに変更 / Replace `webhookUrl` with your n8n URL
5. 保存 / Save (Ctrl+S)

### トリガーの設定 / Configure the Trigger

1. 左サイドバー → **時計アイコン（トリガー）** / Left sidebar → **Clock icon (Triggers)**
2. **「トリガーを追加」** / Click **"Add Trigger"**
3. 以下の通りに設定 / Configure as follows:

| 設定 / Setting | 値 / Value |
|---|---|
| 実行する関数 / Function | `onFormSubmit` |
| イベントのソース / Event source | フォームから / From form |
| イベントの種類 / Event type | フォーム送信時 / On form submit |

4. 保存して認証を完了 / Save and complete authorization

---

## 8. テスト手順 / Testing Protocol

以下の順番でテストを実施してください。  
Run tests in this order.

### テスト1 — 正常系 / Test 1: Happy Path

```bash
curl -X POST https://yourname.app.n8n.cloud/webhook/lead-capture \
  -H "Content-Type: application/json" \
  -d '{
    "name": "田中 花子",
    "email": "your-own-email@gmail.com",
    "phone": "090-1234-5678",
    "message": "予約について聞きたいです",
    "source": "Instagram"
  }'
```

**確認事項 / Check:**
- [ ] Googleスプレッドシートに新しい行が追加される / New row added to Google Sheets
- [ ] ウェルカムメールが届く / Welcome email received
- [ ] Slackに通知が届く / Slack notification received

### テスト2 — 必須項目なし / Test 2: Missing Required Fields

```bash
curl -X POST https://yourname.app.n8n.cloud/webhook/lead-capture \
  -H "Content-Type: application/json" \
  -d '{"name": "山田 太郎", "message": "テスト"}'
```

**確認事項 / Check:**
- [ ] ワークフローが400エラーを返す / Workflow returns 400 error
- [ ] スプレッドシートに行が追加されない / No row added to Sheets

### テスト3 — 重複送信 / Test 3: Duplicate Submission

```bash
# 1回目 / First submission
curl -X POST https://yourname.app.n8n.cloud/webhook/lead-capture \
  -H "Content-Type: application/json" \
  -d '{"name":"田中 花子","email":"duplicate@example.com","message":"初回"}'

sleep 3

# 2回目（同じメール）/ Second submission (same email)
curl -X POST https://yourname.app.n8n.cloud/webhook/lead-capture \
  -H "Content-Type: application/json" \
  -d '{"name":"田中 花子","email":"duplicate@example.com","message":"重複"}'
```

**確認事項 / Check:**
- [ ] 1回目のみスプレッドシートに追加される / Only first submission logged to Sheets
- [ ] ウェルカムメールは1通のみ送信される / Only one welcome email sent

### テスト4 — 日本語名 / Test 4: Japanese Names

```bash
curl -X POST https://yourname.app.n8n.cloud/webhook/lead-capture \
  -H "Content-Type: application/json" \
  -d '{"name":"佐藤 義雄","email":"sato@example.com","message":"ご相談があります"}'
```

**確認事項 / Check:**
- [ ] メールの宛名が正しく表示される (`佐藤 義雄 様`) / Name renders correctly in email
- [ ] 文字化けがない / No character encoding issues

### テスト5 — バーストテスト / Test 5: Burst Test

```bash
for i in {1..10}; do
  curl -X POST https://yourname.app.n8n.cloud/webhook/lead-capture \
    -H "Content-Type: application/json" \
    -d "{\"name\":\"テストユーザー$i\",\"email\":\"test$i@example.com\",\"message\":\"バーストテスト$i\"}" &
done
wait
echo "10件送信完了 / 10 submissions sent"
```

**確認事項 / Check:**
- [ ] 10行すべてスプレッドシートに追加される / All 10 rows appear in Sheets
- [ ] 10通のメールが送信される / All 10 emails sent
- [ ] エラーが発生しない / No errors triggered

---

## 9. エラーログの確認 / Checking Error Logs

### n8n実行ログ / n8n Execution Log

1. n8nの左サイドバー → **「Executions」**
2. 各実行の結果を確認 / Check results of each execution:
   - 🟢 緑 / Green = 成功 / Success
   - 🔴 赤 / Red = エラー / Error

### Googleスプレッドシートのエラーログ / Google Sheets Error Log

`Errors` シートを定期的に確認してください。  
Check the `Errors` sheet regularly.

エラーが発生した場合 / If an error occurs:
1. `Status` 列を `Investigating` に変更 / Change Status to `Investigating`
2. n8nのExecution Logで詳細を確認 / Check n8n Execution Log for details
3. 修正後、`Status` を `Resolved` に変更 / After fixing, change Status to `Resolved`

---

## 10. カスタマイズ方法 / Customization

### 業種別のフィールド追加 / Add Industry-Specific Fields

| 業種 / Business Type | 追加フィールド / Add Field |
|---|---|
| サロン / Salon | 希望日時 / Preferred Date & Time |
| クリニック / Clinic | 症状・ご相談内容 / Symptoms / Consultation |
| 不動産 / Real Estate | ご予算 / Budget Range |
| レストラン / Restaurant | 人数・希望日時 / Party Size + Date |

### 通知先の変更 / Change Notification Destination

n8nのSlackノードを以下に変更可能 / You can replace the Slack node with:

| ツール / Tool | 対応 / Support |
|---|---|
| Chatwork | 〇 n8n Chatworkノードあり / n8n Chatwork node available |
| LINE WORKS | 〇 HTTP Requestノードで対応 / Via HTTP Request node |
| Microsoft Teams | 〇 n8n Teamsノードあり / n8n Teams node available |
| メール / Email | 〇 追加のGmailノードを使用 / Use additional Gmail node |

---

## 11. よくあるご質問 / FAQ

**Q: Webhookが動作しません / The webhook is not working.**  
A: n8nのワークフローが「Active」になっているか確認してください。テスト用URLと本番用URLは異なります。  
Check that the workflow is toggled **Active** in n8n. The test URL and production URL are different.

---

**Q: メールが届きません / Emails are not being received.**  
A: Gmailの認証情報が有効か確認してください。また、迷惑メールフォルダを確認してください。  
Check that Gmail credentials are valid. Also check the spam/junk folder.

---

**Q: スプレッドシートに行が追加されません / Rows are not being added to the sheet.**  
A: スプレッドシートIDが正しいか確認してください。n8nのExecution Logでエラーの詳細を確認できます。  
Verify the Spreadsheet ID is correct. Check the n8n Execution Log for error details.

---

**Q: 日本語が文字化けします / Japanese characters appear garbled.**  
A: メールのContent-TypeがUTF-8になっているか確認してください。  
Ensure the email Content-Type is set to UTF-8.

---

## 12. サポート / Support

ご不明な点がございましたら、以下までお問い合わせください。  
If you have any questions, please contact us at:

- **Email:** markanthonyalatracajr@gmail.com
- **GitHub:** https://github.com/remarkable-btns
- **対応時間 / Support Hours:** 平日9:00〜18:00 JST / Weekdays 9:00–18:00 JST

---

> **注意 / Note:**  
> このシステムはn8n Cloudで動作しています。n8nのプランによって実行回数の上限が異なります。  
> This system runs on n8n Cloud. Execution limits vary depending on your n8n plan.  
> 詳細: [n8n Pricing](https://n8n.io/pricing)

---

*Lead Capture Automation System — Day 01 of 30-Day Automation Portfolio*  
*Built by Mark Alatraca Jr. (rmarkable) · Powered by n8n*
