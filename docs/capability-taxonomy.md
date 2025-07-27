# AMP Capability Taxonomy v1.0
*Comprehensive standardized capability definitions for AI agents in the Agent Mesh Protocol*

## Overview

This taxonomy provides a hierarchical classification of agent capabilities with standardized identifiers, schemas, and semantic definitions. Each capability is versioned and includes clear input/output specifications.

## Core Capability Categories

### 1. Text Processing (text-*)

#### text-analysis
Analyze and extract insights from text content.

```json
{
  "id": "text-analysis",
  "category": "text-processing",
  "subcategories": ["sentiment", "entities", "topics", "language", "statistics"],
  "input_schema": {
    "type": "object",
    "properties": {
      "text": {
        "type": "string",
        "description": "Text to analyze",
        "maxLength": 50000
      },
      "analysis_types": {
        "type": "array",
        "items": {
          "enum": ["sentiment", "entities", "topics", "language", "statistics", "all"]
        }
      },
      "language_hint": {
        "type": "string",
        "pattern": "^[a-z]{2}(-[A-Z]{2})?$"
      }
    },
    "required": ["text"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "sentiment": {
        "type": "object",
        "properties": {
          "label": {"enum": ["positive", "negative", "neutral", "mixed"]},
          "score": {"type": "number", "minimum": -1, "maximum": 1},
          "confidence": {"type": "number", "minimum": 0, "maximum": 1}
        }
      },
      "entities": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "text": {"type": "string"},
            "type": {"enum": ["PERSON", "ORGANIZATION", "LOCATION", "DATE", "TIME", "MONEY", "PERCENTAGE", "PRODUCT", "EVENT", "OTHER"]},
            "start": {"type": "integer"},
            "end": {"type": "integer"},
            "confidence": {"type": "number"}
          }
        }
      },
      "topics": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "label": {"type": "string"},
            "score": {"type": "number", "minimum": 0, "maximum": 1},
            "keywords": {"type": "array", "items": {"type": "string"}}
          }
        }
      },
      "language": {
        "type": "object",
        "properties": {
          "detected": {"type": "string"},
          "confidence": {"type": "number"}
        }
      },
      "statistics": {
        "type": "object",
        "properties": {
          "char_count": {"type": "integer"},
          "word_count": {"type": "integer"},
          "sentence_count": {"type": "integer"},
          "avg_word_length": {"type": "number"},
          "readability_score": {"type": "number"}
        }
      }
    }
  }
}
```

#### text-summarization
Create concise summaries of longer texts.

```json
{
  "id": "text-summarization",
  "category": "text-processing",
  "variants": ["extractive", "abstractive", "headline", "bullets"],
  "input_schema": {
    "type": "object",
    "properties": {
      "text": {"type": "string", "minLength": 100},
      "summary_type": {"enum": ["extractive", "abstractive", "headline", "bullets"]},
      "max_length": {"type": "integer", "minimum": 10, "maximum": 1000},
      "length_type": {"enum": ["words", "sentences", "characters", "percentage"]},
      "preserve_entities": {"type": "boolean", "default": true},
      "focus_topics": {"type": "array", "items": {"type": "string"}}
    },
    "required": ["text"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "summary": {"type": "string"},
      "summary_type": {"type": "string"},
      "compression_ratio": {"type": "number"},
      "key_points": {"type": "array", "items": {"type": "string"}},
      "preserved_entities": {"type": "array", "items": {"type": "string"}}
    }
  }
}
```

#### text-translation
Translate text between languages.

```json
{
  "id": "text-translation",
  "category": "text-processing",
  "input_schema": {
    "type": "object",
    "properties": {
      "text": {"type": "string"},
      "source_language": {"type": "string", "pattern": "^[a-z]{2}(-[A-Z]{2})?$"},
      "target_language": {"type": "string", "pattern": "^[a-z]{2}(-[A-Z]{2})?$"},
      "preserve_formatting": {"type": "boolean", "default": true},
      "formality": {"enum": ["formal", "informal", "auto"]},
      "domain": {"enum": ["general", "technical", "medical", "legal", "financial"]}
    },
    "required": ["text", "target_language"]
  }
}
```

