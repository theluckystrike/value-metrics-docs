# value-metrics

**Core equity valuation ratios** — the handful of numbers a deep-value screen
runs on: price-to-earnings, price-to-book, earnings yield, EV/EBIT, and
dividend yield. Cheap-on-the-multiples is the starting point of value
investing; this crate computes all five from per-share inputs.

`value-metrics` is a pure-Rust, **zero-dependency** crate that returns each
ratio as an `f64`. It is the signal layer behind the
[DeepValueRadar value screener](https://deepvalueradar.com/), a free tool for
screening stocks on classic value multiples.

## Why these ratios

A valuation ratio puts a stock's price in context against something the
business actually produces (earnings, book value, cash flow, dividends). The
lower the multiple — relative to the company's history and its sector — the
cheaper the stock:

| Ratio            | Formula                  | Reads as "cheap" when…                |
|------------------|--------------------------|---------------------------------------|
| **P/E**          | `price ÷ eps`            | Low vs. sector / vs. own history.     |
| **P/B**          | `price ÷ book/share`     | `< 1` trades below liquidation value. |
| **Earnings yield** | `eps ÷ price`          | High; comparable to a bond yield.     |
| **EV/EBIT**      | `enterprise_value ÷ ebit` | Low; neutral to leverage.             |
| **Dividend yield** | `dividend/share ÷ price` | High and well-covered.               |

Earnings yield is the **reciprocal** of P/E — useful when comparing stocks to
bonds, because it is itself a yield (e.g. a 20 P/E → 5% earnings yield).

## Install

```toml
[dependencies]
value-metrics = "0.1"
```

## Quick start

```rust
use value_metrics::*;

let pe = price_to_earnings(100.0, 5.0);   // 20.0
let ey = earnings_yield(5.0, 100.0);      // 0.05  (1/pe)
let pb = price_to_book(50.0, 25.0);       // 2.0
let ev = ev_to_ebit(1_000_000.0, 100_000.0); // 10.0
let dy = dividend_yield(4.0, 100.0);      // 0.04

println!("P/E={pe}, EY={:.1}%, P/B={pb}, EV/EBIT={ev}, DY={:.1}%", ey*100.0, dy*100.0);
```

!!! tip "Screen a whole universe interactively"
    The crate gives you the per-stock ratios. To rank a universe of tickers,
    apply sector medians, and flag deep-value candidates, use the
    [DeepValueRadar value screener](https://deepvalueradar.com/).

## Features

- **Pure Rust, no deps** — `f64` arithmetic, single file, no features.
- **Five ratios** — P/E, P/B, earnings yield, EV/EBIT, dividend yield.
- **Division-safe** — non-positive denominators return `INFINITY` or `0.0`
  rather than panicking.
- **MIT licensed** — embed anywhere (screeners, dashboards, backtests).

## Links

- Source crate: [`theluckystrike/deepvalueradar`](https://github.com/theluckystrike/deepvalueradar)
- Interactive tool: [DeepValueRadar](https://deepvalueradar.com/)
