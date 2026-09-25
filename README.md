# Multi-Timeframe OTE & FVG Backtester (Pine Script v5)

ICT/SMC-style backtester for TradingView: HTF bias confluence, Optimal Trade
Entry (OTE) fib zones, Fair Value Gaps, liquidity/EQL filtering, killzone
session filtering. Built in milestones; this repo currently contains
**Milestone 1 — Foundation & Bias Engine**.

## Files

- `M1_Foundation_Bias_Engine.pine` — Milestone 1 deliverable (draft, pending client review).
- `BIAS_3_Filter.pine` — client-supplied reference script. Its weekly/daily
  bias classification logic (Level 1/2/3/Walkaway) was ported into M1 as-is.

## Milestone 1 scope

1. Fibonacci & entry-zone inputs (4H swing lookback, fib entry level, 0.79 deep-zone toggle)
2. HTF Bias Engine (Level 1/2/3/Walkaway dropdown) + Daily PD-Array filter mode (FVG Only / Order Block Only / Both)
3. Liquidity / EQL detection filter (ATR-based equal-highs/lows at swing origin)
4. Killzone/session filter for all 10 traded assets (UTC+2)
5. Lightweight visual sanity-check overlay (fib zone box, bias label, setup-candidate marker)

Not in this milestone: entry trigger/SL models/position sizing/break-even (M2),
news filter/SMT filter/dashboard/6-line trade visualization/backtest run (M3).

## Setup — TradingView

1. Open TradingView → Pine Editor (bottom panel, or **Pine Editor** tab).
2. New script → paste in the contents of `M1_Foundation_Bias_Engine.pine`.
3. Click **Add to Chart**.
4. **Apply on the 15m chart** of the target symbol — the script pulls 4H/Daily/Weekly data itself via `request.security` (all `lookahead_off`, non-repainting), so 15m is the intended base timeframe (client's entry TF).
5. Open the script's **Settings → Inputs** tab to configure:
   - **1. Fibonacci & Entry Zone** — swing pivot length, fib entry level, deep-zone toggle
   - **2. HTF Bias & PD-Array** — pivot lengths, minimum bias level to trade, PD-array filter mode
   - **3. Liquidity / EQL Filter** — EQL toggle, ATR sensitivity, scan lookback
   - **4. Killzone / Session Filter** — on/off toggle

## Killzone ticker mapping — action needed before use

The killzone filter matches on `syminfo.ticker` (see `f_killzone_session()`
in the script). Mapped tickers: `EURUSD`, `GBPUSD`, `AUDUSD`, `NZDUSD`,
`EURJPY`, `GBPJPY`, `XAUUSD`, plus index aliases `USTEC100`/`NAS100`/`US100`/`USTEC`,
`SPX500`/`US500`/`SP500`, `US30`/`DJ30`.

Broker feeds label indices/gold differently (e.g. `NAS100USD` on OANDA vs
`USTECH100` elsewhere). If the chart shows the orange "ticker not in killzone
map" warning label, add the exact ticker string to the `switch` in
`f_killzone_session()`.

## Known open items (flagged for client review)

- **PD-array Level 2/3 gate**: the spec appendix and the client's own bias
  script don't fully agree on how the daily discount-zone check applies —
  current implementation is documented inline where the assumption was made.
- **Order Block detection**: intentionally simplified (single most-recent
  instance per direction), per the agreed scope trim in the $1500 quote —
  not an exhaustive multi-candidate OB engine.