#### text-classification
Classify text into predefined or dynamic categories.

```json
{
  "id": "text-classification",
  "category": "text-processing",
  "input_schema": {
    "type": "object",
    "properties": {
      "text": {"type": "string"},
      "classification_type": {"enum": ["topic", "intent", "emotion", "spam", "toxicity", "custom"]},
      "categories": {
        "type": "array",
        "items": {"type": "string"},
        "description": "For custom classification"
      },
      "multi_label": {"type": "boolean", "default": false},
      "threshold": {"type": "number", "minimum": 0, "maximum": 1}
    },
    "required": ["text", "classification_type"]
  }
}
```

### 2. Generation (gen-*)

#### gen-text
Generate text content based on prompts or templates.

```json
{
  "id": "gen-text",
  "category": "generation",
  "variants": ["creative", "structured", "completion", "rewrite"],
  "input_schema": {
    "type": "object",
    "properties": {
      "prompt": {"type": "string"},
      "generation_type": {"enum": ["creative", "structured", "completion", "rewrite"]},
      "max_tokens": {"type": "integer", "minimum": 1, "maximum": 10000},
      "temperature": {"type": "number", "minimum": 0, "maximum": 2},
      "style": {"type": "string", "description": "Writing style guide"},
      "format": {"enum": ["plain", "markdown", "html", "json"]},
      "constraints": {
        "type": "object",
        "properties": {
          "avoid_topics": {"type": "array", "items": {"type": "string"}},
          "include_keywords": {"type": "array", "items": {"type": "string"}},
          "tone": {"enum": ["professional", "casual", "academic", "creative"]}
        }
      }
    },
    "required": ["prompt"]
  }
}
```

#### gen-code
Generate programming code.

```json
{
  "id": "gen-code",
  "category": "generation",
  "input_schema": {
    "type": "object",
    "properties": {
      "description": {"type": "string"},
      "language": {"type": "string"},
      "framework": {"type": "string"},
      "code_type": {"enum": ["function", "class", "module", "script", "test"]},
      "include_comments": {"type": "boolean", "default": true},
      "include_docstrings": {"type": "boolean", "default": true},
      "style_guide": {"type": "string"},
      "dependencies": {"type": "array", "items": {"type": "string"}},
      "context_code": {"type": "string", "description": "Existing code for context"}
    },
    "required": ["description", "language"]
  }
}
```

#### gen-structured-data
Generate structured data in various formats.

```json
{
  "id": "gen-structured-data",
  "category": "generation",
  "input_schema": {
    "type": "object",
    "properties": {
      "description": {"type": "string"},
      "format": {"enum": ["json", "yaml", "xml", "csv", "sql"]},
      "schema": {"type": "object", "description": "Target schema"},
      "examples": {"type": "array", "items": {"type": "object"}},
      "row_count": {"type": "integer", "description": "For tabular data"},
      "realistic": {"type": "boolean", "default": true}
    },
    "required": ["description", "format"]
  }
}
```

### 3. Question Answering (qa-*)

#### qa-factual
Answer factual questions based on knowledge or context.

```json
{
  "id": "qa-factual",
  "category": "question-answering",
  "input_schema": {
    "type": "object",
    "properties": {
      "question": {"type": "string"},
      "context": {"type": "string", "description": "Optional context"},
      "sources": {"type": "array", "items": {"type": "string"}},
      "require_citation": {"type": "boolean", "default": false},
      "answer_format": {"enum": ["short", "detailed", "list", "yes_no"]},
      "confidence_threshold": {"type": "number", "minimum": 0, "maximum": 1}
    },
    "required": ["question"]
  }
}
```

#### qa-reasoning
Answer questions requiring logical or mathematical reasoning.

```json
{
  "id": "qa-reasoning",
  "category": "question-answering",
  "subtypes": ["logical", "mathematical", "causal", "hypothetical"],
  "input_schema": {
    "type": "object",
    "properties": {
      "question": {"type": "string"},
      "reasoning_type": {"enum": ["logical", "mathematical", "causal", "hypothetical"]},
      "show_steps": {"type": "boolean", "default": true},
      "assumptions": {"type": "array", "items": {"type": "string"}},
      "constraints": {"type": "object"}
    },
    "required": ["question"]
  }
}
```

