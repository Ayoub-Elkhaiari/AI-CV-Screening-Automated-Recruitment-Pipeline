# 🤖 AI CV Screening: Automated Recruitment Pipeline

> An intelligent, fully automated recruitment workflow built with **n8n**, **OpenRouter LLMs**, and **Google Sheets** screens job applications, analyzes CVs, and sends personalized responses without human intervention.

[![n8n](https://img.shields.io/badge/n8n-Self--Hosted-EA4B71?logo=n8n&logoColor=white)](https://n8n.io/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![OpenRouter](https://img.shields.io/badge/OpenRouter-Free%20LLMs-6366F1?logoColor=white)](https://openrouter.ai/)
[![LLaMA](https://img.shields.io/badge/LLaMA-3.3%2070B-FF6B6B?logo=meta&logoColor=white)](https://openrouter.ai/models)
[![GPT OSS](https://img.shields.io/badge/GPT--OSS-Open%20Source-74AA9C?logo=openai&logoColor=white)](https://openrouter.ai/models)
[![JavaScript](https://img.shields.io/badge/JavaScript-Logic%20Nodes-F7DF1E?logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![Gmail](https://img.shields.io/badge/Gmail-IMAP%20%2F%20SMTP-EA4335?logo=gmail&logoColor=white)](https://mail.google.com/)
[![Google Sheets](https://img.shields.io/badge/Google%20Sheets-Dashboard-34A853?logo=googlesheets&logoColor=white)](https://sheets.google.com/)
[![PDF](https://img.shields.io/badge/PDF-CV%20Parsing-EC1C24?logo=adobeacrobatreader&logoColor=white)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/Status-Complete-brightgreen)]()

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Architecture](#-architecture)
- [Workflow Breakdown](#-workflow-breakdown)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Setup & Installation](#-setup--installation)
- [Configuration](#-configuration)
- [Usage](#-usage)
- [Project Structure](#-project-structure)
- [Author](#-author)

---

## 🎯 Overview

This project automates the entire first-stage recruitment process for an **AI Automation Engineer** position. When a candidate sends an email with their CV attached, the system:

1. Detects and validates it as a genuine job application (two-phase validation)
2. Extracts and reads the CV content
3. Uses an LLM to score the candidate against the job description
4. Sends a **personalized, human-sounding** acceptance or rejection email
5. Logs everything to a Google Sheets dashboard

**Zero human intervention required.** Every email feels personal candidates never know it's automated.

---

## ✨ Features

| Feature | Description |
|---|---|
| 📧 **Smart Email Detection** | Two-phase filter: keyword matching + LLM validation |
| 📄 **PDF CV Parsing** | Automatically extracts text from attached CVs |
| 🧠 **AI Scoring** | LLM analyzes candidate vs job description, returns structured JSON score |
| ✉️ **Personalized Responses** | LLM generates unique, human-sounding emails every time |
| 📊 **Google Sheets Logging** | Automatic dashboard: timestamp, email, score, decision, reason |
| 🔒 **Secure Credentials** | All API keys stored in n8n encrypted credential store |
| 🐳 **Dockerized** | One command to deploy, runs 24/7 |
| 💸 **100% Free** | Uses free-tier LLMs via OpenRouter, no paid APIs required |

---

## 🏗️ Architecture

<img width="1588" height="559" alt="Screenshot 2026-05-02 222325" src="https://github.com/user-attachments/assets/596ad887-2c25-47e0-a224-a135a8250ba6" />

<img width="429" height="581" alt="architecture" src="https://github.com/user-attachments/assets/52563259-de95-4dc6-944e-c0df44d8144a" />

---

## 🔍 Workflow Breakdown

### Phase 1: Keyword Filter (JavaScript)
Checks email subject + body for recruitment-related keywords. Also verifies a PDF attachment is present. Emails without CVs are immediately discarded.

**Keywords:** `candidature`, `cv`, `application`, `postuler`, `engineer`, `automation`, `poste`

### Phase 2: LLM Validation (OpenRouter)
Sends the email content to a free LLM to confirm it's a genuine job application not spam, not a newsletter, not an unrelated email. Returns structured JSON with confidence score.

### PDF Extraction
Uses n8n's native `Extract from File` node to pull raw text from the attached CV PDF.

### LLM Scoring
The heart of the system. Sends the full job description + candidate CV to the LLM. Returns:
```json
{
  "email_address": "candidate@email.com",
  "score": 85,
  "matched_skills": ["JavaScript", "Docker", "REST APIs"],
  "missing_skills": ["n8n"],
  "recommendation": "accept",
  "reason": "Strong technical alignment, missing only n8n experience"
}
```

### Personalized Email Generation
A second LLM call generates a unique, human-sounding email in the candidate's language. Every email is different — no templates, no copy-paste feel.

### Google Sheets Logging
Every processed application is logged with:

| Timestamp | Email | Subject | Score | Decision | Reason |
|---|---|---|---|---|---|
| 2026-04-28T23:10:43Z | candidate@gmail.com | Candidature AI Engineer | 85 | accept | Strong technical fit |
this can help the ATS system in the future.
---

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| **Workflow Engine** | n8n (self-hosted) |
| **Containerization** | Docker + Docker Compose |
| **Email Trigger** | IMAP (Gmail) |
| **Email Sending** | SMTP (Gmail) |
| **LLM Provider** | OpenRouter (free tier) |
| **LLM Models** | `meta-llama/llama-3.3-70b-instruct:free` and `gpt-oss` |
| **PDF Parsing** | n8n Extract from File |
| **Data Storage** | Google Sheets |
| **Scripting** | JavaScript (n8n Code nodes) |

---

## 📦 Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed
- Gmail account (for IMAP + SMTP)
- [OpenRouter](https://openrouter.ai) account (free)
- [Google Cloud Console](https://console.cloud.google.com) project with Sheets API enabled
- Google Sheets file with headers: `Timestamp | Email | Subject | Score | Decision | Reason`

---

## 🚀 Setup & Installation

### 1. Clone the repository

```bash
git clone https://github.com/Ayoub-Elkhaiari/AI-CV-Screening-Automated-Recruitment-Pipeline.git
cd AI-CV-Screening-Automated-Recruitment-Pipeline
```

### 2. Start n8n with Docker

```bash
docker compose up -d
```

n8n will be available at **http://localhost:5678**

### 3. Import the workflow

1. Open n8n → click **"+"** → **"Import from file"**
2. Select `workflow/AI_CV.json`
3. Click **"Import"**

### 4. Configure credentials

Set up the following credentials in n8n (**Settings → Credentials**):

| Credential | Type | Details |
|---|---|---|
| IMAP | Email (IMAP) | Gmail + App Password |
| SMTP | Email (SMTP) | Gmail + App Password |
| OpenRouter | Header Auth | `Bearer sk-or-xxxx` |
| Google Sheets | OAuth2 | Google Cloud OAuth credentials |

### 5. Activate the workflow

Click **"Publish"** in the top right → the workflow runs 24/7 automatically.

---

## ⚙️ Configuration

### Gmail App Password

1. Go to [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)
2. Create a new app password for "Mail"
3. Use this 16-character password for both IMAP and SMTP credentials

### OpenRouter API Key

1. Sign up at [openrouter.ai](https://openrouter.ai)
2. Go to **API Keys** → create a new key
3. In n8n credential: Name = `Authorization`, Value = `Bearer YOUR_KEY`

### Google Sheets OAuth

1. Create a project on [Google Cloud Console](https://console.cloud.google.com)
2. Enable **Google Sheets API** and **Google Drive API**
3. Create OAuth 2.0 credentials (Web Application)
4. Add redirect URI: `http://localhost:5678/rest/oauth2-credential/callback`
5. Add your Gmail as a test user in OAuth consent screen

### Customize the Job Description

In the **Expert** node (Basic LLM Chain), update the job description to match your open position:

```
Position: YOUR POSITION
Required skills: skill1, skill2, skill3
Location: YOUR CITY
```

---

## 📖 Usage

Once activated, the workflow runs automatically:

1. **Send a test email** to your Gmail with a CV PDF attached
2. Subject should contain recruitment keywords (e.g., "Candidature AI Engineer")
3. Wait ~1 minute for the IMAP poll
4. The candidate receives a personalized email response
5. Check your Google Sheets dashboard for the logged entry

### Scoring Threshold

- Score **≥ 70** → Acceptance email sent
- Score **< 70** → Polite rejection email sent

Adjust the threshold in the **IF1** node.

---

## 📁 Project Structure

```
ai-cv-screening-n8n/
├── workflow/
│   └── AI_CV.json          # n8n workflow export
├── docker-compose.yml       # Docker configuration
├── .env.example             # Environment variables template
└── README.md                # This file
```

---

## 👤 Author

**Ayoub EL KHAIARI**


- 💼 [LinkedIn](https://linkedin.com/in/ayoub-elkhaiari-2001c17)
- 🐙 [GitHub](https://github.com/Ayoub-Elkhaiari)

---

## 📄 License

MIT License feel free to use, modify, and distribute.

---

*Built as a practical AI automation project demonstrating real-world n8n workflow design, LLM integration, and automated recruitment pipelines.*
