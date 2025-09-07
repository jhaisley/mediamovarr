# Python Project Setup Instructions

This document outlines the complete setup process for a modern Python CLI application. It covers project structure, tooling, testing, building, and publishing to PyPI.

## 📁 Project Structure

```plaintext
project-name/
├── .github/                    # GitHub Actions, issue templates
├── docs/                       # Documentation
├── project_name/              # Main package
│   ├── __init__.py           # Package initialization, version info
│   ├── __main__.py           # CLI entry point
│   ├── core_module.py        # Core functionality
│   └── utils.py              # Utility functions
├── tests/                     # Test suite
│   ├── __init__.py
│   ├── test_core.py
│   └── test_integration.py
├── .gitignore                 # Git ignore rules
├── .pre-commit-config.yaml    # Pre-commit hooks
├── LICENSE                    # MIT license
├── README.md                  # Project documentation
├── pyproject.toml            # Project configuration
├── requirements.txt          # Legacy dependencies (optional)
└── uv.lock                   # UV dependency lock file
```

## 🛠️ Initial Setup

### 1. Create Project with UV

```bash
# Install UV if not already installed
pip install uv

# Create and initialize new project
uv init project-name
cd project-name

# Create virtual environment
uv venv

# Activate the virtual environment
# On Windows (PowerShell)
.venv\Scripts\activate
# On macOS/Linux
source .venv/bin/activate

# Initialize git repository
git init
```

### 2. Alternative: Manual Setup (if needed)

If you prefer manual control over the initial structure:

```bash
# Create main package directory
mkdir project-name
cd project-name

# Initialize with UV
uv init

# Create virtual environment
uv venv

# Activate environment
source .venv/bin/activate  # or .venv\Scripts\activate on Windows

# Initialize git
git init

# Create additional directories as needed
mkdir -p docs .github/workflows
```

## 📝 Configuration Files

### pyproject.toml - Complete Configuration

```toml
[build-system]
requires = ["setuptools>=45", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "project-name"
version = "0.1.0"
description = "A brief description of your project"
readme = {file = "README.md", content-type = "text/markdown"}
authors = [{name = "Jordan Haisley", email = "jordan@jordanhaisley.me"}]
license = "MIT"
license-files = ["LICENSE"]
classifiers = [
    "Development Status :: 4 - Beta",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.8",
    "Programming Language :: Python :: 3.9",
    "Programming Language :: Python :: 3.10",
    "Programming Language :: Python :: 3.11",
    "Programming Language :: Python :: 3.12",
    "Programming Language :: Python :: 3.13",
]
dependencies = [
    "click>=8.0.0",
    "requests>=2.25.0",
]
requires-python = ">=3.8"

[project.optional-dependencies]
dev = [
    "pytest>=6.0.0",
    "pytest-cov>=4.0.0",
    "ruff>=0.1.0",
    "mypy>=1.0.0",
    "pre-commit>=3.0.0",
    "build>=1.0.0",
]

[project.scripts]
project-name = "project_name.__main__:main"

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]

[tool.ruff]
line-length = 88
target-version = "py38"

[tool.ruff.lint]
select = [
    "E",  # pycodestyle errors
    "W",  # pycodestyle warnings
    "F",  # pyflakes
    "I",  # isort
    "B",  # flake8-bugbear
    "C4", # flake8-comprehensions
    "UP", # pyupgrade
]
ignore = [
    "E501", # line too long, handled by formatter
    "B008", # do not perform function calls in argument defaults
    "C901", # too complex
]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
skip-magic-trailing-comma = false
line-ending = "auto"

[tool.bumpversion]
current_version = "0.1.0"
parse = "(?P<major>\\d+)\\.(?P<minor>\\d+)\\.(?P<patch>\\d+)"
serialize = ["{major}.{minor}.{patch}"]
search = "{current_version}"
replace = "{new_version}"
regex = false
ignore_missing_version = false
tag = true
sign_tags = false
tag_name = "v{new_version}"
tag_message = "Bump version: {current_version} → {new_version}"
allow_dirty = false
commit = true
message = "Bump version: {current_version} → {new_version}"
commit_args = ""

[[tool.bumpversion.files]]
filename = "pyproject.toml"

[[tool.bumpversion.files]]
filename = "project_name/__init__.py"
```

