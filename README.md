# Anthropic Prompt Engineering Playground

An interactive playground for experimenting with and refining prompts for Claude using Python, Jupyter notebooks, and the Anthropic SDK.

## Features

- Interactive prompt testing with Jupyter notebooks
- A/B testing for comparing prompt variants
- Response analysis and visualization
- Prompt template management
- Token usage and cost tracking
- Response caching for efficiency

## Tech Stack

- **uv** - Fast Python package manager
- **Python 3.11+**
- **Anthropic SDK** - Official SDK for Claude API
- **Jupyter** - Interactive notebook environment
- **pandas** - Data analysis
- **matplotlib** - Visualization

## Getting Started

### Prerequisites

- Python 3.11 or higher
- Anthropic API key
- uv package manager

### Installation

```bash
# Install uv if not already installed
curl -LsSf https://astral.sh/uv/install.sh | sh

# Clone the repository
git clone https://github.com/sofianhadi1983/anthropic-prompt-engineering.git
cd anthropic-prompt-engineering

# Install dependencies
uv sync

# Set up environment variables
cp .env.example .env
# Edit .env and add your ANTHROPIC_API_KEY
```

### Quick Start

```bash
# Start Jupyter Lab
uv run jupyter lab

# Open notebooks/playground.ipynb
```

## Project Status

🚧 This project is currently under development. Stay tuned for updates!

## License

MIT License
