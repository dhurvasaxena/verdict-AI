# Verdikt-An AI PAYMENT AGENT

> An autonomous AI intermediary that eliminates human bias from the freelance payment cycle — using multi-agent negotiation, simulated escrow, and a dynamic reputation scoring system.

![Stack](https://img.shields.io/badge/Frontend-React_18-61DAFB?style=flat-square&logo=react)
![Stack](https://img.shields.io/badge/Backend-FastAPI-009688?style=flat-square&logo=fastapi)
![Stack](https://img.shields.io/badge/AI-Gemini_1.5_Flash-4285F4?style=flat-square&logo=google)
![Cost](https://img.shields.io/badge/API_Cost-Free-22c55e?style=flat-square)

---

## What Is This?

PayAgent AI removes the "Trust Gap" in freelance work. Instead of employers and freelancers arguing over whether a milestone was completed, two AI agents negotiate on their behalf — one representing the employer, one the freelancer — and a third AI arbitrator delivers a final verdict. Payment releases automatically based on the score.

No human middleman. No payment delays. No biased judgements.

---

## How It Works

```
Employer describes project → AI generates milestone plan → Employer approves
        ↓
Freelancer submits work for Milestone 1
        ↓
Employer Agent  ←→  Freelancer Agent  →  Arbitrator gives score (0–100)
        ↓
Score ≥ 75  →  100% of milestone payment released
Score 50–74 →  75% released, 25% refunded to employer
Score < 50  →  Full refund to employer
        ↓
PFI (Professional Fidelity Index) updates on freelancer's profile in real time
```

---

## Features

**Intelligent Milestone Generator**
Employer types a plain-English project brief. Gemini breaks it into 3–5 milestones, each with acceptance criteria, deadline, and payment split. The employer can request changes and the AI revises the plan live.

**Autonomous Escrow**
Total project budget is locked upfront. Funds release automatically per milestone based on the AI verdict — no manual approval needed.

**Multi-Agent Negotiation (USP)**
The system's core differentiator. When a freelancer submits work, three AI agents activate:
- **Employer Agent** — argues critically on behalf of the employer
- **Freelancer Agent** — defends the quality of submitted work
- **Arbitrator** — reviews both arguments and delivers a final score

The entire negotiation is visualised on screen as a live chat conversation, message by message.

**Professional Fidelity Index (PFI)**
A dynamic reputation score (0–100) maintained per freelancer. Calculated as a weighted rolling average of milestone scores with a bonus for on-time delivery. Updates in real time after every evaluation.

| PFI Range | Tier |
|---|---|
| 85 – 100 | Elite |
| 70 – 84 | Trusted |
| 55 – 69 | Growing |
| 40 – 54 | New |
| 0 – 39 | At Risk |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + Tailwind CSS |
| Backend | Python FastAPI + Uvicorn |
| AI Engine | Google Gemini 1.5 Flash |
| AI Package | `google-generativeai` |
| State | In-memory Python dict |
| HTTP Client | Axios |

---

## Project Structure

```
ai-payment-agent/
├── backend/
│   ├── main.py                 # FastAPI app + all routes
│   ├── models.py               # Pydantic data models
│   └── services/
│       ├── ai_service.py       # All Gemini API calls
│       ├── escrow.py           # Payment release logic
│       └── pfi_engine.py       # PFI score calculation
├── frontend/
│   └── src/
│       ├── pages/
│       │   ├── EmployerDashboard.jsx
│       │   ├── FreelancerDashboard.jsx
│       │   └── NegotiationView.jsx
│       └── components/
│           ├── MilestoneCard.jsx
│           ├── EscrowPanel.jsx
│           ├── NegotiationAgent.jsx
│           └── PFIScore.jsx
└── README.md
```

---

## Getting Started

### Prerequisites

- Python 3.9+
- Node.js 18+
- A free Google Gemini API key — get one at [aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey) (no credit card required)

### 1. Clone the repo

```bash
git clone https://github.com/your-username/payagent-ai.git
cd payagent-ai
```

### 2. Set up the backend

```bash
cd backend
pip install fastapi uvicorn google-generativeai pydantic python-dotenv
```

Create a `.env` file:

```bash
echo "GEMINI_API_KEY=your_AIza_key_here" > .env
```

Start the server:

```bash
uvicorn main:app --reload --port 8000
```

### 3. Set up the frontend

```bash
cd frontend
npm install
npm start
```

The app will open at `http://localhost:3000`.

---

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/project/create` | Create project, generate milestone plan |
| `POST` | `/api/project/{id}/revise` | Revise milestone plan based on feedback |
| `POST` | `/api/milestone/{project_id}/{milestone_id}/submit` | Submit work, trigger negotiation, release payment |
| `GET` | `/api/freelancer/{id}/pfi` | Get freelancer's current PFI score and history |

---

## Payment Logic

| Score | Payment Released | Status |
|---|---|---|
| 75 – 100 | 100% of milestone split | Full Release |
| 50 – 74 | 75% of milestone split | Partial Release |
| 0 – 49 | 0% (full refund to employer) | Refunded |

**Example:** $1,000 project, Milestone 1 = 25% split = $250 locked.
- Score 82 → **$250.00** released to freelancer
- Score 61 → **$187.50** released, $62.50 refunded
- Score 38 → **$0.00** released, $250.00 refunded

---

## PFI Score Formula

```
PFI = (0.50 × most recent score)
    + (0.30 × second most recent)
    + (0.20 × third most recent)
    + 3.0  if submitted on time
    - 5.0  if submitted late
    (clamped 0–100)
```

---

## Environment Variables

| Variable | Description |
|---|---|
| `GEMINI_API_KEY` | Your Google Gemini API key from aistudio.google.com |

> **Free tier limits:** 15 requests/minute, 1 million tokens/day. Sufficient for demos and development.

---

## Known Gotchas

**Gemini returns JSON wrapped in markdown fences**
All API responses pass through a `clean_json()` helper that strips ` ```json ` fences before parsing. Never call `json.loads()` directly on a Gemini response.

**Rate limit during negotiation**
The negotiation flow makes 3 Gemini calls back-to-back. A `time.sleep(1)` delay between calls keeps you under the 15 req/min free tier limit. Remove for paid tier.

**Safety filter blocks prompts**
Avoid using words like "attack", "exploit", or "hack" in test submissions. Gemini's safety filter will return a `finish_reason: SAFETY` error.

**Scores returned as strings**
Gemini occasionally returns integers as strings in JSON output. Cast with `int(verdict['final_score'])` after parsing.

---

## Roadmap

- [ ] Replace in-memory state with Firebase / PostgreSQL
- [ ] Add real payment processing via Stripe
- [ ] Migrate escrow to smart contracts (on-chain PFI portability)
- [ ] Support file uploads for milestone submissions (not just text)
- [ ] Add project risk scoring before project launch
- [ ] Export PFI Passport as a shareable verified link

---

## Contributing

Pull requests are welcome. For major changes, open an issue first to discuss what you'd like to change.

---

## License

[MIT](LICENSE)
