# 🤖 Polymarket BTC Trading Bot — Beginner's Guide

> **What does this bot do?**  
> It automatically trades on [Polymarket](https://polymarket.com) — a prediction market where you bet on whether Bitcoin's price will go **Up ⬆️** or **Down ⬇️** in the next 5 minutes. The bot watches prices, places trades, and collects winnings — all on autopilot.

> ⚠️ **Real money warning:** This bot places real trades with real money on the Polygon blockchain. Start small, read everything, and never risk money you can't afford to lose.

---

## 📖 Table of Contents

1. [How It Works (Plain English)](#-how-it-works-plain-english)
2. [What You'll Need Before Starting](#-what-youll-need-before-starting)
3. [Step-by-Step Setup](#-step-by-step-setup)
4. [Running the Bot](#-running-the-bot)
5. [Understanding the Settings](#-understanding-the-settings)
6. [Copy Trading (Follow Someone Else's Trades)](#-copy-trading-follow-someone-elses-trades)
7. [Reading the Logs](#-reading-the-logs)
8. [Common Problems & Fixes](#-common-problems--fixes)
9. [Safety Tips for Beginners](#-safety-tips-for-beginners)

---

## 💡 How It Works (Plain English)

Every 5 minutes, Polymarket opens a new market asking: **"Will Bitcoin's price be higher or lower in 5 minutes?"**

You can buy "Up" shares or "Down" shares. The winning side pays out $1 per share, and losers get $0.

**The bot's strategy:**

1. **Finds the next 5-minute BTC market** right before it opens.
2. **Posts buy orders on both sides** (both Up and Down) at slightly different prices — like a store buying wholesale.
3. **Grabs deals** when the combined price of Up + Down is less than $1 (locking in a guaranteed profit).
4. **Cashes out matched pairs** — if it holds both Up and Down shares of equal size, it can merge them back into cash.
5. **Collects winnings** after the market resolves.

There's also a **Copy Trading** mode where it just watches what a top trader buys and mirrors their trades automatically.

---

## 🧰 What You'll Need Before Starting

Before installing anything, make sure you have these:

| Requirement | What It Is | Where to Get It |
|---|---|---|
| **Node.js 18+** | The programming runtime the bot uses | [nodejs.org](https://nodejs.org) — download "LTS" version |
| **Polymarket account** | Your prediction market account | [polymarket.com](https://polymarket.com) — sign up free |
| **Polygon wallet** | A crypto wallet for Polygon network | Use MetaMask at [metamask.io](https://metamask.io) |
| **Private key** | A secret key to let the bot sign transactions | Exported from your wallet (see note below) |
| **Proxy wallet address** | A special Polymarket-generated wallet | Found in your Polymarket account settings |
| **USDC on Polygon** | The currency used to trade | Buy on an exchange and bridge to Polygon |
| **MATIC tokens** | Pays for transaction fees ("gas") | Buy a small amount (~$5 worth) on any exchange |
| **Polygon RPC URL** | An internet connection point to the blockchain | Free at [alchemy.com](https://alchemy.com) or [infura.io](https://infura.io) |

> 🔐 **What is a private key?**  
> Your private key is like the master password for your wallet. In MetaMask: click the three dots → Account Details → Export Private Key. **Never share it with anyone.** Use a dedicated wallet just for this bot — not your main wallet.

> 🔐 **What is a proxy wallet?**  
> Polymarket creates a special "proxy" wallet linked to your account for trading. Find it by logging into Polymarket, going to your profile, and looking in the settings or developer section.

---

## 🚀 Step-by-Step Setup

### Step 1 — Download the Bot

Open your terminal (Command Prompt on Windows, Terminal on Mac/Linux) and run:

```bash
git clone https://github.com/Parallax-Trading/polymarket-copy-trading-bot
cd polymarket-copy-trading-bot
```

> **Don't have git?** Download it from [git-scm.com](https://git-scm.com) first.

---

### Step 2 — Install Dependencies

```bash
npm install
```

This downloads all the code libraries the bot needs. Wait for it to finish (may take a minute).

---

### Step 3 — Create Your Settings File

**On Mac/Linux:**
```bash
cp .env.example .env
```

**On Windows (PowerShell):**
```powershell
Copy-Item .env.example .env
```

This creates a `.env` file — your personal settings file. Now open it in any text editor (Notepad works fine).

---

### Step 4 — Fill In Your Settings

Open `.env` and find these lines. Fill in **your** information:

```env
# --- REQUIRED: Your wallet details ---
PRIVATE_KEY=your_private_key_goes_here
PROXY_WALLET=your_polymarket_proxy_wallet_address_goes_here
POLYGON_RPC=https://polygon-mainnet.g.alchemy.com/v2/YOUR_ALCHEMY_KEY_HERE

# --- RECOMMENDED: Start with small amounts ---
MAX_SPEND_PER_MARKET=5
LADDER_SIZE_PER_LEVEL_USDC=0.50
MAX_LOSS_PER_HOUR_USDC=10
```

> 💡 **Tip on RPC:** Sign up at [alchemy.com](https://alchemy.com), create a Polygon app, and copy your API key URL. This is much more reliable than free public endpoints.

> 💡 **Leave `POLY_API_KEY`, `POLY_API_SECRET`, and `POLY_API_PASSPHRASE` blank.** The bot generates these automatically from your private key on first run.

---

### Step 5 — Verify Everything Looks Right

Double-check:
- [ ] `PRIVATE_KEY` is filled in (long string of letters and numbers)
- [ ] `PROXY_WALLET` is filled in (starts with `0x`)
- [ ] `POLYGON_RPC` is a real URL (not the placeholder text)
- [ ] You have USDC and some MATIC in your wallet
- [ ] Your spend caps are set low for your first test run

---

## ▶️ Running the Bot

### Start the Main Arbitrage Bot

```bash
npm start
```

The bot will:
1. Connect to your wallet ✅
2. Set up required approvals ✅
3. Find the next BTC 5-minute market ✅
4. Start trading ✅

You'll see colored log output in your terminal showing what it's doing in real time.

**To stop the bot:** Press `Ctrl + C`

---

### Alternative Start Commands

| Command | What It Does |
|---|---|
| `npm start` | Start the main arb bot |
| `npm run arb` | Same as above (explicit) |
| `npm run dev` | Start with auto-restart on code changes (for testing) |
| `node src/copy/index.js` | Start the dedicated copy trader only |

---

## ⚙️ Understanding the Settings

Here's what each setting in your `.env` file controls, in plain language:

### Core Risk Controls (Most Important)

| Setting | What It Means | Suggested Starter Value |
|---|---|---|
| `MAX_SPEND_PER_MARKET` | Max $ to spend in one 5-minute round | `5` |
| `MAX_LOSS_PER_HOUR_USDC` | Bot stops if it loses this much in an hour | `10` |
| `MAX_TAKER_FILL_USDC` | Max $ on a single "grab a deal" trade | `2` |
| `COMBINED_ASK_STOP` | Don't buy if the market is too expensive (e.g., 0.99 = stop at 99¢) | `0.99` |
| `TARGET_EDGE` | Minimum profit margin before buying (e.g., 0.02 = 2¢ profit minimum) | `0.02` |

### Ladder Settings

| Setting | What It Means | Suggested Starter Value |
|---|---|---|
| `LADDER_LEVELS` | How many price levels to post buy orders at | `5` |
| `LADDER_SIZE_PER_LEVEL_USDC` | $ at each price level | `0.50` |

### Merge & Redeem

| Setting | What It Means | Suggested Starter Value |
|---|---|---|
| `MERGE_THRESHOLD_USDC` | Minimum matched pair size before converting back to cash | `1` |
| `STOP_BUYING_BEFORE_CLOSE` | Stop buying this many seconds before market closes | `30` |

---

## 👥 Copy Trading (Follow Someone Else's Trades)

Instead of running the full arbitrage strategy, you can simply mirror another trader's buys.

### Option A — Simple Mirror (runs alongside main bot)

Add to your `.env`:
```env
TARGET_WALLET=0xTHEIR_WALLET_ADDRESS_HERE
COPY_TRADE_BUY_PERCENT=50
COPY_TRADE_POLL_MS=5000
```

This mirrors 50% of whatever the target wallet spends, checking every 5 seconds.

Then run the main bot as normal:
```bash
npm start
```

---

### Option B — Dedicated Copy Trader (copy trades only, no arb)

This is simpler and safer for beginners who just want to follow a trader.

Add to your `.env`:
```env
COPY_TARGETS=0xWALLET1,0xWALLET2
COPY_SIZE_MODE=FIXED
COPY_FIXED_USDC=2
COPY_MAX_USDC_PER_TRADE=5
COPY_MAX_USDC_PER_HOUR=20
COPY_MAX_USDC_TOTAL=50
COPY_DRY_RUN=true
```

> 🧪 **Always start with `COPY_DRY_RUN=true`** — this logs what it *would* do without spending real money. Once you're happy with what you see, change it to `false`.

Then run:
```bash
node src/copy/index.js
```

### Copy Size Modes Explained

| Mode | What It Does | Example |
|---|---|---|
| `FIXED` | Always spend the same amount | Always buys $2 worth |
| `MIRROR` | Match exactly what the target spent | Target spent $10 → you spend $10 |
| `RATIO` | Spend a percentage of what target spent | Target $10 × 0.5 ratio → you spend $5 |

### Copy Safety Filters

| Setting | What It Does |
|---|---|
| `COPY_MAX_SLIPPAGE` | Skip if the price moved too much since the target bought |
| `COPY_MAX_PRICE` | Skip if the share price is too high (e.g., 0.9 = skip if above 90¢) |
| `COPY_MIN_PRICE` | Skip if the share price is too low (e.g., 0.1 = skip if below 10¢) |
| `COPY_STALE_MS` | Skip if the trade signal is older than this many milliseconds |

---

## 📋 Reading the Logs

The bot prints colored logs to your terminal and saves full logs to a file called `bot.log`.

**Common log messages you'll see:**

| Message | What It Means |
|---|---|
| `Market discovered: btc-updown-5m-...` | Found the next market to trade |
| `Posting ladder on UP/DOWN` | Placing buy orders |
| `Arb opportunity: combined ask = 0.97` | Found a good deal — buying both sides |
| `Merge triggered` | Cashing out matched pairs |
| `Market resolved — redeeming` | Collecting winnings |
| `Circuit breaker triggered` | Hit a loss limit — bot paused for safety |

To view the full log file:
```bash
# Mac/Linux
cat bot.log

# Windows
type bot.log
```

---

## 🔧 Common Problems & Fixes

### ❌ "Bot exits immediately with missing env var error"

**Fix:** Open `.env` and make sure these are filled in (not blank, not placeholder text):
- `PRIVATE_KEY`
- `PROXY_WALLET`

---

### ❌ "Orders fail" or "Cannot authenticate"

**Check these things:**
1. Your `PRIVATE_KEY` matches the account you used to create your Polymarket account.
2. Your `PROXY_WALLET` is the correct proxy address for that account (not your main EOA).
3. Try a private RPC (Alchemy/Infura) instead of a public one.
4. Leave `POLY_API_KEY` / `POLY_API_SECRET` / `POLY_API_PASSPHRASE` blank — the bot handles these automatically.

---

### ❌ "Copy trades are not firing"

**Check these things:**
1. The target wallet address is **lowercase** (e.g., `0xabc123...` not `0xABC123...`).
2. The target wallet is placing fresh **BUY** trades (not sells or old fills).
3. Your caps (`COPY_MAX_USDC_PER_TRADE`, etc.) aren't set too low.
4. `COPY_DRY_RUN` is set to `false` if you want real trades.

---

### ❌ "Merge or redeem transactions fail"

**Check these things:**
1. Approvals were granted during startup (watch for approval log messages).
2. You actually hold enough matched positions to merge.
3. The market has fully resolved before trying to redeem.
4. You have enough MATIC for gas fees.
5. Check `bot.log` for the specific error.

---

### ❌ "RPC errors / connection timeouts"

**Fix:** Switch to a private RPC endpoint. Public Polygon RPCs are often overloaded. Sign up at [Alchemy](https://alchemy.com) for a free private endpoint.

---

## 🛡️ Safety Tips for Beginners

Follow these rules, especially when starting out:

✅ **Use a dedicated wallet** — don't use your main wallet or any wallet holding other assets you care about.

✅ **Start with tiny amounts** — set `MAX_SPEND_PER_MARKET` to $5 or less for your first few runs.

✅ **Test copy trading in dry-run first** — set `COPY_DRY_RUN=true` and watch the logs for a session before using real money.

✅ **Keep enough MATIC** — you need MATIC for gas on every approval, merge, and redeem transaction. ~$5–10 worth is a good starting buffer.

✅ **Use a private RPC** — free public Polygon RPCs are unreliable for active trading.

✅ **Read `bot.log` after your first session** — review what happened before scaling up your limits.

❌ **Don't skip the `.env.example` comments** — open that file and read the notes next to each setting. They explain the intended behavior.

❌ **Don't assume this will be profitable** — this is an experimental strategy. Market conditions change. Past performance doesn't guarantee future results.

---

## 📁 Project File Reference

```
polymarket-copy-trading-bot/
│
├── .env.example          ← Template settings file — copy this to .env
├── .env                  ← Your personal settings (never share this!)
├── bot.log               ← Full trade history and error log
│
└── src/
    ├── index.js          ← Main bot entrypoint (start here)
    ├── trader.js         ← Core trading logic (ladder, arb, merge, redeem)
    ├── market.js         ← Finds and monitors Polymarket markets
    ├── clob.js           ← Handles order placement and WebSocket feed
    ├── onchain.js        ← Blockchain transactions (approvals, merge, redeem)
    ├── copy-trader.js    ← Integrated wallet mirroring module
    ├── pnl.js            ← Profit/loss tracking
    ├── logger.js         ← Logging setup
    │
    └── copy/             ← Dedicated copy-trading system
        ├── index.js      ← Entrypoint for copy-only mode
        ├── activityFeed.js ← Watches target wallet activity
        ├── copyTrader.js ← Executes copy trades
        └── config.js     ← Copy trading configuration
```

---

## ⚖️ Disclaimer

This software is for research and educational use by experienced users. It is **not financial advice** and does **not guarantee profits**. Trading on Polymarket involves real financial risk including market risk, execution risk, smart contract risk, and more. You are fully responsible for how you configure and use this software.

---

*Built for Polymarket's Polygon-based complementary-token markets.*
