# Grok the Trencher

<p align="center">
  <img src="https://www.image2url.com/r2/default/images/1783560591251-82f9a818-0e16-4f64-b6e5-0cbacb252c7a.png" alt="Grok the Trencher" width="100%" />
</p>

<p align="center">
  <strong>Autonomous AI trading bot for Solana memecoins, powered by xAI Grok 4.5</strong>
</p>

<p align="center">
  <a href="https://x.com/Groktrencher">
    <img src="https://img.shields.io/badge/Follow-%40Groktrencher-black?style=flat-square&logo=x" alt="X" />
  </a>
  <img src="https://img.shields.io/badge/Chain-Solana-9945FF?style=flat-square&logo=solana" alt="Solana" />
  <img src="https://img.shields.io/badge/AI-Grok%204.5-ff4500?style=flat-square" alt="Grok 4.5" />
  <img src="https://img.shields.io/badge/TypeScript-5.9-3178C6?style=flat-square&logo=typescript" alt="TypeScript" />
</p>

---

## What is it?

**Grok the Trencher** is a fully autonomous on-chain AI agent that hunts, scores, and trades newly launched Solana memecoins in real time.

It streams every new token launch from **pump.fun**, enriches the data with **DexScreener** market metrics, runs a multi-signal scoring pass, then asks **xAI Grok 4.5** whether to ape in — all within seconds of a token going live.

As profits accumulate, a pixel-art robot moonbot builds a lunar base on an isometric moon surface rendered live in the browser.

---

## Features

- **Real-time token scanning** — subscribes to the pump.fun WebSocket for instant new-token events
- **DexScreener enrichment** — pulls market cap, volume, and buy/sell pressure from DexScreener
- **AI trading decisions** — Grok 4.5 evaluates every candidate and decides buy / watch / skip
- **Automated execution** — buys via PumpPortal's trading API; positions managed in Postgres
- **Live world canvas** — isometric moon scene where the moonbot walks and builds a base as SOL profits grow
- **Signal dashboard** — real-time table of scanned tokens with pattern, confidence score, and verdict
- **Trade history & open positions** — full audit trail of every buy and sell on-chain

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Browser (React + Vite)                │
│  Hero · Signal Table · Trade Log · Position List        │
│  WorldCanvas (isometric moonbase)                        │
└──────────────────────┬──────────────────────────────────┘
                       │ REST /api/*
┌──────────────────────▼──────────────────────────────────┐
│                  API Server (Express 5)                  │
│  /brain/signals  /brain/trades  /brain/positions         │
│  /brain/stats    /brain/world   /solana/balance          │
└──┬──────────────────┬──────────────────────────────────┘
   │                  │
   ▼                  ▼
Postgres          pump.fun WS
(Drizzle ORM)     DexScreener API
                  Helius RPC
                  OpenRouter → Grok 4.5
                  PumpPortal trading API
```

---

## Tech Stack

| Layer | Tech |
|---|---|
| Frontend | React 18 + Vite 7, Tailwind CSS, Framer Motion |
| Backend | Node.js 24, Express 5, Pino logging |
| Database | PostgreSQL + Drizzle ORM |
| Validation | Zod v4, drizzle-zod |
| AI | xAI Grok 4.5 via OpenRouter |
| Chain | Solana — @solana/web3.js |
| Monorepo | pnpm workspaces, TypeScript 5.9 |

---

## How the bot works

```
New token event (pump.fun WS)
         │
         ▼
   wsTokenToInput()          ← bonding curve → price / mcap
         │
         ▼
   scoreToken()              ← pattern recognition, buy pressure,
         │                      age, volume, reply count
         ▼
   MIN_MCAP_USD gate         ← skip dust tokens
         │
         ▼
   decideOnToken() → Grok    ← AI evaluates all signals
         │
    action = "buy"?
         │  yes
         ▼
   executeBuy()              ← PumpPortal API, Helius RPC
         │
         ▼
   openPosition()            ← stored in Postgres
         │
         ▼
   sell monitor              ← take-profit / stop-loss loop
```

**Verdict labels:**

| Label | Meaning |
|---|---|
| `APE` | Strong signal — bot will attempt a buy |
| `WATCH` | Promising but not strong enough yet |
| `SKIP` | Filtered out (bad pattern, low volume, high mcap) |

---

## Moonbase progression

The isometric WorldCanvas tracks cumulative realized SOL profit and evolves through 6 levels:

| Level | SOL profit | What appears |
|---|---|---|
| 0 | — | Barren moon surface |
| 1 | 0.05 SOL | Dome + antenna |
| 2 | 0.25 SOL | Second dome + solar panels |
| 3 | 1 SOL | Habitat modules |
| 4 | 5 SOL | Landing pad + silo |
| 5 | 20 SOL | Full moonbase complex |

---

## Getting started

### Prerequisites

- Node.js 24+
- pnpm 9+
- PostgreSQL database (`DATABASE_URL`)
- [OpenRouter](https://openrouter.ai) API key (for Grok 4.5)
- Solana wallet private key (for trading)
- Helius RPC URL

### Install

```bash
pnpm install
```

### Configure environment

```bash
# Required
DATABASE_URL=postgres://...
OPENROUTER_API_KEY=sk-or-...
WALLET_PRIVATE_KEY=1,2,3,...   # comma-separated byte array
HELIUS_RPC_URL=https://...

# Optional
SESSION_SECRET=...
```

### Push DB schema

```bash
pnpm --filter @workspace/db run push
```

### Run

```bash
# API server (port 5000)
pnpm --filter @workspace/api-server run dev

# Frontend (separate terminal)
pnpm --filter @workspace/grok-the-trencher run dev
```

---

## Project structure

```
├── artifacts/
│   ├── api-server/          # Express 5 backend + trading engine
│   │   └── src/
│   │       ├── trader.ts    # Core pipeline: score → AI → buy → sell
│   │       ├── ai-decision.ts
│   │       ├── scorer.ts
│   │       ├── pumpportal.ts
│   │       └── routes/
│   └── grok-the-trencher/   # React + Vite frontend
│       └── src/
│           ├── pages/home.tsx
│           └── components/WorldCanvas.tsx
└── lib/
    └── db/                  # Drizzle schema + shared DB client
```

---

## Commands

```bash
pnpm run typecheck                              # full typecheck
pnpm run build                                  # typecheck + build all
pnpm --filter @workspace/api-spec run codegen  # regenerate API hooks
pnpm --filter @workspace/db run push           # push schema (dev only)
```

---

## Follow

**[@Groktrencher](https://x.com/Groktrencher)** on X for live trade updates and moonbase progress.

---

<p align="center">
  <sub>Built with xAI Grok 4.5 · Solana · pump.fun</sub>
</p>
