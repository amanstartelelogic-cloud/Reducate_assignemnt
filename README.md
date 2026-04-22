# Trading Bot - Binance Futures Testnet

A Python application for placing orders on Binance Futures Testnet (USDT-M). This trading bot provides a clean, reusable structure with proper logging, error handling, and input validation.

## Features

- ✅ Place **Market** and **Limit** orders on Binance Futures Testnet
- ✅ Support for **BUY** and **SELL** orders
- ✅ Command-line interface with Click
- ✅ Comprehensive input validation
- ✅ Detailed logging to file (API requests, responses, errors)
- ✅ Structured error handling with custom exceptions
- ✅ Clean layered architecture (API client → business logic → CLI)
- ✅ Type hints throughout codebase
- ✅ Unit tests for validators

## Project Structure

```
trading_bot/
├── bot/
│   ├── __init__.py                 # Package initialization
│   ├── cli.py                      # CLI entry point with Click commands
│   ├── client.py                   # Binance API client wrapper
│   ├── config.py                   # Configuration management (credentials, URLs)
│   ├── exceptions.py               # Custom exception classes
│   ├── logging_config.py           # Logging setup with file rotation
│   ├── orders.py                   # Order models and placement logic
│   └── validators.py               # Input validation functions
├── tests/
│   ├── __init__.py
│   └── test_validators.py          # Unit tests for validators
├── logs/                           # Log files (created at runtime)
├── .env.example                    # Environment variables template
├── .gitignore                      # Git ignore rules
├── pyproject.toml                  # Project configuration
├── requirements.txt                # Python dependencies
└── README.md                       # This file
```

## Setup Instructions

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)
- Binance Futures Testnet account with API credentials

### 1. Register Binance Futures Testnet Account

