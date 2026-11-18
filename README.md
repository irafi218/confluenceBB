# 📈 BB Breakout Trading Bot

A professional algorithmic trading bot implementing a **Bollinger Band Breakout** strategy with full backtesting, live trading support, risk management, and a Streamlit dashboard.

---

## 🗂 Project Structure

```
bb_trading_bot/
├── config/
│   ├── config.yaml          # Master configuration
│   └── .env.example         # Credential template → copy to .env
├── broker/
│   └── binance_client.py    # Binance REST + WebSocket wrapper
├── strategies/
│   └── bb_breakout.py       # Backtrader strategy (signals, sizing, exits)
├── risk/
│   └── risk_manager.py      # Pre-trade checks, drawdown limits, sizing
├── database/
│   └── db_manager.py        # SQLite: OHLCV, trades, equity, backtest results
├── dashboard/
│   ├── app.py               # Streamlit entry point
│   └── pages/
│       ├── overview.py      # Portfolio KPIs & equity curve
│       ├── trade_log.py     # Filterable trade journal
│       ├── backtest_page.py # Backtest runner & history
│       └── config_page.py   # Live config viewer
├── scripts/
│   ├── run_backtest.py      # CLI backtest runner
│   └── download_data.py     # Historical data downloader
├── utils/
│   ├── config_loader.py     # YAML + .env loader
│   └── logger_setup.py      # Rotating file + console logging
├── tests/
│   └── test_strategy.py     # Unit tests (risk manager, sizing)
├── data/
│   ├── historical/          # Optional CSV data
│   └── exports/             # CSV trade exports
├── logs/                    # Rotating log files
└── requirements.txt
```

---

## 🚀 Quick Start

### 1. Install dependencies
```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Configure credentials
```bash
cp config/.env.example config/.env
# Edit config/.env — add your Binance API key/secret
# Edit config/config.yaml — adjust strategy & risk parameters
```

### 3. Download historical data
```bash
python scripts/download_data.py --symbol BTCUSDT --timeframe 1h --start 2023-01-01
```

### 4. Run a backtest
```bash
python scripts/run_backtest.py --symbol BTCUSDT --start 2023-01-01 --end 2024-12-31
```

### 5. Launch dashboard
```bash
streamlit run dashboard/app.py
```

### 6. Run tests
```bash
pytest tests/ -v
```

---

## 📐 Strategy Logic

| Signal | Condition |
|--------|-----------|
| **Long entry** | Close crosses **above** upper BB + optional volume spike |
| **Short entry** | Close crosses **below** lower BB + optional volume spike |
| **Stop-loss** | Entry ± (ATR × `stop_loss_atr_mult`) |
| **Take-profit** | Entry ± (stop distance × `take_profit_rr`) |
| **Trailing stop** | Optional % trail after entry |
| **Squeeze filter** | Skip trades when BB bandwidth < `squeeze_threshold` |

---

## ⚙️ Key Config Parameters

```yaml
strategy:
  bb_period: 20          # Bollinger Band lookback
  bb_std_dev: 2.0        # Band width in standard deviations
  volume_confirm: true   # Require volume > MA × multiplier

risk:
  max_position_pct: 0.10        # Max 10% of portfolio per trade
  stop_loss_atr_mult: 2.0       # Stop = entry ± 2 × ATR
  take_profit_rr: 2.0           # 2:1 reward-to-risk
  max_daily_drawdown_pct: 0.05  # Halt at 5% daily drawdown
  max_total_drawdown_pct: 0.15  # Halt at 15% total drawdown
```

---

## ⚠️ Disclaimer

This software is for **educational purposes only**. Trading cryptocurrencies carries significant financial risk. Always test thoroughly on paper/testnet before committing real capital. Past backtest performance does not guarantee future results.
