# Prompt Engineering Playground

![Python Version](https://img.shields.io/badge/python-3.11+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![uv](https://img.shields.io/badge/package%20manager-uv-orange.svg)

An interactive playground for experimenting with and refining prompts for Claude using Python, Jupyter notebooks, and the Anthropic SDK. Built for prompt engineers, developers, and researchers who want to systematically test, analyze, and optimize their AI prompts.

## Features

### 🎯 Interactive Prompt Testing
- Test prompts directly in Jupyter notebooks with instant feedback
- Adjust parameters (temperature, max_tokens) with interactive widgets
- Real-time response visualization with rich formatting

### ⚖️ A/B Testing Capabilities
- Compare multiple prompt variants side-by-side
- Statistical analysis of response differences
- Visual comparison charts and metrics

### 📊 Response Analysis & Visualization
- Comprehensive metrics (word count, token usage, cost estimation)
- Tone analysis (formality, complexity, perspective)
- Key point extraction
- Beautiful matplotlib visualizations

### 📝 Template Management
- Create reusable prompt templates with variable substitution
- Save and load templates from persistent library
- Template validation and versioning
- Pre-built templates for common use cases

### 💰 Cost Tracking
- Real-time token usage monitoring
- Accurate cost estimation per request
- Session-wide cost analytics
- Budget-aware batch processing

### ⚡ Response Caching
- SQLite-based caching system
- TTL (time-to-live) support
- Thread-safe operations
- Cache statistics and hit rate tracking

## Prerequisites

- **Python 3.11+** - Modern Python with type hints support
- **Anthropic API Key** - Get yours at [console.anthropic.com](https://console.anthropic.com/)
- **uv** - Fast Python package manager ([installation guide](https://github.com/astral-sh/uv))

## Installation

### 1. Install uv (if not already installed)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Or on Windows:
```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### 2. Clone the Repository

```bash
git clone https://github.com/sofianhadi1983/anthropic-prompt-engineering.git
cd anthropic-prompt-engineering
```

### 3. Install Dependencies

```bash
uv sync
```

This will:
- Create a virtual environment
- Install all required packages (anthropic, jupyter, pandas, matplotlib, etc.)
- Set up the project for development

### 4. Configure Environment Variables

```bash
cp .env.example .env
```

Edit `.env` and add your Anthropic API key:
```env
ANTHROPIC_API_KEY=your_api_key_here
DEFAULT_MODEL=claude-sonnet-4-5-20250929
CACHE_ENABLED=true
CACHE_TTL=3600
```

## Quick Start

### Launch Jupyter Lab

```bash
uv run jupyter lab
```

### Your First Prompt

Open `notebooks/playground.ipynb` and run:

```python
from prompt_playground.client import create_client, send_prompt

client = create_client()

response = send_prompt(
    prompt="Explain quantum computing in simple terms.",
    temperature=0.7,
    max_tokens=200,
    client=client
)

print(response['text'])
print(f"Tokens used: {response['total_tokens']}")
```

### Or Use the CLI

```python
from prompt_playground import client, analysis

client_instance = client.create_client()
response = client.send_prompt("What is prompt engineering?", client=client_instance)

metrics = analysis.calculate_metrics(response)
print(f"Words: {metrics['word_count']}, Cost: ${metrics['estimated_cost']:.6f}")
```

## Project Structure

```
prompt-engineering/
├── README.md                   # This file
├── pyproject.toml             # Project configuration and dependencies
├── .env.example               # Environment variables template
├── .gitignore                 # Git ignore rules
│
├── src/
│   └── prompt_playground/     # Main package
│       ├── __init__.py        # Package initialization
│       ├── client.py          # Anthropic API client wrapper
│       ├── prompts.py         # Template system
│       ├── analysis.py        # Response analysis tools
│       ├── cache.py           # SQLite caching system
│       ├── history.py         # Prompt versioning
│       ├── evaluation.py      # Evaluation framework
│       └── utils.py           # Utility functions
│
├── notebooks/
│   ├── playground.ipynb       # Main interactive playground
│   └── examples/              # Example notebooks
│       ├── 01_getting_started.ipynb
│       ├── 02_prompt_templates.ipynb
│       ├── 03_ab_testing.ipynb
│       ├── 04_batch_processing.ipynb
│       └── 05_evaluation_metrics.ipynb
│
└── tests/                     # Unit tests
    ├── test_client.py
    ├── test_prompts.py
    └── test_analysis.py
```

## Usage Examples

### Basic Prompt Testing

```python
from prompt_playground.client import create_client, send_prompt

client = create_client()

response = send_prompt(
    prompt="Explain machine learning to a beginner.",
    system="You are a helpful teacher.",
    temperature=0.7,
    max_tokens=300,
    client=client
)

print(response['text'])
```

### Using Templates

```python
from prompt_playground.prompts import PromptTemplate, PromptLibrary

template = PromptTemplate(
    template="Explain {topic} to a {audience} in {style} language.",
    variables=["topic", "audience", "style"]
)

prompt = template.fill(
    topic="blockchain",
    audience="beginner",
    style="simple"
)

response = send_prompt(prompt=prompt, client=client)
```

### A/B Testing

```python
from prompt_playground.client import send_batch
from prompt_playground.analysis import compare_responses

variants = [
    "Summarize this article.",
    "Summarize this article in 3 key points.",
]

responses = send_batch(prompts=variants, client=client)
comparison = compare_responses(responses)

print(comparison[['word_count', 'output_tokens', 'estimated_cost']])
```

### Response Caching

```python
from prompt_playground.cache import ResponseCache

cache = ResponseCache(db_path="cache.db", ttl=3600)

cached = cache.get(prompt, model, params)
if cached:
    print("Using cached response!")
else:
    response = send_prompt(prompt=prompt, client=client)
    cache.set(prompt, model, params, response)
```

### Batch Processing

```python
from prompt_playground.client import send_batch

topics = ["Python", "JavaScript", "Rust"]
prompts = [f"Explain {topic} in one sentence." for topic in topics]

responses = send_batch(
    prompts=prompts,
    temperature=0.5,
    max_tokens=100,
    client=client
)

for topic, response in zip(topics, responses):
    print(f"{topic}: {response['text']}")
```

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `ANTHROPIC_API_KEY` | Your Anthropic API key | Required |
| `DEFAULT_MODEL` | Default Claude model to use | `claude-sonnet-4-5-20250929` |
| `CACHE_ENABLED` | Enable response caching | `true` |
| `CACHE_TTL` | Cache time-to-live (seconds) | `3600` |

### Model Options

Available Claude models:
- `claude-opus-4-5-20251101` - Most capable, highest cost
- `claude-sonnet-4-5-20250929` - Balanced performance and cost (recommended)
- `claude-3-7-sonnet-20250219` - Fast and capable
- `claude-3-5-haiku-20241022` - Fastest, lowest cost

### Parameters

Common parameters for `send_prompt()`:
- `temperature` (0.0-1.0): Controls randomness. Lower = more focused.
- `max_tokens` (int): Maximum response length.
- `system` (str): System prompt to set context.

## Documentation

### Example Notebooks

Learn by example with our comprehensive tutorial notebooks:

1. **[Getting Started](notebooks/examples/01_getting_started.ipynb)** - Basic setup and first prompts
2. **[Prompt Templates](notebooks/examples/02_prompt_templates.ipynb)** - Create reusable templates
3. **[A/B Testing](notebooks/examples/03_ab_testing.ipynb)** - Compare prompt variants
4. **[Batch Processing](notebooks/examples/04_batch_processing.ipynb)** - Process at scale
5. **[Evaluation Metrics](notebooks/examples/05_evaluation_metrics.ipynb)** - Define quality metrics

### API Reference

#### Client Module (`prompt_playground.client`)

- `create_client(api_key=None)` - Initialize Anthropic client
- `send_prompt(prompt, model, system, temperature, max_tokens, client)` - Send single prompt
- `send_batch(prompts, model, **kwargs)` - Send multiple prompts
- `count_tokens(text)` - Count tokens in text
- `estimate_cost(input_tokens, output_tokens, model)` - Estimate API cost

#### Template Module (`prompt_playground.prompts`)

- `PromptTemplate(template, variables)` - Create template with placeholders
- `PromptLibrary(storage_path)` - Manage template collection
- `validate_prompt(prompt)` - Check prompt quality

#### Analysis Module (`prompt_playground.analysis`)

- `calculate_metrics(response)` - Get response metrics
- `compare_responses(responses)` - Compare multiple responses
- `visualize_comparison(responses, metric)` - Create comparison charts
- `analyze_tone(text)` - Analyze response tone

#### Cache Module (`prompt_playground.cache`)

- `ResponseCache(db_path, ttl)` - Initialize cache
- `cache.get(prompt, model, params)` - Retrieve cached response
- `cache.set(prompt, model, params, response)` - Store response
- `cache.get_stats()` - Get cache statistics

### Best Practices

1. **Start with clear, specific prompts** - Vague prompts yield inconsistent results
2. **Use system prompts** to set context and behavior
3. **Test multiple variations** with A/B testing
4. **Monitor costs** with built-in tracking tools
5. **Cache responses** for repeated queries
6. **Use templates** for consistency across similar prompts
7. **Iterate systematically** - change one variable at a time

## Contributing

We welcome contributions! Here's how to get started:

### Development Setup

```bash
git clone https://github.com/sofianhadi1983/anthropic-prompt-engineering.git
cd anthropic-prompt-engineering
uv sync
```

### Running Tests

```bash
uv run pytest tests/
```

### Code Style

- Follow PEP 8 guidelines
- Use type hints for all functions
- Include docstrings for public APIs
- Keep functions focused and testable

### Pull Request Process

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Add tests for new functionality
5. Ensure all tests pass
6. Commit your changes (`git commit -m 'Add amazing feature'`)
7. Push to your branch (`git push origin feature/amazing-feature`)
8. Open a Pull Request

## License

This project is licensed under the MIT License - see below for details:

```
MIT License

Copyright (c) 2025 Sofian Hadianto

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## Acknowledgments

- Built with [Anthropic's Claude API](https://www.anthropic.com/)
- Package management by [uv](https://github.com/astral-sh/uv)
- Interactive notebooks powered by [Jupyter](https://jupyter.org/)

## Support

- **Issues**: [GitHub Issues](https://github.com/sofianhadi1983/anthropic-prompt-engineering/issues)
- **Discussions**: [GitHub Discussions](https://github.com/sofianhadi1983/anthropic-prompt-engineering/discussions)
- **Documentation**: See `notebooks/examples/` for detailed guides

---

**Happy Prompt Engineering! 🚀**
