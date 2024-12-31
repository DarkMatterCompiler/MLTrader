
# MLTrader: Alpaca Trading Strategy with Sentiment Analysis

This project implements an automated trading strategy using the [Lumibot](https://lumibot.org/) framework in conjunction with Alpaca’s paper trading API. The strategy incorporates sentiment analysis of news headlines using [FinBERT](https://github.com/yiyanghkust/FinBERT), a pre-trained model for financial sentiment analysis.

## Features
- **Alpaca Paper Trading**: The strategy executes buy and sell orders based on market sentiment extracted from financial news.
- **Sentiment Analysis**: Uses the FinBERT model to estimate sentiment (positive or negative) from news headlines.
- **Position Sizing**: Determines the number of shares to buy or sell based on available cash and a specified percentage of risk.
- **Bracket Orders**: The strategy places bracket orders (buy/sell with take-profit and stop-loss) to automate trading actions.
  
## Requirements
- Python 3.8+
- Alpaca account with paper trading enabled
- FinBERT and other dependencies for sentiment analysis

## Installation

To get started with this project, clone this repository and install the required dependencies:

```bash
git clone https://github.com/your-repository/mltrader.git
cd mltrader
pip install -r requirements.txt
```

### Dependencies
1. **lumibot**: A framework for algorithmic trading with support for backtesting and live trading.
2. **Alpaca API**: API for interacting with Alpaca’s paper trading account.
3. **FinBERT**: A pre-trained model for financial sentiment analysis.
4. **dotenv**: Used to securely manage environment variables, such as Alpaca API keys.

## Setup

1. **Alpaca API Keys**:  
   Sign up for an Alpaca account and generate your API keys.  
   Store your API keys in a `.env` file at the root of the project:

```plaintext
ALPACA_API_KEY=your_api_key
ALPACA_API_SECRET=your_api_secret
```

2. **FinBERT Sentiment Analysis**:
   The project uses [FinBERT](https://github.com/yiyanghkust/FinBERT) for sentiment analysis of financial news headlines. The `finbert_utils.py` file is assumed to have an `estimate_sentiment` function that takes a list of headlines as input and returns a sentiment label and probability.

   To use FinBERT:
   - Clone the [FinBERT repository](https://github.com/yiyanghkust/FinBERT).
   - Install the dependencies in FinBERT as needed (you might want to use its `requirements.txt`).

   Example of how `estimate_sentiment` works:
   
   ```python
   def estimate_sentiment(news):
       # Perform sentiment analysis with FinBERT
       # Returns probability and sentiment (positive/negative)
   ```

## Usage

The strategy `MLTrader` follows these steps:

1. **Position Sizing**: 
   It calculates how many shares to buy or sell based on the available cash and a predefined risk threshold.
   
2. **Sentiment Analysis**:
   The strategy fetches financial news headlines for the past 3 days and uses FinBERT to analyze the sentiment. If the sentiment is highly positive (probability > 0.8), it places a **buy** order. If the sentiment is negative (probability > 0.8), it places a **sell** order.

3. **Trading Logic**:
   - If the sentiment is positive, the strategy places a **buy order**.
   - If the sentiment is negative, the strategy places a **sell order**.
   - Orders are placed with **take-profit** and **stop-loss** limits.

### Example Workflow:

```python
from datetime import datetime
from lumibot.brokers import Alpaca
from lumibot.strategies.strategy import Strategy
from lumibot.backtesting import YahooDataBacktesting
from finbert_utils import estimate_sentiment
import os
from dotenv import load_dotenv

# Load Alpaca API credentials
load_dotenv()
API_KEY = os.getenv("ALPACA_API_KEY")
API_SECRET = os.getenv("ALPACA_API_SECRET")

# Setup Alpaca broker and strategy
broker = Alpaca({"API_KEY": API_KEY, "API_SECRET": API_SECRET, "PAPER": True})
strategy = MLTrader(name='mlstrat', broker=broker, parameters={"symbol": "SPY", "cash_at_risk": 0.5})

# Backtesting the strategy
start_date = datetime(2023, 1, 1)
end_date = datetime(2023, 12, 31)
strategy.backtest(YahooDataBacktesting, start_date, end_date, parameters={"symbol": "SPY", "cash_at_risk": 0.5})
```

## Backtesting

The strategy is designed to be backtested using historical data. The `YahooDataBacktesting` class is used to simulate trading over a specified period. This can be run to evaluate the strategy's performance before using it for live trading.

```python
# Backtesting with YahooFinance data
strategy.backtest(
    YahooDataBacktesting,
    start_date,
    end_date,
    parameters={"symbol": "SPY", "cash_at_risk": 0.5}
)
```

## Live Trading

For live trading with Alpaca, you can uncomment and execute the following code:

```python
from lumibot.traders import Trader

# Set up trader and run strategy
trader = Trader()
trader.add_strategy(strategy)
trader.run_all()
```

### Notes on Risk Management:
- **Cash at Risk**: This strategy calculates the number of shares to buy based on a percentage of available cash (`cash_at_risk`).
- **Stop-Loss and Take-Profit**: The strategy automatically sets a stop-loss and take-profit for each order to help manage risk.

## License

This project is licensed under the MIT License.

## Acknowledgments

- **Lumibot**: A powerful framework for creating, backtesting, and deploying trading strategies.
- **Alpaca**: A commission-free trading platform for building algorithmic trading systems.
- **FinBERT**: A financial sentiment analysis model based on BERT.

---

If you encounter any issues or have questions, feel free to raise an issue or pull request!

