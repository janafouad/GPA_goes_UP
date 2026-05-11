<p align="center">
  <img src="./frontend/src/LOGO.png" alt="GPA Goes UP Logo" width="180" />
</p>

<h1 align="center">GPA Goes UP 🎓</h1>

<p align="center">
  <strong>An AI-Powered Academic Recommendation System for University Students</strong>
</p>

<p align="center">
  🚀 <a href="https://gpa-goes-up-git-main-mariamehab.vercel.app/"><strong>Live Demo: GPA Goes UP</strong></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/React-18.3-61DAFB?style=flat-square&logo=react&logoColor=white" alt="React" />
  <img src="https://img.shields.io/badge/Vite-6.4-646CFF?style=flat-square&logo=vite&logoColor=white" alt="Vite" />
  <img src="https://img.shields.io/badge/Flask-3.0-000000?style=flat-square&logo=flask&logoColor=white" alt="Flask" />
  <img src="https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/XGBoost-ML_Engine-F7931E?style=flat-square" alt="XGBoost" />
  <img src="https://img.shields.io/badge/Gemini-2.5_Flash-4285F4?style=flat-square&logo=google&logoColor=white" alt="Gemini" />
  <img src="https://img.shields.io/badge/MySQL-Database-4479A1?style=flat-square&logo=mysql&logoColor=white" alt="MySQL" />
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Vercel-Frontend-000000?style=flat-square&logo=vercel&logoColor=white" alt="Vercel" />
  <img src="https://img.shields.io/badge/Hugging_Face-Backend-FFD21E?style=flat-square&logo=huggingface&logoColor=black" alt="Hugging Face" />
  <img src="https://img.shields.io/badge/Cloudflare_R2-Storage-F38020?style=flat-square&logo=cloudflare&logoColor=white" alt="Cloudflare R2" />
  <img src="https://img.shields.io/badge/TiDB_Cloud-Database-CC0000?style=flat-square&logo=pingcap&logoColor=white" alt="TiDB Cloud" />
</p>

---

## 📖 Table of Contents

