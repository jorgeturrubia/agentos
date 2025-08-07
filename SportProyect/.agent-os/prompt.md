# Agent OS - Sport Project Prompt

## Project Overview

This is a comprehensive sports management application built with modern web technologies, following Clean Architecture principles and best practices.

## Tech Stack

### Backend (.NET 8)
- **Framework**: ASP.NET Core 8 Web API
- **Architecture**: Clean Architecture (Domain, Application, Infrastructure, WebAPI)
- **Database**: Supabase (PostgreSQL 15+)
- **ORM**: Entity Framework Core 8+
- **CQRS**: MediatR for command/query separation
- **Authentication**: Supabase Auth with JWT
- **Validation**: FluentValidation
- **Mapping**: AutoMapper
- **Testing**: xUnit + Moq + FluentAssertions

### Frontend (Angular 20+)
- **Framework**: Angular 20+ with Standalone Components
- **Language**: TypeScript 5.6+ with strict mode
- **State Management**: Angular Signals + NgRx/SignalStore
- **UI**: Angular Material 20 + Custom components
- **Styling**: TailwindCSS 4.0+
- **Build**: Angular CLI with esbuild
- **Testing**: Jest + Testing Library

### Database & Services
- **Primary Database**: Supabase (PostgreSQL 15+)
- **Authentication**: Supabase Auth
- **Authorization**: Row Level Security (RLS) + .NET policies
- **Real-time**: Supabase Realtime
- **Storage**: Supabase Storage for files

## Development Standards

### Code Style
- Follow the established code style guides in `/standards/code-style/`
- Use 2 spaces for indentation
- Implement proper naming conventions (PascalCase for classes, camelCase for variables)
- Write self-documenting code with clear variable names
- Add comments for "why" not "what"

### Best Practices
- Keep It Simple - implement in fewest lines possible
- Optimize for Readability over micro-optimizations
- DRY principle - extract repeated logic
- Single Responsibility - keep files focused
- Always use async/await for I/O operations
- Implement proper error handling with Result pattern
- Use cancellation tokens in async operations
- Implement structured logging

### Architecture Guidelines
- Follow Clean Architecture principles
- Use CQRS pattern with MediatR
- Implement proper dependency injection
- Use repository pattern for data access
- Implement proper validation at entry points
- Use DTOs for data transfer
- Implement proper unit and integration testing

## Project Structure

```
SportProyect/
├── .agent-os/           # Agent OS configuration
├── claude-code/         # Claude-specific code
├── commands/            # Available commands
├── instructions/        # Development instructions
└── standards/           # Code standards and guidelines
    ├── best-practices.md
    ├── code-style/
    ├── code-style.md
    └── tech-stack.md
```

## Available Commands

- `analyze-product.md` - Product analysis guidelines
- `create-spec.md` - Specification creation process
- `execute-task.md` - Task execution framework
- `plan-product.md` - Product planning methodology

## Key Features to Implement

1. **User Management**
   - Authentication with Supabase Auth
   - Role-based authorization (admin, coach, player, viewer)
   - User profiles and preferences

2. **Team Management**
   - Team creation and management
   - Player roster management
   - Coach assignments

3. **Match Management**
   - Match scheduling
   - Score tracking
   - Real-time updates

4. **Statistics & Analytics**
   - Player statistics
   - Team performance metrics
   - Historical data analysis

## Development Workflow

1. **Planning**: Use product planning commands
2. **Analysis**: Analyze requirements thoroughly
3. **Specification**: Create detailed specs
4. **Implementation**: Follow coding standards
5. **Testing**: Implement comprehensive tests
6. **Documentation**: Update relevant documentation

## Security Considerations

- Implement Row Level Security (RLS) in Supabase
- Use proper JWT validation
- Implement rate limiting
- Secure API endpoints with proper authorization
- Validate all inputs
- Use HTTPS everywhere
- Implement proper CORS policies

## Performance Guidelines

- Use async/await for all I/O operations
- Implement proper caching strategies
- Optimize database queries
- Use pagination for large datasets
- Implement proper error handling
- Monitor application performance

## Deployment

- **Frontend**: Vercel with Edge Functions
- **Backend**: Azure App Service (Linux)
- **Database**: Supabase Cloud
- **CI/CD**: GitHub Actions
- **Monitoring**: Application Insights + Sentry

This prompt serves as the central reference for all development activities in the Sport Project, ensuring consistency and adherence to established standards and best practices.