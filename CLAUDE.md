# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with the vine-iam service codebase.

## Project Overview & Architectural Vision

**vine-iam** is an Identity and Access Management (IAM) service implementing OAuth2, OpenID Connect, and advanced authentication protocols. This service provides secure authentication and authorization for the Vine ecosystem with support for modern security standards.

### Core Architectural Principles

1. **Security First**: All authentication flows must comply with OAuth2 and OpenID Connect specifications
2. **Event Sourcing by Design**: State is derived from strongly typed events, never stored directly in entities
3. **Protocol Compliance**: OAuth2 RFC 6749, OpenID Connect, PKCE, mTLS specifications are non-negotiable
4. **Domain-Driven Architecture**: Business logic lives in domain entities, infrastructure is pluggable
5. **Interface Segregation**: Concrete types hidden behind well-defined interfaces

## Technology Stack

- **Language**: Go 1.23+ (latest version)
- **Architecture**: Domain-Driven Design (DDD) with Clean Architecture
- **Dependencies**:
  - **Fx**: Dependency injection framework
  - **GORM**: ORM for database operations
  - **Zap**: Structured logging
  - **Pericarp**: Core DDD library for domain entities and event bus
  - **Moq**: Mock generation for testing
- **Testing**: TDD (Test-Driven Development) and BDD (Behavior-Driven Development)
- **Database**: PostgreSQL (production), SQLite (development/testing)

## Development Commands

```bash
# Core development workflow
make build              # Build the application
make run               # Build and run the application
make test              # Run all tests
make test-cover        # Run tests with coverage report
make lint              # Run golangci-lint
make fmt               # Format code

# Development setup
make dev-setup         # Setup development environment (install tools)
make tidy              # Tidy Go modules

# Docker operations
make docker-build      # Build Docker image
make docker-run        # Build and run in Docker

# Multi-platform builds
make build-all         # Build for multiple platforms (Linux, macOS, Windows)
```

## Domain-Driven Design Structure

The codebase follows strict DDD principles with a clean architecture folder structure:

```
internal/
├── application/           # Application Services & Handlers
│   ├── handler/          # HTTP handlers for OAuth2/OIDC endpoints
│   └── service/          # Application services
├── domain/               # Domain Layer (Business Logic)
│   ├── entity/           # Domain entities/aggregates (NO ENTITIES YET)
│   ├── service/          # Domain services
│   ├── repository/       # Repository interfaces
│   └── event/            # Domain events
└── infrastructure/       # Infrastructure Layer
    ├── database/         # Database connections and GORM setup
    ├── config/           # Configuration management
    ├── middleware/       # HTTP middleware
    ├── server/           # HTTP server setup
    ├── di/               # Dependency injection modules
    └── app/              # Application setup and lifecycle
```

## IAM Domain Concepts

When implementing domain entities, focus on these core IAM business concepts:

### Identity Management
- **Users**: User accounts, profiles, credentials
- **Identity Providers**: External authentication providers (Google, Facebook, SAML)
- **Identity Linking**: Connecting multiple identities to single accounts
- **User Sessions**: Session management across devices and applications

### Access Management
- **Clients**: OAuth2/OIDC client applications
- **Scopes**: Permission granularity for resource access
- **Roles**: User roles and permissions within applications
- **Policies**: Access control policies and rules

### Token Management
- **Access Tokens**: Short-lived tokens for API access
- **Refresh Tokens**: Long-lived tokens for token renewal
- **ID Tokens**: OpenID Connect identity tokens
- **Authorization Codes**: Temporary codes for OAuth2 flows

### Security Features
- **Multi-Factor Authentication**: Additional security layers
- **Password Policies**: Complexity and security requirements
- **Certificate Authentication**: mTLS for service-to-service
- **Audit Logging**: Security event tracking and compliance

## Architectural Decision Framework

When implementing features, follow this decision hierarchy:

### 1. Protocol Compliance Check
- **Question**: Does this violate OAuth2, OpenID Connect, or security specifications?
- **Action**: If yes, reject the approach. Find specification-compliant alternative.
- **Example**: Never store passwords in plain text, always follow OAuth2 state parameter requirements

### 2. Security-First Design
- **Question**: What are the security implications of this implementation?
- **Action**: Start with security requirements, then build functionality
- **Pattern**: Encrypt sensitive data, validate all inputs, audit all security events

