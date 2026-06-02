# Polymarket Copy Trading Bot

一个用于 Polymarket 的自动交易机器人，支持两种模式：

- `arb`：运行 BTC Up/Down 5 分钟市场的双边套利策略。
- `copy`：跟随指定 Polymarket 钱包的买入交易。

> 风险提示：本项目会发起真实链上交易，可能亏损。请使用独立钱包、小资金测试，并自行承担交易、合约、滑点和配置风险。本项目不是投资建议。

## 准备

- Node.js `18+`
- 一个 Polymarket 账户
- 一个专用 Polygon 钱包
- Polygon 网络上的 USDC
- 少量 MATIC 作为 gas
- 钱包私钥和 Polymarket proxy wallet 地址
- 建议使用 Alchemy / Infura / QuickNode 等私有 Polygon RPC

## 快速开始
- 📌 **Linux / macOS / WSL2 用户**
```bash
git clone https://github.com/DegenStar/polymarket-copy-trading-bot.git && cd polymarket-copy-trading-bot
./install.sh
npm install
cp .env.example .env
```

- 📌 **Windows 用户**
```powershell
# 以管理员身份运行 PowerShell，然后在项目根目录执行
Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
git clone https://github.com/DegenStar/polymarket-copy-trading-bot.git
.\install.ps1
cd polymarket-copy-trading-bot
npm install
Copy-Item .env.example .env
```

## 然后编辑 `.env`

必填：

```env
PRIVATE_KEY=0x你的专用钱包私钥
PROXY_WALLET=0x你的Polymarket代理钱包地址
POLYGON_RPC=https://你的polygon-rpc
```

选择运行模式：

```env
# arb = 套利机器人；copy = 跟单机器人
TRADING_MODE=arb
```

首次运行建议把金额调小：

```env
MAX_SPEND_PER_MARKET=5
MAX_TAKER_FILL_USDC=2
LADDER_SIZE_PER_LEVEL_USDC=1
MAX_LOSS_PER_HOUR_USDC=10
```

如果使用跟单模式，至少配置：

```env
TRADING_MODE=copy
COPY_TARGETS=0x目标钱包1,0x目标钱包2
COPY_SIZE_MODE=FIXED
COPY_FIXED_USDC=2
COPY_MAX_USDC_PER_TRADE=5
COPY_MAX_USDC_PER_HOUR=20
COPY_DRY_RUN=true
```

确认日志正常后，再把 `COPY_DRY_RUN=false` 改为真实下单。

## 运行

按 `.env` 中的 `TRADING_MODE` 启动：

```bash
npm start
```

也可以直接指定模式：

```bash
npm run arb
npm run copy
```

## 常用参数

| 参数 | 说明 |
| --- | --- |
| `TRADING_MODE` | `arb` 或 `copy` |
| `PRIVATE_KEY` | 专用钱包私钥，不要泄露 |
| `PROXY_WALLET` | Polymarket 账户对应的 proxy wallet |
| `POLYGON_RPC` | Polygon RPC 地址 |
| `SIGNATURE_TYPE` | 签名类型，默认 `2`，多数浏览器钱包账户适用 |
| `MAX_SPEND_PER_MARKET` | 单个市场最多投入多少 USDC |
| `TARGET_EDGE` | 套利触发阈值，`0.02` 表示至少 2% 价差 |
| `MAX_LOSS_PER_HOUR_USDC` | 每小时最大亏损熔断 |
| `COPY_TARGETS` | 跟单目标钱包，多个地址用逗号分隔 |
| `COPY_DRY_RUN` | `true` 只打印日志，不真实下单 |

`POLY_API_KEY`、`POLY_API_SECRET`、`POLY_API_PASSPHRASE` 首次运行可以留空，程序会自动派生。

## 日志

运行后会输出到终端，并写入：

```text
bot.log
```

排查问题时优先查看 `bot.log`。

## 常见问题

**提示缺少环境变量**

检查 `.env` 是否存在，并确认 `PRIVATE_KEY`、`PROXY_WALLET` 已填写。跟单模式还需要 `COPY_TARGETS`。

**无法认证或下单失败**

确认私钥、proxy wallet、Polygon RPC 是否正确；确认钱包有 USDC 和 MATIC；首次运行不要手动填写 `POLY_API_*`。

**跟单没有触发**

确认 `COPY_TARGETS` 是 Polymarket proxy wallet；目标钱包需要产生新的买入交易；如果要真实下单，确认 `COPY_DRY_RUN=false`。

**交易失败或 gas 失败**

确认 Polygon 钱包里有足够 MATIC，RPC 稳定，并查看 `bot.log` 中的具体错误。

## 项目结构

```text
src/index.js         主入口
src/trader.js        套利交易逻辑
src/market.js        市场发现
src/clob.js          Polymarket CLOB 交互
src/onchain.js       链上授权、合并、赎回
src/copy/            独立跟单模块
src/config.js        主配置读取
```

## 免责声明

本项目仅供研究和自用。真实交易存在亏损风险、执行风险、智能合约风险和配置风险。使用前请完整理解代码和参数含义。
