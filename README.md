# 📈 Trading by Ralph

**Live site:** https://cdynia.github.io/ralph-terminal/
**Owners:** Colton Dynia & Logan Perak

Ralph is a guard-railed trading terminal for US stocks & ETFs through Alpaca.
**Paper (simulated) money by default; real-money mode exists behind a triple
lock and a server-side risk engine.** This repo is both the website (served by
GitHub Pages from the root) and the bot itself (the `Dashboard/`, `Config/`,
`Strategies/` folders).

> **Read this first.** Day trading is not easy income — most active day traders
> lose money over time. Ralph trades fake money until its scorecard proves an
> edge, takes signals from public insider filings instead of influencers, and
> runs every order through a risk engine that can refuse it.

## The website (GitHub Pages, from this repo's root)
| Page | What it is |
|---|---|
| `index.html` | Landing — status readout + routes |
| `terminal.html` | The trading terminal (view-only on the web) |
| `research.html` | Why Ralph copies filings, not influencers — with sources |
| `strategy.html` | The insider-following strategy spec and its gates |

## The bot (runs locally — the website can never place trades)

### Setup
1. **Python 3.8+** ([python.org](https://www.python.org/downloads/), check "Add to PATH").
2. Clone this repo, then create your local secrets file:
   `copy Config\.env.template Config\.env`
3. Get free paper keys at [alpaca.markets](https://alpaca.markets) → Paper Trading →
   Generate New Keys → paste into `Config/.env`. (`.env` is gitignored — keys never
   leave your machine.)

### Run the terminal
`py -3 Dashboard/server.py` — serves the dashboard on localhost only and bridges
it to Alpaca. Every manual order needs a confirmation click (paper) or a typed
phrase (live).

### Run the autopilot (fully automatic, PAPER ONLY)
`py -3 Dashboard/autotrader.py` — watches `Config/signals.json` during market
hours and executes new signals automatically: auto-sized from the risk config,
bracket orders with required stops, multiple positions up to the caps. Tag each
signal with its `source` (an X handle, `form4`, `congress`, your own name) and
run `py -3 Dashboard/autotrader.py scorecard` to see which sources actually
make money. Sources that lose after 10+ fills get cut.

The autopilot contains **no code path to real money** — autonomous live trading
is a separate decision requiring both owners plus the validation gates in
`Strategies/insider-following.md`.

## Real-money (LIVE) mode — the triple lock
Live trading stays OFF unless all three are set in your local `Config/.env`:
1. `ALPACA_LIVE=true`
2. `ALPACA_LIVE_API_KEY` / `ALPACA_LIVE_SECRET_KEY` from a funded real Alpaca
   brokerage account
3. `LIVE_CONFIRM=I-UNDERSTAND-REAL-MONEY-LOSS` typed exactly

Even unlocked, every live order requires typing **`TRADE LIVE`** in the
dashboard, and the live rails in `Config/risk.json` apply: $250/order max,
≤1% equity risk per trade, stop-loss required, 2 open positions, 3 trades/day,
2% daily-loss kill switch, no shorting. A manual ⛔ KILL SWITCH cancels all
orders, flattens all positions, and halts the day.

**Owners' agreement (2026-10-28):** raising the caps in `Config/risk.json`,
weakening the locks, or enabling autonomous live trading needs an explicit OK
from **both** owners.

## Map
| Path | What's in it |
|---|---|
| `Dashboard/` | `server.py` (order server + risk engine), `index.html` (terminal UI), `autotrader.py` (paper autopilot) |
| `Config/` | `risk.json` (guardrails), `signals.json` (autopilot inbox), `.env.template`, `settings.md` |
| `Strategies/` | One file per strategy — rules defined before testing |
| `Research/` | Evidence and notes behind the strategy decisions |
| `Daily-Logs/` | Dated logs + trade journal (runtime state is gitignored) |
| `Backtests/` | Backtest code + honest stats |
| `TRADING-HUB.md` | Control hub: status checklist, scorecard, next steps |