### 3. Domain-First Design
- **Question**: Can this be modeled as domain behavior with events?
- **Action**: Start with strongly typed events, then build entity methods
- **Pattern**: `UserRegistered`, `TokenIssued`, `SessionCreated` events

### 4. Interface Boundary Decisions
- **Question**: Should this be exposed as an interface?
- **Action**: If it's used by multiple layers or needs testing isolation, create interface
- **Rule**: Domain entities, application services, and repositories = interfaces

### 5. Pericarp Integration Points
- **Question**: Is this using pericarp idiomatically?
- **Checklist**:
  - ✅ Entities embed `domain.Entity`
  - ✅ From/With methods return interface types
  - ✅ Events are strongly typed structs
  - ✅ Errors added via `AddError()`, not returned

## Critical Design Constraints

### Non-Negotiable Rules
1. **Never store state in domain entities** - Only events and behavior
2. **All security operations are atomic** - One entity, one event, one transaction
3. **Test-first development** - No code without tests
4. **Interface-driven design** - Concrete types are implementation details
5. **Audit everything** - All security events must be logged

### IAM Security Constraints
- All endpoints must use HTTPS in production
- Passwords must be hashed using bcrypt or stronger
- JWT tokens must be signed with RS256 or stronger algorithms
- Session tokens must be cryptographically secure
- All sensitive data must be encrypted at rest
- Rate limiting must be applied to authentication endpoints

## Test-Driven Development (TDD)

**CRITICAL**: Always generate/update tests FIRST, then implement functionality.

### TDD Workflow:
1. **Red**: Write failing test first
2. **Green**: Write minimal code to pass test
3. **Refactor**: Improve code while keeping tests green

### Testing Structure:
```
internal/
├── domain/
│   ├── entity/
│   │   ├── user.go
│   │   └── user_test.go    # Unit tests for domain logic
├── application/
│   ├── service/
│   │   ├── auth_service.go
│   │   └── auth_service_test.go
└── infrastructure/
    └── database/
        ├── user_repository.go
        └── user_repository_test.go
```

### Testing Conventions:
- **Test Package Naming**: All test packages MUST end with `_test` (e.g., `package domain_test`, `package service_test`)
- **Mock Generation**: Use `moq` to generate mocks for interfaces:
  ```bash
  //go:generate moq -out repository_mock.go . Repository
  ```
- **Test Files**: Test files must end with `_test.go`

### BDD Testing:
- Use Gherkin scenarios for acceptance tests
- Place feature files in `test/features/`
- Implement step definitions in `test/steps/`
- Reference the comprehensive BDD specs in `planning/specs/`

## Implementation Strategy & Patterns

### Feature Development Strategy

**Phase 1: Domain Modeling (Always Start Here)**
1. **Identify the Domain Event**: What business event is occurring?
2. **Design Strongly Typed Event**: Create struct with all necessary data
3. **Model Entity Interface**: What behaviors does this entity need?
4. **Implement From/With Methods**: How is this entity constructed?
5. **Write Tests First**: TDD for all entity behavior

**Phase 2: Application Orchestration**
1. **Application Services**: Coordinate multiple entities
2. **HTTP Handlers**: OAuth2/OIDC compliant endpoints
3. **Event Handlers**: React to domain events for projections

**Phase 3: Infrastructure Implementation**
1. **Repository Interfaces**: Define what persistence needs
2. **GORM Models**: Pure data structures for persistence
3. **Security Middleware**: Authentication and authorization

### Code Organization Patterns

```bash
# Domain-first development order
internal/domain/entity/user_test.go         # 1. Write failing test
internal/domain/entity/user.go             # 2. Make test pass
internal/domain/repository/user.go         # 3. Define interface
internal/application/service/auth_test.go  # 4. Application test
internal/application/service/auth.go       # 5. Application logic
internal/infrastructure/repository/user_test.go # 6. Infrastructure test
internal/infrastructure/repository/user.go     # 7. Infrastructure impl
```

### Common Anti-Patterns to Avoid
1. **State in Entities**: Domain entities storing data instead of emitting events
2. **Generic Events**: Using `map[string]interface{}` instead of typed structs
3. **Logic in Models**: GORM models with business logic
4. **Infrastructure Leakage**: Domain depending on infrastructure types
5. **Missing Interfaces**: Concrete types in application layer
6. **Plain Text Secrets**: Never store passwords or secrets unencrypted

## OAuth2 and OpenID Connect Implementation

