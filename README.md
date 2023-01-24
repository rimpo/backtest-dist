# BACKTEST 


## Step 1:

```
downloader.exe -username=<user-id> -password=<password> -symbol=banknifty
downloader.exe -username=<user-id> -password=<password> -symbol=nifty
```


## Step 2:

```
db-importer.exe -db-path=./db/log -purge
```
- purge option will delete all the current data in database


## Step 3:

```
strategy -start-dt=2018-01-01 -end-dt=2022-12-30 -db=./db/log/nse.db -config=./config/strangle.yml -output=./output.csv
```

- config contains all the strategy input.
- output.csv contains the order executed in the strategy



## Note: 
1. downloader.exe downloads from website directly. Please don't over use it and overload the servers.
2. All commands have -h for help please go through them
3. There are few files which will be generated (e.g. dates_banknifty.cfg or dates_banknifty_skipped.cfg) in local path, Please dont delete these files as these are used by the strategy command all time.
4. The database created is of almost 6+ GB (with extension .db).
