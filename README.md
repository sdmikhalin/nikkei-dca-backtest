[README_nikkei.md](https://github.com/user-attachments/files/32706528/README_nikkei.md)
# nikkei-dca-backtest# Dollar-Cost Averaging into the Nikkei 225 — Backtest and Trend Filters

An investor buys the Nikkei 225 for a fixed **$100 every week, starting
1 January 1990** — the worst possible entry point in the index's modern
history, months after the December 1989 peak that began Japan's lost decades.
Two questions:

1. What USD return does plain weekly DCA produce by the end of the history?
2. Can a rule improve the return and reduce time spent in drawdown?

Everything is computed in **US dollars**, so the JPY/USD exchange rate is part
of the result, not an afterthought — a dollar investor in Japanese equities
holds two exposures, not one.

## Results

| Strategy | Final value | Profit | Return | Max drawdown | Time in drawdown |
|---|---|---|---|---|---|
| Baseline DCA | $325,485 | $141,785 | 77.2% | −55.0% | 92.6% |
| SMA-200 contribution buffer | $326,816 | $143,116 | 77.9% | −49.9% | 92.5% |
| SMA-12 partial exit | **$336,894** | **$153,194** | **83.4%** | **−49.0%** | 92.4% |

Total contributed across the whole period: **$183,700**.

Two honest readings of this table:

- **The SMA-200 buffer is essentially a wash on return** (+0.7 pp) while
  cutting max drawdown by 5 pp. It buys risk reduction, not performance.
- **Neither rule meaningfully reduced *time* in drawdown** (92.6% → 92.4%).
  This is inherent to DCA rather than a failure of the rules: contributions
  keep arriving, so the portfolio's running peak keeps being reset upward and
  the position sits below its own high-water mark almost permanently. The
  second task asked to reduce time under water, and the honest answer is that
  these rules do not do it.

## Strategies

**Baseline.** Buy $100 of the index on the first trading day of each week, at
the open. Never sell. Units accumulate; portfolio value is units × price in USD.

**Strategy A — contribution buffer on SMA-200.** The 200-day moving average
acts as a trend indicator. Existing holdings are **never sold**; the rule only
decides the fate of each new $100. Above the SMA the contribution is invested;
below it, the money accumulates in cash. When price crosses back above the
SMA, the entire accumulated buffer is deployed at once. This is a
"don't feed a falling market" rule rather than a market-timing rule.

**Strategy B — partial exit on SMA-12 with a buffer band.** Uses a short
12-period moving average. When price falls below it, the strategy moves
`exit_frac = 70%` of the portfolio to cash but keeps the remainder invested —
partial de-risking rather than a full exit. When price recovers above the
average, all cash is reinvested. Two mechanisms limit whipsaw: a `band = 3%`
buffer zone around the average, and signal evaluation only on weekly purchase
days rather than daily.

## Metrics

- **Return** — final portfolio value over total amount contributed. For DCA
  this is not comparable to a lump-sum return, since capital is deployed
  gradually and the average dollar is invested for far less than the full period.
- **Max drawdown** — deepest decline of portfolio value from its running peak.
- **Time in drawdown** — share of days on which value sat below its running peak.

## Caveats

- **Parameters were chosen on the same history they were tested on.** SMA
  window, exit fraction and band width were not selected out of sample, so the
  reported improvement is an upper bound on what this rule would have delivered
  in real time. This is the single biggest limitation of the study.
- **No transaction costs, slippage, taxes or bid-ask spread.** Strategy B
  trades meaningfully more than the baseline, so it is penalised most by this
  omission.
- **A single path through a single market.** One index, one currency pair, one
  start date. Different start dates would give materially different answers —
  which is itself the point of choosing 1990.
- **Index, not a tradable fund.** No tracking error, management fee or dividend
  treatment is modelled.

## Data

- **Nikkei 225** daily index level
- **JPY/USD** daily exchange rate (FRED)

Both are loaded from CSV. Loading handles thousands separators (`38,915.87`)
and FRED's `.` placeholder for missing observations, then aligns the two series
by date and converts the index level to USD.

## Stack

Python, pandas, NumPy, matplotlib

## Repository

```
├── notebook.ipynb    # both tasks, top to bottom
├── data/             # nikkei_225.csv, jpyusd.csv
└── README.md
```

Weekly DCA into the Nikkei 225 from 1990 in USD: baseline vs two trend-filter rules.
