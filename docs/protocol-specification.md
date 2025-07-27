# Agent Mesh Protocol (AMP) v1.0
*A standardized protocol for autonomous AI agent communication in the MeshAI ecosystem*

## Overview

The Agent Mesh Protocol (AMP) defines a standard for how AI agents discover, communicate, and collaborate across different frameworks within a mesh network. Unlike general-purpose protocols, AMP is specifically designed for the unique requirements of autonomous AI agents, including capability negotiation, context sharing, and task delegation.

## Core Concepts

### 1. Agent Identity

Every agent MUST have a unique identity that includes:

```json
{
  "agent": {
    "id": "agent-unique-identifier",
    "name": "Human-readable agent name",
    "version": "1.0.0",
    "framework": "langchain|crewai|autogen|custom",
    "protocol_version": "1.0"
  }
}
```

### 2. Capability Declaration

Agents MUST declare their capabilities using a standardized taxonomy:

```json
{
  "capabilities": [
    {
      "id": "text-analysis",
      "version": "1.0",
      "description": "Analyze and extract insights from text",
      "input_schema": {
        "$ref": "#/definitions/TextAnalysisInput"
      },
      "output_schema": {
        "$ref": "#/definitions/TextAnalysisOutput"
      },
      "constraints": {
        "max_input_length": 10000,
        "supported_languages": ["en", "es", "fr"],
        "response_time_ms": 5000
      }
    }
  ]
}
```

### 3. Message Format

All agent communications MUST follow this message structure:

```json
{
  "protocol": "AMP/1.0",
  "message": {
    "id": "unique-message-id",
    "type": "request|response|event|error",
    "timestamp": "2025-01-27T10:00:00Z",
    "source": {
      "agent_id": "source-agent-id",
      "session_id": "optional-session-id"
    },
    "destination": {
      "agent_id": "destination-agent-id",
      "capability": "required-capability"
    },
    "headers": {
      "correlation_id": "tracking-id",
      "priority": 5,
      "timeout_ms": 30000,
      "routing_hints": {}
    },
    "payload": {
      // Type-specific payload
    }
  }
}
```

## Message Types

### 1. Capability Request

Used to invoke a specific capability of another agent:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "request",
    "payload": {
      "capability": "text-analysis",
      "parameters": {
        "text": "Analyze this customer feedback...",
        "analysis_type": ["sentiment", "topics"]
      },
      "context": {
        "session_id": "user-session-123",
        "conversation_history": []
      }
    }
  }
}
```

### 2. Capability Response

Response to a capability request:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "response",
    "headers": {
      "correlation_id": "original-request-id",
      "execution_time_ms": 1234
    },
    "payload": {
      "status": "success|partial|failed",
      "result": {
        "sentiment": "positive",
        "confidence": 0.89,
        "topics": ["product-quality", "customer-service"]
      },
      "metadata": {
        "model_used": "gpt-4",
        "tokens_consumed": 150
      }
    }
  }
}
```

### 3. Discovery Request

Used to discover agents with specific capabilities:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "request",
    "destination": {
      "agent_id": "registry",
      "capability": "discovery"
    },
    "payload": {
      "query": {
        "capabilities": ["text-analysis", "summarization"],
        "constraints": {
          "max_latency_ms": 5000,
          "min_success_rate": 0.95
        }
      }
    }
  }
}
```

### 4. Context Sharing

For maintaining state across agent interactions:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "event",
    "payload": {
      "event_type": "context_update",
      "context": {
        "session_id": "shared-session-123",
        "shared_memory": {
          "user_preferences": {},
          "conversation_summary": "",
          "task_history": []
        },
        "access_control": {
          "read": ["agent-1", "agent-2"],
          "write": ["agent-1"]
        }
      }
    }
  }
}
```

## Protocol Operations

### 1. Agent Registration

Agents MUST register with the mesh before participating:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "request",
    "destination": {
      "agent_id": "registry",
      "capability": "registration"
    },
    "payload": {
      "agent": {
        "id": "my-agent-123",
        "name": "Customer Service Agent",
        "framework": "langchain"
      },
      "capabilities": [...],
      "endpoints": {
        "invoke": "https://agent.example.com/invoke",
        "health": "https://agent.example.com/health"
      },
      "authentication": {
        "type": "bearer",
        "credentials": "..."
      }
    }
  }
}
```

### 2. Capability Negotiation

Agents can negotiate task execution:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "request",
    "payload": {
      "negotiation_type": "capability_check",
      "requirements": {
        "capabilities": ["text-analysis"],
        "estimated_tokens": 1000,
        "deadline": "2025-01-27T11:00:00Z"
      }
    }
  }
}
```

### 3. Task Delegation

