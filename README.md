# BACKTEST

A free backtesting tool for doing intraday strategy.

## Supports features

- yml based strategy configration
- strategy level mtm stoploss
- individual leg level ltp stoploss. tracking spot for stoploss
- add as many as leg possible.

## Installation

### 1. Download the tick data from website

Visit <http://historical.maticalgos.com> to create your username and password for the commands given below. Once you have your user ID and password, you can open command prompt in your windows PC, navigate to the folder where you have downloaded, and unzipped these files, and run the below code as,

```
downloader.exe -username=xyz@abc.com -password=585243 -symbol=banknifty
downloader.exe -username=xyz@abc.com -password=585243 -symbol=nifty
```

open a new window of command prompt and do the same process for Nifty
downloader.exe -username=xyz@abc.com -password=585243 -symbol=nifty

Leave these two commands running for some time, as it might take 1-5 hours, or more, depending on your internet speed. Keep 10-20 GB space free in your PC for all the data of back-tests.

### 2. Imports the downloaded data to the local database

```
db-importer.exe -db-path=./db/log -purge
```

purge option will delete all the current data in database (if exist)
This process should complete in 15 mins.

You are ready for running strategy.

## Usage

```
strategy -start-dt=2018-01-01 -end-dt=2022-12-30 -db=./db/log/nse_bin.db -config=./config/strangle.yml -output=./output.csv
```

- output.csv will contain the order executed in the strategy which can be used to analyze the results.
- config contains all the strategy input
  - daily MTM based SL
  - individual leg based SL (time, LTP percent, change of percent in spot or future)
  - N number of legs supported.
  - dynamic strike selection (near ltp, range ltp)

Refer [Guide for updating Config yml](./config/README.md)

## Debuggging tool

search-db can be used to check the minute wise OHLC in the database for the instrument.

```
search-db -instrument-name=BANKNIFTY14MAR1929000PE -date=2019-03-14

output:
2019-03-14 09:15 OHLC 126.35 146.95 100.00 123.05
2019-03-14 09:16 OHLC 123.55 124.95 103.25 103.25
2019-03-14 09:17 OHLC 102.35 102.35 81.06 89.95
2019-03-14 09:18 OHLC 89.07 96.00 84.05 84.05
2019-03-14 09:19 OHLC 82.05 95.07 82.05 87.75
2019-03-14 09:20 OHLC 86.00 86.00 73.06 80.95
2019-03-14 09:21 OHLC 81.03 81.03 74.00 77.08
2019-03-14 09:22 OHLC 76.08 78.00 70.03 78.00
2019-03-14 09:23 OHLC 78.01 80.35 75.15 77.55
```

For spot use instrument-name as `BANKNIFTY` and for future use `BANKNIFTY-I` (same is applicable for nifty)

### Notes

1. downloader.exe downloads from website directly. Please don't over use it and overload the servers.
2. All commands have -h for help please go through them
3. There are few files which will be generated (e.g. dates_banknifty.cfg or dates_banknifty_skipped.cfg) in local path, Please dont delete these files as these are used by the strategy command all time.
4. The database created is of almost 6+ GB (with extension .db).
5. To check the date ranges available or downloaded, go to /historical_data/dates_<symbol>.cfg
6. Only windows binaries (if you need for linux/mac. Please let me know)
7. All performance time can vary systme to system.

## Calculation
