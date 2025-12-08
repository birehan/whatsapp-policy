# 📘 Google Review Automation - Complete Setup Guide

A step-by-step guide to set up and run your Google Review Automation workflow on **n8n Cloud Platform**.

---

## 📋 Table of Contents

1. [Overview](#-overview)
2. [Prerequisites](#-prerequisites)
3. [Step 1: Sign Up for n8n Cloud](#-step-1-sign-up-for-n8n-cloud)
4. [Step 2: Create Google Sheet](#-step-2-create-google-sheet)
5. [Step 3: Set Up Google Sheets Credentials](#-step-3-set-up-google-sheets-credentials)
6. [Step 4: Set Up Email (SMTP) Credentials](#-step-4-set-up-email-smtp-credentials)
7. [Step 5: Set Up WhatsApp Business API](#-step-5-set-up-whatsapp-business-api)
8. [Step 6: Get Your Google Review Link](#-step-6-get-your-google-review-link)
9. [Step 7: Import the Workflow](#-step-7-import-the-workflow)
10. [Step 8: Configure Workflow Variables](#-step-8-configure-workflow-variables)
11. [Step 9: Connect Credentials to Nodes](#-step-9-connect-credentials-to-nodes)
12. [Step 10: Get Your Webhook URL](#-step-10-get-your-webhook-url)
13. [Step 11: Test the Workflow](#-step-11-test-the-workflow)
14. [Step 12: Activate and Go Live](#-step-12-activate-and-go-live)
15. [Troubleshooting](#-troubleshooting)
16. [FAQ](#-faq)

---

## 🎯 Overview

### What This Automation Does

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         AUTOMATION FLOW                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1️⃣ NEW CUSTOMER                                                            │
│     └─► Google Sheet new row triggers workflow                              │
│                                                                             │
│  2️⃣ SEND RATING REQUEST                                                     │
│     └─► WhatsApp (if phone) or Email (if no phone)                         │
│     └─► Customer clicks 1-5 rating                                          │
│                                                                             │
│  3️⃣ PROCESS RATING                                                          │
│     ├─► Rating 4-5: Send Google Review link ⭐                              │
│     └─► Rating 1-3: Send apology + Notify owner 😔                         │
│                                                                             │
│  4️⃣ 24H REMINDER                                                            │
│     └─► Auto-remind customers who haven't responded                         │
│                                                                             │
│  5️⃣ LOG EVERYTHING                                                          │
│     └─► All actions logged to Google Sheet                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Credentials Needed

| Credential | Purpose | Required |
|------------|---------|----------|
| Google Sheets OAuth2 | Read/write customer data | ✅ Yes |
| SMTP Email | Send emails | ✅ Yes |
| WhatsApp Business API | Send WhatsApp messages | ⚠️ Optional |

---

## ✅ Prerequisites

Before starting, make sure you have:

- [ ] A computer with internet access
- [ ] A Google account
- [ ] An email account (Gmail recommended)
- [ ] A Meta Business account (for WhatsApp - optional)
- [ ] Your business Google Review link

---

## 🔧 Step 1: Sign Up for n8n Cloud

📺 **Video Tutorial:** [Getting Started with n8n Cloud](https://www.youtube.com/watch?v=1MwSoB0gnM4)

### 1.1 Create n8n Account

1. Go to [n8n.io](https://n8n.io/)
2. Click **Get Started Free** or **Sign Up**
3. Choose sign up method:
   - Email and password
   - Google account
   - GitHub account
4. Verify your email if required

### 1.2 Access Your n8n Instance

1. After signing up, you'll get your n8n cloud URL:
   ```
   https://your-name.app.n8n.cloud
   ```
2. Bookmark this URL for easy access
3. This is also your **webhook base URL** (important for later!)

### 1.3 Explore the Interface

```
┌─────────────────────────────────────────────────────────────────┐
│  n8n Cloud Interface                                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐                                                   │
│  │ Workflows│  ← Your automation workflows                      │
│  ├──────────┤                                                   │
│  │Credentials│ ← API keys and login info                        │
│  ├──────────┤                                                   │
│  │Executions│  ← History of workflow runs                       │
│  ├──────────┤                                                   │
│  │ Settings │  ← Account settings                               │
│  └──────────┘                                                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 1.4 Note Your Webhook URL

Your webhook URL format is:
```
https://your-name.app.n8n.cloud/webhook/
```

📝 **Save this URL** - you'll need it in Step 10!

---

## 📊 Step 2: Create Google Sheet

📺 **Video Tutorial:** [Google Sheets Basics](https://www.youtube.com/watch?v=FIkZ1sPmKNw)

### 2.1 Create New Sheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Click **+ Blank** to create new spreadsheet
3. Name it: `Customer Reviews - [Your Business Name]`

### 2.2 Set Up Columns

Create these columns in **Row 1**:

| Column | Header Name |
|--------|-------------|
| A | Name |
| B | Phone |
| C | Email |
| D | Visit Date |
| E | Status |
| F | Rating |
| G | First Message Sent |
| H | Rating Received |
| I | Review Link Sent |
| J | Owner Notified |
| K | Reminder Count |
| L | Last Reminder Sent |
| M | Last Updated |
| N | row_number |

### 2.3 Add Sample Data (Row 2)

| Column | Value |
|--------|-------|
| Name | John Doe |
| Phone | +1234567890 |
| Email | john@example.com |
| Visit Date | 2024-12-08 |
| Status | New |
| row_number | 2 |

### 2.4 Get Sheet ID

Your Google Sheet URL looks like:
```
https://docs.google.com/spreadsheets/d/1aBcDeFgHiJkLmNoPqRsTuVwXyZ/edit
                                      └──────────────────────────┘
                                           This is your SHEET ID
```

📝 **Save this ID** - you'll need it later!

---

## 🔐 Step 3: Set Up Google Sheets Credentials

📺 **Video Tutorial:** [n8n Google Sheets OAuth Setup](https://www.youtube.com/watch?v=RHhaDb4LYOI)

### 3.1 Create Google Cloud Project

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Click **Select a project** → **New Project**
3. Name: `n8n-review-automation`
4. Click **Create**

### 3.2 Enable Google Sheets API

1. Go to **APIs & Services** → **Library**
2. Search for `Google Sheets API`
3. Click **Enable**
4. Also enable `Google Drive API`

### 3.3 Create OAuth Credentials

1. Go to **APIs & Services** → **Credentials**
2. Click **+ CREATE CREDENTIALS** → **OAuth client ID**
3. If prompted, configure OAuth consent screen:
   - User Type: **External**
   - App name: `n8n Review Automation`
   - User support email: Your email
   - Developer contact: Your email
   - Click **Save and Continue** through all steps
4. Back to Credentials → **+ CREATE CREDENTIALS** → **OAuth client ID**
5. Application type: **Web application**
6. Name: `n8n`
7. Authorized redirect URIs: 
   ```
   https://your-name.app.n8n.cloud/rest/oauth2-credential/callback
   ```
   > ⚠️ Replace `your-name` with your actual n8n cloud subdomain!
8. Click **Create**
9. **Copy Client ID and Client Secret**

### 3.4 Add Credentials to n8n

1. In n8n, go to **Settings** → **Credentials**
2. Click **+ Add Credential**
3. Search for `Google Sheets API`
4. Select **OAuth2**
5. Paste:
   - Client ID
   - Client Secret
6. Click **Sign in with Google**
7. Authorize access
8. Click **Save**

---

## 📧 Step 4: Set Up Email (SMTP) Credentials

📺 **Video Tutorial:** [n8n Email SMTP Setup](https://www.youtube.com/watch?v=6FWLJ7aKRlE)

### Option A: Gmail SMTP (Recommended)

**1. Enable 2-Factor Authentication:**
- Go to [Google Account Security](https://myaccount.google.com/security)
- Enable **2-Step Verification**

**2. Create App Password:**
- Go to [App Passwords](https://myaccount.google.com/apppasswords)
- Select app: **Mail**
- Select device: **Other** → Name it `n8n`
- Click **Generate**
- **Copy the 16-character password**

**3. Add to n8n:**
1. Go to **Settings** → **Credentials**
2. Click **+ Add Credential**
3. Search for `SMTP`
4. Fill in:

| Field | Value |
|-------|-------|
| User | your-email@gmail.com |
| Password | (16-char app password) |
| Host | smtp.gmail.com |
| Port | 465 |
| SSL/TLS | ✅ Enabled |

5. Click **Save**

### Option B: Other Email Providers

| Provider | Host | Port |
|----------|------|------|
| Outlook | smtp.office365.com | 587 |
| Yahoo | smtp.mail.yahoo.com | 465 |
| SendGrid | smtp.sendgrid.net | 587 |

---

## 📱 Step 5: Set Up WhatsApp Business API

📺 **Video Tutorial:** [WhatsApp Business API Setup for n8n](https://www.youtube.com/watch?v=2lOochQFz2o)

> ⚠️ **Note:** WhatsApp setup is more complex and requires a Meta Business account. Skip this if you only want email functionality.

### 5.1 Create Meta Business Account

1. Go to [Meta Business Suite](https://business.facebook.com/)
2. Create a business account if you don't have one

### 5.2 Set Up WhatsApp Business

1. Go to [Meta for Developers](https://developers.facebook.com/)
2. Click **My Apps** → **Create App**
3. Select **Business** → **Next**
4. App name: `Review Automation`
5. Click **Create App**
6. Find **WhatsApp** and click **Set Up**
7. Add a phone number or use test number

### 5.3 Get API Credentials

1. In your app, go to **WhatsApp** → **API Setup**
2. Copy:
   - **Phone Number ID**
   - **WhatsApp Business Account ID**
3. Generate a **Permanent Access Token**:
   - Go to **Business Settings** → **System Users**
   - Create system user → Generate token with `whatsapp_business_messaging` permission

### 5.4 Add to n8n

1. Go to **Settings** → **Credentials**
2. Click **+ Add Credential**
3. Search for `WhatsApp Business Cloud API`
4. Fill in:

| Field | Value |
|-------|-------|
| Access Token | (your permanent token) |
| Business Account ID | (from step 5.3) |

5. Click **Save**

### 5.5 Set Up Webhook (For Receiving Replies)

1. In Meta Developer Dashboard → **WhatsApp** → **Configuration**
2. Webhook URL: `https://your-name.app.n8n.cloud/webhook/whatsapp-incoming`
3. Verify Token: Create a secret token (e.g., `my_secret_token_123`)
4. Subscribe to: **messages**

> 💡 Replace `your-name` with your actual n8n cloud subdomain!

---

## ⭐ Step 6: Get Your Google Review Link

📺 **Video Tutorial:** [How to Get Google Review Link](https://www.youtube.com/watch?v=xjnD4aQSj8w)

### Method 1: From Google Search

1. Google your business name
2. Click your business listing
3. Click **Write a Review**
4. Copy the URL from browser

### Method 2: From Google Business Profile

1. Go to [Google Business Profile](https://business.google.com/)
2. Select your business
3. Click **Get more reviews**
4. Copy the short link

### Method 3: Create Short Link

Your link format:
```
https://search.google.com/local/writereview?placeid=YOUR_PLACE_ID
```

To find Place ID:
1. Go to [Place ID Finder](https://developers.google.com/maps/documentation/places/web-service/place-id)
2. Search your business
3. Copy the Place ID

---

## 📥 Step 7: Import the Workflow

### 7.1 Download Workflow File

Save the `Google_Review_Automation.json` file to your computer.

### 7.2 Import to n8n

1. Open n8n
2. Click **+ Add Workflow** (or go to Workflows)
3. Click the **⋮** menu → **Import from File**
4. Select your JSON file
5. Click **Import**

📺 **Video Tutorial:** [How to Import Workflows in n8n](https://www.youtube.com/watch?v=TDq96K3TTQE)

---

## ⚙️ Step 8: Configure Workflow Variables

### 8.1 Open Variable Settings

1. In your workflow, click **Settings** (gear icon)
2. Go to **Variables** tab

### 8.2 Add These Variables

Click **+ Add Variable** for each:

| Variable Name | Description | Example Value |
|---------------|-------------|---------------|
| `GOOGLE_SHEET_ID` | Your Google Sheet ID | `1aBcDeFgHiJkLmNoPqRsTuVwXyZ` |
| `SHEET_NAME` | Sheet tab name | `Sheet1` |
| `BUSINESS_NAME` | Your business name | `Joe's Cafe` |
| `GOOGLE_REVIEW_LINK` | Your Google review URL | `https://g.page/r/abc123/review` |
| `FROM_EMAIL` | Sender email | `reviews@yourbusiness.com` |
| `OWNER_EMAIL` | Owner notification email | `owner@yourbusiness.com` |
| `OWNER_PHONE` | Owner WhatsApp number | `+1234567890` |
| `WHATSAPP_PHONE_ID` | WhatsApp Phone ID | `123456789012345` |
| `WEBHOOK_BASE_URL` | Your n8n webhook URL | `https://your-name.app.n8n.cloud/webhook` |
| `THANK_YOU_PAGE_URL` | Redirect after rating | `https://yourbusiness.com/thanks` |

### 8.3 Save Variables

Click **Save** to save all variables.

---

## 🔗 Step 9: Connect Credentials to Nodes

### 9.1 Google Sheets Nodes

1. Double-click each Google Sheets node:
   - `Google Sheets Trigger`
   - `Log Rating to Sheet`
   - `Get Satisfied Customer`
   - `Get Unsatisfied Customer`
   - `Log Review Link Sent`
   - `Log Issue Flagged`
   - `Get Pending Responses`
   - `Log Reminder Sent`

2. Click **Credential** dropdown → Select your Google Sheets credential

### 9.2 Email Nodes

1. Double-click each email node:
   - `Send email`
   - `Send Review Link Email`
   - `Send Sorry Message`
   - `Notify Owner Email`
   - `Send Reminder Email`

2. Click **Credential** dropdown → Select your SMTP credential

### 9.3 WhatsApp Nodes (If using)

1. Double-click each WhatsApp node:
   - `Send message`
   - `Notify Owner WhatsApp`

2. Click **Credential** dropdown → Select your WhatsApp credential

---

## 🌐 Step 10: Get Your Webhook URL

### n8n Cloud Webhook URL

On n8n Cloud, your webhooks are already publicly accessible! No ngrok needed.

📺 **Video Tutorial:** [n8n Webhooks Explained](https://www.youtube.com/watch?v=PXrI0lFg5Ks)

### 10.1 Find Your Webhook URL

1. In your workflow, click on the **Rating Webhook** node
2. Look at the **Webhook URLs** section at the top
3. You'll see two URLs:
   - **Test URL:** `https://your-name.app.n8n.cloud/webhook-test/rating`
   - **Production URL:** `https://your-name.app.n8n.cloud/webhook/rating`

```
┌─────────────────────────────────────────────────────────────────┐
│  Webhook Node                                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Webhook URLs                                                   │
│  ─────────────                                                  │
│  Test URL (for testing):                                        │
│  https://your-name.app.n8n.cloud/webhook-test/rating           │
│                                                                 │
│  Production URL (when active):                                  │
│  https://your-name.app.n8n.cloud/webhook/rating                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 10.2 Update WEBHOOK_BASE_URL Variable

1. Go to **Settings** (gear icon) → **Variables**
2. Set `WEBHOOK_BASE_URL` to:
   ```
   https://your-name.app.n8n.cloud/webhook
   ```
   > ⚠️ Do NOT include `/rating` at the end - just the base URL!

3. Click **Save**

### 10.3 Test vs Production URLs

| Mode | URL | When to Use |
|------|-----|-------------|
| Test | `webhook-test/rating` | While building/testing workflow |
| Production | `webhook/rating` | After activating workflow |

> 💡 **Important:** Production webhooks only work when the workflow is **Active**!

---

## 🧪 Step 11: Test the Workflow

### 11.1 Test Google Sheets Trigger

1. Open your Google Sheet
2. Add a new row:

| Name | Phone | Email | Visit Date | Status | row_number |
|------|-------|-------|------------|--------|------------|
| Test User | +1234567890 | test@email.com | 2024-12-08 | New | 3 |

3. In n8n, click **Execute Workflow** (or wait 1 minute for trigger)
4. Check if message was sent

### 11.2 Test Rating Response

1. Check your email/WhatsApp for the rating message
2. Click a rating (4 or 5 for happy path, 1-3 for sad path)
3. Verify:
   - Google Sheet updated
   - Follow-up message sent
   - Owner notified (if low rating)

### 11.3 Test Execution Logs

1. In n8n, click **Executions** (left sidebar)
2. Review each execution for errors
3. Click on any execution to see details

---

## 🚀 Step 12: Activate and Go Live

### 12.1 Final Checklist

- [ ] All credentials connected
- [ ] All variables configured
- [ ] Test execution successful
- [ ] Webhook URL is accessible
- [ ] Google Sheet has correct columns

### 12.2 Activate Workflow

1. Click the **Active** toggle (top right)
2. Confirm activation

### 12.3 Monitor

1. Check **Executions** regularly
2. Set up error notifications:
   - Settings → **Error Workflow** → Create notification workflow

---

## 🔧 Troubleshooting

### Common Issues

| Problem | Solution |
|---------|----------|
| Google Sheets not triggering | Check if workflow is Active; Verify credentials |
| Emails not sending | Check SMTP credentials; Verify app password |
| WhatsApp errors | Verify access token; Check phone number format |
| Webhook not receiving | Use Production URL (not test); Ensure workflow is Active |
| Variables not working | Ensure variables are saved; Use correct syntax `$vars.NAME` |

### Error: "Trigger not firing"

1. Check workflow is **Active** (green toggle in top right)
2. Verify Google Sheets credential is connected
3. Make sure the sheet has new rows with Status = "New"
4. Check **Executions** tab for any error messages

### Error: "Authentication failed"

1. Re-authorize Google Sheets credential:
   - Go to **Credentials** → Click your Google credential
   - Click **Reconnect** or create new credential
2. Generate new Gmail app password
3. Check WhatsApp token hasn't expired

### Error: "Webhook not receiving data"

1. Make sure workflow is **Active** (production webhooks only work when active)
2. Verify you're using the correct URL:
   - Testing: `https://your-name.app.n8n.cloud/webhook-test/rating`
   - Production: `https://your-name.app.n8n.cloud/webhook/rating`
3. Check `WEBHOOK_BASE_URL` variable is correct
4. Test webhook manually by visiting:
   ```
   https://your-name.app.n8n.cloud/webhook/rating?id=1&rating=5
   ```

### Error: "Execution failed"

1. Go to **Executions** in left sidebar
2. Click on the failed execution
3. Look at which node failed (highlighted in red)
4. Click the node to see the error details
5. Common fixes:
   - Missing credentials → Connect the credential
   - Invalid data → Check input data format
   - API error → Check external service status

---

## ❓ FAQ

### Q: Can I use this without WhatsApp?

**A:** Yes! The workflow checks if a phone number exists. If not, it sends email instead. You can disable WhatsApp nodes entirely.

### Q: How do I add more clients?

**A:** 
1. Duplicate the workflow
2. Create new Google Sheet
3. Update variables for new client
4. Connect credentials

### Q: Can I customize the email templates?

**A:** Yes! Double-click any email node and edit the HTML in the message body.

### Q: How do I change the rating threshold?

**A:** Edit the "Rating 4 or Higher" IF node and change the comparison value.

### Q: Is my data secure?

**A:** Yes, data stays in your Google Sheet and n8n instance. Use HTTPS for production.

---

## 📚 Additional Resources

### Video Tutorials

| Topic | Link |
|-------|------|
| n8n Basics | [n8n Beginner Course](https://www.youtube.com/watch?v=1MwSoB0gnM4) |
| Google Sheets Integration | [n8n + Google Sheets](https://www.youtube.com/watch?v=RHhaDb4LYOI) |
| Email Automation | [Send Emails with n8n](https://www.youtube.com/watch?v=6FWLJ7aKRlE) |
| WhatsApp Integration | [n8n + WhatsApp](https://www.youtube.com/watch?v=2lOochQFz2o) |
| Webhooks Explained | [n8n Webhooks](https://www.youtube.com/watch?v=PXrI0lFg5Ks) |

### Documentation

- [n8n Official Docs](https://docs.n8n.io/)
- [Google Sheets API Docs](https://developers.google.com/sheets/api)
- [WhatsApp Business API Docs](https://developers.facebook.com/docs/whatsapp/cloud-api)

---

## 📞 Support

If you need help:

1. Check the [n8n Community Forum](https://community.n8n.io/)
2. Review [n8n Documentation](https://docs.n8n.io/)
3. Search for similar issues on GitHub

---

**Happy Automating! 🎉**

*Last Updated: December 2024*
