# 🌿 AtmoSense — Intelligent Air Quality Health Agent

> **Automated Daily Health & AQI Report System** powered by n8n · OpenWeatherMap · GPT-4.1 · Gmail · WhatsApp · Telegram
## 📌 Overview

**AtmoSense** is an intelligent air quality health agent that triggers every day at **7AM IST**, fetches live weather and AQI data from OpenWeatherMap, evaluates pollution risk levels, and uses **GPT-4.1** to generate personalized health tips — delivering formatted reports via **Gmail**, logging to **Google Sheets**, and sending alerts via **WhatsApp Cloud API** and **Telegram Bot** — fully automated, zero manual effort.

---

## ✨ Features

- ⏰ **Scheduled Daily Trigger** — Runs automatically at 7AM IST every day
- 🌦️ **Live Weather & AQI Fetching** — Real-time data from OpenWeatherMap APIs
- 🧠 **AI-Powered Health Tips** — GPT-4.1 generates 5 India-specific health recommendations
- ⚠️ **Smart Risk Detection** — Flags AQI ≥ 4 as High Risk, routes to appropriate advisory
- 📧 **HTML Email Reports** — Beautiful formatted report sent to Gmail
- 📊 **Google Sheets Logging** — Every reading appended for longitudinal tracking
- 📱 **WhatsApp Alerts** — Instant notifications via WhatsApp Cloud API
- 🤖 **Telegram Bot Alerts** — Parallel delivery via Telegram

---

## 🔁 Workflow Architecture

```
7AM Daily Trigger
       │
       ▼
┌─────────────────┐     ┌─────────────────┐
│  Fetch Weather  │     │   Fetch AQI     │
│ OpenWeatherMap  │     │ OpenWeatherMap  │
└────────┬────────┘     └────────┬────────┘
         │                       │
         └──────────┬────────────┘
                    ▼
           ┌────────────────┐
           │ Merge & Analyze│
           │  (JSON merge)  │
           └───────┬────────┘
                   ▼
          ┌─────────────────┐
          │  AQI Risk Check │
          │   AQI ≥ 4 ?     │
          └──┬──────────┬───┘
             │ true     │ false
             ▼          ▼
     ┌──────────┐  ┌──────────────┐
     │ High AQI │  │  Normal AQI  │
     │   Tips   │  │    Tips      │
     │ (GPT-4.1)│  │  (GPT-4.1)  │
     └─────┬────┘  └──────┬───────┘
           └──────┬────────┘
                  ▼
         ┌────────────────┐
         │  Build Report  │
         │  (HTML + Text) │
         └───────┬────────┘
                 │
    ┌────────────┼────────────┐
    ▼            ▼            ▼
┌────────┐ ┌─────────┐ ┌──────────┐
│ Gmail  │ │WhatsApp │ │ Telegram │
│ Report │ │  Alert  │ │   Bot    │
└────────┘ └────┬────┘ └──────────┘
                │
           ┌────▼─────┐
           │  Google  │
           │  Sheets  │
           │  Logger  │
           └──────────┘
```

---

## 🧩 Node Breakdown

| Node | Type | Purpose |
|------|------|---------|
| **7AM Daily Trigger** | Schedule Trigger | Fires every day at 7:00 AM IST |
| **Fetch Weather** | HTTP Request | GET current weather from OpenWeatherMap |
| **Fetch AQI** | HTTP Request | GET air pollution index from OpenWeatherMap |
| **Merge & Analyze** | Code / Merge | Combines weather + AQI, calculates heat index & timestamp |
| **AQI Risk Check** | IF Node | Routes flow based on AQI ≥ 4 threshold |
| **High AQI Tips** | OpenAI GPT-4.1 | Generates urgent health tips for high pollution |
| **Normal AQI Tips** | OpenAI GPT-4.1 | Generates standard wellness tips |
| **Build Report** | Code Node | Constructs HTML email + plain-text message |
| **Gmail Trigger** | Gmail | Sends formatted HTML health report |
| **Log to Sheets** | Google Sheets | Appends row with all readings |
| **WhatsApp Trigger** | HTTP Request | Posts alert via Facebook Graph API |
| **Telegram Bot** | Telegram | Sends message via Telegram Bot API |

---

## 🛠️ Tech Stack

| Technology | Role |
|-----------|------|
| **n8n** | Workflow automation engine |
| **OpenWeatherMap API** | Weather + Air Quality data source |
| **OpenAI GPT-4.1** | AI health tip generation |
| **Gmail API** | HTML email delivery |
| **Google Sheets API** | Data logging & history |
| **WhatsApp Cloud API** | Mobile push alerts |
| **Telegram Bot API** | Secondary alert channel |

---

## ⚙️ Setup & Configuration

### Prerequisites
- n8n instance (cloud or self-hosted)
- OpenWeatherMap API key (free tier works)
- OpenAI API key
- Google account (Gmail + Sheets)
- WhatsApp Business API access
- Telegram Bot token

### Environment Variables / Credentials Needed

```env
OPENWEATHERMAP_API_KEY=your_key_here
OPENAI_API_KEY=your_key_here
GMAIL_OAUTH_CLIENT_ID=your_client_id
GMAIL_OAUTH_CLIENT_SECRET=your_client_secret
WHATSAPP_ACCESS_TOKEN=your_token
WHATSAPP_PHONE_NUMBER_ID=your_phone_id
TELEGRAM_BOT_TOKEN=your_bot_token
```

### Installation

1. **Clone this repository**
   ```bash
   git clone https://github.com/yourusername/atmosense.git
   cd atmosense
   ```

2. **Import workflow into n8n**
   - Open your n8n instance
   - Go to **Workflows → Import from File**
   - Select `atmosense-workflow.json`

3. **Configure credentials**
   - Add all required API credentials in n8n's credential manager

4. **Update location**
   - In `Fetch Weather` and `Fetch AQI` nodes, update the coordinates/city to your location

5. **Activate the workflow**
   - Toggle the workflow to **Active**
   - It will trigger automatically at 7AM IST

---

## 📊 AQI Risk Scale Reference

| AQI Level | Category | Action |
|-----------|----------|--------|
| 1 | Good | ✅ Normal tips generated |
| 2 | Fair | ✅ Normal tips generated |
| 3 | Moderate | ✅ Normal tips generated |
| **4** | **Poor** | ⚠️ **High risk tips triggered** |
| **5** | **Very Poor** | 🔴 **High risk tips triggered** |

---

## 📬 Sample Output

**Gmail Report includes:**
- Current temperature & humidity
- AQI value with risk level badge
- 5 AI-generated health tips
- India timezone timestamp
- Styled HTML template

**WhatsApp / Telegram message includes:**
- Plain-text summary
- AQI status
- Top 3 health tips

---

## 🗂️ Repository Structure

```
atmosense/
├── README.md                    # This file
├── atmosense-workflow.json      # n8n workflow export
├── screenshots/
│   └── workflow-diagram.png     # Visual flow diagram
└── docs/
    └── setup-guide.md           # Detailed setup instructions
```

---

## 🙋 Author

**Arpan Chakraborty**
- 🌐 n8n Cloud: [wolverine123.app.n8n.cloud](https://wolverine123.app.n8n.cloud)
- 📧 arpan.chakraborty+1@codeclouds.in
- 🔗 n8n Community Template: *[Under Review — link coming soon]*

---

## 📄 License

This project is licensed under the **MIT License** — feel free to use, modify, and distribute.

---

