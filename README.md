# ExloBot 

ExloBot is a Python bot implementing several strategies. It can work with any assets pair on the [PolarityEx](https://polarity.exchange/) and [TurtleNetwork](https://wallet.turtlenetwork.eu/)

The bot is based on [MortyBot](https://github.com/iammortimer/MortyBot)

Currently included strategies are: GRID, SCALP, UPTREND and MOVING_GRID

## Grid Trading
Grid trading doesn’t care about which way the market’s going — in fact, as a profitable strategy it works best in ranging markets. The strategy places a ladder of sells at regular intervals above market price, and another ladder of buys beneath it. If a sell is filled, those funds are used to place a buy just beneath that sell. Thus you can think of the grid as a series of pairs of buys/sells stretching up and down the price chart, with either the buy or sell in each pair always active.

For example, let’s say the last price is 2000 satoshis you’ve got sells laddered up at 2100, 2200, 2300… If the price hits 2100, you immediately use those funds to place a new buy at 2000. If it drops to 2000 again, you buy back the Incent you sold at 2100. If it rises further, you sell at 2200 and open a buy at 2100. Whichever way the price moves, you’re providing depth — buffering the market and smoothing out any peaks and troughs. Additionally, if you open and then close a trade within a tranche (e.g. you sell at 2200, then buy back at 2100) then you make a small profit.

## Moving Grid Trading
Moving Grid trading takes the bases of Grid trading, but ads the functionality to recalculate the Grid when the market moves out of the Grid range. Ones the Bot detects that the last filled trade is the last sell order in the grid it will reinitialise the Grid for the current market conditions. This will only work of you haven't set a constant price for base calculations and the grid type is set to SYMMETRIC.

## Scalp Trading
Scalping exploits small changes in currency prices: it buys at the mean price minus some step and sells at the mean price plus some step, in order to gain the bid/ask difference. It normally involves establishing and liquidating a position quickly.

For example, let's trade on TN-BTC pair (TN is an amount asset and BTC is a price_asset). The spread mean price is ```(best_bid + best_ask) / 2```. The price step is ```0.5%``` from mean price. ExloBot will place the buy order at price ```meanprice * (1 - price_step)``` and the amount ```(BTC_balance / bid_price) - order_fee```. The sell order is placed at ```meanprice * (1 + price_step)``` and the amount equal to ```TN_balance - order_fee```.
### The SCALP strategy will use the total balance in the set wallet for trading the selected pair, so use a seperate trader wallet!

## Uptrend Trading
Updrend trading is used when you expect for a pair to start pumping but don't want to monitor it the whole time. The PROFITMARGIN and STOPLOSS values are percentage values that you set. ExloBot will buy the set currency for current market price and place a sell order at current market price + profitmargin. Furthermore it will calculate the stoploss value at current market price - stoploss. If the market goes up and the sell order is hit, it will repeat the process. If the market goes down and the stoploss is hit, ExloBot will liquidate ALL of the set currency at the last available ask (either stoploss price or below).
### The UPTREND strategy will use the total balance in the set wallet for trading the selected pair, so use a seperate trader wallet!

## Getting Started

Create a new wallet for use with the trader bot and move the funds you want to use over to it.

ExloBot requires Python 2.7 or 3.x and the following Python packages:

* PyWaves
* ConfigParser (with Python 2.7)
* configparser (with Python 3.x)

You can install them with

```
pip install pywaves
pip install ConfigParser (python 2.7)
pip install configparser (python 3.x)
```

You can start ExloBot with this command:

```
python ExloBot.py sample-bot.cfg
```

below you can find a sample configuration file:
```
[network]
node = https://privatenode2.blackturtle.eu
network = turtlenetwork
matcher = https://privatematcher.blackturtle.eu
datafeed = https://bot.blackturtle.eu
order_fee = 4000000

[main]
order_lifetime = 86400 
sleeptimer = 5 
strategy = grid

[account]
private_key = XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

[market]
amount_asset = TN
price_asset = 8LQW8f7P5d5PZM7GtZEBgaqRPGSzS3DfPuiXrURJ4AJS

[grid]
interval = 0.005
tranche_size = 200000000
grid_levels = 20
base = last
type = symmetric

[uptrend]
profitmargin = 0.05
stoploss = 0.03

[logging]
logfile = bot.log
```

#### network section
```node``` is the address of the fullnode

```matcher``` is the matcher address

```order_fee``` is the fee to place buy and sell orders

#### main section
```order_fee``` is the fee to place buy and sell orders

```sleeptimer``` is the number of seconds the bot waits before rechecking orders

```strategy``` is the strategy to use (grid, moving_grid, uptrend or scalp)

#### account section
```private_key``` is the private key of the trading account

#### market section
```amount_asset``` and ```price_asset``` are the IDs of the traded assets pair

#### grid section
```interval``` is the % interval between grid levels

```tranche_size``` is the size amount of each buy and sell order

```grid_levels``` is the number of grid levels

```base``` is the price level around which the grid is setup; it can be LAST, for the last traded price, BID for the current bid price, ASK for the current ask price or a fixed constant price can be specified

```flexibility``` amount flexibility in percent, 20% flexibility means that the amount of the order might flucture +/- 10% around the defined tranche_size

```type``` the initial grid can be SYMMETRIC, if there are both buy and sell orders; BIDS if the are only buy orders; ASKS if there are only sell orders

#### uptrend section
```profitmargin``` is the profit % where your sellorder will be set - marketprice + profitmargin %

```stoploss``` is the loss % you are willing to take - marketprice - stoploss %

#### logging section
```logfile``` is the file where the log will be written