Agents can delegate subtasks to other agents:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "request",
    "payload": {
      "delegation": {
        "parent_task_id": "main-task-123",
        "subtask": {
          "capability": "data-extraction",
          "parameters": {...},
          "completion_callback": "https://agent.example.com/callback"
        }
      }
    }
  }
}
```

## Context Management

### Context Object Structure

```json
{
  "context": {
    "id": "context-uuid",
    "session_id": "session-uuid",
    "created_at": "2025-01-27T10:00:00Z",
    "expires_at": "2025-01-27T11:00:00Z",
    "shared_memory": {
      // Shared state accessible to all agents
    },
    "agent_memory": {
      "agent-123": {
        // Private state for specific agent
      }
    },
    "lineage": {
      "parent_context": "parent-context-id",
      "root_context": "root-context-id"
    }
  }
}
```

### Context Operations

1. **Create Context**: Initialize new shared context
2. **Get Context**: Retrieve context data
3. **Update Context**: Modify shared or private state
4. **Merge Contexts**: Combine multiple contexts
5. **Fork Context**: Create child context

## Error Handling

Standardized error responses:

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "error",
    "payload": {
      "error": {
        "code": "CAPABILITY_NOT_FOUND",
        "message": "Requested capability 'quantum-analysis' not found",
        "details": {
          "available_capabilities": ["text-analysis", "summarization"],
          "suggestion": "Try 'text-analysis' instead"
        }
      }
    }
  }
}
```

### Standard Error Codes

- `AGENT_NOT_FOUND`: Target agent doesn't exist
- `CAPABILITY_NOT_FOUND`: Requested capability unavailable
- `AUTHENTICATION_FAILED`: Invalid credentials
- `RATE_LIMIT_EXCEEDED`: Too many requests
- `TIMEOUT`: Request exceeded timeout
- `INVALID_SCHEMA`: Input doesn't match schema
- `CONTEXT_NOT_FOUND`: Referenced context doesn't exist
- `INSUFFICIENT_RESOURCES`: Agent lacks resources

## Security

### Authentication Methods

1. **API Key**: Simple key-based auth
2. **JWT**: Token-based with expiration
3. **mTLS**: Certificate-based mutual auth
4. **HMAC**: Message signing for integrity

### Message Security

```json
{
  "protocol": "AIP/1.0",
  "message": {
    "headers": {
      "authentication": {
        "type": "jwt",
        "token": "eyJ..."
      },
      "signature": {
        "algorithm": "HMAC-SHA256",
        "value": "base64-signature"
      }
    }
  }
}
```

## Transport Bindings

AIP can be transported over:

1. **HTTP/REST**: Synchronous request-response
2. **WebSocket**: Real-time bidirectional
3. **Message Queue**: Async via RabbitMQ/Kafka
4. **gRPC**: High-performance RPC

### HTTP Binding Example

```
POST /aip/v1/invoke
Content-Type: application/json
Authorization: Bearer <token>

{
  "protocol": "AIP/1.0",
  "message": {...}
}
```

## Capability Taxonomy

Standardized capability identifiers:

### Text Processing
- `text-analysis`: General text analysis
- `sentiment-analysis`: Sentiment detection
- `summarization`: Text summarization
- `translation`: Language translation
- `question-answering`: Q&A from text

### Data Processing
- `data-extraction`: Extract structured data
- `data-transformation`: Transform data formats
- `data-validation`: Validate data integrity

### Generation
- `text-generation`: Generate text content
- `code-generation`: Generate code
- `image-generation`: Generate images

### Reasoning
- `logical-reasoning`: Logical inference
- `mathematical-reasoning`: Math problem solving
- `causal-reasoning`: Cause-effect analysis

### Specialized
- `web-search`: Internet search capability
- `tool-use`: External tool usage
- `memory-retrieval`: Long-term memory access

## Implementation Requirements

### For Agent Developers

1. Implement message parsing and validation
2. Support at least one transport binding
3. Implement capability declaration
4. Handle standard error codes
5. Support context management
6. Implement health endpoint

### For Framework Adapters

1. Map framework-specific concepts to AIP
2. Handle protocol version negotiation
3. Implement message transformation
4. Support async and sync operations
5. Provide SDK/library for easy integration

## Protocol Versioning

Version format: `MAJOR.MINOR`

- Breaking changes increment MAJOR
- New features increment MINOR
- Agents MUST support version negotiation

## Examples

### Simple Q&A Exchange

```json
// Request
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "request",
    "destination": {
      "capability": "question-answering"
    },
    "payload": {
      "capability": "question-answering",
      "parameters": {
        "question": "What is the capital of France?",
        "context": "Geography questions"
      }
    }
  }
}

// Response
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "response",
    "payload": {
      "status": "success",
      "result": {
        "answer": "The capital of France is Paris.",
        "confidence": 0.99,
        "sources": ["general-knowledge"]
      }
    }
  }
}
```

### Multi-Agent Collaboration

```json
// Agent A delegates research to Agent B
{
  "protocol": "AIP/1.0",
  "message": {
    "type": "request",
    "source": {"agent_id": "agent-a"},
    "destination": {"agent_id": "agent-b"},
    "payload": {
      "capability": "web-search",
      "parameters": {
        "query": "latest AI developments 2025",
        "max_results": 10
      },
      "context": {
        "parent_task": "Create AI industry report",
        "deadline": "2025-01-27T15:00:00Z"
      }
    }
  }
}
```

## Future Extensions

Planned for future versions:

1. **Streaming Support**: For real-time data streams
2. **Batch Operations**: Multiple requests in one message
3. **Capability Composition**: Combining capabilities
4. **Quality of Service**: Guaranteed delivery levels
5. **Observability**: Built-in tracing and metrics
6. **Federation**: Cross-mesh communication