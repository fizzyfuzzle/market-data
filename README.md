# market-data

Historical instrument data (OHLCV bars) for backtesting and analysis. Files
are stored as GitHub Release assets, not committed to git — they're binary
and don't diff meaningfully, so there's no benefit to Git LFS or committing
them directly; this keeps clones of this repo instant regardless of how much
data accumulates.

## Where the data lives

Single release, tag `schema-v1`. Each instrument/timeframe combination is one
asset on that release, named:

```
<SYMBOL>_<timeframe>.parquet
```

e.g. `XAUUSD_1m.parquet`, `EURUSD_1m.parquet`.

Adding a new instrument: upload a new asset to the `schema-v1` release.
Updating an existing one: delete and re-upload that asset — there's no
versioning of individual files' content, since diffing binary data isn't
useful here.

The tag versions the *schema*, not the data. Content changes (new
instruments, extended history, corrected values) never need a new tag —
they're just asset replacements on `schema-v1`. Only bump to `schema-v2` if
the file structure itself changes (columns added/renamed/retyped, index
changed, etc.) — something that would break a loader written against the
current format. That keeps anything referencing `schema-v1` working even
after a `schema-v2` exists for a reshaped format.

## Schema (schema-v1)

Each parquet file is a pandas DataFrame with:
- A `DatetimeIndex` named `timestamp`, UTC
- Float columns: `open`, `high`, `low`, `close`, `volume`

Plain OHLCV bars, usable with anything that reads parquet/pandas — no
tool-specific structure or assumptions about the consumer.

## Fetching one instrument

Don't clone-and-pull everything — download only the asset(s) you need for a
given backtest:

```
curl -L -o XAUUSD_1m.parquet \
  https://github.com/fizzyfuzzle/market-data/releases/download/schema-v1/XAUUSD_1m.parquet
```
