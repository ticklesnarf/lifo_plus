# Local Deployment Guide

This bot must run from a **residential IP** because Nado blocks trading API calls from cloud providers (AWS, GCP, Replit, etc.).

---

## Prerequisites

- **Node.js 18+** installed ([download here](https://nodejs.org/))
- **Your private key** for the Ink wallet with funds on Nado
- A computer with a **residential internet connection** (not a VPS/cloud server)

---

## Quick Start

### 1. Download the Code

Download this project as a ZIP from Replit:
- Click the three dots menu (⋮) → "Download as zip"
- Extract to a folder on your computer

### 2. Install Dependencies

Open a terminal in the project folder and run:

```bash
npm install
```

### 3. Configure Environment

Copy the example environment file:

```bash
cp .env.example .env
```

Edit `.env` and set your private key:

```
PRIVATE_KEY=0xYOUR_ACTUAL_PRIVATE_KEY_HERE
```

### 4. Test in DRY_RUN Mode First

The bot starts in DRY_RUN mode by default (no real trades). Run it to verify everything works:

```bash
npm start
```

You should see:
- "Strategy engine starting" with `dryRun: true`
- BBO price updates every 1.5 seconds
- "DRY_RUN observation" logs showing simulated trade decisions

Let it run for a few minutes to confirm no errors.

### 5. Enable Live Trading

When ready, edit `.env`:

```
DRY_RUN=false
```

Then restart:

```bash
npm start
```

---

## Configuration Reference

| Variable | Default | Description |
|----------|---------|-------------|
| `PRIVATE_KEY` | (required) | Your Ink wallet private key |
| `DRY_RUN` | `true` | Set to `false` to enable real trading |
| `BALANCE_FLOOR_USD` | `175` | Stop trading if balance drops below this |
| `TARGET_LEVERAGE` | `1` | Target leverage for position sizing |
| `MAX_LEVERAGE` | `2` | Maximum allowed leverage |
| `MIN_ORDER_SIZE_X18` | `250000000000000` | 0.00025 BTC minimum order |
| `MAKER_WAIT_MS` | `3000` | Wait time for maker fill before IOC fallback |
| `REST_POLL_INTERVAL_MS` | `1500` | Market data polling interval |

---

## Safety Features

The bot includes multiple safety mechanisms:

1. **DRY_RUN Mode** - Default ON, prevents any trades until explicitly disabled
2. **Balance Floor** - Automatically cancels orders and flattens position if balance drops below threshold
3. **Position Reconciliation** - Syncs with exchange every 15 seconds
4. **Startup Cleanup** - Cancels stale orders on startup
5. **Graceful Shutdown** - Ctrl+C cancels orders and flattens before exit
6. **Max Hold Time** - Force-exits positions held too long (45s default)

---

## Stopping the Bot

Press `Ctrl+C` for graceful shutdown. The bot will:
1. Cancel all open orders
2. Flatten any open position
3. Exit cleanly

---

## Troubleshooting

### "403 Forbidden" errors
You're running from a cloud IP. Must run from residential internet.

### "Cannot read properties of undefined"
Usually means no open orders to cancel - this is normal on startup.

### Rate limiting
The bot is configured for 40 queries/min, well under Nado's limits. If you see rate limit errors, increase `REST_POLL_INTERVAL_MS`.

### Connection errors
Check your internet connection and that Nado's API is reachable:
```bash
curl https://gateway.prod.nado.xyz/v1/query
```

---

## Volume Target

With current settings:
- Order size: ~0.00025 BTC (~$22.50 at $90k BTC)
- Each round-trip: ~$45 volume
- Target 1M volume: ~22,222 round-trips

At ~1 trade per 5 seconds average, this would take approximately 31 hours of runtime.

---

## Support

For Nado-specific issues, check:
- [Nado Docs](https://docs.nado.xyz/)
- [Nado Discord](https://discord.gg/nado)
