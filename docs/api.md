# API Reference

`value-metrics` exposes five free functions. All take and return `f64`. No
structs, no traits, no features to enable. Every function is **division-safe**:
a non-positive denominator returns `f64::INFINITY` (for ratios) or `0.0` (for
yields), never a panic.

## `price_to_earnings`

```rust
pub fn price_to_earnings(price: f64, eps: f64) -> f64
```

**Price-to-earnings ratio** = `price ÷ earnings per share`.

- Returns `f64::INFINITY` when `eps <= 0.0` (loss-making or break-even).
- A "cheap" P/E is low relative to the company's history and its sector.

```rust
use value_metrics::price_to_earnings;

assert!((price_to_earnings(100.0, 5.0) - 20.0).abs() < 1e-9);
assert!(price_to_earnings(100.0, 0.0).is_infinite()); // loss → undefined
```

## `price_to_book`

```rust
pub fn price_to_book(price: f64, book_value_per_share: f64) -> f64
```

**Price-to-book ratio** = `price ÷ book value per share`.

- Returns `f64::INFINITY` when `book_value_per_share <= 0.0` (negative equity).
- A P/B **below 1.0** means the stock trades below its accounting book value.

```rust
use value_metrics::price_to_book;

assert!((price_to_book(50.0, 25.0) - 2.0).abs() < 1e-9);
assert!((price_to_book(20.0, 25.0) - 0.8).abs() < 1e-9); // below book
```

## `earnings_yield`

```rust
pub fn earnings_yield(eps: f64, price: f64) -> f64
```

**Earnings yield** = `eps ÷ price` — the reciprocal of P/E.

- Returns `0.0` when `price <= 0.0`.
- Expressed as a fraction; multiply by 100 for a percentage comparable to a
  bond yield. A 20 P/E → 5% earnings yield.

```rust
use value_metrics::{earnings_yield, price_to_earnings};

let ey = earnings_yield(5.0, 100.0);  // 0.05
let pe = price_to_earnings(100.0, 5.0); // 20.0
assert!((pe * ey - 1.0).abs() < 1e-9);  // reciprocal relationship
```

## `ev_to_ebit`

```rust
pub fn ev_to_ebit(enterprise_value: f64, ebit: f64) -> f64
```

**EV/EBIT** = `enterprise value ÷ EBIT`.

- Returns `f64::INFINITY` when `ebit <= 0.0`.
- Preferred by many value investors over P/E because it is **neutral to
  capital structure** (EV includes debt and cash, not just market cap).

```rust
use value_metrics::ev_to_ebit;

assert!((ev_to_ebit(1_000_000.0, 100_000.0) - 10.0).abs() < 1e-9);
```

## `dividend_yield`

```rust
pub fn dividend_yield(dividend_per_share: f64, price: f64) -> f64
```

**Dividend yield** = `annual dividend per share ÷ price`.

- Returns `0.0` when `price <= 0.0`.
- Fractional form; multiply by 100 for a percentage.

```rust
use value_metrics::dividend_yield;

assert!((dividend_yield(4.0, 100.0) - 0.04).abs() < 1e-9); // 4%
```

!!! note "Ratios are a starting point, not a verdict"
    A low multiple can mean cheap, or it can mean the market expects earnings
    to collapse. Always pair a ratio with quality and growth context. For a
    full ranked screen with sector medians and value traps flagged, use the
    [DeepValueRadar on api deepvalueradar value](https://deepvalueradar.com/).
