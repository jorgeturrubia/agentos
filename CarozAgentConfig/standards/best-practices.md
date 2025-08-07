# Development Best Practices - CarozProject

## Context

Tailored development guidelines for CarozProject microservices architecture (.NET 8 + Angular 18 + PostgreSQL).

<conditional-block context-check="core-principles">
IF this Core Principles section already read in current context:
  SKIP: Re-reading this section
  NOTE: "Using Core Principles already in context"
ELSE:
  READ: The following principles

## Core Principles

### Keep It Simple
- Implement code in the fewest lines possible
- Avoid over-engineering solutions
- Choose straightforward approaches over clever ones

### Optimize for Readability
- Prioritize code clarity over micro-optimizations
- Write self-documenting code with clear variable names
- Add comments for "why" not "what"

### DRY (Don't Repeat Yourself)
- Extract repeated business logic to private methods
- Extract repeated UI markup to reusable components
- Create utility functions for common operations

### File Structure
- Keep files focused on a single responsibility
- Group related functionality together
- Use consistent naming conventions
</conditional-block>

<conditional-block context-check="dependencies" task-condition="choosing-external-library">
IF current task involves choosing an external library:
  IF Dependencies section already read in current context:
    SKIP: Re-reading this section
    NOTE: "Using Dependencies guidelines already in context"
  ELSE:
    READ: The following guidelines
ELSE:
  SKIP: Dependencies section not relevant to current task

## Dependencies

### Choose Libraries Wisely
When adding third-party dependencies:
- Select the most popular and actively maintained option
- Check the library's GitHub repository for:
  - Recent commits (within last 6 months)
  - Active issue resolution
  - Number of stars/downloads
  - Clear documentation
</conditional-block>

<conditional-block context-check="microservices-architecture" task-condition="microservices-development">
IF current task involves microservices development:
  IF Microservices Architecture section already read in current context:
    SKIP: Re-reading this section
    NOTE: "Using Microservices Architecture practices already in context"
  ELSE:
    READ: The following microservices guidelines
ELSE:
  SKIP: Microservices section not relevant to current task

## Microservices Architecture Best Practices

### 🏗️ **SERVICE DESIGN PRINCIPLES**

#### Single Responsibility
- Each microservice should handle one business domain
- Purchase Order service: only order management
- Auth service: only authentication and authorization
- Avoid shared databases between services

#### API Contract First
- Design API contracts before implementation
- Use OpenAPI/Swagger for documentation
- Version APIs (v1, v2) for backward compatibility
- Never break existing API contracts

#### Database Per Service
- Each microservice owns its data
- No direct database access between services
- Use API calls for cross-service communication
- Implement saga pattern for distributed transactions

### 🔐 **SECURITY PRACTICES**

#### Authentication & Authorization
- Centralized authentication through Auth service
- JWT tokens with short expiration (1 hour max)
- Implement refresh token rotation
- Use HTTPS for all service communication
- Store secrets in environment variables or Azure Key Vault

#### API Gateway Security
- Rate limiting per endpoint and user
- Request validation at gateway level
- CORS configuration for frontend domains
- Security headers (X-Frame-Options, X-XSS-Protection)

### 📊 **DATA MANAGEMENT**

#### Database Best Practices
- Use PostgreSQL connection pooling
- Implement database migrations with Entity Framework
- Create indexes for frequently queried columns
- Use read replicas for reporting queries
- Implement soft deletes for audit trails

#### Caching Strategy
- Redis for session storage and frequently accessed data
- Cache with appropriate TTL (Time To Live)
- Implement cache-aside pattern
- Use distributed cache for multi-instance scenarios

### 🚦 **ERROR HANDLING & RESILIENCE**

#### Microservices Communication
- Implement circuit breaker pattern for external calls
- Use retry policies with exponential backoff
- Set appropriate timeouts for HTTP calls
- Log correlation IDs across service boundaries

#### Health Checks
- Implement comprehensive health checks
- Check database connectivity
- Verify external service dependencies
- Expose /health endpoint for monitoring

### 📈 **MONITORING & OBSERVABILITY**

#### Logging
- Structured logging with Serilog
- Include correlation IDs in all logs
- Log at appropriate levels (Error, Warning, Information)
- Store logs in centralized system (Elasticsearch)

#### Metrics
- Track response times and error rates
- Monitor resource usage (CPU, Memory)
- Set up alerts for critical thresholds
- Use Application Performance Monitoring (APM)

