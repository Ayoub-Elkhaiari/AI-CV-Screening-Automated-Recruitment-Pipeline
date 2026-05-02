# 🤖 AI CV Screening — Automated Recruitment Pipeline

> An intelligent, fully automated recruitment workflow built with **n8n**, **OpenRouter LLMs**, and **Google Sheets** — screens job applications, analyzes CVs, and sends personalized responses without human intervention.

![n8n](https://img.shields.io/badge/n8n-Self%20Hosted-orange?style=flat-square&logo=n8n)
![OpenRouter](https://img.shields.io/badge/LLM-OpenRouter%20Free-blue?style=flat-square)
![Google Sheets](https://img.shields.io/badge/Storage-Google%20Sheets-green?style=flat-square&logo=googlesheets)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=flat-square&logo=docker)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)

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

**Zero human intervention required.** Every email feels personal — candidates never know it's automated.

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

```
┌─────────────────────────────────────────────────────────────────┐
│                     AI CV SCREENING PIPELINE                     │
└─────────────────────────────────────────────────────────────────┘

📧 Email received (IMAP / Gmail)
        │
        ▼
┌───────────────────┐
│  Phase 1 Filter   │  ← JavaScript keyword matching
│  + PDF Check      │    "candidature", "cv", "engineer"...
└───────────────────┘
        │ match + PDF attached
        ▼
┌───────────────────┐
│  Phase 2 — LLM    │  ← OpenRouter LLM validates
│  Validation       │    is_application: true/false
└───────────────────┘
        │ confirmed application
        ▼
┌───────────────────┐
│  PDF Extraction   │  ← Extract raw text from CV
└───────────────────┘
        │
        ▼
┌───────────────────┐
│  Data Merge       │  ← Combine email metadata + CV text
└───────────────────┘
        │
        ▼
┌───────────────────┐
│  LLM Scoring      │  ← Score candidate vs job description
│  & Analysis       │    Returns: score, skills, recommendation
└───────────────────┘
        │
        ├── score ≥ 70 ──────────────────┐
        │                                │
        ▼                                ▼
┌───────────────┐              ┌───────────────────┐
│  ✅ Accept     │              │  ❌ Reject         │
│  Email (LLM)  │              │  Email (LLM)      │
└───────────────┘              └───────────────────┘
        │                                │
        └──────────────┬─────────────────┘
                       ▼
              ┌─────────────────┐
              │  SMTP Send      │  ← Personalized email sent
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │  Google Sheets  │  ← Log: timestamp, email,
              │  Dashboard      │    score, decision, reason
              └─────────────────┘
```

---

## 🔍 Workflow Breakdown

### Phase 1 — Keyword Filter (JavaScript)
Checks email subject + body for recruitment-related keywords. Also verifies a PDF attachment is present. Emails without CVs are immediately discarded.

**Keywords:** `candidature`, `cv`, `application`, `postuler`, `engineer`, `automation`, `poste`

### Phase 2 — LLM Validation (OpenRouter)
Sends the email content to a free LLM to confirm it's a genuine job application — not spam, not a newsletter, not an unrelated email. Returns structured JSON with confidence score.

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

---

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| **Workflow Engine** | n8n (self-hosted) |
| **Containerization** | Docker + Docker Compose |
| **Email Trigger** | IMAP (Gmail) |
| **Email Sending** | SMTP (Gmail) |
| **LLM Provider** | OpenRouter (free tier) |
| **LLM Models** | `meta-llama/llama-3.3-70b-instruct:free` |
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
git clone https://github.com/YOUR_USERNAME/ai-cv-screening-n8n.git
cd ai-cv-screening-n8n
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

Data Scientist & AI Engineer — specialized in ML pipelines, LLMs, and AI automation.

- 🌐 [ayoub-elkhaiari.netlify.app](https://ayoub-elkhaiari.netlify.app)
- 💼 [LinkedIn](https://linkedin.com/in/ayoub-elkhaiari-2001c17)
- 🐙 [GitHub](https://github.com/Ayoub-Elkhaiari)
- 📧 ayoub.elkhaiari@usmba.ac.ma

---

## 📄 License

MIT License — feel free to use, modify, and distribute.

---

*Built as a practical AI automation project demonstrating real-world n8n workflow design, LLM integration, and automated recruitment pipelines.*