1. Visit [Binance Futures Testnet](https://testnet.binancefuture.com)
2. Register a new account or sign in
3. Go to API Management
4. Create a new API key with trading permissions
5. **Restricted IP**: Leave empty or set to your IP
6. **Restrictions**: Enable "Enable Futures"

### 2. Clone / Download the Project

```bash
git clone <repository-url>
cd trading_bot
```

Or extract the project directory.

### 3. Install Dependencies

```bash
# Using pip
pip install -r requirements.txt

# Or using pyproject.toml
pip install -e .
```

### 4. Configure Environment Variables

Create a `.env` file in the project root (copy from `.env.example`):

```bash
cp .env.example .env
```

Edit `.env` and add your Binance API credentials:

```
BINANCE_API_KEY=your_api_key_here
BINANCE_API_SECRET=your_api_secret_here
```

**Security Note**: Never commit `.env` to version control. It's listed in `.gitignore`.

### 5. Verify Setup

```bash
python -m bot.cli --help
```

You should see the CLI help message with available commands.

## How to Run

### Place a Market Order

```bash
python -m bot.cli place-order \
  --symbol BTCUSDT \
  --side BUY \
  --type MARKET \
  --quantity 0.001
```

**Output:**
```
============================================================
  Trading Bot - Order Placement
============================================================

📋 Order Request Summary:
  Symbol              : BTCUSDT
  Side                : BUY
  Type                : MARKET
  Quantity            : 0.001

Proceed with order placement? [Y/n]: y

============================================================
  Order Response
============================================================

  Order ID            : 12345678
  Status              : FILLED
  Side                : BUY
  Type                : MARKET
  Quantity            : 0.001
  Executed Qty        : 0.001
  Average Price       : 45000.50

✓ Order placed successfully! Order #12345678 - FILLED

📝 Logs saved to: logs/trading_bot.log

============================================================
```

### Place a Limit Order

```bash
python -m bot.cli place-order \
  --symbol ETHUSDT \
  --side SELL \
  --type LIMIT \
  --quantity 0.01 \
  --price 3500
```

### Batch Testing

Run multiple orders:

```bash
# Market order
python -m bot.cli place-order --symbol BTCUSDT --side BUY --type MARKET --quantity 0.001

# Limit order
python -m bot.cli place-order --symbol ETHUSDT --side SELL --type LIMIT --quantity 0.01 --price 3500
```

## Command-Line Options

```
Usage: python -m bot.cli place-order [OPTIONS]

  Place a market or limit order on Binance Futures Testnet (USDT-M).

Options:
  --symbol TEXT           Trading symbol (e.g., BTCUSDT)  [required]
  --side [BUY|SELL]       Order side: BUY or SELL  [required]
  --type [MARKET|LIMIT]   Order type: MARKET or LIMIT  [default: MARKET; required]
  --quantity FLOAT        Order quantity  [required]
  --price FLOAT           Order price (required for LIMIT orders)
  --help                  Show this message and exit.
```

## Logging

All API requests, responses, and errors are logged to `logs/trading_bot.log`.

### Log Format

```
2024-04-22 10:30:45 - bot.client - DEBUG - API Request: POST /fapi/v1/order
  Params: {
    "symbol": "BTCUSDT",
    "side": "BUY",
    "type": "MARKET",
    "quantity": 0.001,
    "timestamp": 1713787845000
  }
2024-04-22 10:30:46 - bot.client - DEBUG - API Response Status: 200
2024-04-22 10:30:46 - bot.client - INFO - Order placed successfully: orderId=12345678, status=FILLED
```

### View Logs

```bash
# View live logs
tail -f logs/trading_bot.log

# View last 50 lines
tail -n 50 logs/trading_bot.log

# Search for specific order
grep "orderId" logs/trading_bot.log
```

## Error Handling

The bot handles various error scenarios gracefully:

### Validation Errors

```bash
$ python -m bot.cli place-order --symbol BTC --side BUY --type MARKET --quantity -1

============================================================
  Validation Error
============================================================

✗ Order validation failed:

  ✗ Symbol must be at least 5 characters (e.g., BTCUSDT)
  ✗ Quantity must be greater than 0
```

### API Errors

```bash
$ python -m bot.cli place-order --symbol BTCUSDT --side BUY --type LIMIT --quantity 0.001 --price 5

============================================================
  Binance API Error
============================================================

✗ Binance returned an error (Code -1013):

  Filter failure: PRICE_FILTER
```

### Network Errors

The bot retries and logs connection issues:

```bash
============================================================
  Network Error
============================================================

✗ Failed to connect to Binance:

  Connection error: [Errno 11001] getaddrinfo failed
```

## Testing

Run unit tests for validators:

```bash
pytest tests/ -v
```

Run specific test file:

```bash
pytest tests/test_validators.py -v
```

Run with coverage:

```bash
pytest tests/ --cov=bot --cov-report=html
```

## Architecture

### Layered Design

```
CLI Layer (cli.py)
    ↓
Business Logic (orders.py)
    ↓
Validators (validators.py)
    ↓
API Client (client.py)
    ↓
Binance Testnet API
```

### Module Responsibilities

- **cli.py**: Command-line interface, user input, formatted output
- **orders.py**: Order request/response models, orchestration
- **validators.py**: Input validation with detailed error messages
- **client.py**: Binance API communication, HMAC-SHA256 signing, error parsing
- **config.py**: Credential and URL management
- **logging_config.py**: File-based logging with rotation
- **exceptions.py**: Custom exception hierarchy

## Assumptions

1. **Testnet Only**: This bot is configured for Binance Futures Testnet (`https://testnet.binancefuture.com`)
2. **USDT-M Futures**: All trades are on USDT-M perpetual futures
3. **No Production Safeguards**: There are no circuit breakers, position limits, or anti-duplicate checks
4. **API Key Security**: API keys should never be committed to version control
5. **Network Connectivity**: Assumes stable internet connection to Binance servers
6. **Order Execution**: Orders execute immediately on testnet (market orders typically fill instantly)
7. **TimeInForce**: Limit orders use "GTC" (Good Till Canceled) by default

## Bonus Features (Optional)

### Enhanced CLI UX

The CLI includes:
- Order confirmation prompts before placement
- Color-coded output (green for success, red for errors)
- Formatted sections and summaries
- Helpful error messages with validation details

### Example Output

```
60
  Trading Bot - Order Placement
============================================================

📋 Order Request Summary:
  Symbol              : BTCUSDT
  Side                : BUY
  Type                : MARKET
  Quantity            : 0.001

Proceed with order placement? [Y/n]:
```

## Troubleshooting

### "ModuleNotFoundError: No module named 'bot'"

Make sure you're running from the project root directory and have installed dependencies:

```bash
pip install -r requirements.txt
# or
pip install -e .
```

### ".env file not found" or credential errors

1. Create `.env` file in project root
2. Copy contents from `.env.example`
3. Add your Binance Testnet API key and secret
4. Verify file is not in `.gitignore` (it shouldn't be)

### "Connection refused" to testnet

1. Verify you're using the correct testnet URL: `https://testnet.binancefuture.com`
2. Check internet connectivity
3. Verify Binance testnet is accessible

### "Filter failure" errors from Binance

This typically means the order parameters violate Binance trading rules:

- **PRICE_FILTER**: Price doesn't match market precision
- **LOT_SIZE**: Quantity is too small or doesn't match increments
- **MIN_NOTIONAL**: Order value is below minimum (usually ~10 USDT)

Check current trading rules on the testnet UI.

### API key not working

1. Verify API key and secret are correctly copied to `.env`
2. Check API key has "Enable Futures" permission enabled
3. Regenerate API key if necessary

## Development

### Code Style

The project follows Python best practices:

- Type hints on all functions
- Clear, descriptive variable names
- Docstrings for all modules and functions
- Clean code principles (SOLID)

### Running Linters (Optional)

```bash
# Format code
ruff format bot/ tests/

# Lint code
ruff check bot/ tests/

# Type checking
mypy bot/
```

### Adding New Features

1. Add validators for new parameters in `validators.py`
2. Extend `OrderRequest` dataclass in `orders.py`
3. Add CLI options in `cli.py`
4. Add tests in `tests/`
5. Update README

## Deliverables

✅ Complete source code in organized structure
✅ README with setup, run examples, and assumptions
✅ requirements.txt with all dependencies
✅ Log files from market and limit order execution
✅ Unit tests for critical components
✅ Comprehensive error handling
✅ Type hints throughout codebase

## License

MIT License - See LICENSE file for details

## Support

For issues or questions:

1. Check logs in `logs/trading_bot.log` for error details
2. Review README troubleshooting section
3. Verify `.env` configuration
4. Check Binance Testnet API documentation

## Notes

- The bot uses HMAC-SHA256 signatures for all signed requests
- Requests have a 10-second timeout by default (configurable via `REQUEST_TIMEOUT` in `.env`)
- All timestamps are in milliseconds (epoch time * 1000)
- Order responses include full Binance API data for reference
