# 🔧 ClientSense — Setup Guide

Complete step-by-step instructions to import and configure all 3 workflows in your n8n instance.

**Estimated setup time: 30–45 minutes**

---

## Prerequisites

Before starting, make sure you have:

- [ ] An **n8n account** (cloud at n8n.io — starter plan works fine)
- [ ] A **Google account** with API key
- [ ] A **Google account** (for Sheets, Gmail, Calendar)
- [ ] A **Tally.so account** (free tier is enough)

---

## Step 1 — Set Up Your Google Sheet

Create a new Google Sheet named `ClientSense-Sheets`.

Create **two tabs** inside it:

### Tab 1: `ALL Leads`
Add these exact column headers in Row 1 (case-sensitive):

| Column | Header Name |
|--------|-------------|
| A | Full Name |
| B | Email |
| C | Company/Project Name |
| D | Budget |
| E | Timeline |
| F | Project Description |
| G | Score |
| H | Tier |
| I | Reasoning |
| J | Submitted At |
| K | Status |

### Tab 2: `Priority Hot Leads`
Add these exact column headers in Row 1:

| Column | Header Name |
|--------|-------------|
| A | Full Name |
| B | Email |
| C | Company/Project Name |
| D | Budget |
| E | Score |
| F | Reasoning |
| G | Submitted At |

> **Important:** Save the Google Sheet URL. You'll need the Sheet ID (the long string in the URL between `/d/` and `/edit`).

---

## Step 2 — Create Your Tally Form

