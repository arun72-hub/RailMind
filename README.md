# RailMind
**AI-Powered Railway Safety, Monitoring & Decision Intelligence Platform**
> *"Predicting incidents before they happen and helping operators respond in real time."*
## Live Demo
| Service | URL |
|---------|-----|
| Dashboard | [https://railmind-alpha.vercel.app](https://railmind-alpha.vercel.app) |
| Backend API | [https://railmind.onrender.com](https://railmind.onrender.com) |
| API Docs | [https://railmind.onrender.com/docs](https://railmind.onrender.com/docs) |
## Demo Video
[![RailMind Demo](https://img.youtube.com/vi/YOUR_VIDEO_ID/0.jpg)](https://www.youtube.com/watch?v=YOUR_VIDEO_ID)
*Click above to watch the full walkthrough — shows live detection, risk prediction, emergency response, and dashboard interaction.*
---
## Overview
RailMind is an intelligent co-pilot for railway operations. Instead of operators staring at dozens of disparate screens, RailMind continuously watches CCTV feeds, monitors track conditions, predicts delays, detects dangerous situations, and suggests actions automatically.
### The Problem
Railways suffer from:
- Track failures and signal faults
- Human intrusions (trespassing, track crossing)
- Unattended objects at stations and platforms
- Delays cascading across the network
- Slow emergency response
- Lack of unified monitoring
Current systems are **reactive**. RailMind makes them **predictive**.
### The Solution
RailMind is a unified platform that combines:
| Capability | Status |
|-----------|--------|
| **Computer Vision** — Real-time detection of humans, objects, and hazards on tracks via CCTV | Built |
| **Risk Prediction** — ML-based incident likelihood scoring using weather, track, and operational data | Built |
| **Emergency Response Agent** — Structured action plans with step-by-step guidance for any incident type | Built |
| **Control Dashboard** — Real-time command center with live map, alerts, CCTV viewer, and AI assistant | Built |
| **Delay Propagation** — Predict how a single delayed train cascades across the network | Planned |
| **Railway Digital Twin** — Interactive simulation of trains, stations, and network state | Planned |
| **Conversational AI Agent** — Natural language querying of railway operations | Planned |
---
## Features
### Computer Vision Detection
- **Human Intrusion** — Detect people on tracks, trespassing, crossing railway lines
- **Unattended Object** — Detect bags, suitcases, suspicious objects near platforms/tracks
- **Track Obstacle** — Detect vehicles, debris, and obstructions on railway lines
- **Animal Detection** — Detect animals on or near tracks
- **Smoke / Fire Detection** — Identify smoke and fire hazards from CCTV feeds
### Risk Prediction Engine
- Predicts incident likelihood per track section (score 0–100)
- 18 input features: weather, speed, track condition, crowd density, visibility, time, geometry, maintenance history
- Identifies top 5 contributing risk factors
- Provides inspection recommendations with priority levels
### Emergency Response Agent
- LangGraph-powered stateful workflow: classify → recommend → format
- 7 incident categories with pre-configured response plans
- Optional OpenAI LLM integration for dynamic reasoning
- Rule-based fallback works without any API keys
- Generates structured actions with priority levels and stakeholder assignments
### Command Dashboard
- **Live Network Map** — Leaflet map with real-time train positions, stations, and risk zones
- **CCTV Feed Viewer** — Simulated multi-camera feed with auto-rotate
- **Risk Alert Panel** — Scrollable alert list with severity badges and timestamps
- **Emergency Recommendations** — Collapsible cards with step-by-step actions
- **AI Chat Assistant** — Conversational interface for railway queries
- **System Status** — Live clock, connectivity indicators, and phase badges
---
## System Architecture
```
                         CCTV Feeds / Video Input
                                |
                                v
              +------------------------------------+
              |     Computer Vision Engine         |
              |   (YOLOv8 + ByteTrack + OpenCV)   |
              +------------------------------------+
                                |
                   detected events
                                |
                                v
              +------------------------------------+
              |    Unified FastAPI Backend (:8000) |
              |                                    |
              |  +------------------------------+  |
              |  |   Risk Prediction (XGBoost)  |  |
              |  |   /risk/predict              |  |
              |  +------------------------------+  |
              |  +------------------------------+  |
              |  | Emergency Agent (LangGraph)  |  |
              |  |   /emergency/assess          |  |
              |  +------------------------------+  |
              |  +------------------------------+  |
              |  | Core REST API                |  |
              |  |   /api/trains, /api/alerts   |  |
              |  |   /api/dashboard/summary     |  |
              |  |   /api/dashboard/map         |  |
              |  +------------------------------+  |
              |  +------------------------------+  |
              |  | WebSocket /ws                |  |
              |  | (Redis pub/sub real-time)    |  |
              |  +------------------------------+  |
              +------------------------------------+
                     |           |
                     v           v
           PostgreSQL        Redis
              |
              v
   +----------------------------+
   |  Next.js Control Dashboard |
   |  (React + TypeScript       |
   |   + Tailwind CSS + Leaflet)|
   |  :3000                     |
   +----------------------------+
```
---
## Tech Stack
| Layer | Technology |
|-------|-----------|
| **Vision** | YOLOv8 (Ultralytics), ByteTrack, OpenCV, PyTorch |
| **Backend** | Python 3.12, FastAPI, Uvicorn |
| **ML / Risk** | XGBoost, Pandas, NumPy, Scikit-learn |
| **Agent** | LangGraph, LangChain, OpenAI GPT-4o-mini (optional), Google Gemini 2.0 Flash (optional) |
| **Database** | PostgreSQL 16, SQLAlchemy 2.0 ORM (SQLite fallback if no DATABASE_URL set) |
| **Real-time** | WebSockets, Redis pub/sub |
| **Frontend** | Next.js 16, React 19, TypeScript 5 |
| **Styling** | Tailwind CSS v4, Lucide Icons |
| **Maps** | Leaflet, React-Leaflet |
| **Deployment** | Docker, Docker Compose, Vercel, Render |
---
## Project Structure
```
railmind/
├── backend/                          # Unified FastAPI backend
│   ├── db/
│   │   ├── database.py               # SQLAlchemy engine & session
│   │   ├── models.py                 # ORM models (7 tables)
│   │   └── schema.sql                # PostgreSQL DDL
│   ├── routers/
│   │   ├── dashboard.py              # GET /api/dashboard/summary, /map
│   │   ├── trains.py                 # GET /api/trains, /api/trains/{id}
│   │   └── alerts.py                 # GET /api/alerts, /api/alerts/{id}
│   ├── ws/
│   │   ├── manager.py                # WebSocket connection manager
│   │   └── redis_client.py           # Redis pub/sub client
│   ├── main.py                       # FastAPI app entry point
│   ├── seed.py                       # Database seeder (demo data)
│   └── requirements.txt
├── dashboard/                        # Next.js Command Center
│   ├── src/
│   │   ├── app/
│   │   │   ├── layout.tsx            # Root layout with sidebar
│   │   │   ├── page.tsx              # Home (overview + news)
│   │   │   ├── dashboard/page.tsx    # Command Center
│   │   │   ├── map/page.tsx          # Live map
│   │   │   ├── alerts/page.tsx       # Alerts & incidents
│   │   │   ├── cctv/page.tsx         # CCTV surveillance
│   │   │   └── assistant/page.tsx    # AI chat assistant
│   │   ├── components/
│   │   │   ├── Sidebar.tsx           # Collapsible navigation
│   │   │   ├── DashboardHeader.tsx   # Header with clock & status
│   │   │   ├── MapView.tsx           # Leaflet map
│   │   │   ├── CctvFeed.tsx          # Multi-camera feed
│   │   │   ├── RiskAlertPanel.tsx    # Alert list
│   │   │   ├── StatCards.tsx         # Stats summary cards
│   │   │   ├── EmergencyRecommendations.tsx
│   │   │   ├── AiChatAssistant.tsx   # Conversational AI
│   │   │   ├── AgentReasoningPanel.tsx
│   │   │   ├── IntelligenceLog.tsx
│   │   │   ├── RailwayDigitalTwin.tsx
│   │   │   └── AlertCard.tsx
│   │   ├── hooks/
│   │   │   └── useHomeInteractions.ts
│   │   └── lib/
│   │       ├── types.ts              # TypeScript interfaces
│   │       ├── mockData.ts           # Demo/mock data
│   │       ├── api.ts                # API client (risk & emergency)
│   │       └── formatIst.ts          # Date formatting utilities
│   ├── package.json
│   ├── next.config.ts
│   ├── tailwind.config.ts
│   └── tsconfig.json
├── modules/
│   ├── detection/                    # Computer vision (YOLOv8 + ByteTrack)
│   │   ├── config.py
│   │   ├── detector.py
│   │   ├── tracker.py
│   │   ├── alert.py
│   │   ├── pipeline.py
│   │   └── cli.py
│   ├── risk_prediction/              # XGBoost risk model
│   │   ├── config.py
│   │   ├── model.py
│   │   ├── router.py                 # POST /risk/predict
│   │   ├── schemas.py
│   │   ├── app.py                    # Standalone entry point
│   │   ├── data_generator.py
│   │   └── train.py
│   └── emergency_agent/              # LangGraph emergency agent
│       ├── config.py
│       ├── graph.py                  # StateGraph workflow
│       ├── nodes.py                  # classify → recommend → format
│       ├── router.py                 # POST /emergency/assess
│       ├── schemas.py
│       └── app.py                    # Standalone entry point
├── designs/                          # UI design exports
├── models/
│   └── risk_model.json               # Trained XGBoost model
├── data/
│   └── image1.jpg                    # Sample detection image
├── run_detection.py                  # CLI entry for detection
├── Dockerfile                        # Backend container (Python 3.12)
├── docker-compose.yml                # Redis + Backend + Dashboard
├── requirements.txt                  # ML module dependencies
├── .env.example
├── .gitignore
├── LICENSE
└── README.md
```
---
## Modules
### 1. Human / Obstacle Detection
Real-time detection of objects and hazards from CCTV feeds using computer vision.
**Tech:** YOLOv8, ByteTrack, OpenCV, PyTorch
```bash
# Run with webcam
python3 run_detection.py 0
# Run with video file (enable tracking)
python3 run_detection.py sample_video.mp4 --track
# Run on single image
python3 run_detection.py image.jpg
# Record annotated output
python3 run_detection.py 0 --output recording.mp4
```
**Detection Classes:**
- Person (trespassing, walking on tracks)
- Bicycle, motorcycle, car, truck, bus (track obstacles)
- Train, animal, bag, suitcase (unattended objects)
- Fire, smoke (hazard detection)
### 2. Incident Risk Prediction
Predict incident likelihood for a track section using 18 features covering weather, operational conditions, and infrastructure.
**Model:** XGBoost (trained on 10,000 synthetic railway samples, score 0–100)
**Risk Categories:**
| Score | Category | Action Required |
|-------|----------|----------------|
| 0–20 | Low | No action required |
| 20–40 | Moderate | Monitor section routinely |
| 40–60 | Elevated | Schedule inspection within 24h |
| 60–80 | High | Inspect within 2 hours |
| 80–100 | Critical | Stop traffic, dispatch emergency team |
```bash
# Start standalone API
uvicorn modules.risk_prediction.app:app --host 0.0.0.0 --port 8001
# Predict risk
curl -X POST http://localhost:8001/risk/predict \
  -H "Content-Type: application/json" \
  -d '{"weather_encoded":3,"track_condition_encoded":2,"crowd_density_encoded":3,"is_night":1}'
```
### 3. Emergency Recommendation Agent
When an incident is detected, the AI agent generates structured response actions. Uses LangGraph for a stateful workflow: `classify` → `recommend` → `format`.
Supports 7 incident types with comprehensive response plans:
- Human Intrusion, Unattended Object, Track Obstacle
- Animal on Tracks, Fire Hazard, Fall Detected, Smoke/Fire
**Triple mode:**
- **Rule-based fallback** (default, no API key needed) — comprehensive plans for all 7 types
- **Gemini-powered** (set `GOOGLE_API_KEY`) — dynamic reasoning via Gemini 2.0 Flash (tried first if key present)
- **OpenAI-powered** (set `OPENAI_API_KEY`) — dynamic reasoning via GPT-4o-mini (used if no Gemini key)
```bash
# Start standalone API
uvicorn modules.emergency_agent.app:app --host 0.0.0.0 --port 8002
# Assess an incident
curl -X POST http://localhost:8002/emergency/assess \
  -H "Content-Type: application/json" \
  -d '{"incident_type":"fire_hazard","location":"Platform 3, Dadar","severity":"critical","risk_score":92}'
```
**Example response:**
```json
{
  "incident_id": "INC-1740000000",
  "incident_type": "fire_hazard",
  "location": "Platform 3, Dadar",
  "severity": "critical",
  "category": "Fire Hazard",
  "escalation_level": "critical",
  "summary": "Fire Hazard detected at Platform 3, Dadar. Severity: CRITICAL. Escalation: CRITICAL.",
  "actions": [
    {"step": 1, "action": "Stop all trains in the affected zone", "priority": "immediate", "assigned_to": "Train Controller"},
    {"step": 2, "action": "Notify fire department immediately", "priority": "immediate", "assigned_to": "Fire Department"},
    {"step": 3, "action": "Evacuate nearby structures and platforms", "priority": "short_term", "assigned_to": "Station Master"},
    {"step": 4, "action": "Cut off power supply to the section", "priority": "short_term", "assigned_to": "Power Control"},
    {"step": 5, "action": "Dispatch emergency response team", "priority": "ongoing", "assigned_to": "Emergency Response Team"},
    {"step": 6, "action": "Activate fire suppression system", "priority": "ongoing", "assigned_to": "Fire Department"}
  ],
  "stakeholders": [
    {"name": "Fire Department", "role": "Fire suppression", "contact": "Emergency 101"},
    {"name": "Train Controller", "role": "Stop traffic", "contact": "OCC"},
    {"name": "Station Master", "role": "Evacuation", "contact": "Control Room"},
    {"name": "Power Control", "role": "Cut power supply", "contact": "Power House"},
    {"name": "Emergency Medical Services", "role": "Medical standby", "contact": "EMS"}
  ]
}
```
### 4. Unified Backend API
A FastAPI backend that integrates all modules, exposes REST and WebSocket endpoints, and connects to PostgreSQL for persistent storage. The unified backend includes routes from all modules, so only one service needs to run.
**Endpoints:**
| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `GET` | `/` | — | Service info & endpoint listing |
| `GET` | `/api/dashboard/summary` | — | Aggregated stats (train count, delays, alerts, avg risk) |
| `GET` | `/api/dashboard/map` | — | Trains, stations, and risk zones for map rendering |
| `GET` | `/api/trains` | — | List all trains with positions, speed, status |
| `GET` | `/api/trains/{id}` | — | Single train details |
| `GET` | `/api/alerts` | — | List alerts (optional `?resolved=true`) |
| `GET` | `/api/alerts/{id}` | — | Single alert with associated incidents |
| `POST` | `/risk/predict` | — | Predict risk score for a section (18 features) |
| `POST` | `/emergency/assess` | — | Generate emergency response plan |
| `GET` | `/risk/health` | — | Risk prediction module health check |
| `GET` | `/emergency/health` | — | Emergency agent module health check |
| `WS` | `/ws` | — | Real-time event stream via WebSocket |
```bash
# Start unified backend
uvicorn backend.main:app --reload
# Opens at http://localhost:8000
# API docs at http://localhost:8000/docs
```
### 5. Interactive Command Dashboard
A real-time control room dashboard with multi-page navigation, live map, CCTV viewer, alerts, emergency recommendations, and AI chat assistant.
**Pages:**
| Route | Page | Description |
|-------|------|-------------|
| `/` | **Home** | Railway overview with stats cards, system status, feature links, and news updates |
| `/dashboard` | **Command Center** | Full operational dashboard with live map, CCTV, alerts, emergency recs, and AI assistant |
| `/map` | **Live Map** | Full-screen interactive Leaflet map with trains, stations, and risk zones |
| `/alerts` | **Alerts & Incidents** | Active alerts and emergency recommendations side by side |
| `/cctv` | **Surveillance** | Dedicated CCTV feed viewer with camera switching |
| `/assistant` | **AI Assistant** | Conversational AI interface for railway queries |
**Components:**
| Component | Description |
|-----------|-------------|
| **Sidebar** | Collapsible navigation with feature links and active route highlighting |
| **DashboardHeader** | Top bar with title, system status indicator, and live clock |
| **MapView** | Leaflet map with train positions (color-coded by status), stations (crowd density), and risk zone circles |
| **CctvFeed** | Simulated multi-camera feed with auto-rotate and thumbnail navigation |
| **RiskAlertPanel** | Scrollable list of active alerts with severity badges and relative timestamps |
| **EmergencyRecommendations** | Collapsible cards with step-by-step actions, priorities, and assigned stakeholders |
| **AiChatAssistant** | Conversational interface for queries about delays, risks, emergencies, and trains |
| **StatCards** | Summary cards showing total trains, delays, active alerts, and average risk score |
| **IntelligenceLog** | Timeline of system events and AI agent reasoning steps |
| **RailwayDigitalTwin** | Network visualization showing sections, signals, and train flow |
| **AlertCard** | Individual alert component with severity styling and action buttons |
```bash
cd dashboard
npm install
npm run dev
# Opens at http://localhost:3000
```
---
## Database
RailMind uses **PostgreSQL** with **SQLAlchemy 2.0** ORM. If no `DATABASE_URL` is set, it falls back to a local **SQLite** file (`./railmind.db`), so you can run the backend without any database server.
### Tables
| Table | Description |
|-------|-------------|
| `trains` | Live train positions, speed, status, delay minutes |
| `stations` | Station metadata with coordinates |
| `sections` | Track sections with location and risk radius |
| `alerts` | Detected incidents with severity, type, location, risk score |
| `incidents` | Escalated alerts with action plans and stakeholder assignments |
| `risk_logs` | Historical risk predictions with contributing factors |
| `crowd_logs` | Crowd density measurements per station |
### Using Supabase (Cloud PostgreSQL)
RailMind is fully compatible with Supabase:
1. Create a project at [supabase.com](https://supabase.com)
2. Copy the connection URI from Project Settings → Database
3. Set it in `.env`:
   ```
   DATABASE_URL=postgresql+psycopg2://postgres:PASSWORD@db.REF.supabase.co:6543/postgres?sslmode=require
   ```
4. Seed the database:
   ```bash
   python backend/seed.py
   ```
---
## Environment Variables
Copy `.env.example` to `.env`:
```bash
cp .env.example .env
```
| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `DATABASE_URL` | No | `sqlite:///./railmind.db` | PostgreSQL connection string. If unset, uses local SQLite file (no server needed). |
| `OPENAI_API_KEY` | No | — | OpenAI API key for LLM-powered emergency reasoning. Falls back to rule-based if unset. |
| `OPENAI_MODEL` | No | `gpt-4o-mini` | OpenAI model name for emergency agent |
| `GOOGLE_API_KEY` | No | — | Google AI API key for Gemini-powered emergency reasoning (tried before OpenAI if set). |
| `GEMINI_MODEL` | No | `gemini-2.0-flash` | Gemini model name for emergency agent |
| `REDIS_URL` | No | `redis://localhost:6379/0` | Redis connection for WebSocket pub/sub |
**Frontend environment** (set at build time on Vercel):
| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `NEXT_PUBLIC_RISK_API` | No | `http://localhost:8001` | URL for risk prediction API |
| `NEXT_PUBLIC_EMERGENCY_API` | No | `http://localhost:8002` | URL for emergency assessment API |
---
## Getting Started
### Prerequisites
- Python 3.10+
- Node.js 22+
- npm
- Docker & Docker Compose (optional, for containerized setup)
- PostgreSQL 16 (optional, for database features)
### Quick Start
```bash
# 1. Clone the repo
git clone https://github.com/soham-v14/RailMind-Smart-Surveillance-Decision-Intelligence.git
cd RailMind-Smart-Surveillance-Decision-Intelligence
# 2. Backend setup
python -m venv .venv
source .venv/bin/activate
pip install -r backend/requirements.txt
# 3. Start the backend (no PostgreSQL or Redis needed — uses SQLite by default)
uvicorn backend.main:app --reload
# API at http://localhost:8000
# Docs at http://localhost:8000/docs
# 4. (Optional) Seed the database with demo data
python backend/seed.py
# 5. Frontend setup (separate terminal)
cd dashboard
npm install
npm run dev
# Dashboard at http://localhost:3000
```
### Docker Setup
```bash
docker compose up
# Backend at http://localhost:8000
# Dashboard at http://localhost:3000
# Redis at localhost:6379
```
### Run Individual Modules
```bash
# Computer vision detection
python3 run_detection.py 0 --track
# Risk prediction API (standalone, port 8001)
uvicorn modules.risk_prediction.app:app --host 0.0.0.0 --port 8001
# Emergency agent API (standalone, port 8002)
uvicorn modules.emergency_agent.app:app --host 0.0.0.0 --port 8002
# Unified backend (port 8000 — includes all modules)
uvicorn backend.main:app --reload
```
---
## API Reference
### Dashboard
```
GET /api/dashboard/summary
```
Returns aggregated operational statistics. Requires database connection.
**Response:**
```json
{
  "total_trains": 5,
  "delayed_trains": 1,
  "active_alerts": 2,
  "critical_alerts": 2,
  "average_risk_score": 88.5
}
```
```
GET /api/dashboard/map
```
Returns map data with trains, stations, and risk zones. Requires database connection.
### Trains
```
GET /api/trains                  # List all trains
GET /api/trains/{train_id}       # Get specific train
```
### Alerts
```
GET /api/alerts                  # List unresolved alerts
GET /api/alerts?resolved=true    # List resolved alerts
GET /api/alerts/{alert_id}       # Get alert with incidents
```
### Risk Prediction
```
POST /risk/predict
Content-Type: application/json
{
  "weather_encoded": 3,
  "temperature": 28,
  "visibility_km": 2,
  "wind_speed_kmh": 25,
  "rainfall_mm": 15,
  "train_speed_kmh": 60,
  "track_condition_encoded": 2,
  "track_age_days": 720,
  "days_since_maintenance": 45,
  "crowd_density_encoded": 3,
  "hour_of_day": 22,
  "is_night": 1,
  "section_length_km": 2.5,
  "historical_faults": 3,
  "num_signals": 4,
  "has_level_crossing": 1,
  "curvature_deg": 3.5,
  "gradient_pct": 1.2
}
```
**Input fields:**
| Field | Type | Range | Description |
|-------|------|-------|-------------|
| `weather_encoded` | int | 0–4 | 0=sunny, 1=cloudy, 2=rainy, 3=stormy, 4=foggy |
| `temperature` | float | -10–45 | Ambient temperature (°C) |
| `visibility_km` | float | 0.1–10 | Visibility distance |
| `wind_speed_kmh` | float | 0–100 | Wind speed |
| `rainfall_mm` | float | 0–200 | Rainfall amount |
| `train_speed_kmh` | float | 0–160 | Current train speed |
| `track_condition_encoded` | int | 0–3 | 0=good, 1=fair, 2=poor, 3=critical |
| `track_age_days` | float | 0–3650 | Age of track section |
| `days_since_maintenance` | float | 0–365 | Days since last inspection |
| `crowd_density_encoded` | int | 0–3 | 0=low, 1=medium, 2=high, 3=critical |
| `hour_of_day` | int | 0–23 | Current hour |
| `is_night` | int | 0–1 | Nighttime flag |
| `section_length_km` | float | 0.5–10 | Section length |
| `historical_faults` | int | 0–20 | Past fault count |
| `num_signals` | int | 1–10 | Number of signals |
| `has_level_crossing` | int | 0–1 | Level crossing present |
| `curvature_deg` | float | 0–10 | Track curvature |
| `gradient_pct` | float | -5–5 | Track gradient |
### Emergency Assessment
```
POST /emergency/assess
Content-Type: application/json
{
  "incident_id": "INC-001",
  "incident_type": "fire_hazard",
  "location": "Platform 3, Dadar",
  "severity": "critical",
  "description": "Sparks detected near electrical panel",
  "detected_objects": [{"label": "fire", "confidence": 0.92}],
  "risk_score": 92
}
```
| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `incident_id` | string | auto | Unique identifier |
| `incident_type` | string | `human_intrusion` | Type: `human_intrusion`, `unattended_object`, `track_obstacle`, `animal`, `fire_hazard`, `fall_detected`, `smoke_fire` |
| `location` | string | `Unknown` | Incident location description |
| `severity` | string | `high` | `low`, `medium`, `high`, `critical` |
| `description` | string | — | Free-text description |
| `detected_objects` | array | `[]` | Objects detected by CV |
| `risk_score` | float | — | Optional risk score (0–100) |
### WebSocket
```
WS /ws
```
Real-time event stream for live train updates, new alerts, and system events.
---
## Deployment
### Frontend → Vercel
1. Push the repository to GitHub
2. In Vercel dashboard, click "Add New Project" and select your repo
3. Configure:
   - **Root Directory:** `dashboard/`
   - **Framework:** Next.js (auto-detected)
   - **Build Command:** `next build` (default)
   - **Output Directory:** `.next` (default)
4. Add environment variables:
   ```
   NEXT_PUBLIC_RISK_API      → https://your-backend.onrender.com
   NEXT_PUBLIC_EMERGENCY_API → https://your-backend.onrender.com
   ```
5. Click **Deploy**
The dashboard uses mock data for core features (trains, alerts, map), so it works standalone. Only risk prediction and emergency assessment require a live backend.
### Backend → Render
The unified backend serves all endpoints (`/api/*`, `/risk/*`, `/emergency/*`, `/ws`) from one service.
1. In Render dashboard, click "New Web Service" and connect your repo
2. Configure:
   - **Root Directory:** `/` (repository root)
   - **Runtime:** Docker
   - **Dockerfile Path:** `./Dockerfile`
   - **Port:** `8000`
3. Add environment variables (optional):
   ```
   DATABASE_URL    → postgresql+psycopg2://user:pass@host:5432/db
   OPENAI_API_KEY  → sk-...
   ```
4. Click **Deploy**
The root `Dockerfile` builds and runs `uvicorn backend.main:app --host 0.0.0.0 --port 8000`.
### Docker Compose (full local stack)
```bash
docker compose up
# Starts Redis, Backend (:8000), and Dashboard (:3000)
```
---
## Design System
RailMind uses a dark-themed Material Design 3-inspired design system. Full specifications are in [`DESIGN.md`](./DESIGN.md).
### Key Tokens
| Token | Value |
|-------|-------|
| Page background | `#0A0F1E` |
| Surface | `#1D2027` |
| Surface container | `#111827` |
| Primary | `#ADC6FF` |
| Secondary | `#D0BCFF` |
| Tertiary | `#FFB786` |
| On surface | `#E1E2EC` |
### Severity Colors
| Level | Text | Border |
|-------|------|--------|
| Critical | `#FFB4AB` | `red-400` (pulsing) |
| High | `orange-400` | `orange-500` |
| Medium | `#FFB786` | `#FFB786` |
| Low | `green-500` | `green-500` |
### Typography
- **UI / Body:** Inter (sans-serif)
- **Data / Stats / Timestamps:** JetBrains Mono (monospace)
---
## What Makes RailMind Stand Out
Most teams: **Detect object → Send alert**
RailMind: **Detect → Predict → Recommend → Simulate → Assist**
A complete intelligence platform, not just a detection system.
---
## Team & Contributors
Built during a hackathon by a small team:
- **Arun Prakash Chaurasiya** ([@arun72-hub](https://github.com/arun72-hub)) 
- **RohitSahani2004** ([@RohitSahani2004](https://github.com/RohitSahani2004))
- **Kusum-tech29** ([@Kusum-tech29](https://github.com/Kusum-tech29))

Contributions of every kind are welcome — open an issue or PR.

---
## License
MIT
