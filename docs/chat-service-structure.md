# Chat Service Structure

This document outlines the detailed folder structure and implementation guidelines for the Chat Server microservice ("The Brain") of the AI Chat Assistance Platform. The Chat Server is built using FastAPI and follows domain-driven design principles to ensure maintainability, scalability, and separation of concerns.

## Table of Contents
- [Overview](#overview)
- [Complete Folder Structure](#complete-folder-structure)
- [Key Components](#key-components)
- [Implementation Guidelines](#implementation-guidelines)
- [Common Patterns and Utilities](#common-patterns-and-utilities)
- [Code Examples](#code-examples)

## Overview

The Chat Server is responsible for:
- Processing user messages using intent classification
- Managing conversation context and session state
- Selecting appropriate AI models based on query complexity
- Generating contextually relevant responses
- Fetching product data from the Adaptor Service when needed
- Maintaining conversation history
- Implementing vector search for similar past conversations

The implementation uses FastAPI as the web framework, with a domain-driven design approach that separates the application into distinct layers:
- API Layer (controllers/routers)
- Domain Layer (services, domain models)
- Infrastructure Layer (repositories, external integrations)

## Complete Folder Structure

```
chat-service/
│
├── README.md                         # Project documentation
├── pyproject.toml                    # Dependencies and build configuration
├── Dockerfile                        # Container definition
├── .env.example                      # Example environment variables
├── .gitignore                        # Git ignore file
│
├── alembic/                          # Database migration tool
│   ├── versions/                     # Migration versions
│   ├── env.py                        # Alembic environment
│   └── alembic.ini                   # Alembic configuration
│
├── tests/                            # Test directory
│   ├── conftest.py                   # Test configuration and fixtures
│   ├── unit/                         # Unit tests
│   │   ├── domain/                   # Domain model tests
│   │   ├── services/                 # Service tests
│   │   └── utils/                    # Utility tests
│   ├── integration/                  # Integration tests
│   │   ├── api/                      # API endpoint tests
│   │   ├── repositories/             # Repository tests
│   │   └── adapters/                 # External adapter tests
│   └── e2e/                          # End-to-end tests
│
├── scripts/                          # Utility scripts
│   ├── seed_db.py                    # Database seeding script
│   └── generate_models.py            # Generate Pydantic models from OpenAPI
│
├── app/                              # Main application package
│   ├── __init__.py                   # Package initialization
│   ├── main.py                       # FastAPI application entry point
│   ├── config.py                     # Application configuration
│   │
│   ├── api/                          # API Layer
│   │   ├── __init__.py
│   │   ├── dependencies.py           # FastAPI dependencies
│   │   ├── error_handlers.py         # Global exception handlers
│   │   ├── middlewares/              # Custom middlewares
│   │   │   ├── __init__.py
│   │   │   ├── correlation_id.py     # Request correlation ID middleware
│   │   │   ├── tenant_context.py     # Multi-tenant context middleware
│   │   │   └── rate_limiting.py      # Rate limiting middleware
│   │   │
│   │   └── routers/                  # API routes grouped by domain
│   │       ├── __init__.py
│   │       ├── health.py             # Health check endpoints
│   │       ├── conversations.py      # Conversation management endpoints
│   │       ├── messages.py           # Message processing endpoints
│   │       ├── intents.py            # Intent classification endpoints
│   │       ├── models.py             # AI model endpoints
│   │       └── admin.py              # Admin operations endpoints
│   │
│   ├── domain/                       # Domain Layer
│   │   ├── __init__.py
│   │   │
│   │   ├── models/                   # Domain models
│   │   │   ├── __init__.py
│   │   │   ├── conversation.py       # Conversation domain model
│   │   │   ├── message.py            # Message domain model
│   │   │   ├── intent.py             # Intent domain model
│   │   │   ├── tenant.py             # Tenant domain model
│   │   │   ├── ai_model.py           # AI model domain model
│   │   │   ├── user.py               # User domain model
│   │   │   └── embedding.py          # Vector embedding domain model
│   │   │
│   │   ├── schemas/                  # Pydantic schemas for API I/O
│   │   │   ├── __init__.py
│   │   │   ├── conversation.py       # Conversation schemas
│   │   │   ├── message.py            # Message schemas
│   │   │   ├── intent.py             # Intent schemas
│   │   │   ├── tenant.py             # Tenant schemas
│   │   │   ├── ai_model.py           # AI model schemas
│   │   │   └── response.py           # Standardized API responses
│   │   │
│   │   ├── services/                 # Business logic services
│   │   │   ├── __init__.py
│   │   │   ├── conversation_service.py   # Conversation management
│   │   │   ├── message_service.py        # Message processing
│   │   │   ├── intent_service.py         # Intent classification
│   │   │   ├── context_service.py        # Context window management
│   │   │   ├── vector_search_service.py  # Vector search implementation
│   │   │   ├── model_selector_service.py # AI model selection logic
│   │   │   └── response_generator_service.py # Response generation
│   │   │
│   │   ├── interfaces/               # Abstract interfaces
│   │   │   ├── __init__.py
│   │   │   ├── model_interface.py    # AI model interface
│   │   │   ├── repository_interface.py  # Data repository interface
│   │   │   ├── adapter_interface.py  # External adapter interface
│   │   │   ├── cache_interface.py    # Cache strategy interface
│   │   │   └── event_interface.py    # Event handling interface
│   │   │
│   │   └── events/                   # Domain events
│   │       ├── __init__.py
│   │       ├── event_base.py         # Base event class
│   │       ├── message_events.py     # Message-related events
│   │       └── conversation_events.py # Conversation-related events
│   │
│   ├── infrastructure/               # Infrastructure Layer
│   │   ├── __init__.py
│   │   │
│   │   ├── database/                 # Database configuration
│   │   │   ├── __init__.py
│   │   │   ├── connection.py         # Database connection manager
│   │   │   ├── mongodb/              # MongoDB specific code
│   │   │   │   ├── __init__.py
│   │   │   │   └── client.py         # MongoDB client
│   │   │   └── postgresql/           # PostgreSQL specific code
│   │   │       ├── __init__.py
│   │   │       └── client.py         # PostgreSQL client
│   │   │
│   │   ├── repositories/             # Data access implementations
│   │   │   ├── __init__.py
│   │   │   ├── conversation_repository.py  # Conversation storage
│   │   │   ├── message_repository.py       # Message storage
│   │   │   ├── intent_repository.py        # Intent storage
│   │   │   ├── tenant_repository.py        # Tenant configuration storage
│   │   │   ├── vector_repository.py        # Vector embeddings storage
│   │   │   └── user_repository.py          # User data storage
│   │   │
│   │   ├── adapters/                 # External service adapters
│   │   │   ├── __init__.py
│   │   │   ├── mcp_adapter.py        # MCP Service adapter
│   │   │   ├── adaptor_service_adapter.py # Adaptor Service adapter
│   │   │   ├── message_queue_adapter.py   # RabbitMQ/Kafka adapter
│   │   │   └── metrics_adapter.py    # Prometheus metrics adapter
│   │   │
│   │   ├── ai/                       # AI model implementations
│   │   │   ├── __init__.py
│   │   │   ├── llm/                  # LLM providers
│   │   │   │   ├── __init__.py
│   │   │   │   ├── openai_adapter.py # OpenAI API adapter
│   │   │   │   ├── anthropic_adapter.py # Anthropic API adapter
│   │   │   │   └── base_llm.py       # Base LLM class
│   │   │   │
│   │   │   ├── fine_tuned/           # Fine-tuned models
│   │   │   │   ├── __init__.py
│   │   │   │   └── custom_model.py   # Custom fine-tuned model adapter
│   │   │   │
│   │   │   ├── embeddings/           # Text embedding models
│   │   │   │   ├── __init__.py
│   │   │   │   └── embedding_service.py # Text to vector embedding
│   │   │   │
│   │   │   ├── intent/               # Intent classification models
│   │   │   │   ├── __init__.py
│   │   │   │   └── intent_classifier.py # Intent classification
│   │   │   │
│   │   │   └── factory.py            # AI model factory
│   │   │
│   │   ├── cache/                    # Caching implementations
│   │   │   ├── __init__.py
│   │   │   ├── redis_cache.py        # Redis cache implementation
│   │   │   └── local_cache.py        # In-memory cache implementation
│   │   │
│   │   ├── auth/                     # Authentication and authorization
│   │   │   ├── __init__.py
│   │   │   ├── jwt_handler.py        # JWT token handling
│   │   │   ├── oauth2.py             # OAuth2 implementation
│   │   │   └── rbac.py               # Role-based access control
│   │   │
│   │   └── vector_db/                # Vector database integration
│   │       ├── __init__.py
│   │       ├── pinecone_client.py    # Pinecone integration
│   │       └── qdrant_client.py      # Qdrant integration
│   │
│   └── utils/                        # Utility modules
│       ├── __init__.py
│       ├── logger.py                 # Logging configuration
│       ├── tracing.py                # Distributed tracing
│       ├── metrics.py                # Metrics collection
│       ├── exceptions.py             # Custom exceptions
│       ├── validators.py             # Validation utilities
│       ├── security.py               # Security utilities
│       ├── pagination.py             # Pagination helpers
│       └── profiler.py               # Performance profiling
│
└── k8s/                              # Kubernetes deployment files
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    ├── configmap.yaml
    ├── secrets.yaml
    └── hpa.yaml                      # Horizontal Pod Autoscaler
```

## Key Components

### API Layer Components

#### Routers
Responsible for defining API endpoints, handling HTTP requests/responses, and managing input validation.

- **health.py**: Health check endpoints for monitoring system status
- **conversations.py**: Endpoints for conversation management (create, get, update, delete)
- **messages.py**: Endpoints for message processing (send, receive, history)
- **intents.py**: Endpoints for intent classification and management
- **models.py**: Endpoints for AI model management and selection
- **admin.py**: Administrative endpoints for system configuration

#### Middleware
Intercepts requests and responses to implement cross-cutting concerns.

- **correlation_id.py**: Adds unique ID to all requests for distributed tracing
- **tenant_context.py**: Establishes tenant context for multi-tenancy
- **rate_limiting.py**: Implements rate limiting for API endpoints

### Domain Layer Components

#### Models
Core domain entities representing business concepts.

- **conversation.py**: Represents a conversation session between a user and the system
- **message.py**: Represents individual messages within a conversation
- **intent.py**: Represents classified user intents
- **tenant.py**: Represents a tenant in the multi-tenant system
- **ai_model.py**: Represents an AI model configuration
- **user.py**: Represents a user of the system

#### Services
Implements business logic for the application.

- **conversation_service.py**: Manages conversation lifecycle and state
- **message_service.py**: Processes incoming and outgoing messages
- **intent_service.py**: Classifies user intents from messages
- **context_service.py**: Manages conversation context window
- **vector_search_service.py**: Implements semantic search functionality
- **model_selector_service.py**: Selects appropriate AI model based on query complexity
- **response_generator_service.py**: Generates responses using AI models

#### Interfaces
Defines abstract interfaces for interacting with external systems.

- **model_interface.py**: Interface for AI model integration
- **repository_interface.py**: Interface for data storage operations
- **adapter_interface.py**: Interface for external service communication
- **cache_interface.py**: Interface for caching strategies
- **event_interface.py**: Interface for event handling

### Infrastructure Layer Components

#### Repositories
Implements data access logic for persistent storage.

- **conversation_repository.py**: Stores and retrieves conversation data
- **message_repository.py**: Stores and retrieves message data
- **intent_repository.py**: Stores and retrieves intent data
- **tenant_repository.py**: Stores and retrieves tenant configurations
- **vector_repository.py**: Stores and retrieves vector embeddings
- **user_repository.py**: Stores and retrieves user data

#### Adapters
Implements communication with external services.

- **mcp_adapter.py**: Communicates with the MCP Service
- **adaptor_service_adapter.py**: Communicates with the Adaptor Service
- **message_queue_adapter.py**: Interacts with message queues (RabbitMQ/Kafka)
- **metrics_adapter.py**: Sends metrics to monitoring systems

#### AI
Implements AI model integrations.

- **llm/**: Large language model integrations
- **fine_tuned/**: Custom fine-tuned model integrations
- **embeddings/**: Text embedding model integrations
- **intent/**: Intent classification model integrations
- **factory.py**: Factory for creating AI model instances

#### Cache
Implements caching strategies.

- **redis_cache.py**: Redis-based caching implementation
- **local_cache.py**: In-memory caching implementation

#### Auth
Implements authentication and authorization.

- **jwt_handler.py**: JWT token handling
- **oauth2.py**: OAuth2 implementation
- **rbac.py**: Role-based access control

### Utility Components

- **logger.py**: Configures structured logging
- **tracing.py**: Implements distributed tracing
- **metrics.py**: Collects and exposes metrics
- **exceptions.py**: Defines custom exceptions
- **validators.py**: Implements validation utilities
- **security.py**: Implements security utilities
- **pagination.py**: Implements pagination helpers
- **profiler.py**: Implements performance profiling

## Implementation Guidelines

### Domain-Driven Design Principles

1. **Bounded Contexts**: The Chat Service is a bounded context focused on conversation management and response generation.

2. **Entities and Value Objects**: 
   - Entities (with identity): Conversation, Message, User, Tenant
   - Value Objects (identity-less): Intent, Embedding

3. **Aggregates**: 
   - Conversation is the main aggregate root containing Messages
   - Tenant is an aggregate root for configuration

4. **Repositories**: 
   - One repository per aggregate root
   - Repositories abstract data access details

5. **Services**: 
   - Domain services implement business logic
   - Application services coordinate tasks
   - Infrastructure services handle technical concerns

6. **Factories**:
   - Used to create complex domain objects
   - Encapsulate creation logic

### FastAPI Application Structure

1. **Dependencies Injection**:
   - Use FastAPI's dependency system for clean dependency injection
   - Register services and repositories as dependencies

2. **Schema Validation**:
   - Use Pydantic models for request/response validation
   - Define clear schema hierarchies

3. **Error Handling**:
   - Implement global exception handlers
   - Use custom exceptions for domain errors

4. **Middleware Pipeline**:
   - Organize middlewares in a logical processing order
   - Use middleware for cross-cutting concerns

### Database Access Patterns

1. **Repository Pattern**:
   - Abstract database access behind repository interfaces
   - Implement repository for each aggregate root

2. **Multiple Database Types**:
   - PostgreSQL for structured relational data
   - MongoDB for semi-structured conversation data
   - Vector database for semantic embeddings

3. **Connection Management**:
   - Use connection pooling
   - Implement proper error handling and retries

### Caching Strategy

1. **Multi-level Caching**:
   - L1: In-memory cache for frequent accesses
   - L2: Redis for distributed caching

2. **Cache Invalidation**:
   - Time-based expiration
   - Event-based invalidation
   - Version-based invalidation

3. **Cacheable Resources**:
   - Tenant configurations
   - Frequently accessed conversations
   - AI model configurations
   - External API responses

### AI Model Integration

1. **Strategy Pattern for Models**:
   - Abstract AI model interface
   - Concrete implementations for different providers

2. **Model Selection Logic**:
   - Cost-based routing
   - Complexity-based selection
   - Fallback mechanisms

3. **Context Management**:
   - Window size management
   - Token counting and optimization
   - Context prioritization

## Common Patterns and Utilities

### Logging System

The logging system uses structured logging with correlation IDs to enable tracing requests across services.

### Redis Caching Strategy

The Redis caching module provides a robust caching mechanism with support for:
- Different cache key strategies
- Customizable TTL (Time To Live)
- Cache invalidation patterns
- Circuit breaker for Redis failures

### Authentication and Security

The authentication system implements:
- JWT token validation
- OAuth2 authentication flows
- Role-based access control (RBAC)
- Multi-tenant security isolation

### Abstract Base Classes

The system defines several key abstract base classes:
- `ModelInterface`: For AI model implementations
- `RepositoryInterface`: For data storage implementations
- `AdapterInterface`: For external service communication
- `CacheInterface`: For caching strategies

### Configuration Management

The configuration system supports:
- Environment-based configuration
- Dotenv file loading
- Configuration hierarchies (default → environment → override)
- Sensitive value encryption