1. Go to [tally.so](https://tally.so) and create a free account
2. Create a **New Form** with these fields:

| Field Label | Field Type |
|-------------|------------|
| Full Name | Short Text |
| Email | Email |
| Company/Project Name | Short Text |
| Budget | Multiple Choice — options: `Under $500`, `$500–$2000`, `$2000–$10,000`, `$10,000+` |
| Timeline | Multiple Choice — options: `ASAP`, `Within 1 month`, `1–3 months`, `Just exploring` |
| Project Description | Long Text |
| How did you find me? | Short Text |

3. **Don't publish yet** — you'll add the webhook URL in Step 5.

---

## Step 3 — Connect Credentials in n8n

Go to your n8n dashboard → **Credentials** → Add the following:

### Google Sheets + Gmail + Calendar (OAuth2)
1. Click **Add Credential** → search "Google"
2. Select **Google OAuth2 API**
3. Follow the OAuth flow to connect your Google account
4. n8n will ask for scopes — allow: Gmail, Sheets, Calendar
5. Name it: `Google Account`

> **Tip:** In n8n cloud, you can use the built-in Google credential which handles OAuth automatically.

### Gemini API Key
1. Click **Add Credential** → search "Gemini"
2. Paste your API key from **Google AI Studio**
3. Name it: `Gemini Account`

---

## Step 4 — Import the Workflows

Import each workflow one at a time:

1. In n8n, click **New Workflow**
2. Click the **three-dot menu** (top right) → **Import from File**
3. Upload the JSON file
4. Repeat for all 3 workflows

**Import order (recommended):**
1. `ClientSense_Lead_Engine.json`
2. `Daily_Warm_Lead_Digest.json`
3. `Reply_Tracker.json`

---

## Step 5 — Configure Workflow 1: Lead Engine

Open `ClientSense Lead Engine` workflow.

### 5a — Copy the Webhook URL
1. Click the **Webhook** trigger node
2. Copy the **Production URL** shown (looks like: `https://yourinstance.app.n8n.cloud/webhook/abc123`)
3. Go to your Tally form → **Integrations** → **Webhooks** → Paste this URL
4. Publish your Tally form

### 5b — Update the AI Agent System Prompt
Click the **Gemini** node (or AI Agent node). Replace the system prompt with:

```
You are a lead qualification specialist for a freelance AI automation agency.

Given the following lead information, return a JSON object with exactly these fields:
- score: integer from 1 to 10
- tier: exactly one of "hot", "warm", or "cold"
- reasoning: one sentence explaining the score
- personalized_opener: one sentence to open an email to this specific lead, using their name

Scoring criteria:
- Budget $2000+ = strong positive (add 3 points)
- Budget $500–$2000 = neutral
- Budget under $500 = strong negative (subtract 3 points)  
- Clear timeline (ASAP or Within 1 month) = positive (add 1 point)
- "Just exploring" timeline = negative (subtract 1 point)
- Detailed project description = positive (add 1 point)
- Vague or very short description = negative (subtract 1 point)

Tier rules:
- Score 8–10 = "hot"
- Score 5–7 = "warm"
- Score 1–4 = "cold"

IMPORTANT: Return ONLY a valid JSON object. No markdown, no backticks, no extra text.
```

### 5c — Update Google Sheets nodes
For each Google Sheets node in this workflow:
1. Click the node → click the **Credential** field → select your Google Account
2. Click the **Document** field → search for and select `ClientSense-Sheets`
3. Select the correct sheet tab (ALL Leads or Priority Hot Leads)

### 5d — Update Gmail nodes
For each Gmail node:
1. Click the node → select your Gmail credential
2. For the **rejection email**, replace the body with your own polite rejection message
3. For the **hot lead email**, replace `YOUR_CALENDLY_LINK` with your actual booking link

### 5e — Update Google Calendar node (Hot branch)
1. Click the Calendar node → connect your Google credential
2. The event title is pre-set to use the lead's name — you can customize the description

---

## Step 6 — Configure Workflow 2: Daily Digest

Open `Daily Warm Lead Digest` workflow.

1. Click the **Google Sheets** node → connect credential → select `ClientSense-Sheets` → select `ALL Leads` tab
2. Click the **Gmail** node → connect credential → update `sendTo` with **your own email address**
3. The schedule is set to **9:00 AM daily** — change the hour if needed

---

## Step 7 — Configure Workflow 3: Reply Tracker

Open `Reply Tracker` workflow.

1. Click the **Gmail Trigger** node → connect your Gmail credential
2. The poll interval is set to **every 15 minutes** — leave it as is
3. Click both **Google Sheets** nodes → connect credential → select `ClientSense-Sheets` → select `ALL Leads`
4. No other changes needed — the email regex matching is already configured

---

## Step 8 — Test the Full System

Run these tests in order:

### Test 1 — Hot Lead
1. Fill your Tally form with: Budget = `$2000–$10,000`, Timeline = `ASAP`, detailed description
2. Within 30 seconds, check:
   - [ ] Calendly email received in Gmail
   - [ ] Row added to **Priority Hot Leads** sheet with score 8+
   - [ ] Row added to **ALL Leads** sheet with Tier = "hot"
   - [ ] Google Calendar event created for today

### Test 2 — Cold Lead
1. Fill form with: Budget = `Under $500`, Timeline = `Just exploring`, vague description
2. Within 30 seconds, check:
   - [ ] Rejection email received in Gmail
   - [ ] Row added to ALL Leads with Tier = "cold"

### Test 3 — Warm Lead
1. Fill form with: Budget = `$500–$2000`, moderate description
2. Within 30 seconds, check:
   - [ ] Nurture email received
   - [ ] Row in ALL Leads with Tier = "warm", Status = "Pending"

### Test 4 — Daily Digest
1. Manually trigger the Daily Digest workflow (click the play button on the Schedule node)
2. Check your inbox for the digest email
3. It will only show leads older than 48h — so use a test row with an old timestamp to verify

### Test 5 — Reply Tracker
1. Reply to one of the test emails you received
2. Wait up to 15 minutes (or manually trigger the workflow)
3. Check that the Status column updated to "Replied" for that lead

---

## Common Issues & Fixes

**Problem:** Gemini node returns an error about invalid JSON
**Fix:** Add a Code node after Gemini that strips any markdown formatting:
```javascript
const raw = $input.first().json.content[0].text;
const clean = raw.replace(/```json|```/g, '').trim();
return [{ json: JSON.parse(clean) }];
```

**Problem:** Gmail trigger isn't detecting replies
**Fix:** Make sure the Gmail credential has permission to read inbox (not just send). Re-authenticate and check scopes.

**Problem:** Reply Tracker updates the wrong row
**Fix:** Make sure the `row_number` field is being passed correctly. In the Get Rows node, enable "Return Row Numbers" in the options.

**Problem:** Tally webhook not firing
**Fix:** Make sure you're using the **Production** webhook URL in n8n, not the Test URL. Also ensure your Tally form is published (not in draft).

---

## You're Done 🎉

All 3 workflows are live. Your lead pipeline is now fully automated.

For questions or customization requests, reach out via the links in [README.md](./README.md).
