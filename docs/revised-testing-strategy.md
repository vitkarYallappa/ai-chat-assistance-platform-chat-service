# Revised Testing Strategy for Chat Service

## Table of Contents
- [Introduction](#introduction)
- [Testing Principles](#testing-principles)
- [Test Categories](#test-categories)
  - [Unit Testing](#unit-testing)
  - [Integration Testing](#integration-testing)
  - [Contract Testing](#contract-testing)
  - [End-to-End Testing](#end-to-end-testing)
  - [Resilience Testing](#resilience-testing)
  - [Performance Testing](#performance-testing)
  - [Security Testing](#security-testing)
  - [AI Model Testing](#ai-model-testing)
- [Testing Infrastructure](#testing-infrastructure)
- [Test Data Management](#test-data-management)
- [Continuous Testing Approach](#continuous-testing-approach)
- [Coverage Targets](#coverage-targets)
- [Test Folder Structure](#test-folder-structure)
- [Implementation Guidelines](#implementation-guidelines)
- [Example Test Cases](#example-test-cases)
- [Risk Management](#risk-management)

## Introduction

The Chat Service ("The Brain") requires a comprehensive testing strategy due to its complex responsibilities:
- Processing user messages using intent classification
- Managing conversation context and session state
- Selecting appropriate AI models based on query complexity
- Generating contextually relevant responses
- Fetching product data when needed
- Maintaining conversation history
- Implementing vector search for similar past conversations

This testing strategy addresses both functional correctness and non-functional requirements such as performance, resilience, and security, with special focus on AI model integration challenges.

## Testing Principles

1. **Shift-left testing**: Incorporate testing from the earliest stages of development
2. **Test isolation**: Ensure tests are independent and can run in any order
3. **Test at the appropriate level**: Unit test for logic, integration test for interactions
4. **Domain-focused testing**: Structure tests around domain boundaries and use cases
5. **Mock external dependencies**: Use appropriate mocking strategies for external services
6. **AI-specific validation**: Incorporate strategies for testing AI model integration and response quality
7. **Test for failure**: Verify system behavior under error conditions
8. **Continuous testing**: Automate test execution in the CI/CD pipeline
9. **Test close to production**: Create staging environments that closely mirror production
10. **Observability validation**: Test that proper logging, metrics, and tracing are implemented

## Test Categories

### Unit Testing

Unit tests validate the behavior of individual components in isolation, with dependencies mocked or stubbed.

**Focus Areas:**
- Domain models (Conversation, Message, Intent, etc.)
- Service logic
- Utility functions
- AI model selection algorithms
- Context management logic

**Key Practices:**
- Use dependency injection to facilitate mocking
- Test both happy path and edge cases
- Cover all public methods on domain models
- Test validation logic extensively
- Verify proper error handling

**Coverage Target:** 90% code coverage for all domain models and service classes

### Integration Testing

Integration tests verify that components work together correctly.

**Focus Areas:**
- Repository implementations with actual databases
- Service interactions
- API controllers with real services (but mocked external dependencies)
- Cache implementations
- Event handling between components

**Key Practices:**
- Use test containers for database testing
- Test transaction handling and error scenarios
- Verify proper multi-tenant data isolation
- Test repository query performance with substantial datasets
- Verify caching behavior and invalidation strategies

**Coverage Target:** 80% integration test coverage for repository and service interactions

### Contract Testing

Contract tests ensure API boundaries are well-defined and maintained.

**Focus Areas:**
- API endpoints schema validation
- Request/response format compliance
- Event message schema compliance
- External service contract adherence 

**Key Practices:**
- Define schema expectations in OpenAPI format
- Validate response structures against schemas
- Test error response formats
- Verify pagination behavior
- Test backward compatibility for versioned APIs

**Coverage Target:** 100% contract test coverage for all public API endpoints

### End-to-End Testing

E2E tests validate complete user journeys and critical business flows.

**Focus Areas:**
- Conversation flows from start to finish
- Intent classification and response generation
- Cross-service interactions
- Multi-tenant scenarios
- WebSocket communication

**Key Practices:**
- Test realistic conversation scenarios
- Verify both synchronous and asynchronous flows
- Test with simulated AI responses and real AI services
- Validate conversation state persistence
- Test error recovery in complete workflows

**Coverage Target:** E2E tests covering all critical business flows and user scenarios

### Resilience Testing

Resilience tests verify the system's ability to handle failure conditions gracefully.

**Focus Areas:**
- External service failures (AI services, MCP, Adaptor Service)
- Database connection issues
- Cache failures
- Network latency and timeout scenarios
- Rate limiting and circuit breaker behavior

**Key Practices:**
- Use fault injection to simulate failures
- Test circuit breaker functionality
- Verify fallback strategies for critical components
- Test partial system degradation
- Validate error logging and alerting

**Coverage Target:** Resilience tests for all critical external dependencies and failure scenarios

### Performance Testing

Performance tests validate the system's responsiveness, throughput, and scalability.

**Focus Areas:**
- Response time under various loads
- Maximum throughput capacity
- Resource utilization patterns
- Database query performance
- Caching effectiveness

**Key Practices:**
- Establish baseline performance metrics
- Test with gradually increasing load
- Identify bottlenecks through profiling
- Measure key performance indicators (KPIs)
- Test horizontal scaling behavior

**Coverage Target:** Performance tests covering peak load scenarios (2x expected maximum)

### Security Testing

Security tests validate authentication, authorization, and data protection measures.

**Focus Areas:**
- Authentication mechanisms
- Authorization and permission checks
- Tenant data isolation
- Sensitive data handling
- JWT token security

**Key Practices:**
- Test unauthorized access attempts
- Verify proper tenant isolation
- Test role-based access control
- Validate secure handling of sensitive data
- Test token expiration and revocation

**Coverage Target:** Security tests covering all authentication and authorization flows

### AI Model Testing

AI model tests validate the integration with AI services and response quality.

**Focus Areas:**
- Model selection logic
- Context window management
- Token counting accuracy
- Prompt engineering effectiveness
- Model fallback mechanisms
- Response quality assurance

**Key Practices:**
- Test model selection for different intent types
- Verify context window optimization
- Test token counting algorithms
- Compare expected vs. actual responses
- Validate cost optimization strategies
- Test model version compatibility

**Coverage Target:** AI model tests covering all model integration paths and selection scenarios

## Testing Infrastructure

The testing infrastructure provides the environment and tools needed to execute tests efficiently.

**Components:**
- **CI/CD Pipeline**: Jenkins or GitHub Actions for automated test execution
- **Test Environments**:
  - Development: For unit and component tests
  - Integration: For integration and contract tests
  - Staging: For E2E and performance tests
- **Test Containers**: For database and dependency isolation
- **Mock Services**: For simulating external dependencies
- **Monitoring**: For capturing performance metrics during tests
- **Test Result Visualization**: For tracking test outcomes and trends

**Key Practices:**
- Provision ephemeral test environments
- Use infrastructure as code for environment consistency
- Implement parallel test execution
- Maintain dedicated test databases
- Capture detailed test logs and metrics

## Test Data Management

Effective test data management ensures tests have appropriate and realistic data.

**Approaches:**
- **Test Fixtures**: Pre-defined data for unit and integration tests
- **Data Factories**: Dynamic generation of test data
- **Synthetic Data Generation**: Creation of realistic conversation datasets
- **Data Cleanup**: Automatic cleanup after test execution
- **Data Versioning**: Versioning of test datasets

**Key Practices:**
- Create representative test data for various scenarios
- Generate synthetic conversations with known patterns
- Implement tenant isolation in test data
- Version control test fixtures
- Provide realistic embedding vectors for vector search testing

## Continuous Testing Approach

Continuous testing ensures that tests are executed regularly as part of the development process.

**Pipeline Stages:**
1. **Pre-commit**: Linting, formatting, and fast unit tests
2. **Build**: Full unit test suite execution
3. **Integration**: Integration and contract tests
4. **Deployment**: E2E tests in staging environment
5. **Periodic**: Performance and resilience tests (nightly/weekly)

**Key Practices:**
- Fast feedback for developers
- Test suite categorization by execution time
- Automated test selection based on code changes
- Regular execution of complete test suite
- Continuous monitoring of test metrics

## Coverage Targets

Coverage targets establish measurable goals for test completeness.

**Coverage Types:**
- **Code Coverage**: Percentage of code executed during tests
- **Functional Coverage**: Percentage of requirements covered by tests
- **Domain Coverage**: Coverage of domain scenarios and use cases
- **Edge Case Coverage**: Coverage of exceptional conditions

**Targets by Area:**
- Domain Models: 90% code coverage
- Services: 85% code coverage
- Utilities: 80% code coverage
- APIs: 100% endpoint coverage
- Database Operations: 90% query coverage
- Error Conditions: 95% of identified error scenarios

## Test Folder Structure

The test folder structure mirrors the application structure while organizing tests by category.

```
chat-service/
├── tests/
│   ├── conftest.py                           # Common test fixtures and utilities
│   ├── unit/                                 # Unit tests
│   │   ├── domain/                           # Domain model tests
│   │   │   ├── test_conversation.py          # Conversation model tests
│   │   │   ├── test_message.py               # Message model tests
│   │   │   ├── test_intent.py                # Intent model tests
│   │   │   ├── test_tenant.py                # Tenant model tests
│   │   │   ├── test_ai_model.py              # AI model domain tests
│   │   │   ├── test_user.py                  # User model tests
│   │   │   └── test_embedding.py             # Embedding model tests
│   │   ├── services/                         # Service layer tests
│   │   │   ├── test_conversation_service.py  # Conversation service tests
│   │   │   ├── test_message_service.py       # Message service tests
│   │   │   ├── test_intent_service.py        # Intent service tests
│   │   │   ├── test_context_service.py       # Context service tests
│   │   │   ├── test_vector_search_service.py # Vector search tests
│   │   │   ├── test_model_selector_service.py # Model selection tests
│   │   │   └── test_response_generator_service.py # Response generation tests
│   │   └── utils/                            # Utility tests
│   │       ├── test_logger.py                # Logging utility tests
│   │       ├── test_validators.py            # Validation utility tests
│   │       ├── test_security.py              # Security utility tests
│   │       ├── test_pagination.py            # Pagination utility tests
│   │       └── test_profiler.py              # Profiler utility tests
│   ├── integration/                          # Integration tests
│   │   ├── api/                              # API endpoint tests
│   │   │   ├── test_health_endpoints.py      # Health check endpoint tests
│   │   │   ├── test_conversations_api.py     # Conversation API tests
│   │   │   ├── test_messages_api.py          # Message API tests
│   │   │   ├── test_intents_api.py           # Intent API tests
│   │   │   ├── test_models_api.py            # Model API tests
│   │   │   └── test_admin_api.py             # Admin API tests
│   │   ├── repositories/                     # Repository tests
│   │   │   ├── test_conversation_repository.py # Conversation repository tests
│   │   │   ├── test_message_repository.py    # Message repository tests
│   │   │   ├── test_intent_repository.py     # Intent repository tests
│   │   │   ├── test_tenant_repository.py     # Tenant repository tests
│   │   │   ├── test_vector_repository.py     # Vector repository tests
│   │   │   └── test_user_repository.py       # User repository tests
│   │   ├── adapters/                         # External adapter tests
│   │   │   ├── test_mcp_adapter.py           # MCP adapter tests
│   │   │   ├── test_adaptor_service_adapter.py # Adaptor service tests
│   │   │   ├── test_message_queue_adapter.py # Message queue tests
│   │   │   └── test_metrics_adapter.py       # Metrics adapter tests
│   │   ├── ai/                               # AI integration tests
│   │   │   ├── test_openai_adapter.py        # OpenAI integration tests
│   │   │   ├── test_anthropic_adapter.py     # Anthropic integration tests
│   │   │   ├── test_embedding_service.py     # Embedding service tests
│   │   │   └── test_intent_classifier.py     # Intent classifier tests
│   │   ├── cache/                            # Cache tests
│   │   │   ├── test_redis_cache.py           # Redis cache tests
│   │   │   └── test_local_cache.py           # Local cache tests
│   │   └── vector_db/                        # Vector database tests
│   │       ├── test_pinecone_client.py       # Pinecone client tests
│   │       └── test_qdrant_client.py         # Qdrant client tests
│   ├── contract/                             # Contract tests
│   │   ├── api/                              # API contract tests
│   │   │   ├── test_openapi_compliance.py    # OpenAPI specification compliance
│   │   │   ├── test_conversations_schema.py  # Conversation schema tests
│   │   │   ├── test_messages_schema.py       # Message schema tests
│   │   │   └── test_error_responses.py       # Error response format tests
│   │   ├── events/                           # Event contract tests
│   │   │   ├── test_message_events.py        # Message event schema tests
│   │   │   └── test_conversation_events.py   # Conversation event schema tests
│   │   └── external/                         # External service contracts
│   │       ├── test_mcp_contract.py          # MCP service contract tests
│   │       └── test_adaptor_contract.py      # Adaptor service contract tests
│   ├── e2e/                                  # End-to-end tests
│   │   ├── test_conversation_flow.py         # Conversation flow tests
│   │   ├── test_intent_classification.py     # Intent classification flow tests
│   │   ├── test_product_query_flow.py        # Product query flow tests
│   │   ├── test_websocket_conversation.py    # WebSocket conversation tests
│   │   ├── test_conversation_resumption.py   # Conversation resumption tests
│   │   └── test_multi_tenant_isolation.py    # Multi-tenant isolation tests
│   ├── resilience/                           # Resilience tests
│   │   ├── test_ai_service_failures.py       # AI service failure tests
│   │   ├── test_database_failures.py         # Database failure tests
│   │   ├── test_redis_failures.py            # Redis failure tests
│   │   ├── test_vector_db_failures.py        # Vector DB failure tests
│   │   ├── test_circuit_breakers.py          # Circuit breaker tests
│   │   └── test_fallback_strategies.py       # Fallback strategy tests
│   ├── performance/                          # Performance tests
│   │   ├── test_conversation_throughput.py   # Conversation throughput tests
│   │   ├── test_message_processing_latency.py # Message processing latency tests
│   │   ├── test_vector_search_performance.py # Vector search performance tests
│   │   ├── test_cache_effectiveness.py       # Cache effectiveness tests
│   │   └── test_scaling_behavior.py          # Scaling behavior tests
│   ├── security/                             # Security tests
│   │   ├── test_authentication.py            # Authentication tests
│   │   ├── test_authorization.py             # Authorization tests
│   │   ├── test_tenant_isolation.py          # Tenant isolation tests
│   │   ├── test_sensitive_data_handling.py   # Sensitive data handling tests
│   │   └── test_jwt_security.py              # JWT security tests
│   ├── ai_models/                            # AI model-specific tests
│   │   ├── test_model_selection_strategy.py  # Model selection tests
│   │   ├── test_context_window_management.py # Context window management tests
│   │   ├── test_token_counting.py            # Token counting tests
│   │   ├── test_model_fallback.py            # Model fallback tests
│   │   ├── test_prompt_engineering.py        # Prompt engineering tests
│   │   └── test_cost_optimization.py         # Cost optimization tests
│   └── fixtures/                             # Test fixtures
│       ├── conversation_fixtures.py          # Conversation test data
│       ├── message_fixtures.py               # Message test data
│       ├── intent_fixtures.py                # Intent test data
│       ├── tenant_fixtures.py                # Tenant test data
│       └── embedding_fixtures.py             # Embedding test data
└── scripts/
    ├── test_data_generators/                 # Test data generation
    │   ├── generate_conversations.py         # Conversation data generator
    │   ├── generate_messages.py              # Message data generator
    │   └── generate_intents.py               # Intent data generator
    └── performance/                          # Performance test scripts
        ├── load_test.py                      # Load test runner
        └── generate_traffic.py               # Traffic generator
```

## Implementation Guidelines

These guidelines help ensure consistent and effective test implementation.

### General Guidelines:
- Use descriptive test names that indicate behavior being tested
- Follow AAA pattern (Arrange, Act, Assert)
- Keep tests focused and small
- Avoid test interdependencies
- Use parametrized tests for similar test cases
- Separate test setup from assertions

### Framework-Specific Guidelines:
- Use pytest as the primary test framework
- Leverage fixtures for reusable test components
- Use pytest-mock for mocking
- Implement custom pytest markers for test categorization
- Use factory_boy for test data generation

### AI Testing Guidelines:
- Create deterministic mock AI responses for repeatable tests
- Use recorded real API responses for realistic testing
- Implement model-specific test cases
- Test with varied prompt structures
- Verify expected vs. actual response patterns

## Example Test Cases

The following examples demonstrate the approach to testing key components.

### Unit Test Example: Model Selection

```python
# tests/unit/services/test_model_selector_service.py

def test_model_selection_based_on_intent_confidence(mocker):
    """Test that high confidence intents route to appropriate models"""
    # Arrange
    tenant_repo_mock = mocker.Mock()
    ai_model_factory_mock = mocker.Mock()
    selector = ModelSelectorService(tenant_repo_mock, ai_model_factory_mock)
    
    # Mock the tenant repository to return specific settings
    tenant_repo_mock.get_tenant_settings.return_value = {
        "allowed_models": ["gpt-4", "claude-3", "gpt-3.5-turbo"]
    }
    
    # Create test intent for product query with high confidence
    intent = Intent(name="product_inquiry", confidence=0.92, 
                    parameters={"product_id": "12345"})
    
    # Act
    selected_model = selector.select_model_for_intent(intent, "tenant1")
    
    # Assert
    # High confidence product queries should use a more capable model
    assert selected_model == "gpt-4" 
```

### Integration Test Example: Conversation Repository

```python
# tests/integration/repositories/test_conversation_repository.py

def test_add_message_to_conversation(db_session):
    """Test adding a message to a conversation in the repository"""
    # Arrange
    repo = ConversationRepository(db_session)
    conversation = repo.create_conversation("tenant1", "user123")
    
    # Act
    repo.add_message_to_conversation(
        conversation.id, 
        "Hello, I need help with my order", 
        "user"
    )
    
    # Get the updated conversation
    updated_conversation = repo.get_conversation(conversation.id)
    
    # Assert
    assert len(updated_conversation.messages) == 1
    assert updated_conversation.messages[0].content == "Hello, I need help with my order"
    assert updated_conversation.messages[0].role == "user"
```

### E2E Test Example: Conversation Flow

```python
# tests/e2e/test_conversation_flow.py

def test_complete_conversation_flow(client, tenant_fixture):
    """Test a complete conversation flow from start to finish"""
    # Arrange
    tenant_id = tenant_fixture["id"]
    headers = {"X-Tenant-ID": tenant_id, "Authorization": f"Bearer {tenant_fixture['api_key']}"}
    
    # Act - Create conversation
    response = client.post(
        "/api/conversations", 
        headers=headers,
        json={"user_id": "user123"}
    )
    assert response.status_code == 201
    conversation_id = response.json()["id"]
    
    # Send user message
    response = client.post(
        f"/api/conversations/{conversation_id}/messages",
        headers=headers,
        json={"content": "I'm looking for a red shirt", "role": "user"}
    )
    assert response.status_code == 201
    
    # Get AI response
    response = client.get(
        f"/api/conversations/{conversation_id}/messages",
        headers=headers
    )
    assert response.status_code == 200
    messages = response.json()["items"]
    
    # Assert
    assert len(messages) == 2  # User message and AI response
    assert messages[0]["role"] == "user"
    assert messages[0]["content"] == "I'm looking for a red shirt"
    assert messages[1]["role"] == "assistant"
    # Check that response contains product information
    assert "shirt" in messages[1]["content"].lower()
    assert "red" in messages[1]["content"].lower()
```

### Resilience Test Example: AI Service Failure

```python
# tests/resilience/test_ai_service_failures.py

def test_fallback_on_primary_model_failure(mocker, client, tenant_fixture):
    """Test fallback to alternative model when primary model fails"""
    # Arrange
    tenant_id = tenant_fixture["id"]
    headers = {"X-Tenant-ID": tenant_id, "Authorization": f"Bearer {tenant_fixture['api_key']}"}
    
    # Mock OpenAI adapter to simulate failure
    mock_openai = mocker.patch(
        "app.infrastructure.ai.llm.openai_adapter.OpenAIAdapter.generate_response"
    )
    mock_openai.side_effect = Exception("Service unavailable")
    
    # Mock Anthropic adapter for fallback
    mock_anthropic = mocker.patch(
        "app.infrastructure.ai.llm.anthropic_adapter.AnthropicAdapter.generate_response"
    )
    mock_anthropic.return_value = "I can help you find a red shirt."
    
    # Act - Create conversation and send message
    response = client.post(
        "/api/conversations", 
        headers=headers,
        json={"user_id": "user123"}
    )
    conversation_id = response.json()["id"]
    
    response = client.post(
        f"/api/conversations/{conversation_id}/messages",
        headers=headers,
        json={"content": "I need a red shirt", "role": "user"}
    )
    
    # Get messages to see response
    response = client.get(
        f"/api/conversations/{conversation_id}/messages",
        headers=headers
    )
    messages = response.json()["items"]
    
    # Assert
    assert len(messages) == 2
    assert messages[1]["role"] == "assistant"
    assert "shirt" in messages[1]["content"].lower()
    assert mock_openai.called  # Primary model was attempted
    assert mock_anthropic.called  # Fallback model was used
    
    # Check that failure was logged
    # (Assuming we have a way to capture logs in tests)
    # assert "Fallback to secondary model" in captured_logs
```

## Risk Management

This section identifies testing risks and mitigation strategies.

### Key Testing Risks:

1. **AI Model Variability**: AI responses may vary, making tests non-deterministic
   - **Mitigation**: Use controlled test prompts and response validation patterns rather than exact matching

2. **Performance Testing Accuracy**: Performance in test environments may not reflect production
   - **Mitigation**: Establish baseline metrics and focus on relative changes rather than absolute values

3. **External Service Dependencies**: Tests may fail due to external service issues
   - **Mitigation**: Use reliable mocks for unit/integration tests and implement retry logic for E2E tests

4. **Multi-Tenant Isolation Testing**: Difficult to verify complete tenant isolation
   - **Mitigation**: Create structured test matrices to verify all cross-tenant access scenarios

5. **Test Data Management**: Maintaining relevant test data as system evolves
   - **Mitigation**: Implement data generation frameworks rather than static fixtures

6. **Test Execution Time**: Comprehensive test suites may become slow
   - **Mitigation**: Categorize tests by execution time and use selective test execution in CI/CD

7. **AI Model Costs**: Testing with real AI services may incur significant costs
   - **Mitigation**: Use sandbox environments, request limiting, and careful scoping of AI tests

### Continuous Improvement:

The testing strategy should evolve based on:
- Analysis of production issues not caught by tests
- Feedback from development and QA teams
- Changes in system architecture or requirements
- Advances in testing tools and methodologies
- Periodic review of test coverage and effectiveness
