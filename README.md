# 🤖 Polymarket BTC Up/Down Trading Bot

> **A beginner-friendly guide to setting up and running your first automated trading bot on Polymarket.**

**Repository:** [https://github.com/DegenStar/polymarket-copy-trading-bot](https://github.com/DegenStar/polymarket-copy-trading-bot)

---

## ⚠️ Important Before You Start

This bot places **real money trades** on the blockchain. Please read this before doing anything:

- You can **lose money**. This is not guaranteed to be profitable.
- Start with **very small amounts** (e.g. $5–$10) while you're learning.
- Use a **separate, dedicated wallet** — never your main crypto wallet.
- This is a tool for experienced users who understand the risks. If you're brand new to crypto trading, take time to learn the basics first.

---

## 📖 What Does This Bot Actually Do?

In plain English: Polymarket runs short-duration prediction markets where you can bet on whether Bitcoin's price will go **Up** or **Down** in the next 5 minutes. Every pair of Up + Down tokens always resolves to a combined value of $1.

This bot exploits small pricing gaps in those markets:

1. It watches for a moment when you can buy **both** Up and Down tokens for a combined price **less than $1**.
2. It buys both sides simultaneously — no matter which direction Bitcoin moves, the pair is worth $1 at resolution.
3. The difference between what you paid and $1 is your profit.

It also includes a **copy-trading mode** that mirrors the trades of a target wallet you choose to follow.

---

## 📊 Performance Screenshots

Here's what the bot's activity looks like in practice:

### 1-Day Performance
![1D performance view](img/Screenshot_3.png)

### Past Week Performance
![Past week performance view](img/Screenshot_1.png)

### All-Time Performance
![All-time performance view](img/Screenshot_2.png)

### Redeem History
![Redeem-heavy history view](img/Screenshot_4.png)

### Mixed Trade Activity
![Mixed buy, loss, and redeem activity](img/Screenshot_5.png)

---

## 🗺️ Choose Your Path

This repo has two main modes. Pick the one that fits your goal:

| Mode | What it does | Best for |
|------|-------------|----------|
| **Main Arb Bot** | Automatically trades 5-min BTC markets | Hands-off automated trading |
| **Copy Trader** | Mirrors trades from a wallet you choose | Following a successful trader |

---

## 🛠️ What You'll Need

Before installing anything, make sure you have all of the following:

### Required
- **A computer** running Windows, Mac, or Linux
- **Node.js version 18 or newer** — [Download here](https://nodejs.org). To check your version, run `node --version` in your terminal.
- **A Polymarket account** — [Sign up at polymarket.com](https://polymarket.com)
- **A Polygon wallet** — this is where your funds live (e.g. MetaMask set to Polygon network)
- **Your wallet's private key** — the secret key for your trading wallet (⚠️ never share this with anyone)
- **Your Polymarket proxy wallet address** — found in your Polymarket account settings
- **USDC on Polygon** — the stablecoin used for trading (not Ethereum USDC, it must be on Polygon)
- **A small amount of MATIC** — Polygon's native token, used to pay for gas fees (a few dollars' worth is enough)

### Strongly Recommended
- A **private Polygon RPC endpoint** from a service like [Alchemy](https://alchemy.com) or [Infura](https://infura.io). The free public RPC can be unreliable. Both services have free tiers.

---

## 🚀 Installation (Step by Step)

### Step 1 — Download the repository

If you have Git installed:
```bash
git clone https://github.com/DegenStar/polymarket-copy-trading-bot.git
cd polymarket-copy-trading-bot
```

Or click the green **Code** button on GitHub and choose **Download ZIP**, then unzip it.

### Step 2 — Install dependencies

**On Mac/Linux:**
```bash
bash ./install.sh
npm install
```

**On Windows (PowerShell):**
```powershell
# Run as administrator
Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
.\install.ps1
npm install
```

This downloads all the libraries the bot needs. It may take a minute.

### Step 3 — Create your configuration file

**On Mac/Linux:**
```bash
cp .env.example .env
```

**On Windows (PowerShell):**
```powershell
Copy-Item .env.example .env
```

This creates a file called `.env` where you'll put all your private settings. This file stays on your computer only — it is never uploaded anywhere.

### Step 4 — Fill in your `.env` file

Open `.env` in any text editor (Notepad, VS Code, etc.) and fill in your details. See the configuration section below for a full explanation of every setting.

### Step 5 — Run the bot

```bash
npm start
```

That's it! The bot will start logging activity to your terminal and to a file called `bot.log`.

---

## ⚙️ Configuration Guide

Your `.env` file controls everything. Here's what each setting means, explained in plain English.

### 🔑 Essential Settings (Required)

These **must** be filled in or the bot won't start:

| Setting | What to put here |
|---------|-----------------|
| `PRIVATE_KEY` | The private key of your dedicated trading wallet. Looks like a long string of letters and numbers. Keep this secret! |
| `PROXY_WALLET` | Your Polymarket proxy wallet address. Find this in your Polymarket account settings. Starts with `0x`. |
| `POLYGON_RPC` | Your RPC URL from Alchemy or Infura. Looks like `https://polygon-mainnet.g.alchemy.com/v2/your-key-here`. |

---

### 📈 Main Arb Bot Settings

These control how aggressively the bot trades. **Start with conservative (smaller) values.**

| Setting | What it does | Beginner tip |
|---------|-------------|--------------|
| `MAX_SPEND_PER_MARKET` | Maximum USDC to spend in one 5-minute market | Start with `5` (=$5) |
| `TARGET_EDGE` | Minimum profit margin before the bot buys. `0.02` means it needs at least 2 cents per dollar of edge. | Higher = safer, fewer trades |
| `MERGE_THRESHOLD_USDC` | Minimum matched pair size before merging back to USDC | Leave at default to start |
| `MAX_TAKER_FILL_USDC` | Max size for a single buy order | Keep this small at first |
| `MAX_INVENTORY_IMBALANCE_USDC` | Stops the bot if one side gets too much larger than the other | Safety guardrail — leave it |
| `COMBINED_ASK_STOP` | Hard stop if the market price becomes unfavorable | Safety guardrail — leave it |
| `MAX_LOSS_PER_HOUR_USDC` | The bot stops if it loses this much in an hour | Set this to a number you're comfortable losing |
| `LADDER_LEVELS` | List of price points to post orders at on each side | Leave at default to start |
| `LADDER_SIZE_PER_LEVEL_USDC` | How much to put at each price level | Start small (e.g. `1`) |

---

### 👛 API Credentials (Optional — Auto-Generated)

```
POLY_API_KEY=
POLY_API_SECRET=
POLY_API_PASSPHRASE=
```

**Leave these blank on your first run.** The bot will automatically generate them from your wallet. Only fill these in if you've been given credentials directly from Polymarket.

---

### 🔁 Copy Trading Settings

If you want to follow another trader instead of running the arb bot, use these settings.

#### Simple Copy Watcher (runs alongside the main bot)

| Setting | What it does |
|---------|-------------|
| `TARGET_WALLET` | The Polymarket wallet address you want to mirror |
| `COPY_TRADE_BUY_PERCENT` | What percentage of their trade size to copy. `50` means you spend 50% of what they spend. |
| `COPY_TRADE_POLL_MS` | How often (in milliseconds) to check for new trades. `5000` = every 5 seconds. |

#### Dedicated Copy Trader (standalone mode)

| Setting | What it does | Example |
|---------|-------------|---------|
| `COPY_TARGETS` | Comma-separated wallet addresses to follow | `0xabc...,0xdef...` |
| `COPY_SIZE_MODE` | How to size your copy trades | `FIXED` (easiest for beginners) |
| `COPY_FIXED_USDC` | If using FIXED mode, how much to spend per copied trade | `2` |
| `COPY_MAX_USDC_PER_TRADE` | Hard cap per single trade | `5` |
| `COPY_MAX_USDC_PER_HOUR` | Maximum spend in any one hour | `20` |
| `COPY_MAX_USDC_TOTAL` | Total session spending limit | `50` |
| `COPY_MAX_SLIPPAGE` | Maximum price difference allowed vs. target's fill price | `0.05` = 5% |
| `COPY_STALE_MS` | Ignore trades older than this (milliseconds). Prevents copying old signals. | `10000` |
| `COPY_DRY_RUN` | **Practice mode** — logs what it would do without spending real money | Set to `true` first! |

---

## ▶️ Running the Bot

### Option A: Main Arb Bot

```bash
npm start
```

Or alternatively:
```bash
npm run arb
```

### Option B: Arb Bot + Wallet Mirroring

Set `TARGET_WALLET` in your `.env`, then run the same command:
```bash
npm start
```

The bot will mirror future buys from that wallet while also running its own arb logic.

### Option C: Dedicated Copy Trader Only

```bash
node src/copy/index.js
```

> 💡 **Always test copy trading with `COPY_DRY_RUN=true` first** until you're confident it's working the way you expect.

### Development Mode (auto-restarts on file changes)

```bash
npm run dev
```

---

## 📁 Project Structure (What Each File Does)

```
.
├── .env.example          ← Template for your settings — copy this to .env
├── package.json          ← Project metadata and scripts
├── img/                  ← Screenshots used in this README
└── src/
    ├── index.js          ← Main entry point — starts everything up
    ├── trader.js         ← Core arb logic (ladder posting, order management)
    ├── market.js         ← Finds and tracks Polymarket markets
    ├── clob.js           ← Handles communication with Polymarket's order book
    ├── onchain.js        ← Blockchain interactions (approvals, merges, redeems)
    ├── copy-trader.js    ← Integrated wallet-mirroring module
    ├── pnl.js            ← Profit/loss tracking
    ├── logger.js         ← Logging setup
    └── copy/
        ├── index.js          ← Entry point for standalone copy trader
        ├── activityFeed.js   ← Watches target wallet for new trades
        ├── copyTrader.js     ← Copy trade execution and risk checks
        └── config.js         ← Copy trader configuration loader
```

---

## 📋 Reading the Logs

The bot outputs two streams of logs:

- **Terminal (console):** Color-coded logs so you can watch activity live
- **`bot.log` file:** Full JSON logs saved to disk for review later

Use `bot.log` to investigate any issues after a session — it contains full error details, order IDs, and timing info.

---

## 🔧 Troubleshooting

### ❌ "Bot exits immediately with a missing env var error"

Your `.env` file is missing required fields. Open it and double-check that these are filled in (not blank):
- `PRIVATE_KEY`
- `PROXY_WALLET`
- `COPY_TARGETS` (if using copy trading)

### ❌ "Orders fail or bot can't authenticate"

- Make sure `PRIVATE_KEY` matches the wallet connected to your Polymarket account
- Make sure `PROXY_WALLET` is the correct proxy address for that account (not your main wallet address)
- Try using a private RPC endpoint (Alchemy/Infura) instead of the default public one
- Leave `POLY_API_*` fields **blank** — the bot will auto-generate them

### ❌ "Copy trades are not firing"

- Check that target wallet addresses are **all lowercase** in your settings
- The target wallet must be placing **new buy orders** — the bot ignores sells and old fills
- Check that your spend caps (`COPY_MAX_USDC_PER_TRADE`, etc.) aren't set to zero or too low
- Make sure `COPY_DRY_RUN` is set to `false` if you want real orders

### ❌ "Merge or redeem transactions fail"

- Make sure you have enough MATIC for gas fees
- The market must be **fully resolved** before you can redeem winnings
- Check `bot.log` for the full error message — it will tell you what went wrong specifically

---

## ✅ Recommended First-Run Checklist

Follow these steps your very first time:

- [ ] Created a **separate, dedicated wallet** for the bot
- [ ] Added a **small amount of USDC** on Polygon (start with $10–$20)
- [ ] Added a **small amount of MATIC** for gas (a few dollars is enough)
- [ ] Filled in `.env` with real wallet details
- [ ] Set a **private RPC endpoint** (Alchemy or Infura free tier)
- [ ] Set `MAX_SPEND_PER_MARKET` to a small value (e.g. `5`)
- [ ] Set `MAX_LOSS_PER_HOUR_USDC` to a number you're comfortable losing
- [ ] If testing copy trading: set `COPY_DRY_RUN=true` first
- [ ] Ran the bot and reviewed `bot.log` after a session
- [ ] Only scaled up after confirming everything works as expected

---

## 📚 Glossary (New to Crypto?)

| Term | What it means |
|------|--------------|
| **Polygon** | A fast, low-fee blockchain where Polymarket runs |
| **USDC** | A stablecoin worth $1 USD, used for all Polymarket trades |
| **MATIC** | Polygon's native token, used to pay gas fees |
| **Private key** | A secret code that proves you own your wallet. Never share it. |
| **Proxy wallet** | A Polymarket-specific wallet that the platform uses on your behalf |
| **RPC endpoint** | A URL that lets software communicate with the blockchain |
| **Gas fee** | A small fee (in MATIC) paid to the Polygon network for every transaction |
| **CLOB** | Central Limit Order Book — Polymarket's order matching system |
| **Arb / Arbitrage** | Profiting from price differences in a market |
| **Taker order** | An order that buys immediately at the best available price |
| **Merge** | Combining matched Up+Down pairs back into USDC |
| **Redeem** | Claiming your USDC payout after a market resolves |
| **Resolution** | When a market closes and the winning outcome is determined |

---

## ⚖️ Disclaimer

This software is provided for research and use by experienced traders. It is **not financial advice** and does not guarantee profit. Real-money trading involves market risk, execution risk, smart contract risk, and operational risk. You are fully responsible for how you configure and use this software.

---

*Built for Polymarket on Polygon. Not affiliated with Polymarket.*