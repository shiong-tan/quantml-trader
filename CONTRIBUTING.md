# Contributing to QuantML-Trader

Thank you for your interest in contributing to QuantML-Trader! This document provides guidelines and instructions for contributing to the project.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Testing Requirements](#testing-requirements)
- [Pull Request Process](#pull-request-process)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Enhancements](#suggesting-enhancements)

## Code of Conduct

This project follows professional standards of conduct. Be respectful, constructive, and professional in all interactions.

## Getting Started

1. Fork the repository on GitHub
2. Clone your fork locally
3. Set up your development environment
4. Create a feature branch
5. Make your changes
6. Submit a pull request

## Development Setup

### Prerequisites

- Python 3.10 or higher
- Git
- Virtual environment tool (venv, conda, etc.)

### Initial Setup

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/quantml-trader.git
cd quantml-trader

# Create and activate virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install development dependencies
pip install -e ".[dev]"

# Install pre-commit hooks
pre-commit install

# Copy environment template
cp .env.example .env
# Edit .env with your configuration
```

### Verify Installation

```bash
# Run tests
pytest

# Check code formatting
black --check .
ruff check .

# Run type checking
mypy algo_trading
```

## Development Workflow

### 1. Create a Feature Branch

```bash
git checkout -b feature/your-feature-name
```

Branch naming conventions:
- `feature/` - New features
- `fix/` - Bug fixes
- `refactor/` - Code refactoring
- `docs/` - Documentation changes
- `test/` - Test additions/modifications

### 2. Make Your Changes

- Write clean, documented code
- Follow the coding standards (see below)
- Add tests for new functionality
- Update documentation as needed

### 3. Run Tests Locally

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=algo_trading

# Run specific test file
pytest algo_trading/tests/test_risk_manager.py

# Run tests with specific markers
pytest -m unit  # Only unit tests
pytest -m "not slow"  # Skip slow tests
```

### 4. Format and Lint Code

```bash
# Auto-format code
black .

# Check linting
ruff check .

# Auto-fix linting issues
ruff check --fix .

# Run type checking
mypy algo_trading
```

### 5. Commit Your Changes

```bash
git add .
git commit -m "type: brief description

Detailed explanation of changes (if needed)

Closes #issue_number"
```

Commit message format:
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `refactor:` - Code refactoring
- `test:` - Test changes
- `chore:` - Build/tooling changes
- `perf:` - Performance improvements

### 6. Push and Create Pull Request

```bash
git push origin feature/your-feature-name
```

Then create a pull request on GitHub.

## Coding Standards

### Python Style Guide

- Follow PEP 8
- Use Black for code formatting (line length: 100)
- Use Ruff for linting
- Use type hints where appropriate
- Write docstrings for all public functions, classes, and modules

### Docstring Format

```python
def calculate_sharpe_ratio(returns: pd.Series, risk_free_rate: float = 0.0) -> float:
    """
    Calculate the Sharpe ratio for a series of returns.

    Args:
        returns: Series of percentage returns
        risk_free_rate: Risk-free rate of return (default: 0.0)

    Returns:
        Sharpe ratio as a float

    Raises:
        ValueError: If returns series is empty

    Example:
        >>> returns = pd.Series([0.01, 0.02, -0.01, 0.03])
        >>> sharpe = calculate_sharpe_ratio(returns)
        >>> print(f"Sharpe ratio: {sharpe:.2f}")
    """
    if returns.empty:
        raise ValueError("Returns series cannot be empty")

    excess_returns = returns - risk_free_rate
    return excess_returns.mean() / excess_returns.std()
```

### Code Organization

- Keep functions small and focused (< 50 lines preferred)
- Use meaningful variable names
- Avoid deep nesting (max 3-4 levels)
- Extract complex logic into separate functions
- Use constants for magic numbers

### Type Hints

```python
from typing import List, Dict, Optional, Union
import pandas as pd
import numpy as np

def process_signals(
    data: pd.DataFrame,
    symbols: List[str],
    config: Optional[Dict[str, Union[int, float]]] = None
) -> pd.DataFrame:
    """Process trading signals."""
    if config is None:
        config = {}
    # Implementation
    return processed_data
```

## Testing Requirements

### Test Coverage

- Minimum 70% code coverage (configured in pytest.ini)
- All new features must include tests
- Bug fixes should include regression tests

### Test Structure

```python
import pytest
import pandas as pd
from algo_trading.risk import RiskManager


class TestRiskManager:
    """Tests for RiskManager class."""

    @pytest.fixture
    def risk_manager(self):
        """Create a RiskManager instance for testing."""
        config = {
            "max_position_size": 0.1,
            "max_leverage": 1.0,
        }
        return RiskManager(config)

    def test_position_sizing_basic(self, risk_manager):
        """Test basic position sizing calculation."""
        portfolio_value = 100000
        signal_strength = 0.8

        size = risk_manager.calculate_position_size(
            portfolio_value, signal_strength
        )

        assert 0 < size <= 0.1 * portfolio_value
        assert isinstance(size, float)

    def test_position_sizing_edge_cases(self, risk_manager):
        """Test position sizing with edge cases."""
        # Test with zero signal
        size = risk_manager.calculate_position_size(100000, 0.0)
        assert size == 0

        # Test with negative signal
        with pytest.raises(ValueError):
            risk_manager.calculate_position_size(100000, -0.5)
```

### Test Markers

Use pytest markers to categorize tests:

```python
@pytest.mark.unit
def test_simple_function():
    """Fast unit test."""
    pass

@pytest.mark.integration
def test_data_pipeline():
    """Integration test."""
    pass

@pytest.mark.slow
def test_full_backtest():
    """Slow end-to-end test."""
    pass
```

## Pull Request Process

### Before Submitting

1. ✅ All tests pass locally
2. ✅ Code coverage meets minimum threshold
3. ✅ Code is formatted (black, ruff)
4. ✅ Type hints added for new code
5. ✅ Documentation updated
6. ✅ Commit messages follow conventions
7. ✅ No merge conflicts with main branch

### PR Description Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No new warnings generated
- [ ] Tests pass locally
```

### Review Process

1. Automated checks must pass (CI/CD)
2. Code review by maintainer(s)
3. Address review feedback
4. Approval required before merge
5. Squash and merge to main

## Reporting Bugs

### Bug Report Template

```markdown
**Describe the bug**
Clear and concise description

**To Reproduce**
Steps to reproduce:
1. Configure '...'
2. Run '...'
3. Observe error

**Expected behavior**
What you expected to happen

**Actual behavior**
What actually happened

**Environment**
- OS: [e.g., Ubuntu 22.04]
- Python version: [e.g., 3.11.0]
- Package version: [e.g., 2.0.0]

**Additional context**
Error messages, logs, screenshots
```

## Suggesting Enhancements

### Enhancement Proposal Template

```markdown
**Feature description**
Clear description of the proposed feature

**Motivation**
Why is this feature needed?

**Proposed solution**
How should this feature work?

**Alternatives considered**
Other approaches you've considered

**Additional context**
Examples, mockups, related issues
```

## Trading System Specific Guidelines

### Risk Management Code

- Always validate input parameters
- Include position size limits
- Implement stop losses
- Add circuit breakers for extreme conditions
- Log all trading decisions

### Backtesting Code

- Use realistic slippage assumptions
- Include transaction costs
- Prevent look-ahead bias
- Validate data quality
- Document assumptions

### Live Trading Code

- Triple-check order submission logic
- Implement emergency kill switches
- Add comprehensive logging
- Include position reconciliation
- Never skip testing phases

### Security

- Never commit API keys or credentials
- Use environment variables for secrets
- Validate all external inputs
- Sanitize data before processing
- Follow secure coding practices

## Getting Help

- GitHub Issues: For bugs and feature requests
- GitHub Discussions: For questions and general discussion
- Documentation: Check README.md and docs/

## Recognition

Contributors will be recognized in:
- CONTRIBUTORS.md file
- Release notes
- Project documentation

Thank you for contributing to QuantML-Trader!
