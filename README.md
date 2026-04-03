# ES Futures Trend Trading Strategy

A backtesting framework for a trend-following strategy on the S&P 500 using ES futures specifications. Built in Python with Jupyter notebooks.

---

## Overview

This project backtests a 9/15 EMA crossover strategy on SPX (^GSPC) 5-minute intraday data, simulating trades using ES futures contract sizing and margin requirements. The goal is to validate the strategy before potential live automation.

---

## Strategy Rules

### Entry Conditions
- **Long**: 9 EMA crosses above 15 EMA, RSI > 30, EMA gap between 2–4 points
- **Short**: 9 EMA crosses below 15 EMA, RSI < 50, EMA gap between 2–4 points
- Entry must occur within 20 bars after the crossover

### Position Sizing
- Trades 2 ES contracts per signal (when margin allows)
- **Contract 1**: 1:1 Risk/Reward — exits at 1× ATR target
- **Contract 2**: 2:1 Risk/Reward with trailing stop management

### Exit Rules
- Stop loss: 1× ATR from entry
- Contract 2 moves to breakeven after +2 points profit, then trails by 2 ticks (0.5 points)
- Positions closed at end of data if still open

---

## Technical Indicators

| Indicator | Parameters | Purpose |
|-----------|-----------|---------|
| EMA | 9-period | Fast trend line |
| EMA | 15-period | Slow trend line |
| RSI | 14-period | Momentum filter |
| ATR | 14-period | Stop loss & target sizing |

---

## ES Futures Specifications

| Spec | Value |
|------|-------|
| Point Value | $50 per point |
| Tick Size | 0.25 points |
| Tick Value | $12.50 per tick |
| Initial Margin | ~$14,000 per contract |
| Trailing Stop | 2 ticks (0.5 points = $25) |

---

## Backtest Configuration

| Parameter | Value |
|-----------|-------|
| Starting Capital | $50,000 |
| Max Risk per Trade | 1% of account |
| Account Buffer | 20% (not used for margin) |
| Data Source | Yahoo Finance (`yfinance`) |
| Timeframe | 5-minute bars (~60 days) |
| Ticker | ^GSPC (S&P 500) |

---

## Project Structure

```
trend-trading-analysis/
├── trend-trading.ipynb     # Main backtest notebook
├── trend-trading2.ipynb    # Experimental / alternate version
└── README.md
```

---

## Notebooks

### `trend-trading.ipynb`
The primary backtest notebook. Sections:
1. **Data Fetch** — Downloads 60 days of SPX 5-minute OHLCV data via `yfinance`
2. **Indicator Calculation** — Computes EMAs, RSI, ATR, and EMA gap
3. **Position Class** — Object-oriented position tracker with trailing stop logic
4. **Backtest Engine** — Iterates bars, manages signals, entries, exits, and account balance
5. **Performance Metrics** — Win rate, profit factor, max drawdown, per-contract breakdown
6. **Trade Log** — Full record of all executed trades
7. **Visualizations** — Equity curve, drawdown chart, price chart with trade signals and RSI

---

## Performance Output

The backtest reports:
- Total return ($) and percentage
- Max drawdown ($) and percentage
- Win rate and profit factor
- Average win / average loss
- Exit reason breakdown (Stop Loss, Take Profit, End of Data)
- Per-contract P&L breakdown

---

## Requirements

```
yfinance
pandas
numpy
matplotlib
```

Install dependencies:
```bash
pip install yfinance pandas numpy matplotlib
```

---

## Usage

1. Clone the repository
2. Install dependencies
3. Open `trend-trading.ipynb` in Jupyter or VS Code
4. Run all cells top to bottom

---

## Disclaimer

This project is for educational and research purposes only. Past backtest performance does not guarantee future results. Futures trading involves substantial risk of loss.

