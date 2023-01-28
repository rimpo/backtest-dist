# BACKTEST

A free backtesting tool for doing intraday strategy.

## Supports features

- yml based strategy configration
- strategy level mtm stoploss
- individual leg level ltp stoploss. tracking spot for stoploss
- add as many as leg possible.

## Installation

### 1. Download the json from site mentioned in the discord coding channel

```
downloader.exe -username=<user-id> -password=<password> -symbol=banknifty
downloader.exe -username=<user-id> -password=<password> -symbol=nifty
```

This need to be done only once will mostly take 20-30 mins

### 2. Imports the downloaded data to the local database

```
db-importer.exe -db-path=./db/log -purge
```

purge option will delete all the current data in database (if exist)

You are ready for running strategy.

## Usage

```
strategy -start-dt=2018-01-01 -end-dt=2022-12-30 -db=./db/log/nse.db -config=./config/strangle.yml -output=./output.csv
```

- output.csv will contain the order executed in the strategy which can be used to analyze the results.
- config contains all the strategy input
  - daily MTM based SL
  - individual leg based SL (time, LTP percent, change of percent in spot or future)
  - N number of legs supported.
  - dynamic strike selection (near ltp, range ltp)

## Note

1. downloader.exe downloads from website directly. Please don't over use it and overload the servers.
2. All commands have -h for help please go through them
3. There are few files which will be generated (e.g. dates_banknifty.cfg or dates_banknifty_skipped.cfg) in local path, Please dont delete these files as these are used by the strategy command all time.
4. The database created is of almost 6+ GB (with extension .db).
5. To check the date ranges available or downloaded, go to /historical_data/dates_<symbol>.cfg
6. Only windows binaries (if you need for linux/mac. Please let me know)
