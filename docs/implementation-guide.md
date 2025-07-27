# AMP Implementation Guide

This comprehensive guide walks you through implementing AMP support in your AI agents, from basic message handling to advanced features.

## Table of Contents

1. [Implementation Overview](#implementation-overview)
2. [Basic Agent Implementation](#basic-agent-implementation)
3. [Message Handling](#message-handling)
4. [Capability Implementation](#capability-implementation)
5. [Context Management](#context-management)
6. [Error Handling](#error-handling)
7. [Testing Your Implementation](#testing-your-implementation)
8. [Production Considerations](#production-considerations)
9. [Framework-Specific Implementations](#framework-specific-implementations)

## Implementation Overview

### Implementation Checklist

- [ ] Define agent identity and metadata
- [ ] Implement message parsing and validation
- [ ] Create capability handlers
- [ ] Set up transport mechanism
- [ ] Implement discovery registration
- [ ] Add context management
- [ ] Handle errors gracefully
- [ ] Write comprehensive tests
- [ ] Document your capabilities
- [ ] Deploy with monitoring

### Required Components

1. **Message Parser**: Parse and validate AMP messages
2. **Capability Registry**: Manage agent capabilities
3. **Request Handler**: Process incoming requests
4. **Response Builder**: Create compliant responses
5. **Transport Layer**: Send/receive messages
6. **Error Handler**: Manage failures gracefully

## Basic Agent Implementation

### Step 1: Define Your Agent

```python
from amp_sdk import AMPAgent, AgentIdentity, Capability
from datetime import datetime
import uuid

class MyCustomAgent:
    def __init__(self):
        # Define agent identity
        self.identity = AgentIdentity(
            id=f"agent-{uuid.uuid4()}",
            name="My Custom Agent",
            version="1.0.0",
            framework="custom",
            protocol_version="1.0"
        )
        
        # Initialize capabilities registry
        self.capabilities = {}
        
        # Setup transport
        self.transport = None
        
        # Context storage
        self.contexts = {}
    
    def register_capability(self, capability: Capability, handler):
        """Register a capability with its handler"""
        self.capabilities[capability.id] = {
            'definition': capability,
            'handler': handler
        }
```

### Step 2: Implement Message Handling

```python
class MessageHandler:
    def __init__(self, agent: MyCustomAgent):
        self.agent = agent
    
    async def handle_message(self, raw_message: str) -> str:
        """Main entry point for message processing"""
        try:
            # Parse the message
            message = self.parse_message(raw_message)
            
            # Validate the message
            self.validate_message(message)
            
            # Route based on message type
            if message['type'] == 'request':
                response = await self.handle_request(message)
            elif message['type'] == 'event':
                response = await self.handle_event(message)
            else:
                response = self.create_error_response(
                    "UNSUPPORTED_MESSAGE_TYPE",
                    f"Message type '{message['type']}' not supported"
                )
            
            return json.dumps(response)
            
        except Exception as e:
            return json.dumps(self.create_error_response(
                "INTERNAL_ERROR",
                str(e)
            ))
    
    def parse_message(self, raw_message: str) -> dict:
        """Parse and validate JSON message"""
        try:
            data = json.loads(raw_message)
            if data.get('protocol') != 'AMP/1.0':
                raise ValueError(f"Unsupported protocol: {data.get('protocol')}")
            return data['message']
        except json.JSONDecodeError as e:
            raise ValueError(f"Invalid JSON: {e}")
    
    def validate_message(self, message: dict):
        """Validate message structure"""
        required_fields = ['id', 'type', 'timestamp', 'source', 'destination', 'payload']
        for field in required_fields:
            if field not in message:
                raise ValueError(f"Missing required field: {field}")
```

### Step 3: Implement Request Processing

```python
async def handle_request(self, message: dict) -> dict:
    """Process capability requests"""
    payload = message['payload']
    capability_id = payload.get('capability')
    
    # Check if we support this capability
    if capability_id not in self.agent.capabilities:
        return self.create_error_response(
            "CAPABILITY_NOT_FOUND",
            f"Capability '{capability_id}' not supported",
            available_capabilities=list(self.agent.capabilities.keys())
        )
    
    # Get the capability handler
    capability_info = self.agent.capabilities[capability_id]
    handler = capability_info['handler']
    definition = capability_info['definition']
    
    # Validate parameters against schema
    parameters = payload.get('parameters', {})
    validation_result = self.validate_parameters(parameters, definition.input_schema)
    if not validation_result.is_valid:
        return self.create_error_response(
            "INVALID_PARAMETERS",
            validation_result.error_message
        )
    
    # Execute the capability
    try:
        # Apply context if provided
        context = self.get_context(message)
        result = await handler.execute(parameters, context)
        
        # Validate output against schema
        self.validate_output(result, definition.output_schema)
        
        # Create success response
        return self.create_success_response(
            message_id=message['id'],
            result=result,
            metadata={
                'execution_time_ms': handler.execution_time,
                'model_used': handler.model_used
            }
        )
        
    except Exception as e:
        return self.create_error_response(
            "EXECUTION_ERROR",
            f"Failed to execute capability: {str(e)}"
        )
```

## Capability Implementation

### Creating Capability Handlers

```python
from abc import ABC, abstractmethod
from typing import Dict, Any, Optional

class CapabilityHandler(ABC):
    """Base class for capability handlers"""
    
    def __init__(self):
        self.execution_time = 0
        self.model_used = None
    
    @abstractmethod
    async def execute(self, parameters: Dict[str, Any], 
                     context: Optional[Dict[str, Any]] = None) -> Dict[str, Any]:
        """Execute the capability with given parameters"""
        pass
    
    def validate_input(self, parameters: Dict[str, Any]) -> bool:
        """Additional runtime validation"""
        return True
    
    def prepare_output(self, result: Any) -> Dict[str, Any]:
        """Format output according to capability schema"""
        return result
```

### Example: Text Analysis Capability

```python
class TextAnalysisHandler(CapabilityHandler):
    """Handler for text analysis capability"""
    
    def __init__(self, nlp_model):
        super().__init__()
        self.nlp_model = nlp_model
    
    async def execute(self, parameters: Dict[str, Any], 
                     context: Optional[Dict[str, Any]] = None) -> Dict[str, Any]:
        start_time = time.time()
        
        # Extract parameters
        text = parameters['text']
        analysis_types = parameters.get('analysis_types', ['sentiment'])
        language_hint = parameters.get('language_hint')
        
        # Apply context preferences if available
        if context and 'user_preferences' in context:
            preferences = context['user_preferences']
            if 'language' in preferences and not language_hint:
                language_hint = preferences['language']
        
        result = {}
        
        # Perform requested analyses
        if 'sentiment' in analysis_types:
            result['sentiment'] = await self.analyze_sentiment(text)
        
        if 'entities' in analysis_types:
            result['entities'] = await self.extract_entities(text)
        
        if 'topics' in analysis_types:
            result['topics'] = await self.extract_topics(text)
        
        if 'language' in analysis_types:
            result['language'] = await self.detect_language(text, language_hint)
        
        if 'statistics' in analysis_types:
            result['statistics'] = self.calculate_statistics(text)
        
        self.execution_time = int((time.time() - start_time) * 1000)
        return result
    
    async def analyze_sentiment(self, text: str) -> Dict[str, Any]:
        # Use your NLP model for sentiment analysis
        sentiment_result = self.nlp_model.analyze_sentiment(text)
        return {
            'label': sentiment_result.label,
            'score': sentiment_result.score,
            'confidence': sentiment_result.confidence
        }
    
    async def extract_entities(self, text: str) -> List[Dict[str, Any]]:
        # Entity extraction logic
        entities = self.nlp_model.extract_entities(text)
        return [
            {
                'text': entity.text,
                'type': entity.type,
                'start': entity.start,
                'end': entity.end,
                'confidence': entity.confidence
            }
            for entity in entities
        ]
```

### Capability Registration

```python
# Define capability
text_analysis_capability = Capability(
    id="text-analysis",
    version="1.0",
    description="Analyze and extract insights from text",
    input_schema={
        "type": "object",
        "properties": {
            "text": {
                "type": "string",
                "maxLength": 50000
            },
            "analysis_types": {
                "type": "array",
                "items": {
                    "enum": ["sentiment", "entities", "topics", "language", "statistics"]
                }
            },
            "language_hint": {
                "type": "string",
                "pattern": "^[a-z]{2}(-[A-Z]{2})?$"
            }
        },
        "required": ["text"]
    },
    output_schema={
        "type": "object",
        "properties": {
            "sentiment": {"$ref": "#/definitions/SentimentResult"},
            "entities": {"$ref": "#/definitions/EntitiesResult"},
            "topics": {"$ref": "#/definitions/TopicsResult"}
        }
    },
    constraints=CapabilityConstraints(
        max_input_length=50000,
        supported_languages=["en", "es", "fr", "de"],
        response_time_ms=5000
    )
)

# Register with agent
agent.register_capability(
    text_analysis_capability,
    TextAnalysisHandler(nlp_model)
)
```

## Context Management

### Implementing Context Support

```python
class ContextManager:
    """Manages context for agent interactions"""
    
    def __init__(self):
        self.contexts = {}
        self.context_expiry = {}
    
    def create_context(self, context_id: str, initial_data: Dict[str, Any], 
                      expires_at: Optional[datetime] = None) -> None:
        """Create a new context"""
        self.contexts[context_id] = {
            'id': context_id,
            'created_at': datetime.now(timezone.utc),
            'expires_at': expires_at,
            'data': initial_data,
            'access_log': []
        }
        
        if expires_at:
            self.schedule_expiry(context_id, expires_at)
    
    def get_context(self, context_id: str) -> Optional[Dict[str, Any]]:
        """Retrieve context data"""
        if context_id not in self.contexts:
            return None
        
        context = self.contexts[context_id]
        
        # Check if expired
        if context['expires_at'] and datetime.now(timezone.utc) > context['expires_at']:
            self.delete_context(context_id)
            return None
        
        # Log access
        context['access_log'].append({
            'timestamp': datetime.now(timezone.utc),
            'action': 'read'
        })
        
        return context['data']
    
    def update_context(self, context_id: str, updates: Dict[str, Any]) -> bool:
        """Update context data"""
        if context_id not in self.contexts:
            return False
        
        context = self.contexts[context_id]
        context['data'].update(updates)
        context['access_log'].append({
            'timestamp': datetime.now(timezone.utc),
            'action': 'update',
            'updates': list(updates.keys())
        })
        
        return True
    
    def fork_context(self, parent_id: str, child_id: str) -> bool:
        """Create a child context from parent"""
        parent = self.get_context(parent_id)
        if not parent:
            return False
        
        self.create_context(
            child_id,
            {
                **parent,
                'parent_context': parent_id
            }
        )
        return True
```

### Using Context in Handlers

```python
class ContextAwareHandler(CapabilityHandler):
    """Handler that uses context for personalization"""
    
    async def execute(self, parameters: Dict[str, Any], 
                     context: Optional[Dict[str, Any]] = None) -> Dict[str, Any]:
        # Get user preferences from context
        user_preferences = {}
        if context and 'user_preferences' in context:
            user_preferences = context['user_preferences']
        
        # Get conversation history
        history = []
        if context and 'conversation_history' in context:
            history = context['conversation_history']
        
        # Use context to improve response
        response = await self.generate_response(
            parameters,
            preferences=user_preferences,
            history=history
        )
        
        # Update context with new information
        if context and 'session_id' in context:
            self.update_context_with_response(context['session_id'], response)
        
        return response
```

## Error Handling

### Standard Error Responses

```python
class ErrorHandler:
    """Standardized error handling"""
    
    ERROR_CODES = {
        'AGENT_NOT_FOUND': 'Target agent does not exist',
        'CAPABILITY_NOT_FOUND': 'Requested capability unavailable',
        'AUTHENTICATION_FAILED': 'Invalid credentials',
        'RATE_LIMIT_EXCEEDED': 'Too many requests',
        'TIMEOUT': 'Request exceeded timeout',
        'INVALID_SCHEMA': 'Input does not match schema',
        'CONTEXT_NOT_FOUND': 'Referenced context does not exist',
        'INSUFFICIENT_RESOURCES': 'Agent lacks resources'
    }
    
    def create_error_response(self, error_code: str, message: str, 
                            details: Optional[Dict[str, Any]] = None) -> dict:
        """Create standardized error response"""
        return {
            "protocol": "AMP/1.0",
            "message": {
                "id": str(uuid.uuid4()),
                "type": "error",
                "timestamp": datetime.now(timezone.utc).isoformat(),
                "payload": {
                    "error": {
                        "code": error_code,
                        "message": message,
                        "details": details or {}
                    }
                }
            }
        }
    
    def handle_exception(self, exception: Exception) -> dict:
        """Convert exceptions to AMP errors"""
        if isinstance(exception, CapabilityNotFoundError):
            return self.create_error_response(
                'CAPABILITY_NOT_FOUND',
                str(exception),
                {'available_capabilities': exception.available_capabilities}
            )
        elif isinstance(exception, ValidationError):
            return self.create_error_response(
                'INVALID_SCHEMA',
                str(exception),
                {'validation_errors': exception.errors}
            )
        elif isinstance(exception, TimeoutError):
            return self.create_error_response(
                'TIMEOUT',
                'Request processing exceeded timeout limit'
            )
        else:
            # Generic error for unexpected exceptions
            return self.create_error_response(
                'INTERNAL_ERROR',
                'An unexpected error occurred',
                {'error_type': type(exception).__name__}
            )
```

### Retry Logic

```python
class RetryHandler:
    """Handle retries for failed operations"""
    
    def __init__(self, max_retries: int = 3, backoff_multiplier: float = 2.0):
        self.max_retries = max_retries
        self.backoff_multiplier = backoff_multiplier
    
    async def execute_with_retry(self, operation, *args, **kwargs):
        """Execute operation with exponential backoff retry"""
        last_exception = None
        
        for attempt in range(self.max_retries):
            try:
                return await operation(*args, **kwargs)
            except (TimeoutError, ConnectionError) as e:
                last_exception = e
                if attempt < self.max_retries - 1:
                    wait_time = (self.backoff_multiplier ** attempt)
                    await asyncio.sleep(wait_time)
                    continue
            except Exception as e:
                # Don't retry on non-retryable errors
                raise e
        
        raise last_exception
```

## Testing Your Implementation

### Unit Testing Capabilities

```python
import pytest
from unittest.mock import Mock, AsyncMock

class TestTextAnalysisCapability:
    """Test text analysis capability"""
    
    @pytest.fixture
    def handler(self):
        mock_model = Mock()
        return TextAnalysisHandler(mock_model)
    
    @pytest.mark.asyncio
    async def test_sentiment_analysis(self, handler):
        # Arrange
        handler.nlp_model.analyze_sentiment = Mock(return_value=Mock(
            label='positive',
            score=0.85,
            confidence=0.92
        ))
        
        parameters = {
            'text': 'I love this product!',
            'analysis_types': ['sentiment']
        }
        
        # Act
        result = await handler.execute(parameters)
        
        # Assert
        assert 'sentiment' in result
        assert result['sentiment']['label'] == 'positive'
        assert result['sentiment']['score'] == 0.85
        assert result['sentiment']['confidence'] == 0.92
    
    @pytest.mark.asyncio
    async def test_context_usage(self, handler):
        # Test that context is properly used
        parameters = {'text': 'Bonjour!', 'analysis_types': ['language']}
        context = {'user_preferences': {'language': 'fr'}}
        
        handler.detect_language = AsyncMock(return_value={
            'detected': 'fr',
            'confidence': 0.99
        })
        
        result = await handler.execute(parameters, context)
        
        # Verify language hint from context was used
        handler.detect_language.assert_called_with('Bonjour!', 'fr')
```

### Integration Testing

```python
class TestAMPIntegration:
    """Test full AMP message flow"""
    
    @pytest.mark.asyncio
    async def test_request_response_flow(self):
        # Create test agent
        agent = MyCustomAgent()
        handler = MessageHandler(agent)
        
        # Register test capability
        agent.register_capability(
            test_capability,
            TestCapabilityHandler()
        )
        
        # Create test request
        request = {
            "protocol": "AMP/1.0",
            "message": {
                "id": "test-123",
                "type": "request",
                "timestamp": datetime.now(timezone.utc).isoformat(),
                "source": {"agent_id": "test-client"},
                "destination": {"capability": "test-capability"},
                "headers": {},
                "payload": {
                    "capability": "test-capability",
                    "parameters": {"input": "test"}
                }
            }
        }
        
        # Process request
        response_json = await handler.handle_message(json.dumps(request))
        response = json.loads(response_json)
        
        # Verify response
        assert response['protocol'] == 'AMP/1.0'
        assert response['message']['type'] == 'response'
        assert response['message']['payload']['status'] == 'success'
```

### Protocol Compliance Testing

```python
class AMPComplianceTestSuite:
    """Verify AMP protocol compliance"""
    
    def test_message_structure(self, message: dict):
        """Validate message follows AMP structure"""
        assert 'protocol' in message
        assert message['protocol'] == 'AMP/1.0'
        
        msg = message['message']
        required_fields = ['id', 'type', 'timestamp', 'payload']
        for field in required_fields:
            assert field in msg
        
        # Validate timestamp format
        datetime.fromisoformat(msg['timestamp'].replace('Z', '+00:00'))
    
    def test_capability_schema(self, capability: Capability):
        """Validate capability definition"""
        assert capability.id
        assert capability.version
        assert capability.description
        assert 'type' in capability.input_schema
        assert 'type' in capability.output_schema
```

## Production Considerations

### Health Monitoring

```python
class HealthMonitor:
    """Monitor agent health and performance"""
    
    def __init__(self, agent: MyCustomAgent):
        self.agent = agent
        self.metrics = {
            'requests_total': 0,
            'requests_failed': 0,
            'average_latency_ms': 0,
            'capability_usage': {}
        }
    
    async def health_check(self) -> Dict[str, Any]:
        """Perform health check"""
        checks = {
            'status': 'healthy',
            'timestamp': datetime.now(timezone.utc).isoformat(),
            'checks': []
        }
        
        # Check capability availability
        for cap_id, cap_info in self.agent.capabilities.items():
            try:
                # Simple validation check
                test_params = self.get_test_parameters(cap_id)
                await cap_info['handler'].validate_input(test_params)
                checks['checks'].append({
                    'capability': cap_id,
                    'status': 'available'
                })
            except Exception as e:
                checks['status'] = 'degraded'
                checks['checks'].append({
                    'capability': cap_id,
                    'status': 'unavailable',
                    'error': str(e)
                })
        
        return checks
```

### Performance Optimization

```python
class PerformanceOptimizer:
    """Optimize agent performance"""
    
    def __init__(self):
        self.cache = TTLCache(maxsize=1000, ttl=300)  # 5-minute cache
        self.connection_pool = ConnectionPool(max_size=50)
    
    async def cached_execute(self, capability_id: str, parameters: Dict[str, Any]):
        """Execute with caching"""
        cache_key = f"{capability_id}:{hash(json.dumps(parameters, sort_keys=True))}"
        
        # Check cache
        if cache_key in self.cache:
            return self.cache[cache_key]
        
        # Execute
        result = await self.execute_capability(capability_id, parameters)
        
        # Cache result
        self.cache[cache_key] = result
        return result
    
    def optimize_message_handling(self):
        """Batch message processing"""
        return MessageBatcher(
            batch_size=10,
            timeout_ms=100,
            process_batch=self.process_message_batch
        )
```

### Deployment Configuration

```yaml
# amp-agent-config.yaml
agent:
  id: "prod-agent-001"
  name: "Production Text Analysis Agent"
  version: "1.0.0"
  framework: "custom"

capabilities:
  - id: "text-analysis"
    enabled: true
    constraints:
      max_input_length: 50000
      response_time_ms: 5000
      rate_limit:
        requests_per_minute: 100
        burst: 20

transport:
  http:
    enabled: true
    port: 8080
    ssl:
      enabled: true
      cert_file: "/certs/agent.crt"
      key_file: "/certs/agent.key"
  
  websocket:
    enabled: true
    port: 8081
    max_connections: 1000

registry:
  endpoint: "https://amp-registry.example.com"
  heartbeat_interval_seconds: 30
  reconnect_max_retries: 5

monitoring:
  metrics:
    enabled: true
    endpoint: "/metrics"
    format: "prometheus"
  
  tracing:
    enabled: true
    provider: "opentelemetry"
    endpoint: "http://jaeger:4318"

logging:
  level: "INFO"
  format: "json"
  outputs:
    - type: "file"
      path: "/var/log/amp-agent.log"
    - type: "stdout"
```

## Framework-Specific Implementations

### LangChain Implementation

```python
from langchain.agents import Tool
from langchain.callbacks import AsyncCallbackHandler

class AMPLangChainAgent:
    """LangChain agent with AMP support"""
    
    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        self.amp_tools = []
    
    def create_amp_tool(self, capability_id: str) -> Tool:
        """Create LangChain tool from AMP capability"""
        return Tool(
            name=f"amp_{capability_id}",
            description=f"Invoke AMP capability: {capability_id}",
            func=lambda input: self.invoke_capability(capability_id, input),
            coroutine=lambda input: self.ainvoke_capability(capability_id, input)
        )
    
    async def ainvoke_capability(self, capability_id: str, input: str):
        """Async invoke AMP capability"""
        message = self.create_amp_request(capability_id, {"input": input})
        response = await self.send_amp_message(message)
        return response.payload.get('result')

class AMPCallbackHandler(AsyncCallbackHandler):
    """Callback handler for AMP events"""
    
    async def on_tool_start(self, serialized: Dict[str, Any], 
                           input_str: str, **kwargs):
        """Log AMP tool invocations"""
        if serialized.get("name", "").startswith("amp_"):
            await self.send_amp_event({
                "event_type": "tool_start",
                "tool": serialized["name"],
                "input": input_str
            })
```

### CrewAI Implementation

```python
from crewai import Agent, Task
from typing import List

class AMPCrewAIAgent(Agent):
    """CrewAI agent with AMP capabilities"""
    
    def __init__(self, amp_capabilities: List[str], **kwargs):
        super().__init__(**kwargs)
        self.amp_capabilities = amp_capabilities
        self.amp_client = AMPClient()
    
    def execute_task(self, task: Task) -> str:
        """Execute task using AMP capabilities"""
        # Determine required capability
        required_capability = self.match_task_to_capability(task)
        
        if required_capability in self.amp_capabilities:
            # Use AMP to execute
            result = self.amp_client.request(
                capability=required_capability,
                parameters={
                    "task_description": task.description,
                    "context": task.context
                }
            )
            return result.get('output', '')
        else:
            # Delegate to another agent via AMP
            return self.delegate_via_amp(task, required_capability)
```

### AutoGen Implementation

```python
from autogen import ConversableAgent

class AMPAutoGenAgent(ConversableAgent):
    """AutoGen agent with AMP support"""
    
    def __init__(self, amp_capabilities: Dict[str, bool], **kwargs):
        super().__init__(**kwargs)
        self.amp_capabilities = amp_capabilities
        self.amp_handler = AMPMessageHandler()
    
    def generate_reply(self, messages: List[Dict[str, str]], 
                      sender: ConversableAgent) -> str:
        """Generate reply using AMP capabilities"""
        last_message = messages[-1]["content"]
        
        # Check if this requires an AMP capability
        required_capability = self.analyze_message_needs(last_message)
        
        if required_capability and self.amp_capabilities.get(required_capability):
            # Use AMP capability
            response = self.invoke_amp_capability(
                required_capability,
                {"message": last_message, "history": messages[:-1]}
            )
            return response
        else:
            # Use standard AutoGen reply generation
            return super().generate_reply(messages, sender)
```

## Summary

This implementation guide covered:

1. **Basic Setup**: Creating an AMP-compatible agent from scratch
2. **Message Handling**: Parsing, validating, and processing AMP messages
3. **Capabilities**: Implementing and registering capability handlers
4. **Context Management**: Maintaining state across interactions
5. **Error Handling**: Graceful failure management
6. **Testing**: Comprehensive testing strategies
7. **Production**: Deployment and monitoring considerations
8. **Framework Integration**: Adapting AMP to popular frameworks

Key takeaways:
- Start simple with basic message handling
- Add capabilities incrementally
- Test thoroughly at each step
- Monitor performance in production
- Leverage existing framework features

Ready to implement? Check out our [example implementations](https://github.com/agentmeshprotocol/examples) for complete working code!