#### qa-conversational
Handle conversational Q&A with context awareness.

```json
{
  "id": "qa-conversational",
  "category": "question-answering",
  "input_schema": {
    "type": "object",
    "properties": {
      "question": {"type": "string"},
      "conversation_history": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "role": {"enum": ["user", "assistant"]},
            "content": {"type": "string"}
          }
        }
      },
      "user_profile": {"type": "object"},
      "maintain_context": {"type": "boolean", "default": true}
    },
    "required": ["question"]
  }
}
```

### 4. Data Processing (data-*)

#### data-extraction
Extract structured information from unstructured sources.

```json
{
  "id": "data-extraction",
  "category": "data-processing",
  "input_schema": {
    "type": "object",
    "properties": {
      "source": {"type": "string", "description": "Text, HTML, or document"},
      "source_type": {"enum": ["text", "html", "pdf", "image", "audio"]},
      "extraction_schema": {
        "type": "object",
        "description": "Schema defining what to extract"
      },
      "extraction_rules": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "field": {"type": "string"},
            "type": {"type": "string"},
            "pattern": {"type": "string"},
            "required": {"type": "boolean"}
          }
        }
      }
    },
    "required": ["source", "extraction_schema"]
  }
}
```

#### data-transformation
Transform data between formats or structures.

```json
{
  "id": "data-transformation",
  "category": "data-processing",
  "input_schema": {
    "type": "object",
    "properties": {
      "data": {"type": ["object", "array", "string"]},
      "source_format": {"type": "string"},
      "target_format": {"type": "string"},
      "transformation_rules": {"type": "array"},
      "mapping": {"type": "object"},
      "validation": {"type": "boolean", "default": true}
    },
    "required": ["data", "target_format"]
  }
}
```

#### data-validation
Validate data against schemas or business rules.

```json
{
  "id": "data-validation",
  "category": "data-processing",
  "input_schema": {
    "type": "object",
    "properties": {
      "data": {"type": ["object", "array"]},
      "validation_type": {"enum": ["schema", "business_rules", "quality", "completeness"]},
      "schema": {"type": "object"},
      "rules": {"type": "array"},
      "fix_errors": {"type": "boolean", "default": false},
      "detailed_report": {"type": "boolean", "default": true}
    },
    "required": ["data", "validation_type"]
  }
}
```

### 5. Analysis & Reasoning (analysis-*)

#### analysis-sentiment
Deep sentiment and emotion analysis.

```json
{
  "id": "analysis-sentiment",
  "category": "analysis",
  "input_schema": {
    "type": "object",
    "properties": {
      "text": {"type": "string"},
      "granularity": {"enum": ["document", "sentence", "aspect"]},
      "emotions": {"type": "boolean", "default": false},
      "aspects": {"type": "array", "items": {"type": "string"}},
      "cultural_context": {"type": "string"}
    },
    "required": ["text"]
  }
}
```

#### analysis-pattern
Identify patterns in data.

```json
{
  "id": "analysis-pattern",
  "category": "analysis",
  "input_schema": {
    "type": "object",
    "properties": {
      "data": {"type": "array"},
      "pattern_type": {"enum": ["temporal", "spatial", "behavioral", "anomaly"]},
      "time_window": {"type": "string"},
      "confidence_threshold": {"type": "number"},
      "max_patterns": {"type": "integer"}
    },
    "required": ["data", "pattern_type"]
  }
}
```

#### analysis-comparison
Compare and contrast multiple items.

```json
{
  "id": "analysis-comparison",
  "category": "analysis",
  "input_schema": {
    "type": "object",
    "properties": {
      "items": {"type": "array", "minItems": 2},
      "comparison_criteria": {"type": "array", "items": {"type": "string"}},
      "comparison_type": {"enum": ["feature", "performance", "quality", "cost"]},
      "output_format": {"enum": ["table", "narrative", "scores"]}
    },
    "required": ["items"]
  }
}
```

