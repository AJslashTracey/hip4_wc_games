# HIP 4 Markt Data research

Research archive for Hyperliquid HIP-4 World Cup markets.

During the World Cup I collected a large amount of HIP-4 market data on Hyperliquid on my server and tested several directions:

- market microstructure for selective market making
- lag detection between related markets
- execution feasibility under shallow books
- order-book impulse and liquidity event analysis

The main conclusion was negative but useful: there were visible inefficiencies, and in some cases clear lag, but the market quality was usually too thin to convert those signals into a robust deployable strategy. In practice, fills were the constraint. A naive MM in these markets would mostly behave like a money rinser unless it was extremely selective about where and when to quote. But as I am now looking again at the data there would have been a few opportunities but I collected so much data it was very difficult to search through it all during the knock out game phase maybe I will apply some findings on future HIP4 plays. 

## Research summary

Three areas produced the most signal:

- Microstructure: some markets had enough L2 and BBO coverage to study book pressure, spread behavior, and local liquidity shocks.
- Cross-market lag: related markets sometimes moved with observable delay, but exploitable size was usually not available at the prices you would need. I mainly looked for lag between the separate game markets and the implied probability of a team winning the World Cup.
- Execution simulation: market-making opportunity scores looked better on paper than in live fill-constrained conditions.

The practical bottleneck across almost all ideas was the same:

- weak depth at the top of book
- inconsistent executable size
- adverse selection risk after improving quotes
- insufficient fill quality even when directionally correct

## Best datasets

The strongest datasets are not the same for every use case.

### For microstructure research

1. `argentina_cape_verde_live`
2. `france_sweden_orderbook_live`
3. `france_morocco_live`

### For lag detection

1. `argentina_egypt_live`
2. `france_morocco_live`
3. `germany_paraguay_live` / `netherlands_morocco_live`

### For execution simulation

1. `france_morocco_live`
2. `france_sweden_orderbook_live`
3. `argentina_egypt_live`

`brazil_japan_live` is weaker for cross-market lag work specifically because champion-market BBO coverage was missing from the exported set, even though raw game-market files exist.

## Highlight dataset

`france_sweden_orderbook_live` is one of the best order-book research captures:

- `ws_l2book.jsonl`: 133,111 lines
- `ws_bbo.jsonl`: 4,965 lines
- `ws_trades.jsonl`: 1,744 lines
- `ws_l2_impulses.jsonl`: 3,606 lines
- `orderbook_pressure_events.csv`: 133,106 lines

That makes it especially useful for order-book impulse analysis, even if it is not the best trade-density dataset.

## Data layout

I cannot put the full datasets into this repo because they are too large for GitHub.

For public publication, the repo is intended to showcase how the timeseries are structured through sampled excerpts rather than full raw captures. The checked-in files under `data/samples/<dataset>/` are usually the first 3,000 lines of each included stream, with small metadata and event files left as-is.

The collectors generally subscribe to:

- websocket trades
- websocket BBO
- websocket L2 book
- websocket candles
- periodic REST snapshots for mids and books

[Hyperliquid websocket docs](https://hyperliquid.gitbook.io/hyperliquid-docs/for-developers/api/websocket)

The websocket files are event-driven, not fixed-frequency bars. A new line is written when Hyperliquid sends an update for a watched coin or when the collector derives an event from an update. The REST files are periodic snapshots used as lower-frequency reference data.

Typical dataset layout:

| File | Meaning | Frequency / size notes |
| --- | --- | --- |
| `metadata.jsonl` | Capture configuration, match metadata, game coins, related champion coins, and watch list. | One or a few records per run. |
| `ws_l2book.jsonl` | Raw websocket L2 order-book updates. | Event-driven; the densest stream. |
| `ws_bbo.jsonl` | Raw websocket best bid / best offer updates. | Event-driven; useful for executable spread and quote-age checks. |
| `ws_trades.jsonl` | Raw websocket trade prints. | Event-driven; sparse in thin markets. |
| `rest_mid_samples.csv` | Periodic REST mid-price snapshots across watched coins. | Periodic polling; the France/Sweden sample shows roughly 4-second polling across the watch list. |
| `ws_l2_impulses.jsonl` | Derived L2 impulse events. | Written only when configured depth, notional, or price-move thresholds trigger. |
| `orderbook_pressure_events.csv` | Derived book-pressure observations. | High-frequency derived series, usually close to L2 update density. |
| `event_shocks.jsonl` / `event_state_snapshots.jsonl` | Derived shock events and surrounding state snapshots. | Sparse event files for specific lag or impulse studies. |

Concrete scale example from the full `france_sweden_orderbook_live` capture:

| Stream | Full capture lines | Approx. rate over configured 12,000 seconds |
| --- | ---: | ---: |
| `ws_l2book.jsonl` | 133,111 | 11.1 lines/sec |
| `ws_bbo.jsonl` | 4,965 | 0.41 lines/sec |
| `ws_trades.jsonl` | 1,744 | 0.15 lines/sec |
| `ws_l2_impulses.jsonl` | 3,606 | 0.30 lines/sec |
| `orderbook_pressure_events.csv` | 133,106 | 11.1 lines/sec |

That capture watched six coins: the two game-market coins plus four related champion-market yes/no coins. Other captures follow the same broad structure but vary in duration, watched coins, market activity, and derived event files.

Some collectors also derive event files such as impulse or order-book pressure events.



## workflow:  

1. Run a market-specific collector during live trading.
2. Store raw websocket and REST snapshots.
3. Export local HIP-4 database snapshots when needed.
4. Run monitor or backtest tools against the stored data and see if I can exploit anything on the next game.
5. Compare apparent signal quality against actual executable liquidity.

This last step is where most ideas failed.

See [datasets.md](datasets.md), [visuals.md](visuals.md), and the checked-in
sample excerpts under [`data/samples/`](data/samples/).
