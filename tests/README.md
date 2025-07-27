# AMP Protocol Test Suite

This directory contains the comprehensive test suite for the Agent Mesh Protocol implementation.

## 🧪 Test Structure

```
tests/
├── unit/                    # Unit tests for individual components
│   ├── test_agent.py       # Agent base class tests
│   ├── test_message.py     # Message handling tests
│   ├── test_capability.py  # Capability system tests
│   ├── test_context.py     # Context management tests
│   └── test_security.py    # Security component tests
├── integration/            # Integration tests
│   ├── test_agent_communication.py
│   ├── test_multi_agent.py
│   ├── test_transports.py
│   └── test_frameworks.py
├── protocol/              # Protocol compliance tests
│   ├── test_message_format.py
│   ├── test_capability_spec.py
│   ├── test_versioning.py
│   └── test_compliance.py
├── performance/           # Performance benchmarks
│   ├── test_throughput.py
│   ├── test_latency.py
│   ├── test_scalability.py
│   └── benchmarks.py
├── security/             # Security-specific tests
│   ├── test_authentication.py
│   ├── test_authorization.py
│   ├── test_encryption.py
│   └── test_vulnerabilities.py
├── fixtures/             # Test fixtures and data
│   ├── agents.py        # Mock agents
│   ├── messages.py      # Sample messages
│   ├── capabilities.py  # Test capabilities
│   └── data/           # Test data files
└── conftest.py          # Pytest configuration
```

## 🚀 Running Tests

### Quick Start
```bash
# Run all tests
pytest

# Run with verbose output
pytest -v

# Run specific test file
pytest tests/unit/test_agent.py

# Run specific test
pytest tests/unit/test_agent.py::TestAgent::test_initialization
```

### Test Categories
```bash
# Unit tests only
pytest tests/unit/

# Integration tests
pytest tests/integration/

# Protocol compliance
pytest tests/protocol/

# Performance benchmarks
pytest tests/performance/

# Security tests
pytest tests/security/
```

### Coverage Reports
```bash
# Generate coverage report
pytest --cov=amp --cov-report=html

# View coverage in terminal
pytest --cov=amp --cov-report=term-missing

# Coverage with specific threshold
pytest --cov=amp --cov-fail-under=80
```

## 📋 Test Guidelines

### Writing Tests

#### Test Structure
```python
import pytest
from amp.core import AMPAgent

class TestAgent:
    """Test cases for AMPAgent class."""
    
    @pytest.fixture
    def agent(self):
        """Create test agent instance."""
        return AMPAgent("test-agent", "Test Agent")
    
    def test_initialization(self, agent):
        """Test agent initialization."""
        assert agent.identity.id == "test-agent"
        assert agent.identity.name == "Test Agent"
    
    @pytest.mark.asyncio
    async def test_async_operation(self, agent):
        """Test async agent operations."""
        await agent.initialize()
        assert agent.is_initialized
```

#### Test Naming
- Descriptive test names: `test_should_handle_invalid_message_gracefully`
- Group related tests in classes
- Use docstrings to explain test purpose

#### Fixtures
- Use fixtures for common setup
- Scope fixtures appropriately
- Clean up resources in fixtures

### Test Categories

#### Unit Tests
- Test individual components in isolation
- Mock external dependencies
- Fast execution (< 1 second per test)
- High coverage target (> 90%)

#### Integration Tests
- Test component interactions
- Use real implementations where possible
- Moderate execution time
- Focus on critical paths

#### Protocol Tests
- Validate protocol compliance
- Test message formats
- Verify capability schemas
- Check version compatibility

#### Performance Tests
- Benchmark critical operations
- Track performance over time
- Set performance baselines
- Alert on regressions

#### Security Tests
- Test authentication mechanisms
- Verify authorization logic
- Check for vulnerabilities
- Validate encryption

## 🔧 Test Configuration

### pytest.ini
```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
asyncio_mode = auto
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
    security: marks tests as security tests
```

### Test Markers
```python
# Mark slow tests
@pytest.mark.slow
def test_large_message_handling():
    pass

# Mark integration tests
@pytest.mark.integration
def test_multi_agent_communication():
    pass

# Skip tests conditionally
@pytest.mark.skipif(sys.platform == "win32", reason="Not supported on Windows")
def test_unix_specific():
    pass
```

### Running Marked Tests
```bash
# Run only slow tests
pytest -m slow

# Run all except slow tests
pytest -m "not slow"

# Run integration and security tests
pytest -m "integration or security"
```

## 📊 Test Metrics

### Coverage Goals
- Overall: > 80%
- Core modules: > 90%
- Critical paths: 100%
- New code: > 85%

### Performance Baselines
- Message roundtrip: < 10ms
- Capability discovery: < 50ms
- Agent initialization: < 100ms
- Memory per agent: < 10MB

## 🐛 Debugging Tests

### Verbose Output
```bash
# Show print statements
pytest -s

# Show full diff
pytest -vv

# Stop on first failure
pytest -x

# Debug with pdb
pytest --pdb
```

### Test Isolation
```bash
# Run tests in random order
pytest --random-order

# Run tests in parallel
pytest -n auto

# Run with clean environment
pytest --cache-clear
```

## 🤝 Contributing Tests

### Adding New Tests
1. Follow existing test structure
2. Use appropriate fixtures
3. Add docstrings
4. Check coverage impact
5. Ensure tests pass locally

### Test Review Checklist
- [ ] Tests are descriptive
- [ ] Coverage is adequate
- [ ] No hardcoded values
- [ ] Proper cleanup
- [ ] Runs in CI environment

## 📚 Resources

- [Pytest Documentation](https://docs.pytest.org/)
- [Testing Best Practices](../docs/testing-guide.md)
- [Mock Usage Guide](../docs/mocking-guide.md)
- [Performance Testing](../docs/performance-testing.md)