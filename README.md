# value-metrics-docs

MkDocs Material documentation site for the [`value-metrics`](https://github.com/theluckystrike/deepvalueradar)
Rust crate — core equity valuation ratios: P/E, P/B, earnings yield, EV/EBIT,
and dividend yield.

**Live docs:** <https://theluckystrike.github.io/value-metrics-docs/>

The crate is the signal layer behind the
[DeepValueRadar value screener](https://deepvalueradar.com/).

## Build locally

```bash
pip install -r requirements.txt
mkdocs serve
# → http://127.0.0.1:8000
```

## Publish

The site is built and deployed automatically by GitHub Actions
(`.github/workflows/ci.yml`) on every push to `main`. GitHub Pages serves the
build artifact directly (Pages build type = workflow).
