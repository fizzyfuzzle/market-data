# market-data

Historical instrument data for NautilusTrader backtesting. Files are stored as
GitHub Release assets, not committed to git — they're binary and don't diff
meaningfully, so there's no benefit to Git LFS or committing them directly;
this keeps clones of this repo instant regardless of how much data
accumulates.

## Where the data lives

Single release, tag `data`. Each instrument/timeframe combination is one
asset on that release, named:

```
<SYMBOL>_<timeframe>.parquet
```

e.g. `XAUUSD_1m.parquet`, `EURUSD_1m.parquet`.

Adding a new instrument: upload a new asset to the `data` release.
Updating an existing one: delete and re-upload that asset — there's no
versioning of individual files, since diffing binary data isn't useful here.

## Schema

Each parquet file is a pandas DataFrame with:
- A `DatetimeIndex` named `timestamp`, UTC
- Float columns: `open`, `high`, `low`, `close`, `volume`

This matches the input format NautilusTrader's `BarDataWrangler` expects
directly — no column renaming or timezone conversion needed when loading
into a `ParquetDataCatalog` or straight into a `BacktestEngine`.

## Fetching one instrument

Don't clone-and-pull everything — download only the asset(s) you need for a
given backtest:

```
curl -L -o XAUUSD_1m.parquet \
  https://github.com/fizzyfuzzle/market-data/releases/download/data/XAUUSD_1m.parquet
```
