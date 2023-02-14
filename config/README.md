# **Guide for updating config yml**

The config used by strategy is in yml format e.g [straddle](./straddle.yml)

There are multiple sections in the yml. Please go through the below sections for understanding the config

## Strategy

| Fields | Description| Example |
| ------ | --------------| ---------------|
| name  | Name of the strategy | straddle or strangle or `any plain text` |
| slippage | Slippage of the strategy | `1.0` or `0.5` |
| symbol | Symbol for which you want the strategy to run |  `nifty` or `banknifty` |
| days_to_run | Days the strategy will run values:`0=Sunday 1=Monday 2=Tuesday 3=Wednesday 4=Thursday 5=Friday 6=Saturday` | `3,4` (will only run on Wed and Thu )

The strategy can be stopped using these stop conditions

### Stop

A strategy can have multiple stop conditions. All legs will be closed on any one conditions hit.

**Note: if in same candle stop and leg exit conditions is hit then exit takes precedence for the leg.**

| Type | Fields | Example | Description |
| ----- | ----------- | ----------- | ------------ |
| stop_type_time | time | `13:20` | The strategy will be stopped at 13:20. |
| stop_type_mtm_amount | amount | `5000` or `-10000` | The strategy will be stopped when the profit for the day is hit. Can be negative (loss )or positive (profit)|

A strategy can have multiple Legs

## Legs

| Fields | Description| Example |
| ------ | --------------| ---------------|
| lots  | Number of lots to trade | `1` or `10` (or any number) |
| buy_or_sell | Buy or sell  | Yes | `buy` or `sell`|
| ce_or_pe | To signify if CE leg or PE leg  | `CE` or `PE` |

### Strike Selection

| Type | Fields | Example | Description |
| ----- | ----------- | ----------- | ------------ |
| strike_selection_type_distance_from_atm | distance_from_atm | 0 or 200 or 150 | Please make sure for nifty it should be in multiple of 50 and for banknifty multiple of 100. Note 0 signifies ATM |
| strike_selection_type_near_ltp | near_ltp | 25 | strategy will find next strike available whose premium is less than 25 |
| strike_selection_type_range_ltp | min_ltp, max_ltp | 10, 15 | strike with premium between (10, 15) will be selected |

### Entry

The leg can have multiple entry conditions. In case of any one condition is hit an entry position will be created.

| Type | Fields | Example | Description |
| ----- | ----------- | ----------- | ------------ |
| entry_type_time | time | `13:20` | entry will be created for leg on time 13:20 |
| entry_type_on_leg_exit | leg_id | `0` or `1` | The leg will enter if the leg_id is exited. Note the leg_id are an auto number starting from first leg entry in yml which be leg_id=0, the next one will be 1 and increasing in that order. |

Note: The dependencies of on other leg should be upward. Otherwise, we might face issue like the entry on exit is happening in next minute.

Entry Price logic can be controlled by setting entry_price_type

```
e.g:
    - type: entry_type_time
    points: -190.0
    underlying: spot
    entry_price_type: entry_price_type_use_open      
```

| Entry Price Type | Description |
| ------------- | ------------ |
| entry_price_type_use_open | open of OHLC will be the entry price |
| entry_price_type_use_close (default) | close of OHLC will be the entry price |

if not set default will be used.

### Exit

The leg can have multiple exit conditions. In case of any one condition is hit the leg will exit.

| Type | Fields | Example | Description |
| ----- | ----------- | ----------- | ------------ |
| exit_type_time | time | `15:20` | leg will exit at time 15:20 |
| exit_type_sl_percent | percent | `40.0` | leg will exit when premium price will increase by 40% from entry price. Note high will be considered for sell orders (low for buy orders) |
| exit_type_underlying_moved_percent | percent | `1.0` | leg will exit when underlying moves by 1% |
|  | underlying | `spot` or `future` |  |
| exit_type_underlying_moved_points | points | `200` or `-200` | leg will exit when underlying moves by 200 or -200 |
|  | underlying | `spot` or `future` |  |
| exit_type_on_leg_exit | leg_id | `0` or `1` | The leg will exit if the leg_id is exited. First leg entry in yml will be leg_id=0 and the next one will be 1.  |
| exit_type_sl_percent_on_leg_exit_new_sl_percent | percent | `35.0` |  same as exit_type_sl_percent
| | leg_id | `0` or `1`| The leg which exit will cause the current spot loss move to cost (same as entry price)

