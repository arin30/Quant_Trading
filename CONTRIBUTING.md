# Contributing to Quant Trading System

Thanks for wanting to help. This started as a solo project and has grown big enough to need more hands, so bug reports, ideas and pull requests are all welcome.

> This is a research and engineering project. Nothing here is financial advice. Never test your changes on a live account.

## Where to start

- Look at issues labelled [`good first issue`](https://github.com/vrd07/Quant_Trading/labels/good%20first%20issue) or [`help wanted`](https://github.com/vrd07/Quant_Trading/labels/help%20wanted).
- Comment on an issue before you start, so two people don't build the same thing.
- Have an idea that isn't an issue yet? Open one first and describe it, especially if it touches the risk engine.

## Setup

You need Python 3.11+ and Git. You don't need MetaTrader 5 to run the unit tests.

```bash
git clone https://github.com/vrd07/Quant_Trading.git
cd Quant_Trading
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt -r requirements-test.txt
```

## Running tests

```bash
pytest tests/unit               # fast, no external services
pytest tests/integration        # needs a running MT5 terminal with the file bridge
```

The MT5 file bridge setup is in [`mt5_bridge/README_SETUP.md`](mt5_bridge/README_SETUP.md).

Before opening a PR, make sure `pytest tests/unit` passes, and add tests for any new behaviour.

## Project layout

The folders you'll touch most:

| Path | What's there |
| --- | --- |
| `src/strategies/` | Strategy classes, each a subclass of `BaseStrategy`, plus the strategy registry |
| `src/risk/` | Risk engine, kill switch, circuit breaker, drawdown tracker, position sizer |
| `src/backtest/` | Backtesting engine |
| `src/execution/` | Order execution |
| `config/` | Live configs per account size, and per-strategy parameter grids in `backtest_grids/` |
| `mt5_bridge/` | MetaTrader 5 file bridge (MQL5 EAs and the Python client) |
| `tests/` | `unit/` and `integration/` tests |

## Adding a strategy

1. Create `src/strategies/<name>_strategy.py` with a class that subclasses `BaseStrategy` and implements `on_bar(bars)` (returns a `Signal` or `None`) and `get_name()`.
2. Register it in the strategy registry in `src/strategies/strategy_manager.py`.
3. Add its settings under `strategies:` in the config, and a parameter grid in `config/backtest_grids/` (the README in that folder has the schema).
4. Backtest it with `scripts/run_backtest.py` and put the results in your PR, including out-of-sample results.
5. Add unit tests in `tests/unit/`.

Strategies are only merged if they hold up on data they weren't tuned on.

## Changes to the risk engine

The risk engine (`src/risk/`) can veto every order, so changes here get the closest review:

- Explain what the change protects against, or what it loosens and why.
- Add tests that show the limit triggering.
- Never remove or weaken a check without saying so in the PR.

## Pull requests

- Fork the repo, branch from `main`, and keep each PR to one change.
- Describe what changed and how you tested it.
- Never commit credentials, account numbers, broker logins or personal trade logs.
- Be kind in reviews and discussions.

## Questions

Open an issue with the `question` label.
