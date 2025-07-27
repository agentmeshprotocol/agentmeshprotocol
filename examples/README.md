# AMP Protocol Examples

This directory contains example implementations demonstrating various aspects of the Agent Mesh Protocol.

## 📂 Example Categories

### Basic Examples
Simple examples to get started with AMP:
- `hello-world/` - Minimal agent setup and communication
- `capability-registration/` - How to register and discover capabilities
- `message-exchange/` - Basic message sending and receiving
- `error-handling/` - Proper error handling patterns

### Framework Integration
Examples for specific AI frameworks:
- `langchain-integration/` - LangChain agent with AMP
- `crewai-integration/` - CrewAI crew coordination via AMP
- `autogen-integration/` - AutoGen agents using AMP
- `custom-framework/` - Integrating your own framework

### Advanced Patterns
Complex usage patterns and architectures:
- `multi-agent-workflow/` - Coordinating multiple agents
- `context-sharing/` - Sharing state between agents
- `capability-composition/` - Combining multiple capabilities
- `distributed-agents/` - Agents across multiple machines

### Transport Examples
Different transport implementations:
- `http-transport/` - REST API based communication
- `websocket-transport/` - Real-time WebSocket communication
- `message-queue/` - RabbitMQ/Kafka integration
- `grpc-transport/` - High-performance gRPC transport

### Security Examples
Security implementation patterns:
- `authentication/` - API key and JWT authentication
- `mtls-setup/` - Mutual TLS configuration
- `message-signing/` - HMAC message integrity
- `secure-deployment/` - Production security setup

## 🚀 Running Examples

### Prerequisites
```bash
# Install Python dependencies
pip install agent-mesh-protocol

# Or for development
pip install -e ".[dev]"
```

### Running an Example
```bash
# Navigate to example directory
cd examples/hello-world

# Install example-specific dependencies
pip install -r requirements.txt

# Run the example
python main.py
```

### Docker Support
Many examples include Docker support:
```bash
# Build and run with Docker
docker-compose up

# Or individually
docker build -t amp-example .
docker run amp-example
```

## 📝 Example Structure

Each example follows this structure:
```
example-name/
├── README.md           # Example description and instructions
├── requirements.txt    # Python dependencies
├── main.py            # Main entry point
├── config.yaml        # Configuration file
├── docker-compose.yml # Docker setup (if applicable)
└── tests/             # Example tests
    └── test_example.py
```

## 🎯 Learning Path

### Beginners
1. Start with `hello-world/`
2. Move to `capability-registration/`
3. Try `message-exchange/`
4. Explore `error-handling/`

### Framework Users
1. Check your framework's integration example
2. Study `multi-agent-workflow/`
3. Learn from `context-sharing/`

### Advanced Users
1. Explore `distributed-agents/`
2. Implement custom transports
3. Study security patterns
4. Build production deployments

## 🧪 Testing Examples

Each example includes tests:
```bash
# Run tests for specific example
cd examples/hello-world
pytest tests/

# Run all example tests
pytest examples/
```

## 📚 Documentation Links

- [Protocol Specification](../docs/agent-interoperability-protocol.md)
- [Implementation Guide](../docs/implementation-guide.md)
- [API Reference](https://docs.agentmeshprotocol.io/api)
- [More Examples](https://github.com/agentmeshprotocol/amp-examples)

## 🤝 Contributing Examples

We welcome new examples! To contribute:

1. Create a new directory with descriptive name
2. Include comprehensive README.md
3. Add requirements.txt for dependencies
4. Provide clear, commented code
5. Include tests if possible
6. Submit PR with description

### Example Guidelines
- Keep examples focused on one concept
- Use clear variable and function names
- Include error handling
- Add helpful comments
- Test on Python 3.8+

## 📄 License

All examples are provided under the MIT License. Feel free to use them as starting points for your own projects!