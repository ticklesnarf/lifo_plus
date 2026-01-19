# Nado Trading Bot

A Node.js trading bot for BTC-PERP perpetual futures on the Ink blockchain (Nado). Uses a maker-first strategy with IOC fallback for volume generation while preserving capital.

**IMPORTANT**: Nado blocks trading API calls from cloud IPs (Replit, AWS, etc.). The bot must run locally from a residential IP. See `LOCAL_SETUP.md` for deployment instructions.

---

## Strategy

1. Place a **post_only** limit order at best bid/ask (maker price)
2. Wait **3 seconds** for fill
3. If not filled, cancel and place an **IOC** limit order as aggressive "market" fallback

> Nado does not have traditional market orders - we implement "market" as IOC limit with slippage.

---

## Safety Features

| Feature | Default | Description |
|---------|---------|-------------|
| DRY_RUN | true | Prevents any trades until explicitly disabled |
| Balance Floor | $175 | Emergency shutdown with cancel + flatten |
| Max Hold Time | 45s | Force close positions held too long |
| Target Leverage | 1x | Conservative default |
| Max Leverage | 2x | Hard cap |
| Position Reconciliation | 15s | Syncs local state with on-chain |
| Graceful Shutdown | On exit | Cancels orders and flattens position |

---

## Requirements

1. Ink wallet with private key
2. Collateral deposited to Nado subaccount
3. Node.js 18+
4. **Residential IP** (cloud IPs blocked by Nado)

---

## Local Setup (Required for Trading)

1. Download the code from Replit (Download as zip)
2. Extract to a folder
3. Copy `.env.example` to `.env` and configure:
   ```
   PRIVATE_KEY=0xYOUR_KEY
   DRY_RUN=false
   PRICE_INCREMENT_X18=1000000000000000000
   ```
4. Run:
   ```bash
   npm install
   npm start
   ```

See `LOCAL_SETUP.md` for detailed Windows/Mac/Linux instructions.

---

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `PRIVATE_KEY` | required | Ethereum private key |
| `DRY_RUN` | true | Set to "false" to enable trading |
| `BALANCE_FLOOR_USD` | 175 | Stop trading below this balance |
| `NADO_NETWORK` | mainnet | "mainnet" or "testnet" |
| `PRODUCT_ID` | 2 | BTC-PERP |
| `TARGET_LEVERAGE` | 1 | Target leverage |
| `MAX_LEVERAGE` | 2 | Maximum leverage cap |
| `MIN_ORDER_SIZE_X18` | 250000000000000 | 0.00025 BTC minimum |
| `PRICE_INCREMENT_X18` | 0 | Price tick for rounding (1e18 = 1 USD) |
| `MAKER_WAIT_MS` | 3000 | Wait time for maker fill |
| `IOC_SLIPPAGE_BPS` | 50 | IOC slippage (50 = 0.5%) |
| `MAX_HOLD_MS` | 45000 | Force close after this duration |
| `REST_POLL_INTERVAL_MS` | 1500 | Price polling interval |

---

## Files

```
src/
├── index.js           # Entry point, graceful shutdown
├── config.js          # Environment variable parsing
├── logger.js          # Leveled console logging
├── utils.js           # Helper functions (BigInt math, x18 conversion)
├── nadoClient.js      # Nado SDK client and state queries
├── nadoSubscriptions.js  # REST API polling for market data
└── strategy.js        # Core trading logic and safety controls
```

---

## Disclaimer

Trading perpetual futures involves substantial risk. This code is provided as a technical implementation template. Test on small size first and never risk more than you can afford to lose.
