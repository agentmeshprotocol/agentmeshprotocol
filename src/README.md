# AMP Protocol Source Code

This directory contains the reference implementation of the Agent Mesh Protocol.

## 📁 Source Structure

```
src/
├── core/                  # Core protocol implementation
│   ├── agent.py          # Base agent class
│   ├── message.py        # Message types and validation
│   ├── capability.py     # Capability management
│   ├── context.py        # Context and memory management
│   └── router.py         # Message routing logic
├── transport/            # Transport layer implementations
│   ├── base.py          # Abstract transport interface
│   ├── http.py          # HTTP/REST transport
│   ├── websocket.py     # WebSocket transport
│   ├── grpc.py          # gRPC transport
│   └── mq.py            # Message queue transport
├── security/            # Security implementations
│   ├── auth.py          # Authentication mechanisms
│   ├── signing.py       # Message signing
│   ├── encryption.py    # Encryption utilities
│   └── validation.py    # Security validation
├── frameworks/          # Framework adapters
│   ├── base.py          # Base adapter class
│   ├── langchain.py     # LangChain adapter
│   ├── crewai.py        # CrewAI adapter
│   ├── autogen.py       # AutoGen adapter
│   └── custom.py        # Custom framework template
├── utils/               # Utility functions
│   ├── logging.py       # Logging configuration
│   ├── metrics.py       # Metrics collection
│   ├── errors.py        # Error definitions
│   └── helpers.py       # Helper functions
└── validation/          # Protocol validation
    ├── schema.py        # JSON schema validation
    ├── compliance.py    # Compliance checking
    └── testing.py       # Test utilities
```

## 🏗️ Architecture Overview

### Core Components

#### Agent Base Class
The foundation for all AMP agents:
```python
from amp.core import AMPAgent

class MyAgent(AMPAgent):
    async def initialize(self):
        # Agent initialization
        pass
```

#### Message System
Standardized message format and handling:
- Request/Response pattern
- Event broadcasting
- Error propagation
- Correlation tracking

#### Capability Registry
Dynamic capability registration and discovery:
- Schema validation
- Version management
- Constraint checking
- Runtime updates

### Transport Layer
Pluggable transport implementations:
- Abstract transport interface
- Multiple transport options
- Connection management
- Retry logic

### Security Layer
Comprehensive security features:
- Multiple authentication methods
- Message integrity verification
- Encryption support
- Access control

## 🚀 Development Setup

### Installation
```bash
# Clone repository
git clone https://github.com/agentmeshprotocol/agentmeshprotocol.git
cd agentmeshprotocol

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install in development mode
pip install -e ".[dev]"
```

### Running Tests
```bash
# Run all tests
pytest

# Run specific test category
pytest tests/unit/
pytest tests/integration/

# Run with coverage
pytest --cov=amp --cov-report=html
```

### Code Quality
```bash
# Format code
black src/
isort src/

# Lint code
flake8 src/
mypy src/

# Run all checks
make quality
```

## 📋 Implementation Notes

### Design Principles
1. **Protocol First**: Implementation follows protocol spec exactly
2. **Framework Agnostic**: No framework-specific dependencies in core
3. **Async by Default**: All I/O operations are async
4. **Type Safe**: Full type hints throughout
5. **Testable**: Comprehensive test coverage

### Performance Considerations
- Message pooling for reduced allocation
- Efficient serialization/deserialization
- Connection pooling for transports
- Lazy loading of capabilities
- Metrics collection overhead minimized

### Error Handling
- Graceful degradation
- Detailed error messages
- Proper error propagation
- Recovery mechanisms
- Circuit breakers for resilience

## 🧪 Testing Strategy

### Test Categories
1. **Unit Tests**: Individual component testing
2. **Integration Tests**: Component interaction testing
3. **Protocol Tests**: Protocol compliance validation
4. **Performance Tests**: Benchmark and load testing
5. **Security Tests**: Security validation

### Test Data
- Mock agents for testing
- Sample messages and capabilities
- Error scenarios
- Performance baselines

## 🔧 Extension Points

### Adding Transport
1. Implement `TransportBase` interface
2. Add to transport factory
3. Write transport tests
4. Update documentation

### Adding Framework
1. Implement `FrameworkAdapter` interface
2. Map framework concepts to AMP
3. Add framework tests
4. Create usage examples

### Custom Capabilities
1. Define capability schema
2. Implement handler logic
3. Register with agent
4. Add capability tests

## 📚 Code Documentation

- Docstrings for all public APIs
- Type hints throughout
- Inline comments for complex logic
- Architecture decision records (ADRs)
- API documentation generation

## 🤝 Contributing

See [CONTRIBUTING.md](../CONTRIBUTING.md) for:
- Code style guide
- Commit conventions
- Review process
- Testing requirements
- Documentation standards

## 📄 License

The source code is licensed under the MIT License. See [LICENSE](../LICENSE) for details.