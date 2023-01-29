# **Guide for updating Config yml**

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

| Type | Fields | Example | Descritption |
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

| Type | Fields | Example | Descritption |
| ----- | ----------- | ----------- | ------------ |
| strike_selection_type_distance_from_spot | distance_from_spot | 0 or 200 or 150 | Please make sure for nifty it should be in multiple of 50 and for banknifty multiple of 100. Note 0 signifies ATM |
| strike_selection_type_near_ltp | near_ltp | 25 | strategy will find next strike available whose premium is less than 25 |
| strike_selection_type_range_ltp | min_ltp, max_ltp | 10, 15 | strike with premium between (10, 15) will be selected |

### Entry

The leg can have multiple entry conditions. In case of any one condition is hit an entry position will be created.

| Type | Fields | Example | Descritption |
| ----- | ----------- | ----------- | ------------ |
| entry_type_time | time | `13:20` | entry will be created for leg on time 13:20 |

### Exit

The leg can have multiple exit conditions. In case of any one condition is hit the leg will exit.

| Type | Fields | Example | Descritption |
| ----- | ----------- | ----------- | ------------ |
| exit_type_time | time | `15:20` | leg will exit at time 15:20 |
| exit_type_ltp_loss_percent | percent | `40.0` | leg will exit when premium price will increase by 40% from entry price |
| exit_type_underlying_moved_percent | percent | `1.0` | leg will exit when underlying moves by 1% |
|  | underlying | `spot` or `future` |  |
| exit_type_underlying_moved_points | points | `200` or `-200` | leg will exit when underlying moves by 200 or -200 |
|  | underlying | `spot` or `future` |  |
