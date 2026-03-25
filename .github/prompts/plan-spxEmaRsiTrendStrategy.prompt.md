## Plan: SPX 5-Minute EMA/RSI Trend Strategy with ATR-Based Exits

Build a backtesting system for SPX using 9/15 EMA crossovers, RSI momentum filter, and ATR-based risk management on 5-minute data over 1 year.

**Steps**

1. **Data acquisition setup** - Fetch SPX cash index 5-minute data (ticker: ^GSPC or ^SPX) using yfinance for maximum available history (typically ~60 days; yfinance doesn't provide ES futures, so we use cash index)

2. **Calculate technical indicators** (*parallel with step 3*) - Compute 9 EMA, 15 EMA, 14-period RSI, and ATR for stop-loss calculations

3. **Implement crossover detection** (*parallel with step 2*) - Detect 9 EMA crossing above/below 15 EMA with state tracking to avoid duplicate signals

4. **Apply entry filters** (*depends on steps 2, 3*) - Wait for EMA gap to widen to 2-4 points after crossover; check RSI conditions (bullish: RSI > 30, bearish: RSI < 50)

5. **Position management logic** (*depends on step 4*) - Track open positions, prevent overlapping trades, calculate position sizes based on 1% risk per trade ($500 max risk from $50,000 portfolio); enter 2 contracts per trade with stop loss at entry ± ATR; use $50/point multiplier (simulating ES futures) for P&L calculations

6. **Exit implementation** (*depends on step 5*) - Set initial stop loss at entry ± ATR for both contracts; exit first contract at 1:1 R:R (target = entry ± ATR); for second contract: when profit reaches 2 points (8 ticks), move stop to breakeven, then use trailing stop of 2 ticks (0.5 points) until reaching 2:1 R:R target (entry ± 2×ATR); if stop loss hit, exit remaining contracts

7. **Backtest execution** (*depends on step 6*) - Loop through historical data, generate buy/sell signals, track P&L with portfolio tracking (account starts at $50,000); calculate returns using $50 per index point for each contract, max drawdown, and account equity curve over time

8. **Visualization and reporting** (*depends on step 7*) - Plot price with EMAs, mark buy/sell points, display trade log with entry/exit prices and P&L

**Relevant files**

- [trend-trading.ipynb](trend-trading.ipynb) — Add cells for: data fetching, indicator calculation (pandas_ta or manual), signal generation functions, position tracking class, backtest loop, and visualization with matplotlib

**Verification**

1. Verify data fetched covers maximum available history at 5-minute intervals (check row count ~56,000+ for 60 days × 6.5 hours × 12 bars/hour)
2. Confirm indicator calculations: plot EMAs, RSI, ATR to visually validate values
3. Test crossover detection: manually inspect 2-3 crossover events match actual chart patterns
4. Validate entry filters: check that trades only trigger when gap reaches 2-4 points and RSI conditions met
5. Verify exit logic: confirm initial stop loss = entry ± 1 ATR for both contracts, first contract exits at 1:1 R:R; for second contract, verify stop moves to breakeven at +2 points profit, then trails by 2 ticks (0.5 points) until 2:1 R:R target
6. Review final trade log: ensure no overlapping positions, all trades have entry + exit records for both contracts
7. Calculate metrics: total return, win rate, average trade duration, maximum drawdown, ending account balance, number of trades
8. Verify position sizing: confirm max risk per trade = $500 (1% of $50,000), and only 2 contracts entered per signal

**Decisions**

- **Timeframe updated**: Using 5-minute data instead of 1-minute to maximize historical coverage (yfinance provides ~60 days of 5-min data)
- **RSI condition clarified**: Bearish uses RSI < 50 (not < 30), bullish uses RSI > 30
- **Gap timing**: Entry occurs AFTER crossover when gap widens to 2-4 points (not at crossover moment)
- **Data source & P&L**: Using SPX cash index (^GSPC) from yfinance with $50/point multiplier to simulate ES futures P&L
- **Position sizing**: $50,000 starting portfolio, max 2 contracts per trade, 1% max risk per trade ($500), position size calculated as: contracts = min(2, $500 / (ATR × $50))
- **Split exits with trailing stop**: Exit 1st contract at 1:1 R:R (profit = 1 × ATR × $50); for 2nd contract, move stop to breakeven when +2 points profit (8 ticks), then trail by 2 ticks (0.5 points) until 2:1 R:R target (profit = 2 × ATR × $50)
- **ATR period**: Will use standard 14-period ATR unless specified otherwise
- **Excluded scope**: Commission/slippage modeling, live trading automation, multiple timeframe analysis

**Further Considerations**

1. **EMA gap range** - Currently 2-4 points; should this be a tight filter (exactly 2-4) or minimum threshold (≥2)?
2. **Commission modeling** - Currently excluded; consider adding ~$2-5 per contract (ES futures rate) for more realistic results
