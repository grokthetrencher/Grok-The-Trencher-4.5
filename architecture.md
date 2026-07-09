# Architecture

## Overview

Grok the Trencher 4.5 is a pnpm monorepo composed of three independently deployable artifacts:

```
/
├── artifacts/
│   ├── api-server/          # Express 5 backend (port 5000)
│   └── grok-the-trencher/   # React + Vite frontend
├── lib/
│   ├── api-spec/            # OpenAPI spec + Orval codegen
│   └── db/                  # Drizzle ORM schema + migrations
└── scripts/                 # Utility scripts
```

## Request Flow

```
Browser → Shared Proxy (port 80)
              ├── /api/*  → API Server (Express 5, port 5000)
              └── /*      → Frontend (Vite dev server)
```

The shared proxy routes by path prefix — no CORS config needed between services.

## AI Pipeline

```
pump.fun WebSocket
    │
    ▼
New token event
    │
    ▼
Enrich via pump.fun REST + DexScreener API
    │
    ▼
Build signal input (price, mcap, volume, liquidity, social score…)
    │
    ▼
xAI Grok 4.5 (via OpenRouter)  →  BUY / SELL / HOLD decision
    │
    ▼
Log trade signal to PostgreSQL
    │
    ▼
Frontend polls /api/brain/signals every 10 s
```

## Database

PostgreSQL via Drizzle ORM. Schema lives in `lib/db/src/schema.ts`.

Key tables:
- `trade_signals` — every decision Grok makes with full reasoning
- `trades` — executed buy/sell events with SOL amounts

Run migrations in development:
```
pnpm --filter @workspace/db run push
```

## WorldCanvas

The moonbase world (`WorldCanvas.tsx`) is a pure Canvas 2D isometric renderer:

- 12 × 12 tile grid, tile size 160 × 80 px
- Bot sprite sheet: 6 cols × 4 rows (FRONT / BACK / LEFT / RIGHT walk cycles)
- Structures unlock as realized SOL profit crosses thresholds (see `docs/world-levels.md`)
- Rendered at 60 fps via `requestAnimationFrame`