### Required Endpoints
- `/oauth2/authorize` - Authorization endpoint
- `/oauth2/token` - Token endpoint
- `/oauth2/introspect` - Token introspection
- `/oauth2/revoke` - Token revocation
- `/.well-known/openid_configuration` - Discovery endpoint
- `/userinfo` - OpenID Connect UserInfo endpoint
- `/jwks` - JSON Web Key Set endpoint

### Security Requirements
- State parameter validation for CSRF protection
- PKCE support for public clients
- Proper scope validation and consent
- Secure token storage and transmission
- Certificate-based authentication for mTLS

## Logging with Zap

Use structured logging throughout, especially for security events:

```go
logger.Info("user authenticated",
    zap.String("user_id", user.ID),
    zap.String("client_id", clientID),
    zap.String("grant_type", grantType),
    zap.String("ip_address", clientIP),
)

logger.Warn("failed authentication attempt",
    zap.String("email", email),
    zap.String("ip_address", clientIP),
    zap.String("reason", "invalid_credentials"),
)
```

## Error Handling

Follow Go best practices with security considerations:
- Return errors, don't panic
- Use custom error types for domain errors
- Log security events at appropriate levels
- Never expose sensitive information in error messages
- Use error wrapping for context

## Configuration

Environment variables (see `.env.example`):
- `SERVER_HOST`, `SERVER_PORT`: Server configuration
- `LOG_LEVEL`: Zap logging level
- `IAM_JWT_SECRET`: JWT signing secret (change in production)
- `IAM_ACCESS_TOKEN_TTL`: Access token lifetime
- Database connection strings for GORM

## Dependency Management

- Use `go mod tidy` to manage dependencies
- Pin specific versions in `go.mod`
- Use Fx modules to organize dependency injection

## Key Commands for Development

```bash
# TDD workflow
go test -v ./internal/domain/...           # Test domain layer first
go test -v ./internal/application/...      # Test application layer
go test -v ./internal/infrastructure/...   # Test infrastructure layer
go test -v ./...                          # Run all tests

# BDD workflow
go test -v ./test/features/...            # Run BDD scenarios

# Code quality
make lint                                 # Run linter
make fmt                                  # Format code
go vet ./...                             # Static analysis

# Mock generation
go generate ./...                         # Generate mocks using moq

# Coverage analysis
make test-cover                           # Generate coverage report
open coverage.html                        # View coverage in browser
```

## Pericarp Integration

Use the pericarp core library for domain modeling:

### Domain Entities (Interface-Based Atomic Approach):
```go
// User interface defines the contract for user management
type User interface {
    // Core pericarp methods
    ID() string
    AddEvent(event domain.Event)
    AddError(err error)
    HasErrors() bool
    GetErrors() []error

    // User construction methods
    FromRegistration(email, password string) User
    WithProfile(name, picture string) User
    WithRole(role string) User

    // User operations
    Authenticate(password string) User
    UpdatePassword(newPassword string) User
    LinkIdentity(provider, externalID string) User

    // User metadata
    GetEmail() string
    GetRoles() []string
    IsActive() bool
}

// Strongly typed events for better domain semantics
type UserRegisteredEvent struct {
    UserID    string
    Email     string
    CreatedAt time.Time
}

type UserAuthenticatedEvent struct {
    UserID    string
    IPAddress string
    UserAgent string
    Timestamp time.Time
}

type TokenIssuedEvent struct {
    UserID       string
    ClientID     string
    TokenType    string
    Scopes       []string
    ExpiresAt    time.Time
    IssuedAt     time.Time
}
```

### Event Bus Setup:
```go
// Use pericarp modules in Fx
var PericarpModule = fx.Module("pericarp",
    pericarp.Module, // Include pericarp's Fx module
    fx.Provide(
        // Your domain services that use pericarp event bus
    ),
)
```

## Integration Testing

- Use testcontainers for database integration tests
- Mock external OAuth2 providers in tests
- Use Fx testing utilities for dependency injection in tests
- Test OAuth2 flows end-to-end

## Performance Considerations

- Use GORM's batch operations for bulk inserts
- Implement proper database indexing for user lookups
- Use Zap's structured logging for performance
- Cache JWT verification keys appropriately
- Implement rate limiting for authentication endpoints

## Security

- Validate all inputs at application layer
- Use bcrypt for password hashing
- Implement proper JWT token validation
- Use HTTPS for all endpoints in production
- Follow OWASP security guidelines
- Implement comprehensive audit logging
- Use secure session management
- Protect against timing attacks in authentication