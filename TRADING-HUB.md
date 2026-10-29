# 📈 TRADING BOT — Control Hub

**Owners:** Colton Dynia & Logan Perak · **Market:** US stocks & ETFs
**Built:** 2026-10-05 · **Rebuilt (live-capable):** 2026-10-28
**Mode:** PAPER by default · **LIVE (real money) exists behind a triple lock + server-side risk engine**

> **The truth, up front.** Day trading is not easy passive income — most active day traders lose
> money over time. This bot's risk engine is modeled on the serious open-source bots (freqtrade's
> protections, Alpaca risk-engine repos): stop-loss required on every entry, daily-loss kill switch,
> tiny live caps, full audit trail. The guardrails are server-side — the UI can't bypass them.
> **The strategy is still unproven. Live money deserves a paper track record first.**

---

## 🚦 Current status
**⚠️ SERVER NOT READY FOR LIVE USE** — live-capable code exists, but it's untested against a real
account, has no dashboard authentication yet, and there's no funded live account or paper track
record. Do not set the triple lock until the items below are done.

- [x] Workspace + folders scaffolded
- [x] `trading-bot` subagent + desktop launcher wired
- [x] **Live-capable server with risk engine + kill switch** (2026-10-28)
- [x] GitHub repo private (`cdynia/trading-by-ralph`)
- [ ] **Dashboard authentication** (planned next — login/token before any live unlock)
- [ ] Alpaca **paper** account created → keys in `Config/.env`
- [ ] First strategy defined in `Strategies/`
- [ ] First backtest run
- [ ] First paper-trading day logged
- [ ] Live Alpaca brokerage account approved + funded (Colton — SSN + bank transfer)
- [ ] Triple lock enabled → first REAL trade

## 🔑 Step 1 — PAPER (do this first, it's free & takes 5 min)
1. Free account at **alpaca.markets** → **Paper Trading** toggle → "Generate New Keys".
2. Put them in `Config/.env`:
   ```
   ALPACA_API_KEY=your_paper_key
   ALPACA_SECRET_KEY=your_paper_secret
   ALPACA_PAPER=true
   ```
3. Relaunch the dashboard — it auto-connects and real-time simulated trading is on.

## 🔴 Step 2 — LIVE (real money) — the triple lock
Live mode stays OFF unless **all three** are set in `Config/.env`:
1. `ALPACA_LIVE=true`
2. `ALPACA_LIVE_API_KEY` / `ALPACA_LIVE_SECRET_KEY` — from a **funded real Alpaca brokerage
   account** (apply at alpaca.markets → Live Trading; needs SSN + bank transfer; only Colton/Logan
   can do this part).
3. `LIVE_CONFIRM=I-UNDERSTAND-REAL-MONEY-LOSS` — typed exactly.

Even unlocked, **every live order requires typing `TRADE LIVE`** in the dashboard, and the live
rails in `Config/risk.json` apply:

| Rail (live defaults) | Value |
|---|---|
| Max $ per order | **$250** |
| Max risk per trade (at stop) | **1%** of equity |
| Stop-loss | **required** — no stop, no trade |
| Max open positions / trades per day | **2 / 3** |
| Daily-loss kill switch | **-2%** on the day → all new orders refused until tomorrow |
| Shorting | **disabled** — sells only reduce what you own |
| Manual ⛔ KILL SWITCH | cancels all orders, flattens all positions, halts the day |

Every order attempt — accepted or refused, with the reason — is appended to
`Daily-Logs/orders.jsonl`.

## 🧰 How it's wired
- **Server:** `Dashboard/server.py` — stdlib-only Python, localhost-only, paper
  (`paper-api.alpaca.markets`) or live (`api.alpaca.markets`) chosen by the triple lock.
- **Risk engine:** `Config/risk.json` (separate paper/live sections), enforced server-side on every order.
- **UI:** `Dashboard/index.html` — red REAL-MONEY mode, typed confirmations, kill-switch button.

## 📂 Map
| Folder | What's in it |
|---|---|
| `Strategies/` | One file per strategy — rules defined *before* testing. |
| `Backtests/` | Backtest code + results + honest stats. |
| `Daily-Logs/` | Dated logs, `journal.json`, `orders.jsonl` audit trail, `risk-state.json`. |
| `Research/` | Ticker/sector research, trend scans, notes. |
| `Config/` | `settings.md`, `risk.json` (guardrails), local `.env` (secrets, gitignored). |

## 📊 Running scorecard
_No strategies tested yet — this table must earn its rows before live size grows._

| Strategy | Type | Backtest return | Win rate | Max DD | Paper days | Verdict |
|---|---|---|---|---|---|---|
| — | — | — | — | — | — | — |

## 🗒️ Next step
Generate the **paper keys** (5 min, free) so the bot finally executes its first trades, while the
live Alpaca brokerage application/funding is in progress. Raise live caps in `Config/risk.json`
only after the paper scorecard proves an edge.
