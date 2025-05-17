# Chat Service

The Chat Service ("The Brain") is a core microservice in the AI Chat Assistance Platform responsible for processing user messages, managing conversations, selecting appropriate AI models, and generating contextually relevant responses.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Key Components](#key-components)
- [Setup and Installation](#setup-and-installation)
- [Development](#development)
- [API Documentation](#api-documentation)
- [Testing](#testing)
- [Deployment](#deployment)
- [Performance Considerations](#performance-considerations)
- [Contributing](#contributing)

## Overview

The Chat Service is the intelligence layer of the AI Chat Assistance Platform, processing user messages and generating contextually relevant responses. It handles:

- User message processing and intent classification
- Conversation context and session state management
- AI model selection based on query complexity
- Response generation using various AI models
- Product data retrieval via the Adaptor Service
- Conversation history management and vector search
- Real-time communication via WebSockets

## Architecture

The Chat Service follows a domain-driven design approach with a clean architecture pattern, separating the application into distinct layers:

### API Layer
Handles HTTP requests/responses and WebSocket communication, defining endpoints and managing input validation.

### Domain Layer
Contains the core business logic, domain models, and service interfaces.

### Infrastructure Layer
Implements external integrations, data access, and technical concerns.

### Key Architectural Patterns

- **Domain-Driven Design**: Focuses on core domain logic with bounded contexts
- **Clean Architecture**: Separates concerns with dependency inversion
- **Repository Pattern**: Abstracts data access logic
- **Adapter Pattern**: Handles external service integration
- **Factory Pattern**: Creates instances of AI models and other components
- **Strategy Pattern**: Selects appropriate AI models and processing strategies

## Key Components

### API Endpoints

- **Health**: Service health monitoring endpoints
- **Conversations**: Conversation management endpoints
- **Messages**: Message processing endpoints
- **Intents**: Intent classification endpoints
- **Models**: AI model management endpoints
- **Admin**: Administrative operations endpoints
- **WebSockets**: Real-time communication endpoints

### Domain Services

- **ConversationService**: Manages conversation lifecycle and state
- **MessageService**: Processes incoming and outgoing messages
- **IntentService**: Classifies user intents from messages
- **ContextService**: Manages conversation context window
- **ResponseGeneratorService**: Generates responses using AI models
- **ModelSelectorService**: Selects appropriate AI models
- **VectorSearchService**: Implements semantic search functionality
- **EmbeddingService**: Creates and manages text embeddings

### Data Repositories

- **ConversationRepository**: Stores conversation data
- **MessageRepository**: Stores message data
- **IntentRepository**: Stores intent classification data
- **TenantRepository**: Stores tenant configuration
- **UserRepository**: Stores user data
- **VectorRepository**: Stores vector embeddings

### AI Integration

- **AIModelFactory**: Creates AI model instances
- **LLM Providers**: Adapters for OpenAI, Anthropic, and custom models
- **Embedding Models**: Text-to-vector embedding implementations
- **Intent Classification**: Rule-based and ML-based intent classifiers

### Infrastructure Components

- **Database**: MongoDB for conversations and PostgreSQL for structured data
- **Vector Database**: Pinecone or Qdrant for semantic search
- **Cache**: Redis for distributed caching
- **Message Queue**: RabbitMQ or Kafka for event-driven communication
- **Authentication**: JWT, OAuth2, and RBAC

## Setup and Installation

### Prerequisites

- Python 3.9+
- PostgreSQL 13+
- MongoDB 5+
- Redis 6+
- Vector database (Pinecone or Qdrant)
- RabbitMQ or Kafka (optional, for event-driven features)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourorganization/chat-service.git
   cd chat-service
   ```

2. Create and activate a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:
   ```bash
   pip install -e .
   ```

4. Copy the example environment file:
   ```bash
   cp .env.example .env
   ```

5. Edit `.env` with your configuration values

6. Run database migrations:
   ```bash
   alembic upgrade head
   ```

7. Seed initial data (optional):
   ```bash
   python scripts/seed_db.py
   ```

### Configuration

The Chat Service can be configured through environment variables or `.env` file:

| Variable | Description | Default |
|----------|-------------|---------|
| `DATABASE_URL` | PostgreSQL connection URL | `postgresql://postgres:postgres@localhost:5432/chat` |
| `MONGODB_URL` | MongoDB connection URL | `mongodb://localhost:27017/chat` |
| `REDIS_URL` | Redis connection URL | `redis://localhost:6379/0` |
| `VECTOR_DB_TYPE` | Vector database type (`pinecone` or `qdrant`) | `pinecone` |
| `VECTOR_DB_URL` | Vector database connection URL | `https://pinecone.io` |
| `VECTOR_DB_API_KEY` | Vector database API key | - |
| `OPENAI_API_KEY` | OpenAI API key | - |
| `ANTHROPIC_API_KEY` | Anthropic API key | - |
| `MCP_SERVICE_URL` | MCP Service URL | `http://localhost:5001` |
| `ADAPTOR_SERVICE_URL` | Adaptor Service URL | `http://localhost:5002` |
| `LOG_LEVEL` | Logging level | `INFO` |
| `JWT_SECRET` | Secret for JWT tokens | - |
| `ENABLE_TRACING` | Enable distributed tracing | `true` |
| `PORT` | Service port | `5000` |

## Development
### Development Workflow

1. Create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. Run the development server with hot reload:
   ```bash
   uvicorn app.main:app --reload
   ```

3. Run tests to ensure changes don't break existing functionality:
   ```bash
   pytest
   ```

4. Submit a pull request for review

### Development Guidelines

- Follow PEP 8 style guidelines
- Write unit tests for new features
- Update documentation when changing public APIs
- Use type hints
- Follow the established architectural patterns
- Document new services, repositories, and entities
- Run linting before committing code

## API Documentation

API documentation is available via OpenAPI/Swagger:

- **Swagger UI**: `http://localhost:5000/docs`
- **ReDoc**: `http://localhost:5000/redoc`
- **OpenAPI JSON**: `http://localhost:5000/openapi.json`

### Key API Endpoints

- `GET /health`: Service health check
- `POST /conversations`: Create a new conversation
- `GET /conversations/{conversation_id}`: Get conversation details
- `POST /conversations/{conversation_id}/messages`: Send a message
- `GET /conversations/{conversation_id}/messages`: Get conversation messages
- `POST /intents/classify`: Classify intent from text
- `GET /models`: List available AI models

## Testing

### Running Tests

```bash
# Run all tests
pytest

# Run unit tests only
pytest tests/unit

# Run integration tests only
pytest tests/integration

# Run with coverage report
pytest --cov=app
```

### Test Data

Seed test data for development or testing:

```bash
python scripts/seed_db.py --env=test
```

### Testing Strategy

1. **Unit Tests**: Test individual components in isolation
2. **Integration Tests**: Test component interactions
3. **End-to-End Tests**: Test complete workflows
4. **Contract Tests**: Verify API contracts
5. **Performance Tests**: Verify performance under load

## Deployment

### Local Docker Deployment

```bash
# Build Docker image
docker build -t chat-service .

# Run container
docker run -p 5000:5000 --env-file .env chat-service
```

### Kubernetes Deployment

```bash
# Apply Kubernetes manifests
kubectl apply -f k8s/
```

### Production Considerations

- Use a proper secrets management solution
- Configure horizontal pod autoscaling
- Set up proper logging and monitoring
- Use a production-ready database setup with backups
- Configure rate limiting and security policies

## Performance Considerations

- **Caching Strategy**: Use Redis caching for frequently accessed data
- **Connection Pooling**: Enable database connection pooling
- **Asynchronous Processing**: Use asynchronous handlers for long-running tasks
- **Token Management**: Optimize token usage for AI models
- **Vector Search Optimization**: Tune vector search parameters for balance of speed and relevance
- **Database Indexing**: Ensure proper indexes for common queries
- **Load Testing**: Regularly test performance under load

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

Please make sure to update tests and documentation as appropriate.

## License

This project is licensed under the [MIT License](LICENSE).