Note: The dependencies of other leg if not upward in yml, the trigger will happen in next tick (or minute)

Exit Price logic can be controlled by setting exit_price_type

```
e.g:
    - type: exit_type_underlying_moved_points
    points: -190.0
    underlying: spot
    exit_price_type: exit_price_type_corrected_underlying          <------- calculation below
```

| Exit Price Type | Description |
| ------------- | ------------ |
| exit_price_type_use_close (default) | close of the candle will be picked |
| exit_price_type_use_open | open of the candle will be picked |
| exit_price_type_corrected_underlying | calculation based on underlying candle size |

if not set default will be used.

# Understanding calculation by signals

OHLC = O -> Open, H -> High, L -> Low, C -> Close

## entry_type_time & exit_type_time

if exit_price_type set `exit_price_type_use_open`:

```
    The premium is selected from the candle open.

    Example:
    ```
    2022-12-19 09:19 OHLC 314.09 324.00 286.65 288.00
    2022-12-19 09:20 OHLC 294.06 304.25 281.04 304.25
    2022-12-19 09:21 OHLC 304.15 314.95 270.25 270.25
    ```
    if entry_type_time is set 09:20 the premium used for enter the leg is `304.25`.
```

if exit_price_type set `exit_price_type_use_close`:

```
    The premium is selected from the candle close.

    Example:
    ```
    2022-12-19 09:19 OHLC 314.09 324.00 286.65 288.00
    2022-12-19 09:20 OHLC 294.06 304.25 281.04 304.25
    2022-12-19 09:21 OHLC 304.15 314.95 270.25 270.25
    ```
    if entry_type_time is set 09:20 the premium used for enter the leg is `294.06`.
```

## exit_type_sl_percent uses High of the candle to check trigger

Entry Price with Slippage is considered in this calculation (slippage from yml configuration)
The high/low is checked for condition but the exit price is calculated based on percentage set.

Condition:

For positive `percent`:

- entryPrice + entryPrice*`percent`/100.0 < candle.High

For negative `percent`:

- entryPrice - entryPrice*`percent`/100.0 > candle.Low

Example:

- entryPrice = 237.85  (slippage from yml is considered)
- percent = 40.0

```
2022-12-19 12:36 OHLC 322.07 325.55 319.45 322.03
2022-12-19 12:37 OHLC 323.75 333.07 322.95 333.06
2022-12-19 12:38 OHLC 333.01 335.00 329.75 333.05
```

Calculation: 237.85 + (237.85 * 40.0)/100.0 < 333.07 = 332.99 < 333.0

Therefore, The trigger will happen for candle:`2022-12-19 12:37` high 333.07
The exitPrice will be picked `332.99` and the time of exit `2022-12-19 12:37` (the slippage will be additionally added in the end)

## exit_type_underlying_moved_percent & exit_type_underlying_moved_points when exit_price_type=exit_price_type_corrected_underlying

- underlyingEntryOpen = Open of Underlying at Entry
- underlyingLatestHigh = Current Candle High of Underlying
- underlyingLatestLow = Current Candle Low of Underlying

For positive `percent`:

- ((underlyingLatestHigh - underlyingEntryOpen) / underlyingEntryOpen )*100 > `percent`

For negative `percent`:

- ((underlyingLatestLow - underlyingEntryOpen) / underlyingEntryOpen )*100 < `percent`

In case of the above condition is hit, the exit price is a calculated price.

- Underlying OHLC = U_O, U_H, U_L, U_C
- Option Leg OHLC = O, H, L, C

CE Option Leg:

- correction percentage = CP = (U_L - U_C() / (U_H - H_L)
- Exit Price = CP * (H - L) + L

PE Option Leg:

- correction percentage = CP = (U_H - U_C() / (U_H - H_L)
- Exit Price = CP * (H - L) + L

Note: The important distinction for these signals is that it will add a fixed additional 0.5 slippage to exit_price (slippage of yml is calculated on top of it).