### 6. Tool Use & Integration (tool-*)

#### tool-web-search
Search the internet for information.

```json
{
  "id": "tool-web-search",
  "category": "tool-use",
  "input_schema": {
    "type": "object",
    "properties": {
      "query": {"type": "string"},
      "search_type": {"enum": ["general", "news", "academic", "images", "videos"]},
      "time_range": {"enum": ["anytime", "hour", "day", "week", "month", "year"]},
      "region": {"type": "string", "pattern": "^[A-Z]{2}$"},
      "language": {"type": "string"},
      "max_results": {"type": "integer", "minimum": 1, "maximum": 100},
      "include_snippets": {"type": "boolean", "default": true}
    },
    "required": ["query"]
  }
}
```

#### tool-api-call
Make external API calls.

```json
{
  "id": "tool-api-call",
  "category": "tool-use",
  "input_schema": {
    "type": "object",
    "properties": {
      "endpoint": {"type": "string", "format": "uri"},
      "method": {"enum": ["GET", "POST", "PUT", "DELETE", "PATCH"]},
      "headers": {"type": "object"},
      "params": {"type": "object"},
      "body": {"type": ["object", "string"]},
      "auth_type": {"enum": ["none", "bearer", "api_key", "basic"]},
      "timeout": {"type": "integer"}
    },
    "required": ["endpoint", "method"]
  }
}
```

#### tool-database-query
Query databases.

```json
{
  "id": "tool-database-query",
  "category": "tool-use",
  "input_schema": {
    "type": "object",
    "properties": {
      "database_type": {"enum": ["sql", "nosql", "graph", "vector"]},
      "query": {"type": "string"},
      "parameters": {"type": "object"},
      "limit": {"type": "integer"},
      "timeout": {"type": "integer"},
      "explain": {"type": "boolean", "default": false}
    },
    "required": ["query"]
  }
}
```

### 7. Memory & Context (memory-*)

#### memory-store
Store information in long-term memory.

```json
{
  "id": "memory-store",
  "category": "memory",
  "input_schema": {
    "type": "object",
    "properties": {
      "content": {"type": "object"},
      "memory_type": {"enum": ["episodic", "semantic", "procedural"]},
      "tags": {"type": "array", "items": {"type": "string"}},
      "importance": {"type": "number", "minimum": 0, "maximum": 1},
      "expiry": {"type": "string", "format": "date-time"},
      "access_control": {"type": "object"}
    },
    "required": ["content", "memory_type"]
  }
}
```

#### memory-retrieve
Retrieve information from memory.

```json
{
  "id": "memory-retrieve",
  "category": "memory",
  "input_schema": {
    "type": "object",
    "properties": {
      "query": {"type": "string"},
      "memory_type": {"enum": ["episodic", "semantic", "procedural", "all"]},
      "filters": {
        "type": "object",
        "properties": {
          "tags": {"type": "array", "items": {"type": "string"}},
          "time_range": {"type": "object"},
          "importance_min": {"type": "number"}
        }
      },
      "max_results": {"type": "integer"},
      "include_relevance": {"type": "boolean", "default": true}
    },
    "required": ["query"]
  }
}
```

### 8. Planning & Orchestration (plan-*)

#### plan-task-decomposition
Break down complex tasks into subtasks.

```json
{
  "id": "plan-task-decomposition",
  "category": "planning",
  "input_schema": {
    "type": "object",
    "properties": {
      "task": {"type": "string"},
      "constraints": {
        "type": "object",
        "properties": {
          "deadline": {"type": "string", "format": "date-time"},
          "resources": {"type": "array"},
          "dependencies": {"type": "array"}
        }
      },
      "optimization_goal": {"enum": ["speed", "quality", "cost", "balanced"]},
      "max_depth": {"type": "integer", "minimum": 1, "maximum": 5}
    },
    "required": ["task"]
  }
}
```

#### plan-workflow-generation
Generate execution workflows.