### Package __init__.py

```python
"""Project Name - Brief description of what it does."""

__version__ = "0.1.0"
__author__ = "Your Name"
__description__ = "Brief description of your project"

# Import main classes/functions for easy access
from .core import main_function

__all__ = ["main_function"]
```

### CLI Entry Point (__main__.py)

```python
"""Main CLI entry point."""

import click

from .core import main_function

@click.command()
@click.option("--verbose", "-v", is_flag=True, help="Enable verbose output")
def main(verbose: bool) -> None:
    """Project Name - Brief description."""
    if verbose:
        print("Verbose mode enabled")

    main_function()

if __name__ == "__main__":
    main()
```

## 🧪 Testing Setup

### Install Testing Dependencies

```bash
# Using UV
uv add --dev pytest pytest-cov

# Or using pip
pip install pytest pytest-cov
```

### Basic Test Structure

```python
# tests/test_core.py
import pytest
from project_name.core import main_function

def test_main_function():
    """Test the main function."""
    result = main_function()
    assert result is not None

def test_example():
    """Example test."""
    assert 1 + 1 == 2
```

### Running Tests

```bash
# Run all tests
uv run pytest

# Run with coverage
uv run pytest --cov=project_name --cov-report=html

# Run specific test file
uv run pytest tests/test_core.py

# Run tests matching pattern
uv run pytest -k "test_main"
```

## 🔧 Code Quality Tools

### Ruff (Linting & Formatting)

```bash
# Install ruff
uv add --dev ruff

# Lint code
uv run ruff check .

# Format code
uv run ruff format .

# Fix auto-fixable issues
uv run ruff check --fix .
```

### MyPy (Type Checking)

```bash
# Install mypy
uv add --dev mypy

# Run type checking
uv run mypy project_name/
```

### Pre-commit Hooks

Create `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/psf/black
    rev: 23.7.0
    hooks:
      - id: black

  - repo: https://github.com/pycqa/isort
    rev: 5.12.0
    hooks:
      - id: isort

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.5.1
    hooks:
      - id: mypy
```

Install pre-commit:

```bash
# Install pre-commit
uv add --dev pre-commit

# Install hooks
uv run pre-commit install

# Run on all files
uv run pre-commit run --all-files
```

## 🏗️ Building & Publishing

### Build Package

```bash
# Using UV
uv build

# Or using build tool
uv run python -m build
```

### Test Package Locally

```bash
# Install in development mode
uv pip install -e .

# Test CLI
project-name --help
```

### Publish to PyPI

```bash
# Install twine
uv tool install twine

# Upload to PyPI
uv tool run twine upload dist/*

# Or upload to TestPyPI first
uv tool run twine upload --repository testpypi dist/*
```

## 📊 Version Management

### Using bump-my-version

```bash
# Install bump-my-version
uv add --dev bump-my-version

# Bump patch version (0.1.0 → 0.1.1)
uv run bump-my-version bump patch

# Bump minor version (0.1.0 → 0.2.0)
uv run bump-my-version bump minor

# Bump major version (0.1.0 → 1.0.0)
uv run bump-my-version bump major
```

## 📚 Documentation

### README.md Structure

```markdown
# Project Name

Brief description of what the project does.

## Features

- Feature 1
- Feature 2
- Feature 3

## Installation

```bash
pip install project-name
```

## Usage

```bash
project-name --help
```

## Development

```bash
# Clone repository
git clone <repository-url>
cd project-name

# Install dependencies
uv sync

# Run tests
uv run pytest

# Build package
uv build
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## License

This project is licensed under the MIT License.

```

## 🔄 Development Workflow

### Daily Development

```bash
# Activate environment (if using venv)
source .venv/bin/activate  # or .venv\Scripts\activate on Windows

# Install/update dependencies
uv sync

# Run tests
uv run pytest

# Check code quality
uv run ruff check .
uv run ruff format .

# Run type checking
uv run mypy project_name/
```

### Before Committing

```bash
# Run pre-commit hooks
uv run pre-commit run --all-files

# Run full test suite
uv run pytest --cov=project_name

