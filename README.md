# 🤖 ClientSense — AI Lead Qualification & Follow-up Engine

> **Automatically score, route, and follow up on every inbound lead — powered by AI. Zero manual work.**

Built on **n8n + Google + Google Workspace**. Three connected workflows that turn a form submission into a fully managed lead pipeline in under 30 seconds.

---

![n8n](https://img.shields.io/badge/n8n-Automation-orange?style=for-the-badge&logo=n8n)
![Gemini](https://img.shields.io/badge/Google-Gemini-4285F4?style=for-the-badge&logo=google)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-CRM-34A853?style=for-the-badge&logo=googlesheets)
![Gmail](https://img.shields.io/badge/Gmail-Email_Automation-EA4335?style=for-the-badge&logo=gmail)
![Google Calendar](https://img.shields.io/badge/Google_Calendar-Scheduling-4285F4?style=for-the-badge&logo=googlecalendar)

---

## 🎬 Live Demo

[![Watch the demo](https://img.shields.io/badge/Watch_Demo-Loom_Video-625DF5?style=for-the-badge)](https://drive.google.com/file/d/1kXrHyOiuyY7ghfzCd3Wcvg_1loATqKH_/view?usp=drive_link)

---

## 🧩 The Problem It Solves

Freelancers and small agencies lose deals for two simple reasons:
- They reply to hot leads too slowly
- They forget to follow up with warm leads who went quiet

**ClientSense fixes both — automatically.**

---

## ⚙️ What It Does

This system is made up of **3 connected workflows:**

### Workflow 1 — ClientSense Lead Engine (Main)
The core intake and routing pipeline.

1. A prospect fills your **Tally inquiry form**
2. A **Webhook** instantly catches the submission in n8n
3. An **Google Gemini** reads the form data and produces:
   - A lead **score from 1–10**
   - A **tier classification** (hot / warm / cold)
   - A **reasoning note** explaining the score
   - A **personalized email opener** for that specific lead
4. A **Switch node** routes the lead into 3 branches:
   - 🟢 **Hot (8–10):** Immediate personalized email with Calendly link + logged to Priority Sheet + Google Calendar reminder for you
   - 🟡 **Warm (5–7):** Nurture email sent + logged to All Leads sheet with status = Pending
   - 🔴 **Cold (1–4):** Polite rejection email sent automatically

---

### Workflow 2 — Daily Warm Lead Digest
Runs every morning at **9:00 AM**.

1. Reads all rows from the All Leads sheet
2. Filters leads where: **Tier = Warm** AND **Submitted > 48 hours ago** AND **Status = Pending**
3. Formats them into a clean HTML email
4. Sends the digest to your inbox — your daily "chase list"

---

### Workflow 3 — Reply Tracker
Watches your Gmail inbox **every 15 minutes**.

1. Detects any incoming reply
2. Extracts the sender's email address
3. Looks up that email in the All Leads sheet
4. Updates the row's **Status to "Replied"**
5. That lead automatically disappears from tomorrow's digest

---

## 📊 System Architecture

```
[Tally Form] 
     ↓ webhook
[n8n: Lead Engine]
     ↓
[Google Gemini: Score + Classify]
     ↓
[Switch Node]
 ├─ HOT  → Gmail (Calendly link) + Sheets (Priority) + Calendar reminder
 ├─ WARM → Gmail (nurture email) + Sheets (All Leads, Status=Pending)
 └─ COLD → Gmail (rejection email)

[Schedule: 9AM Daily]
     ↓
[Google Sheets: Read warm + pending + >48h]
     ↓
[Gmail: Daily digest to owner]

[Gmail Trigger: every 15 min]
     ↓
[Google Sheets: Find lead by email]
     ↓
[Google Sheets: Update Status = Replied]
```

---

## 🛠️ Tech Stack

| Tool | Role |
|------|------|
| n8n (cloud) | Automation engine |
| Tally.so | Inquiry form with webhook |
| Google Gemini | AI lead scoring agent |
| Google Sheets | Lead CRM (All Leads + Priority tabs) |
| Gmail | Outbound emails + reply detection |
| Google Calendar | Hot lead follow-up reminders |

---

## 🚀 Setup Instructions

See **[setup-guide.md](./setup-guide.md)** for the full step-by-step import and configuration guide.

**Quick overview:**
1. Import all 3 workflow JSON files into n8n
2. Connect your Google Sheets, Gmail, and Google Calendar credentials
3. Add your OpenAI API key
4. Create the Tally form and paste the webhook URL
5. Set up your Google Sheet with the correct column headers
6. Activate all 3 workflows

---

## 📁 Repository Structure

```
clientsense-lead-engine/
│
├── workflows/
│   ├── ClientSense_Lead_Engine.json       ← Main intake + routing workflow
│   ├── Daily_Warm_Lead_Digest.json        ← 9AM digest workflow
│   └── Reply_Tracker.json                 ← Gmail reply detection workflow
│
├── assets/
│   └── workflow-screenshot.png            ← Add your n8n canvas screenshot here
│
├── README.md                              ← You are here
└── setup-guide.md                         ← Full setup instructions
```

---

## 💡 Customization Ideas

- Swap Tally for **Typeform** or your website's contact form
- Replace Google Calendar with **Calendly API** for automatic booking
- Add a **Slack notification** to Workflow 1 for hot leads
- Connect to **HubSpot or Go High Level** via HTTP node for CRM sync
- Add a **2nd follow-up email** that auto-sends 72h after warm lead silence

---

## 📬 Hire Me

I build custom n8n automation systems for freelancers, agencies, and small businesses.

- 🔗 **Upwork:** [*Dhanraj Sharma*](https://www.upwork.com/freelancers/~010e4c7ac19e0fdda1?mp_source=share)
- 🔗 **Contra:** [*Dhanraj Sharma*](https://contra.com/dhanraj_sharma_rgam8kpb?referralExperimentNid=DEFAULT_REFERRAL_PROGRAM&referrerUsername=dhanraj_sharma_rgam8kpb)
- 💼 **LinkedIn:** [*Dhanraj Sharma*](https://www.linkedin.com/in/dhanraj-sharma-nddimension/)
- 📧 **Email:** *hinatashoyo101824@gmail.com*

---

## 📄 License

This project is released under a proprietary license.

The repository is provided for portfolio and evaluation purposes only. Commercial use, redistribution, resale, and client deployment are prohibited without explicit written permission from the author.

---

*Built by Dhanraj Sharma — AI Automation Specialist*
