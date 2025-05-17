# Chat Service Architecture Diagrams

I've created all **13 diagrams** for the Chat Service architecture as requested.  
This document summarizes each diagram and its purpose.

---

## Sequence Diagrams

### 1. Health Check Sequence Diagram
- Shows how health monitoring requests are processed with different levels of detail.
- Illustrates checks on various system components.
- Includes Kubernetes liveness and readiness probes.

### 2. User Message Processing Sequence Diagram
- Shows the end-to-end flow from user message to response.
- Illustrates interaction between key components: API, services, repositories.
- Demonstrates conversation context management and model selection.

### 3. Intent Classification Sequence Diagram
- Details how user messages are classified into intents.
- Shows confidence-based processing paths.
- Illustrates product intent handling.

### 4. AI Model Selection Sequence Diagram
- Shows the decision process for selecting appropriate AI models.
- Illustrates tenant configuration checks.
- Demonstrates fallback mechanisms.

### 5. Vector Search and Context Retrieval Sequence Diagram
- Shows embedding creation and vector database interaction.
- Illustrates semantic search for context retrieval.
- Demonstrates ranking and merging of similar content.

### 6. Conversation Context Management Sequence Diagram
- Shows how conversation context is built and optimized.
- Illustrates vector search integration.
- Demonstrates token limit management.

### 7. WebSocket Real-Time Communication Sequence Diagram
- Shows WebSocket connection lifecycle.
- Illustrates real-time message handling.
- Demonstrates typing indicators and partial responses.

---

## Class Diagrams

### 8. API Endpoints Class Diagram
- Provides a comprehensive view of all API routers and their endpoints.
- Shows relationships between routers and services.
- Details method signatures and return types.

### 9. Domain Models Class Diagram
- Shows key domain entities and their relationships.
- Details attributes and methods for each class.
- Illustrates entity relationships.

### 10. Service Layer Class Diagram
- Shows all service components and their dependencies.
- Illustrates service coordination patterns.
- Shows relationship hierarchies.

### 11. Repository Layer Class Diagram
- Shows repository implementations and interfaces.
- Illustrates inheritance from repository interface.
- Shows caching mechanisms.

---

## Component Diagrams

### 12. Layered Architecture Component Diagram
- Provides a high-level view of all architectural layers.
- Shows relationships between API, Domain, and Infrastructure layers.
- Illustrates dependency direction between components.

### 13. AI Integration Component Diagram
- Shows how different AI components integrate with the system.
- Illustrates adapter pattern for different AI providers.
- Shows key AI workflows.

### 14. Cross-Service Communication Component Diagram
- Shows how Chat Service interacts with other microservices.
- Illustrates communication patterns and protocols.
- Shows data flow between services.

---

## Specialized Diagrams

### 15. Multi-Tenant Data Isolation Diagram
- Shows how data isolation is implemented across layers.
- Illustrates tenant context propagation.
- Shows database-level isolation mechanisms.