</conditional-block>

<conditional-block context-check="frontend-development" task-condition="angular-development">
IF current task involves Angular frontend development:
  IF Frontend Development section already read in current context:
    SKIP: Re-reading this section
    NOTE: "Using Frontend Development practices already in context"
  ELSE:
    READ: The following frontend guidelines
ELSE:
  SKIP: Frontend section not relevant to current task

## Frontend Development Best Practices

### 🎨 **ANGULAR 18 SPECIFIC PRACTICES**

#### Component Architecture
- Use standalone components for better tree-shaking
- Implement smart/dumb component pattern
- Keep components focused on single responsibility
- Use OnPush change detection strategy when possible

#### State Management
- Use Angular Signals for reactive state
- Combine Signals with RxJS for complex operations
- Implement state services for shared state
- Avoid nested subscriptions - use takeUntil pattern

#### Performance Optimization
- Lazy load feature modules
- Use OnPush change detection strategy
- Implement virtual scrolling for large lists
- Optimize bundle size with webpack analyzer
- Use trackBy functions in *ngFor directives

### 📱 **USER EXPERIENCE**

#### Form Handling
- Use Reactive Forms over Template-driven forms
- Implement proper form validation with custom validators
- Provide clear error messages and field hints
- Show loading states during form submission
- Implement optimistic updates where appropriate

#### Error Handling
- Implement global error handler
- Show user-friendly error messages
- Provide retry mechanisms for failed operations
- Use toast notifications for transient messages
- Log errors for debugging purposes

#### Accessibility
- Use semantic HTML elements
- Implement proper ARIA labels
- Ensure keyboard navigation support
- Maintain adequate color contrast
- Test with screen readers

### 🔧 **DEVELOPMENT WORKFLOW**

#### Code Quality
- Use ESLint and Prettier for consistent code formatting
- Write unit tests for components and services
- Implement integration tests for critical user flows
- Use Angular CDK for common UI patterns
- Follow Angular style guide conventions

#### Build & Deployment
- Use environment-specific configurations
- Implement proper build optimization
- Use source maps for debugging
- Configure proper caching headers
- Implement CI/CD pipeline with automated testing

</conditional-block>

<conditional-block context-check="testing-strategy" task-condition="testing-implementation">
IF current task involves testing implementation:
  IF Testing Strategy section already read in current context:
    SKIP: Re-reading this section
    NOTE: "Using Testing Strategy already in context"
  ELSE:
    READ: The following testing guidelines
ELSE:
  SKIP: Testing section not relevant to current task

## Testing Strategy

### 🧪 **BACKEND TESTING (.NET)**

#### Unit Testing
- Minimum 80% code coverage for business logic
- Use xUnit with appropriate test naming conventions
- Mock external dependencies using Moq or NSubstitute
- Test both happy path and edge cases
- Write AAA tests (Arrange, Act, Assert)

#### Integration Testing
- Test API endpoints with TestServer
- Use in-memory database for isolated tests
- Test with realistic data scenarios
- Verify HTTP status codes and response formats
- Test authentication and authorization scenarios

#### Testing Best Practices
```csharp
// ✅ GOOD: Clear test naming
[Fact]
public async Task CreatePurchaseOrder_WithValidData_ShouldReturnCreatedOrder()

// ✅ GOOD: Proper test isolation
public class PurchaseOrderServiceTests : IDisposable
{
    private readonly Mock<IPurchaseOrderRepository> _mockRepository;
    private readonly PurchaseOrderService _service;
    
    public PurchaseOrderServiceTests()
    {
        _mockRepository = new Mock<IPurchaseOrderRepository>();
        _service = new PurchaseOrderService(_mockRepository.Object);
    }
}
```

### 🎯 **FRONTEND TESTING (ANGULAR)**

#### Component Testing
- Test component behavior, not implementation details
- Use Angular Testing Library for better tests
- Mock HTTP requests with HttpClientTestingModule
- Test user interactions and form submissions
- Verify component outputs and events

#### Service Testing
- Test all public methods
- Mock HTTP dependencies
- Test error handling scenarios
- Verify caching behavior
- Test state management logic

#### E2E Testing
- Test critical user journeys
- Use Cypress or Playwright for E2E tests
- Test across different browsers and devices
- Include authentication flows
- Test data persistence

</conditional-block>
