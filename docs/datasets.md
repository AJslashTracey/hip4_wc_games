# Dataset Inventory

This is a practical ranking of the stored HIP-4 captures based on what is actually present in the files, not on how interesting the underlying match was.

## Ranking by use case

### Microstructure research

| Rank | Dataset | Why it ranks well |
| --- | --- | --- |
| 1 | `argentina_cape_verde_live` | Strong overall raw market-data coverage for book and quote behavior. |
| 2 | `france_sweden_orderbook_live` | Very strong L2 capture plus derived order-book event files. |
| 3 | `france_morocco_live` | Good all-around capture with additional event-oriented outputs. |

### Lag detection

| Rank | Dataset | Why it ranks well |
| --- | --- | --- |
| 1 | `argentina_egypt_live` | Best combination of related-market structure and usable comparison coverage. |
| 2 | `france_morocco_live` | Good balance of main-market and related observations. |
| 3 | `germany_paraguay_live` / `netherlands_morocco_live` | Useful but weaker than the top two. |

### Execution simulation

| Rank | Dataset | Why it ranks well |
| --- | --- | --- |
| 1 | `france_morocco_live` | Best dataset for simulating selective quoting under realistic liquidity constraints. |
| 2 | `france_sweden_orderbook_live` | Good for pressure and quote-improvement analysis. |
| 3 | `argentina_egypt_live` | Good research value, but execution quality remains limited. |

## Detailed note: france_sweden_orderbook_live

This is one of the strongest captures for order-book analysis:

| File | Lines |
| --- | ---: |
| `ws_l2book.jsonl` | 133,111 |
| `ws_bbo.jsonl` | 4,965 |
| `ws_trades.jsonl` | 1,744 |
| `ws_l2_impulses.jsonl` | 3,606 |
| `orderbook_pressure_events.csv` | 133,106 |

Interpretation:

- excellent for L2 event studies
- good for book pressure and local impulse detection
- less strong than the top trade-density datasets if the focus is purely trade-driven analysis

## Weakest datasets

These are least useful for serious research conclusions:

- `brazil_japan_live_test`
- `netherlands_morocco_live_smoke`
- `norway_ivory_coast_live_smoke`

They are still useful as pipeline artifacts because they show the collection stack working end-to-end.

## Important caveat

`brazil_japan_live` is not useless, but it is weaker for cross-market lag analysis because champion-market BBO coverage is missing from the exported set. Raw game-market files do exist, so the limitation is on coverage quality for that specific research question rather than total absence of data.