- [Project Overview](#-project-overview)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Backend Setup](#backend-setup)
  - [Frontend Setup](#frontend-setup)
  - [One-Click Startup (Windows)](#one-click-startup-windows)
  - [Environment Variables](#environment-variables)
- [API Reference](#-api-reference)
- [Project Structure](#-project-structure)
- [Deployment](#%EF%B8%8F-deployment)
- [Team & Contributors](#-team--contributors)
- [License](#-license)

---

## 🌟 Project Overview

**GPA Goes UP** is a full-stack, AI-powered academic advising platform designed to help university students make smarter decisions about their academic journey. By combining a sophisticated **JSON-configurable Rule Engine**, a trained **Machine Learning ensemble model**, and a **Retrieval-Augmented Generation (RAG) chatbot** powered by Google Gemini, the system delivers personalized, data-driven insights — all from a single uploaded transcript.

At its core, the platform addresses three fundamental student needs:

> **"Which courses should I take next semester?"**
> **"Am I on track to graduate on time?"**
> **"How can I realistically improve my GPA?"**

The system was built with a **decoupled, service-oriented architecture** — every major concern (PDF parsing, rule validation, ML ranking, AI advising) lives in its own module, making it independently testable, maintainable, and deployable. Academic rules are externalized into a human-readable JSON configuration file, meaning policy changes never require code changes.

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 📄 **Smart PDF Transcript Parser** | Extracts student metadata, course history, grades, and GPA from uploaded university transcripts using `pdfplumber` with regex-based validation |
| 🧠 **ML Course Recommendations** | An XGBoost + Random Forest + Logistic Regression soft-voting ensemble ranks eligible courses by predicted grade bucket using Leave-One-Student-Out cross-validation |
| 📏 **Configurable Rule Engine** | A JSON-driven academic policy enforcer that handles graduation requirements, credit-hour load limits, academic probation, prerequisite chains, and level classification — with zero code changes needed to update rules |
| 🎯 **Graduation Planner & GPA Simulator** | Projects milestone timelines and simulates GPA outcomes under different course-load scenarios |
| 🤖 **RAG-Powered Chatbot** | An interactive academic advisor backed by a curated knowledge base and Google Gemini 2.5 Flash, grounded via Retrieval-Augmented Generation |
| ☁️ **Cloud-Persistent ML Artifacts** | Trained models are stored in Cloudflare R2 and survive container restarts; a safe local-filesystem fallback is used in development |
| 🔄 **Admin Retrain Endpoint** | Trigger a full ML retraining cycle from anonymized CSV enrollment data via a single API call |
| ⏱️ **Automated Session Cleanup** | APScheduler runs a background TTL sweep every 2 hours, purging student sessions older than 24 hours for privacy and storage efficiency |
| 🔐 **Session-Based Privacy** | No persistent student accounts — all data is scoped to an ephemeral session tied to a single transcript upload |

---

## 🏗️ System Architecture

```
╔══════════════════════════════════════════════════════════════════════╗
║              PRODUCTION DEPLOYMENT TOPOLOGY                          ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║   ┌──────────────────────────────────────────────────────────────┐  ║
║   │               Vercel  (CDN / Edge Network)                   │  ║
║   │          React + Vite SPA  — Static Assets                   │  ║
║   │   Dashboard · Chatbot UI · PDF Upload · Planner              │  ║
║   └───────────────────────────┬──────────────────────────────────┘  ║
║                                │  HTTPS REST  /api/*                ║
║                                ▼                                     ║
║   ┌──────────────────────────────────────────────────────────────┐  ║
║   │           Hugging Face Spaces  (Flask Backend)               │  ║
║   │                                                              │  ║
║   │  ┌─────────────┐  ┌──────────────────┐  ┌────────────────┐  │  ║
║   │  │ PDF Parser  │  │   Rule Engine    │  │   ML Engine    │  │  ║
║   │  │ (pdfplumber)│─▶│ Rule_Engine.json │─▶│ XGBoost Ensem.│  │  ║
║   │  └─────────────┘  └──────────────────┘  └───────┬────────┘  │  ║
║   │         │                                        │           │  ║
║   │         │          ┌─────────────────────────────▼────────┐  │  ║
║   │         │          │         Cloudflare R2                │  │  ║
║   │         │          │   Trained model artifacts  (.pkl)    │  │  ║
║   │         │          │   Persisted across container restarts│  │  ║
║   │         │          └──────────────────────────────────────┘  │  ║
║   │         ▼                                                     │  ║
║   │  ┌──────────────────────────────────────────────────────┐    │  ║
║   │  │         TiDB Cloud Serverless  (MySQL-compatible)    │    │  ║
║   │  │   Student · Course · Enrollment · Prerequisite       │    │  ║
║   │  │   SSL-enforced · Auto-scaled · Globally distributed  │    │  ║
║   │  └──────────────────────────────────────────────────────┘    │  ║
║   │                                                              │  ║
║   │  ┌──────────────────────────────────────────────────────┐    │  ║
║   │  │  Chatbot  —  RAG · Prompt Builder · Gemini 2.5 Flash │    │  ║
║   │  └──────────────────────────────────────────────────────┘    │  ║
║   │                                                              │  ║
║   │  ┌──────────────────────────────────────────────────────┐    │  ║
║   │  │  APScheduler  —  TTL cleanup every 2 hours           │    │  ║
║   │  └──────────────────────────────────────────────────────┘    │  ║
║   └──────────────────────────────────────────────────────────────┘  ║
╚══════════════════════════════════════════════════════════════════════╝
```

The architecture deliberately isolates every major concern:

- **Parser** → extracts raw data from PDF with no business logic
- **Rule Engine** → pure business logic, zero I/O, fully configurable via JSON
- **ML Engine** → stateless inference module; training is a separate offline step
- **Chatbot** → independent Flask blueprint; reads student context but owns no data

---

## 🛠️ Tech Stack

### Frontend

| Technology | Version | Purpose |
|---|---|---|
| [React](https://react.dev/) | 18.3.1 | UI library |
| [Vite](https://vitejs.dev/) | 6.4.2 | Build tool & dev server |
| [TypeScript](https://www.typescriptlang.org/) | 5.x | Type safety |
| [Tailwind CSS](https://tailwindcss.com/) | 4.1.12 | Utility-first styling |
| [Material UI](https://mui.com/) | 7.3.5 | Component library |
| [Radix UI](https://www.radix-ui.com/) | Various | Accessible UI primitives |
| [React Router](https://reactrouter.com/) | 7.13.0 | Client-side routing |
| [React Hook Form](https://react-hook-form.com/) | 7.55.0 | Form state management |
| [Recharts](https://recharts.org/) | 2.15.2 | Data visualization |
| [Motion](https://motion.dev/) | 12.23.24 | Animations |
| [React DnD](https://react-dnd.github.io/react-dnd/) | 16.0.1 | Drag-and-drop course planner |

### Backend

| Technology | Version | Purpose |
|---|---|---|
| [Python](https://www.python.org/) | 3.10+ | Runtime |
| [Flask](https://flask.palletsprojects.com/) | ≥ 3.0 | REST API framework |
| [Flask-CORS](https://flask-cors.readthedocs.io/) | latest | Cross-origin resource sharing |
| [APScheduler](https://apscheduler.readthedocs.io/) | ≥ 3.10 | Background job scheduling |
| [pdfplumber](https://github.com/jsvine/pdfplumber) | ≥ 0.11 | PDF text extraction |
| [mysql-connector-python](https://dev.mysql.com/doc/connector-python/en/) | ≥ 8.0 | MySQL database driver |

### Machine Learning

| Technology | Version | Purpose |
|---|---|---|
| [pandas](https://pandas.pydata.org/) | ≥ 2.0 | Data manipulation |
| [scikit-learn](https://scikit-learn.org/) | ≥ 1.4 | Random Forest, Logistic Regression, cross-validation |
| [XGBoost](https://xgboost.readthedocs.io/) | ≥ 2.0 | Gradient boosting model |
| [imbalanced-learn](https://imbalanced-learn.org/) | ≥ 0.12 | Class imbalance handling |
| [filelock](https://py-filelock.readthedocs.io/) | ≥ 3.13 | Thread-safe CSV appending |

### AI & Chatbot

| Technology | Purpose |
|---|---|
| [Google Gemini 2.5 Flash](https://ai.google.dev/) | Chatbot language model (via `google-genai` SDK) |
| [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) | AWS-compatible S3 client for Cloudflare R2 access |

### Database

| Technology | Purpose |
|---|---|
| **MySQL** (local dev) | Operational data store — Student, Course, Enrollment, Prerequisite tables |
| [TiDB Cloud Serverless](https://tidbcloud.com/) | Production database — MySQL-compatible, serverless, SSL-enforced, globally distributed |

### Hosting & Infrastructure

| Service | Hosts | Why |
|---|---|---|
| [Vercel](https://vercel.com/) | React + Vite frontend (SPA) | Zero-config CDN deployment; edge network; automatic HTTPS and preview deployments per branch |
| [Hugging Face Spaces](https://huggingface.co/spaces) | Flask backend | Free GPU/CPU container hosting with persistent process; ideal for ML-serving workloads |
| [Cloudflare R2](https://www.cloudflare.com/products/r2/) | Trained ML model artifacts (`.pkl`) | S3-compatible object storage with zero egress fees; survives Hugging Face container restarts |
| [TiDB Cloud](https://tidbcloud.com/) | MySQL-compatible production database | Serverless, horizontally scalable, SSL by default; free tier covers the project's data volume |

---

## 🚀 Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:

- **Python** 3.10 or higher (`python --version`)
- **Node.js** 18 or higher + npm (`node --version`)
- **MySQL** server running locally — or a TiDB Cloud / remote MySQL connection string
- *(Optional)* **Cloudflare R2** credentials for cloud-persistent model storage

---

### Backend Setup

```bash
# 1. Clone the repository
git clone https://github.com/janafouad/GPA_goes_UP.git
cd GPA_goes_UP

# 2. Create and activate a Python virtual environment
python -m venv .venv

# Windows (PowerShell)
.\.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate

# 3. Install Python dependencies
pip install -r requirements.txt

# 4. Copy the environment template and fill in your values
cp .env.example .env

# 5. Start the Flask backend (binds to 0.0.0.0:5000)
cd src/backend
python app.py
```

> The backend API will be available at **http://localhost:5000**

---

### Frontend Setup

```bash
# From the repository root, open a NEW terminal
cd src/frontend

# 1. Install Node dependencies
npm install

# 2. Start the Vite development server
npm run dev
```

> The frontend will be available at **http://localhost:5173**
> All `/api/*` requests are automatically proxied to the Flask backend.

---

### One-Click Startup (Windows)

For Windows users, convenience batch scripts are provided in the [`exe/`](exe/) folder:

```powershell
# Terminal 1 — start the backend
.\exe\run_backend.bat

# Terminal 2 — start the frontend
.\exe\run_frontend.bat
```

Each script handles virtual environment activation, dependency installation, and server startup automatically.

---

### Environment Variables

Copy `.env.example` to `.env` in the repository root and configure the following:

| Variable | Default | Description |
|---|---|---|
| `FLASK_SECRET_KEY` | *(required)* | Flask session encryption key — use a strong random string in production |
| `FLASK_ENV` | `production` | Set to `development` to enable dev-only helper routes |
| `DB_HOST` | `localhost` | MySQL server host |
| `DB_PORT` | `3306` | MySQL port (use `4000` for TiDB Cloud) |
| `DB_USER` | *(required)* | MySQL username |
| `DB_PASSWORD` | *(required)* | MySQL password |
| `DB_NAME` | `gpa_goes` | Database name |
| `DB_SSL` | `false` | Set to `true` for TiDB Cloud or any SSL-enforced server |
| `FRONTEND_ORIGIN` | `http://localhost:5173` | Allowed CORS origin for the React dev server |
| `GEMINI_API_KEY` | *(required for chatbot)* | Google Gemini API key |
| `R2_ENDPOINT_URL` | *(optional)* | Cloudflare R2 endpoint URL |
| `R2_ACCESS_KEY_ID` | *(optional)* | R2 access key |
| `R2_SECRET_ACCESS_KEY` | *(optional)* | R2 secret access key |
| `R2_BUCKET_NAME` | *(optional)* | R2 bucket name for model artifact storage |

**Quick local setup (PowerShell):**

```powershell
$env:DB_HOST          = "localhost"
$env:DB_USER          = "root"
$env:DB_PASSWORD      = "your_password"
$env:DB_NAME          = "gpa_goes"
$env:FLASK_SECRET_KEY = "replace-with-a-long-random-string"
$env:FLASK_ENV        = "development"
$env:GEMINI_API_KEY   = "your_gemini_api_key"
```

**Quick local setup (bash / macOS / Linux):**

```bash
export DB_HOST=localhost
export DB_USER=root
export DB_PASSWORD=your_password
export DB_NAME=gpa_goes
export FLASK_SECRET_KEY=replace-with-a-long-random-string
export FLASK_ENV=development
export GEMINI_API_KEY=your_gemini_api_key
```

---

## 📡 API Reference

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/upload` | Upload a transcript PDF (`multipart/form-data`); parses and stores data, creates student session |
| `GET` | `/api/session/student-id` | Returns the active session's student ID |
| `POST` | `/api/session/purge` | Deletes all student data associated with the current session |
| `POST` | `/api/recommend` | Runs the Rule Engine + ML ranker; returns a prioritized course recommendation list |
| `GET` | `/api/planner/autofill` | Returns pre-filled planner data for the current student |
| `POST` | `/api/planner/calculate` | Runs the GPA milestone and graduation timeline projection |
| `POST` | `/api/admin/retrain` | Triggers a full ML model retraining cycle from stored CSV data |
| `POST` | `/api/dev/session/init` | **(Development only)** Manually initializes a student session for testing |
| `POST` | `/api/chat` | Sends a user message to the RAG-powered Gemini chatbot |

---

## 📁 Project Structure

```
GPA_goes_UP/
├── exe/                          # One-click Windows startup scripts
│   ├── run_backend.bat
│   └── run_frontend.bat
├── src/
│   ├── backend/                  # Flask REST API
│   │   ├── app.py                # Application entry point & route registration
│   │   ├── database.py           # MySQL connection layer
│   │   ├── rule_engine.py        # Academic Rule Engine (pure business logic)
│   │   ├── Rule_Engine.json      # Externalized academic policy configuration
│   │   ├── scheduler.py          # APScheduler background jobs (TTL cleanup)
│   │   ├── chatbot/              # RAG chatbot blueprint
│   │   │   ├── chatbot_routes.py
│   │   │   ├── gemini_client.py
│   │   │   ├── rag_engine.py
│   │   │   ├── prompt_builder.py
│   │   │   └── academic_knowledge.json
│   │   ├── ml/                   # Machine Learning engine
│   │   │   ├── train.py          # Training pipeline (RF + XGBoost + LR + ensemble)
│   │   │   ├── predict.py        # Inference & course ranking
│   │   │   ├── features.py       # Feature engineering
│   │   │   ├── data_loader.py    # Data preprocessing
│   │   │   ├── csv_appender.py   # Thread-safe anonymized data accumulation
│   │   │   ├── r2_storage.py     # Cloudflare R2 artifact sync
│   │   │   └── models/           # Stored model artifacts (.pkl)
│   │   ├── parser/               # PDF transcript parsing service
│   │   │   ├── transcript_parser.py
│   │   │   ├── pdf_reader.py
│   │   │   ├── student_parser.py
│   │   │   └── semester_parser.py
│   │   └── data/
│   │       ├── schema/           # MySQL DDL (creation_code.sql)
│   │       └── training/         # CSV training data (student, enrollment, course, prerequisites)
│   └── frontend/                 # React + Vite SPA
│       ├── src/
│       │   ├── app/
│       │   │   ├── App.tsx
│       │   │   ├── routes.tsx
│       │   │   └── components/   # UI components (Dashboard, Chatbot, Planner, Landing)
│       │   └── styles/           # Tailwind, theme, fonts
│       ├── package.json
│       └── vite.config.ts
├── docs/
│   ├── rules/                    # Academic rules documentation
│   └── system_design/            # Architecture diagrams
├── .env.example                  # Environment variable template
├── requirements.txt              # Python dependencies
└── README.md
```

---

## ☁️ Deployment

The production system is split across four cloud services, each chosen for a specific role in the stack.

### Architecture at a Glance

| Layer | Service | URL |
|---|---|---|
| **Frontend** | Vercel | [INSERT_VERCEL_URL] |
| **Backend API** | Hugging Face Spaces | [INSERT_HF_SPACE_URL] |
| **ML Artifact Storage** | Cloudflare R2 | *(private bucket — no public URL)* |
| **Database** | TiDB Cloud Serverless | *(private endpoint — no public URL)* |

---

### 🔷 Frontend — Vercel

The React + Vite SPA is deployed on **Vercel** using its native Git integration.

- Every push to `main` triggers an automatic production build and deployment.
- Every pull request gets its own **preview deployment URL** for isolated testing.
- Vercel's global CDN serves static assets from the edge, minimizing latency worldwide.
- The [`vercel.json`](src/frontend/vercel.json) config rewrites all routes to `index.html` to support React Router's client-side navigation.

**Key config (`vercel.json`):**
```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/" }]
}
```

**Relevant environment variable to set in the Vercel dashboard:**

| Variable | Value |
|---|---|
| `VITE_API_BASE_URL` | Your Hugging Face Space backend URL |

---

### 🤗 Backend — Hugging Face Spaces

The Flask backend runs as a **Hugging Face Space** (Docker or Python SDK runtime).

- Hugging Face provides a persistent container process — the APScheduler background job (TTL cleanup) runs continuously without any extra infrastructure.
- The Space exposes the Flask app on the default HTTPS port; Vercel's frontend calls it directly.
- On cold start, the backend downloads the latest trained model artifact from Cloudflare R2 into the container's local filesystem, so inference is immediately available.
- The `FLASK_ENV` environment variable is set to `production` in the Space settings to disable dev-only routes.

**Environment variables to configure in the Space settings:**

| Variable | Notes |
|---|---|
| `FLASK_SECRET_KEY` | Strong random string for session signing |
| `FLASK_ENV` | `production` |
| `DB_HOST` | TiDB Cloud host |
| `DB_PORT` | `4000` (TiDB Cloud default) |
| `DB_USER` / `DB_PASSWORD` / `DB_NAME` | TiDB credentials |
| `DB_SSL` | `true` |
| `FRONTEND_ORIGIN` | Your Vercel deployment URL (for CORS) |
| `GEMINI_API_KEY` | Google Gemini API key |
| `R2_ENDPOINT_URL` | Cloudflare R2 S3-compatible endpoint |
| `R2_ACCESS_KEY_ID` / `R2_SECRET_ACCESS_KEY` | R2 API token pair |
| `R2_BUCKET_NAME` | Name of your R2 bucket |

---

### 🟠 ML Artifact Storage — Cloudflare R2

Trained model files (`.pkl`) are stored in a **Cloudflare R2** bucket.

- R2 is S3-compatible, so the backend uses `boto3` with a custom endpoint URL — no vendor lock-in.
- **Zero egress fees**: reading model artifacts from R2 into the Hugging Face container costs nothing, unlike AWS S3.
- On each admin retrain (`POST /api/admin/retrain`), the freshly trained model is automatically uploaded to R2 and becomes immediately available to all running instances.
- In local development, if R2 credentials are not configured, the backend transparently falls back to the local `src/backend/ml/models/` directory.

**R2 bucket access pattern:**

```
Upload (after retrain)  →  R2 bucket  →  Download (on container cold start)
                                      →  Local cache (in-memory / filesystem)
                                      →  Inference requests served from cache
```

---

### 🔴 Database — TiDB Cloud Serverless

All operational data (student sessions, course catalog, enrollment history) is stored in **TiDB Cloud Serverless** — a fully managed, MySQL-compatible database.

- TiDB Cloud uses port **4000** by default (set `DB_PORT=4000`).
- SSL is enforced for all connections in production (`DB_SSL=true`).
- The serverless tier scales to zero when idle, keeping costs minimal for academic usage patterns.
- The schema is defined in [`src/backend/data/schema/creation_code.sql`](src/backend/data/schema/creation_code.sql) — run it once against your TiDB cluster to initialize the tables.

**Initialize the production schema:**
```bash
mysql --host <TIDB_HOST> --port 4000 --user <USER> --password --ssl-mode=REQUIRED \
      gpa_goes < src/backend/data/schema/creation_code.sql
```

> **Note:** TiDB Cloud is fully wire-compatible with MySQL 8.0. No application code changes are needed compared to running against a local MySQL server.

---

This project was built by a dedicated team from the **Faculty of Science, Ain Shams University**.

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Department / Major</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Mariam Ehab Mostafa</strong></td>
      <td>Statistics & Computer Science</td>
    </tr>
    <tr>
      <td>Jana fouad Abdel moneam</td>
      <td>Statistics & Computer Science</td>
    </tr>
    <tr>
      <td>Shahd Mostafa</td>
      <td>Statistics & Computer Science</td>
    </tr>
    <tr>
      <td>[Insert Team Member Name]</td>
      <td>[Insert Department]</td>
      <td>[Insert Role]</td>
    </tr>
  </tbody>
</table>

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<p align="center">
  Made with ❤️ by the GPA Goes UP Team &nbsp;|&nbsp; Faculty of Science, Ain Shams University
</p>
