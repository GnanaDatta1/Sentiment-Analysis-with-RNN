# Sentiment Analysis with RNN

A full-stack web application that analyzes the emotional tone of any text using an RNN-inspired sentiment engine. Paste text, and the model deconstructs it word by word — showing exactly which tokens drove the sentiment signal positive or negative.

---

## Features

- **Real-time Analysis** — Submit any text and receive a sentiment breakdown instantly
- **Word-level Highlighting** — Positive and negative words are highlighted inline within your original text
- **Score Gauge** — Visual indicator mapping the raw sentiment score from −5 to +5
- **Confidence Meter** — Shows how certain the model is about its prediction
- **Analysis History** — Every run is saved; browse, re-run, or delete past analyses
- **Statistics Dashboard** — Aggregate sentiment distribution (pie chart), average score, and confidence across all runs

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, Vite, TypeScript |
| Styling | CSS custom properties (teal/charcoal palette) |
| Data Fetching | TanStack Query (React Query) |
| API Contract | OpenAPI 3.1 → Orval codegen (hooks + Zod schemas) |
| Backend | Node.js 24, Express 5, TypeScript |
| Sentiment Engine | AFINN lexicon via `sentiment` npm package |
| Database | PostgreSQL + Drizzle ORM |
| Validation | Zod v4 |
| Package Manager | pnpm workspaces (monorepo) |

---

## Project Structure

```
.
├── artifacts/
│   ├── api-server/          # Express 5 API server
│   │   └── src/
│   │       ├── routes/
│   │       │   └── sentiment.ts   # Sentiment analysis endpoints
│   │       └── lib/
│   │           └── sentimentAnalyzer.ts  # RNN-inspired scoring engine
│   └── sentiment-rnn/       # React + Vite frontend
│       └── src/
│           ├── App.tsx              # Routing and layout shell
│           └── components/          # Analysis, History, Stats pages
├── lib/
│   ├── api-spec/            # OpenAPI 3.1 contract (source of truth)
│   ├── api-client-react/    # Orval-generated React Query hooks
│   ├── api-zod/             # Orval-generated Zod schemas
│   └── db/                  # Drizzle ORM schema and client
└── scripts/                 # Shared utility scripts
```

---

## API Endpoints

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/healthz` | Health check |
| `POST` | `/api/sentiment/analyze` | Analyze text, save and return result |
| `GET` | `/api/sentiment/history` | Retrieve all past analyses |
| `DELETE` | `/api/sentiment/history/:id` | Delete a history entry |
| `GET` | `/api/sentiment/stats` | Aggregate statistics |

---

## Getting Started

### Prerequisites

- Node.js 24+
- pnpm 10+
- PostgreSQL database

### Setup

```bash
# Install dependencies
pnpm install

# Set required environment variable
export DATABASE_URL="postgresql://user:password@localhost:5432/sentiment_db"

# Push database schema
pnpm --filter @workspace/db run push

# Start the API server (port 5000)
pnpm --filter @workspace/api-server run dev

# Start the frontend (separate terminal)
pnpm --filter @workspace/sentiment-rnn run dev
```

### Useful Commands

```bash
# Typecheck everything
pnpm run typecheck

# Regenerate API hooks and Zod schemas from OpenAPI spec
pnpm --filter @workspace/api-spec run codegen

# Build all packages
pnpm run build

# Push DB schema changes (dev only)
pnpm --filter @workspace/db run push
```

---

## How the Sentiment Engine Works

The engine uses the **AFINN-165** lexicon — a curated list of ~3,300 English words each assigned an integer score from −5 (most negative) to +5 (most positive). For a given input:

1. The text is tokenized into words
2. Each token is looked up in the AFINN lexicon
3. Scores are summed and normalized by token count to produce a score in [−5, +5]
4. Words above zero are classified as **positive**, below zero as **negative**
5. Confidence is computed from the magnitude of the normalized score combined with token coverage (what fraction of words had a known sentiment)
6. The final label (**positive / negative / neutral**) is assigned based on thresholds

This mirrors the sequential token-processing behavior of an RNN, where each token contributes incrementally to the final state.

---

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `DATABASE_URL` | Yes | PostgreSQL connection string |
| `SESSION_SECRET` | Optional | Secret for session signing |
| `PORT` | Optional | Port for the API server (default: 5000) |

---

## License

MIT