```json
{
  "id": "plan-workflow-generation",
  "category": "planning",
  "input_schema": {
    "type": "object",
    "properties": {
      "goal": {"type": "string"},
      "available_capabilities": {"type": "array", "items": {"type": "string"}},
      "workflow_type": {"enum": ["sequential", "parallel", "conditional", "loop"]},
      "optimization_criteria": {"type": "array"},
      "include_error_handling": {"type": "boolean", "default": true}
    },
    "required": ["goal", "available_capabilities"]
  }
}
```

### 9. Multimodal (multi-*)

#### multi-image-analysis
Analyze and understand images.

```json
{
  "id": "multi-image-analysis",
  "category": "multimodal",
  "input_schema": {
    "type": "object",
    "properties": {
      "image": {"type": "string", "description": "Base64 or URL"},
      "analysis_type": {"enum": ["description", "objects", "text", "faces", "scene"]},
      "detail_level": {"enum": ["basic", "detailed", "comprehensive"]},
      "specific_questions": {"type": "array", "items": {"type": "string"}}
    },
    "required": ["image", "analysis_type"]
  }
}
```

#### multi-audio-processing
Process and analyze audio.

```json
{
  "id": "multi-audio-processing",
  "category": "multimodal",
  "input_schema": {
    "type": "object",
    "properties": {
      "audio": {"type": "string", "description": "Base64 or URL"},
      "processing_type": {"enum": ["transcription", "translation", "analysis", "enhancement"]},
      "language": {"type": "string"},
      "include_timestamps": {"type": "boolean"},
      "speaker_diarization": {"type": "boolean"}
    },
    "required": ["audio", "processing_type"]
  }
}
```

## Capability Composition

Capabilities can be composed to create complex behaviors:

### Composition Patterns

1. **Sequential**: Output of one capability feeds into another
2. **Parallel**: Multiple capabilities process same input
3. **Conditional**: Choose capability based on conditions
4. **Iterative**: Repeat capability until condition met

### Example Compositions

```json
{
  "composition": {
    "name": "research-assistant",
    "description": "Research and summarize information",
    "steps": [
      {
        "capability": "tool-web-search",
        "input_mapping": {"query": "$.topic"}
      },
      {
        "capability": "text-analysis",
        "input_mapping": {"text": "$.previous.results[*].snippet"},
        "parameters": {"analysis_types": ["topics", "entities"]}
      },
      {
        "capability": "text-summarization",
        "input_mapping": {"text": "$.previous.results[*].snippet"},
        "parameters": {"summary_type": "abstractive", "max_length": 200}
      },
      {
        "capability": "gen-text",
        "input_mapping": {
          "prompt": "Write a research summary based on: {{$.previous.summary}}"
        }
      }
    ]
  }
}
```

## Capability Versioning

Each capability follows semantic versioning:

- **Major version**: Breaking changes to schema
- **Minor version**: New optional fields or features
- **Patch version**: Bug fixes or clarifications

### Version Negotiation

```json
{
  "capability_request": {
    "id": "text-analysis",
    "version_constraint": ">=1.0.0 <2.0.0",
    "preferred_version": "1.2.0"
  }
}
```

## Capability Discovery

Agents can discover capabilities through:

1. **Registry Query**: Ask registry for agents with specific capabilities
2. **Capability Introspection**: Query agent directly for capabilities
3. **Semantic Search**: Find capabilities by description
4. **Capability Matching**: Find compatible capabilities

### Discovery Query Examples

```json
{
  "discovery_query": {
    "capability_pattern": "text-*",
    "input_compatible_with": {"type": "object", "properties": {"text": {"type": "string"}}},
    "output_includes": ["sentiment"],
    "performance_requirements": {
      "max_latency_ms": 1000,
      "min_success_rate": 0.95
    }
  }
}
```

## Best Practices

1. **Use Standard IDs**: Always use taxonomy IDs when available
2. **Version Capabilities**: Include version in capability declarations
3. **Document Constraints**: Clearly specify limits and requirements
4. **Handle Errors Gracefully**: Provide meaningful error messages
5. **Support Introspection**: Allow capability discovery
6. **Validate Inputs**: Use schemas to validate all inputs
7. **Monitor Performance**: Track capability performance metrics