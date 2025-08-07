# Tech Stack - SportProyect

## Context

Stack tecnológico específico para SportProyect - Sistema de gestión deportiva con arquitectura moderna.

- Backend Framework: .NET 8+
- Language: C# 12+
- Runtime: .NET 8 Runtime
- Primary Database: Supabase (PostgreSQL)
- ORM: Entity Framework Core 8+
- Authentication: Supabase Auth
- Real-time: Supabase Realtime
- Storage: Supabase Storage
- Frontend Framework: Angular 20+
- TypeScript Version: 5.6+
- Build Tool: Angular CLI with esbuild
- Package Manager: npm
- Node Version: 22 LTS
- CSS Framework: TailwindCSS 4.0+
- UI Components: Angular Material 20+ or PrimeNG
- State Management: NgRx (for complex apps) or Angular Signals
- HTTP Client: Angular HttpClient with interceptors
- Font Provider: Google Fonts
- Font Loading: Self-hosted for performance
- Icons: Lucide Angular components or Material Icons
- Application Hosting: Vercel (Frontend) + Azure App Service (.NET API)
- Database Hosting: Supabase Cloud
- Database Backups: Supabase automated backups
- Asset Storage: Supabase Storage
- CDN: Vercel Edge Network + Supabase CDN
- Asset Access: Supabase Row Level Security (RLS)
- CI/CD Platform: GitHub Actions
- CI/CD Trigger: Push to main/staging branches
- Tests: .NET xUnit + Angular Jasmine/Karma
- Production Environment: main branch
- Staging Environment: staging branch


## Stack Confirmado

### Backend
- **Framework**: .NET 8+ con ASP.NET Core Web API
- **Language**: C# 12+ con nullable reference types habilitado
- **Architecture**: Clean Architecture (Domain, Application, Infrastructure, WebAPI)
- **ORM**: Entity Framework Core 8+ con PostgreSQL provider
- **CQRS**: MediatR para separación de comandos y queries
- **Validation**: FluentValidation
- **Mapping**: AutoMapper
- **Testing**: xUnit + Moq + FluentAssertions

### Frontend
- **Framework**: Angular 20+ con Standalone Components
- **Language**: TypeScript 5.6+ con strict mode
- **State Management**: Signals (nativo) + NgRx/SignalStore para estado complejo
- **UI Components**: Angular Material 20 + Custom components
- **CSS Framework**: TailwindCSS 4.0+
- **Build Tool**: Angular CLI con esbuild
- **Testing**: Jest + Testing Library

### Base de Datos y Servicios
- **Primary Database**: Supabase (PostgreSQL 15+)
- **Authentication**: Supabase Auth con JWT
- **Authorization**: Row Level Security (RLS) + .NET policies
- **Real-time**: Supabase Realtime para actualizaciones en vivo
- **Storage**: Supabase Storage para archivos
- **File Management**: Avatares, logos de equipos, fotos de partidos

### Infraestructura
- **Frontend Hosting**: Vercel con Edge Functions
- **Backend Hosting**: Azure App Service (Linux)
- **Database Hosting**: Supabase Cloud
- **CDN**: Vercel Edge Network + Supabase CDN
- **Monitoring**: Application Insights + Sentry
- **CI/CD**: GitHub Actions

### Herramientas de Desarrollo
- **Version Control**: Git + GitHub
- **Package Manager**: npm (frontend) + NuGet (backend)
- **Code Quality**: ESLint + Prettier (frontend), StyleCop + .NET Analyzers (backend)
- **API Documentation**: Swagger/OpenAPI
- **Database Migrations**: EF Core Migrations + Supabase Migrations

### Integraciones Específicas
- **Supabase Client**: supabase-csharp para .NET
- **Supabase JS**: @supabase/supabase-js para Angular
- **HTTP Client**: Angular HttpClient con interceptors
- **WebSockets**: SignalR para notificaciones adicionales

### Configuración de Seguridad
- **CORS**: Configurado para dominios específicos
- **Rate Limiting**: Por IP y por usuario autenticado
- **Headers Security**: Helmet.js equivalent en .NET
- **Secrets Management**: Azure Key Vault + User Secrets (dev)