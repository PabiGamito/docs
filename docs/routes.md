# Routing

Routes are how you define which **symbol** to trade, in which **time frame**, at which **exchange**, and which **strategy** to use.

Routes are located at the `routes.py` file in your project. 

## Basic syntax

You must implement at least one route. Here is the basic syntax:

```py
(exchange, symbol, timeframe, strategy)
```

Working example:

```py
from jesse.enums import exchanges, timeframes

routes = [
    (exchanges.BINANCE, 'BTCUSDT', timeframes.HOUR_4, 'TrendFollowingStrategy'),
]
```

In this example, we're telling Jesse to trade `BTCUSDT` using `TrendFollowingStrategy` strategy at `Binance` exchange in `4h` time interval.

::: tip
Notice that I used enums. Instead of writing `'4h'`, I wrote `timeframes.HOUR_4`. This is optional but helps to prevent misspelling string.
:::

## Trading multiple routes

You can trade more than one route at the same time. The `routes` variable is a list, so we can put multiple routes in it:

```py
from jesse.enums import exchanges, timeframes

routes = [
    (exchanges.BINANCE, 'BTCUSDT', timeframes.HOUR_4, 'TrendFollowingStrategy'),
    (exchanges.BINANCE, 'ETHUSDT', timeframes.MINUTE_15, 'MeanReverseStrategy'),
]
```

::: warning
The `exchange` and `symbol` pairs must be unique.

That means you CAN trade `BTCUSDT` at the same time in both `Binance` and `Bitfinex`; but you CANNOT trade `BTCUSDT` in `Binance` on both `1h` and `4h` timeframes at the same time.

Why? Because exchanges support only one position per symbol and we want to keep it simple.
:::

## Using multiple time frames

You can use multiple time frames when writing strategies.

A typical example might be to use the daily time frame to detect the bigger trend of the market, and the hourly time frame to detect the smaller trend.

::: tip
This is a common feature that professional traders use in their manual trading. However, in algorithmic trading, it gets tricky because of the [Look-Ahead Bias](https://www.investopedia.com/terms/l/lookaheadbias.asp). This issue _is completely taken care of_ in Jesse.
:::

All you need to do is to define extra candles. The syntax for `extra_candles` is the same as `routes` except no need to define the _strategy_ name at the end.

For example, if you're trading `4h` time frame, and using `1D` time frame in your strategy, this is how your routes must look like:

```py
from jesse.enums import exchanges, timeframes

routes = [
    (exchanges.BINANCE, 'BTCUSDT', timeframes.HOUR_4, 'TrendFollowingStrategy'),
]

extra_candles = [
    (exchanges.BINANCE, 'BTCUSDT', timeframes.DAY_1),
]
```

::: warning
You may be thinking why not just define few extra routes and leave them be; whether or not using them. That would work; however, Jesse goes through expensive calculations to make extra candles work without the [Look-Ahead Bias](https://www.investopedia.com/terms/l/lookaheadbias.asp); hence, you will be facing longer backtest simulations.
:::
