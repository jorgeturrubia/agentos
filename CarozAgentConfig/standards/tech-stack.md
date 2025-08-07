# Tech Stack - CarozProject

## Context

Tech stack específico para CarozProject - Sistema de microservicios .NET 8 con frontend Angular 18.

## Backend Architecture

### Core Framework
- **Backend Framework**: .NET 8.0 Microservices
- **Language**: C# 12 with nullable reference types enabled
- **Runtime**: .NET 8 Runtime
- **Architecture**: Clean Architecture + Domain Driven Design
- **API Gateway**: Ocelot 23.3.4 with advanced rate limiting
- **Service Communication**: HTTP/REST through API Gateway

### Data Layer
- **Primary Database**: PostgreSQL
- **ORM**: Entity Framework Core 8.0.10 with Code First
- **Database Provider**: Npgsql.EntityFrameworkCore.PostgreSQL 8.0.10
- **Caching**: Redis with StackExchange.Redis 2.8.16
- **Migrations**: EF Core Migrations

### Authentication & Security
- **Authentication**: JWT Bearer tokens
- **Identity**: ASP.NET Core Identity with custom implementation
- **Authorization**: Role-based + Claims-based
- **Token Storage**: Redis caching
- **Rate Limiting**: Ocelot built-in rate limiting per endpoint

### Cross-Cutting Concerns
- **Logging**: Serilog 8.0.3 with multiple sinks (Console, Elasticsearch)
- **Object Mapping**: AutoMapper 13.0.1
- **Validation**: FluentValidation 11.3.0
- **Health Checks**: AspNetCore.HealthChecks for PostgreSQL + Redis

### API Documentation
- **OpenAPI Generator**: NSwag 14.0.0 + Swashbuckle
- **API Versioning**: Microsoft.AspNetCore.OpenApi 8.0.0
- **Code Generation**: NSwag MSBuild integration

## Frontend Stack

### Core Framework
- **Frontend Framework**: Angular 18.0.0
- **CLI**: Angular CLI 18.0.3
- **TypeScript**: 5.4.2
- **Build Tool**: Angular CLI with esbuild
- **Package Manager**: npm
- **Node Version**: 18.18.0+

### UI & Styling
- **Styling**: SCSS (inlineStyleLanguage: scss)
- **UI Components**: Angular Material 18 + Angular CDK
- **Rich Text Editor**: Quill 2.0.3 with quill-better-table
- **Scroll**: ngx-slimscroll 12.2.0
- **Utilities**: date-fns 4.1.0, xlsx 0.18.5

### State Management & HTTP
- **HTTP Client**: Angular HttpClient with interceptors
- **State Management**: Angular Signals + RxJS 7.8.0
- **Internationalization**: @ngx-translate/core 16.0.3
- **Authentication**: JWT handling with jwt-decode 4.0.0

### Development Tools
- **Linting**: ESLint 9.8.0 + Angular ESLint 18.3.0
- **TypeScript Linting**: @typescript-eslint/* 8.0.0
- **Code Formatting**: Prettier 3.3.3 + prettier-eslint
- **Design System**: Storybook 8.2.5 with Angular integration
- **Documentation**: Compodoc 1.1.25
- **Visual Testing**: Chromatic 1.6.1

### Testing Framework
- **Unit Testing**: Jasmine 5.1.0 + Karma 6.4.0
- **Test Runner**: Angular CLI test command
- **Coverage**: Karma Coverage
- **E2E Testing**: (Ready for Cypress/Playwright)

## Infrastructure & Deployment

### Containerization
- **Containerization**: Docker (implied from port configurations)
- **Container Orchestration**: Docker Compose (development)
- **Service Discovery**: Built-in container networking

### Service Ports
- **API Gateway**: 8000 (HTTPS)
- **Auth Microservice**: 80
- **Purchase Order API**: 8080
- **Middleware Service**: 8085

### Development Environment
- **Environment Management**: Angular environments (dev/prod)
- **Hot Reload**: Angular dev server
- **API Proxy**: Angular CLI proxy for local development
- **SSL**: HTTPS enabled for gateway

## Key Microservices

1. **OrigenDigital.Gateway** - API Gateway with Ocelot
2. **OrigenDigital.Microservices.Auth.API** - Authentication service
3. **Caroz.Backend.PurchaseOrder** - Purchase Order management
4. **Caroz.Backend.Middleware** - Integration middleware
5. **Caroz.Backend.Carriers** - Carrier management

## External Integrations

- **Lobster Integration**: Custom API integration
- **CSV Processing**: CsvHelper 30.0.1
- **File Management**: Excel processing with xlsx library
