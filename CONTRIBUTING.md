# Contributing to Agent Mesh Protocol

First off, thank you for considering contributing to the Agent Mesh Protocol! It's people like you that make AMP such a great tool for the AI community.

## Code of Conduct

By participating in this project, you are expected to uphold our [Code of Conduct](CODE_OF_CONDUCT.md).

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check existing issues as you might find out that you don't need to create one. When you are creating a bug report, please include as many details as possible:

* Use a clear and descriptive title
* Describe the exact steps which reproduce the problem
* Provide specific examples to demonstrate the steps
* Describe the behavior you observed after following the steps
* Explain which behavior you expected to see instead and why
* Include logs, stack traces, and code samples

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion, please include:

* Use a clear and descriptive title
* Provide a step-by-step description of the suggested enhancement
* Provide specific examples to demonstrate the steps
* Describe the current behavior and explain which behavior you expected to see instead
* Explain why this enhancement would be useful

### Pull Requests

* Fill in the required template
* Do not include issue numbers in the PR title
* Include screenshots and animated GIFs in your pull request whenever possible
* Follow the style guides
* Document new code
* End all files with a newline
* Write clear, concise commit messages

## Development Process

### Setting Up Your Development Environment

1. Fork the repository
2. Clone your fork:
   ```bash
   git clone https://github.com/your-username/agentmeshprotocol.git
   cd agentmeshprotocol
   ```

3. Set up your development environment:
   ```bash
   # Python development
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install -e ".[dev]"
   
   # Run tests
   pytest
   
   # Run linting
   black .
   ruff .
   mypy .
   ```

### Style Guides

#### Git Commit Messages

* Use the present tense ("Add feature" not "Added feature")
* Use the imperative mood ("Move cursor to..." not "Moves cursor to...")
* Limit the first line to 72 characters or less
* Reference issues and pull requests liberally after the first line
* Use conventional commits format:
  * `feat:` - New feature
  * `fix:` - Bug fix
  * `docs:` - Documentation changes
  * `style:` - Code style changes (formatting, etc)
  * `refactor:` - Code refactoring
  * `test:` - Test additions or modifications
  * `chore:` - Build process or auxiliary tool changes

#### Python Style Guide

* Follow [PEP 8](https://www.python.org/dev/peps/pep-0008/)
* Use [Black](https://black.readthedocs.io/) for code formatting
* Use [Ruff](https://github.com/charliermarsh/ruff) for linting
* Use [mypy](http://mypy-lang.org/) for type checking
* Write docstrings for all public functions, classes, and modules
* Use type hints for all function signatures

#### Documentation Style Guide

* Use Markdown for documentation
* Reference functions and classes in backticks: `Agent.connect()`
* Include code examples for new features
* Keep line length to 80 characters where possible
* Use clear, concise language

### Testing

* Write tests for all new functionality
* Ensure all tests pass before submitting PR
* Aim for >90% code coverage
* Include both unit tests and integration tests
* Test edge cases and error conditions

Example test structure:
```python
import pytest
from amp import Agent, AmpError

class TestAgent:
    def test_agent_creation(self):
        agent = Agent(name="Test Agent")
        assert agent.name == "Test Agent"
        assert agent.id is not None
    
    def test_invalid_capability(self):
        with pytest.raises(AmpError):
            Agent(name="Test", capabilities=["invalid"])
    
    @pytest.mark.asyncio
    async def test_connection(self):
        agent = Agent(name="Test Agent")
        await agent.connect("amp://test.local")
        assert agent.is_connected
```

### Documentation

* Update documentation for any changed functionality
* Add docstrings to new functions and classes
* Update the README if adding new features
* Add examples for new capabilities
* Keep API documentation in sync

### Releasing

1. Update version in `pyproject.toml` / `package.json` / `Cargo.toml`
2. Update CHANGELOG.md
3. Create a pull request with release notes
4. After merge, tag the release: `git tag v1.2.3`
5. Push tags: `git push --tags`
6. GitHub Actions will handle the rest

## Project Structure

```
agentmeshprotocol/
├── docs/              # Protocol documentation
├── src/               # Source code
│   ├── core/         # Core protocol implementation
│   ├── capabilities/ # Capability implementations
│   ├── transports/   # Transport layers
│   └── security/     # Security implementations
├── tests/            # Test suite
├── examples/         # Example implementations
└── schemas/          # JSON schemas
```

## Getting Help

* Join our [Discord server](https://discord.gg/agentmeshprotocol)
* Check the [documentation](https://docs.agentmeshprotocol.io)
* Ask questions in [GitHub Discussions](https://github.com/agentmeshprotocol/amp-community/discussions)

## Recognition

Contributors will be recognized in:
* The project README
* Release notes
* Our website's contributors page

Thank you for contributing to AMP! 🎉