# Build package to ensure it works
uv build
```

### Release Process

1. __Update version__: `uv run bump-my-version bump minor`
2. __Update changelog__: Document changes in CHANGELOG.md
3. __Run tests__: `uv run pytest`
4. __Build package__: `uv build`
5. __Test installation__: `uv pip install -e .`
6. __Publish__: `uv tool run twine upload dist/*`
7. __Create GitHub release__: Tag the release on GitHub

## 🐛 Troubleshooting

### Common Issues

__Import errors after installation:__

```bash
# Reinstall in development mode
uv pip install -e .
```

__Build fails:__

```bash
# Clear build artifacts
rm -rf build/ dist/ *.egg-info/

# Rebuild
uv build
```

__PyPI upload fails:__

```bash
# Check if version already exists
# Bump version if needed
uv run bump-my-version bump patch
uv build
uv tool run twine upload dist/*
```

## 🔧 Common UV Tool Commands

### Publishing & Distribution

```bash
# PyPI uploads
uv tool run twine upload dist/*

# Version management
uv tool run bump-my-version bump patch

# Build tools
uv tool run build
```

### Documentation

```bash
# MkDocs with Material theme
uvx --with mkdocs-material mkdocs serve
uvx --with mkdocs-material mkdocs build

# Sphinx documentation
uv tool run sphinx-build docs build/html
```

### Development Tools

```bash
# Pre-commit (if not in dev dependencies)
uv tool run pre-commit install
uv tool run pre-commit run --all-files

# Git tools
uv tool run git-filter-repo --help

# Project templates
uv tool run cookiecutter https://github.com/cookiecutter/cookiecutter-pypackage.git
```

### HTTP & API Tools

```bash
# HTTP clients
uv tool run httpie https://api.github.com/user
uv tool run httpx --help

# API testing
uv tool run newman run collection.json
```

### Database & Data Tools

```bash
# Database clients
uv tool run pgcli postgresql://user:pass@localhost/db

# Data processing
uv tool run pandas --help
uv tool run jupyter notebook
```

### Cloud & Deployment

```bash
# AWS CLI
uv tool run aws s3 ls

# Azure CLI
uv tool run az --help

# Google Cloud
uv tool run gcloud --help

# Docker Compose
uv tool run docker-compose up
```

### Security & Analysis

```bash
# Security scanning
uv tool run bandit -r src/
uv tool run safety check

# License checking
uv tool run licensecheck
```

### Performance & Profiling

```bash
# Profiling tools
uv tool run py-spy --help
uv tool run memory-profiler --help

# Benchmarking
uv tool run pytest-benchmark --help
```

### Miscellaneous Utilities

```bash
# Enhanced CLI output
uv tool run rich-cli --help

# Progress bars for scripts
uv tool run tqdm --help

# File processing
uv tool run pandoc input.md -o output.html

# Image processing
uv tool run pillow --help
```

## 📦 When to Use `uv tool` vs `uv add`

### Use `uv tool run` for

- __One-off tools__: Tools you use occasionally
- __Global utilities__: Tools that don't belong in project environment
- __Version conflicts__: Tools that might conflict with project dependencies
- __Large tools__: Tools with many dependencies you don't want in your env
- __System tools__: Tools that are environment-agnostic

### Use `uv add` for

- __Project dependencies__: Core functionality your code needs
- __Dev dependencies__: Tools used regularly in development
- __Testing tools__: pytest, coverage, etc.
- __Code quality__: ruff, mypy (if used consistently)

## 🚀 UV Tool Best Practices

```bash
# Use uvx as shorthand for uv tool run
uvx twine upload dist/*

# Install tools temporarily
uv tool install ruff
uv tool run ruff check .

# Use with additional dependencies
uvx --with mkdocs-material mkdocs serve

# Run scripts from URLs
uvx https://raw.githubusercontent.com/psf/black/main/black --help
```

## 🔍 Popular UV Tool Packages

| Category | Package | Purpose |
|----------|---------|---------|
| Publishing | `twine` | PyPI uploads |
| Versioning | `bump-my-version` | Semantic versioning |
| Documentation | `mkdocs` | Static site generation |
| HTTP | `httpie` | User-friendly HTTP client |
| Databases | `pgcli` | PostgreSQL CLI |
| Cloud | `awscli` | AWS command line |
| Security | `bandit` | Security linting |
| Performance | `py-spy` | Process profiling |

This approach keeps your project environment clean while giving you access to a wide range of tools when needed!
