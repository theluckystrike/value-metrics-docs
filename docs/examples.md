# Examples

Real-world valuation scenarios using `value-metrics`. Every example is
runnable Rust; copy any block into `cargo run` or the
[DeepValueRadar value screener](https://deepvalueradar.com/) to rank a full
ticker universe.

## 1. A single stock at a glance

All five ratios for one ticker.

```rust
use value_metrics::*;

let price = 80.0;
let eps   = 4.0;
let book  = 60.0;
let div   = 2.0;
let ev    = 900_000_000.0;
let ebit  = 90_000_000.0;

println!("P/E   : {:.1}", price_to_earnings(price, eps));     // 20.0
println!("P/B   : {:.2}", price_to_book(price, book));         // 1.33
println!("EY    : {:.1}%", earnings_yield(eps, price) * 100.0); // 5.0%
println!("EV/EBIT: {:.1}", ev_to_ebit(ev, ebit));              // 10.0
println!("DivYld: {:.1}%", dividend_yield(div, price) * 100.0); // 2.5%
```

## 2. Comparing two stocks

Two companies, same sector — which looks cheaper?

```rust
use value_metrics::*;

struct Ticker { price: f64, eps: f64, book: f64 }

let a = Ticker { price: 50.0, eps: 2.0, book: 30.0 };
let b = Ticker { price: 90.0, eps: 5.0, book: 40.0 };

for (name, t) in [("A", &a), ("B", &b)] {
    let pe = price_to_earnings(t.price, t.eps);
    let pb = price_to_book(t.price, t.book);
    let ey = earnings_yield(t.eps, t.price);
    println!("{name}: P/E {pe:.1}, P/B {pb:.2}, EY {:.1}%", ey * 100.0);
}
// A: P/E 25.0, P/B 1.67, EY 4.0%
// B: P/E 18.0, P/B 2.25, EY 5.6%
```

B is cheaper on earnings (lower P/E, higher yield); A is cheaper on book.

## 3. Ranking a small universe by earnings yield

Higher earnings yield = cheaper. Sort a list of tickers.

```rust
use value_metrics::earnings_yield;

let universe = [
    ("AAA", 100.0, 6.0),
    ("BBB",  40.0, 3.0),
    ("CCC",  75.0, 5.0),
];

let mut ranked: Vec<_> = universe.iter()
    .map(|(s, p, e)| (*s, earnings_yield(*e, *p)))
    .collect();
ranked.sort_by(|a, b| b.1.partial_cmp(&a.1).unwrap()); // desc

for (sym, ey) in &ranked {
    println!("{sym}: earnings yield {:.2}%", ey * 100.0);
}
// BBB: 7.50%
// CCC: 6.67%
// AAA: 6.00%
```

## 4. Stocks below book value

Find names trading under their accounting book value (P/B < 1).

```rust
use value_metrics::price_to_book;

let names = [
    ("X", 18.0, 25.0),
    ("Y", 30.0, 20.0),
    ("Z", 12.0, 15.0),
];

for (s, p, b) in names {
    let pb = price_to_book(p, b);
    if pb < 1.0 {
        println!("{s}: P/B {pb:.2} — below book");
    }
}
// X: P/B 0.72 — below book
// Z: P/B 0.80 — below book
```

Below-book is a classic deep-value screen — but verify the book value is real
(no goodwill inflation) and the business is not impaired.

## 5. Earnings yield vs. the risk-free rate

Compare a stock's earnings yield to a bond yield (the "Fed model" idea).

```rust
use value_metrics::earnings_yield;

let stock_ey  = earnings_yield(6.0, 120.0); // 5.0%
let bond_yld  = 0.042;                       // 4.2%

let spread = (stock_ey - bond_yld) * 100.0;
println!("Equity risk premium over bonds: {:.1} pp", spread); // +0.8 pp
```

When the equity yield exceeds the bond yield, stocks look relatively cheap.

## 6. Loss-making company edge case

A company with negative earnings — P/E is undefined (infinite), but earnings
yield is still finite (and negative).

```rust
use value_metrics::{price_to_earnings, earnings_yield};

assert!(price_to_earnings(50.0, -2.0).is_infinite());  // no positive P/E
let ey = earnings_yield(-2.0, 50.0);                    // -0.04
println!("Negative earnings yield: {:.1}%", ey * 100.0); // -4.0%
```

---

### Next steps

To rank a full ticker universe with sector medians, value-trap detection, and
historical percentile bands, run the screen through the
[DeepValueRadar value screener](https://deepvalueradar.com/).
