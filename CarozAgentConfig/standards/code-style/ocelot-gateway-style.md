# Ocelot API Gateway Style Guide - CarozProject

## Configuration Patterns

### Route Naming Convention
```json
{
  "Routes": [
    {
      // ✅ CORRECTO: Patrón de nombrado consistente
      "DownStreamPathTemplate": "/api/{microservice}/{resource}/{everything}",
      "UpStreamPathTemplate": "/gateway/{resource}/{everything}",
      
      // ✅ Descripción del patrón:
      // - microservice: nombre del microservicio (auth, purchase-orders, suppliers)
      // - resource: recurso en plural (users, orders, products)  
      // - everything: captura todos los parámetros adicionales
    }
  ]
}
```

### Authentication Microservice Routes
```json
{
  "Routes": [
    // ✅ Login Route
    {
      "DownStreamPathTemplate": "/api/auth/login",
      "DownStreamSchema": "http",
      "DownStreamHostAndPorts": [
        {
          "Host": "auth-api",
          "Port": 80
        }
      ],
      "UpStreamPathTemplate": "/gateway/auth/login",
      "UpStreamHttpMethod": ["POST"],
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "10s",
        "PeriodTimespan": 10,
        "Limit": 3
      }
    },
    
    // ✅ User Management Routes
    {
      "DownStreamPathTemplate": "/api/users/{everything}",
      "DownStreamSchema": "http", 
      "DownStreamHostAndPorts": [
        {
          "Host": "auth-api",
          "Port": 80
        }
      ],
      "UpStreamPathTemplate": "/gateway/users/{everything}",
      "UpStreamHttpMethod": ["GET", "POST", "PUT", "DELETE"],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "JwtBearer",
        "AllowedScopes": []
      },
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "10s", 
        "PeriodTimespan": 10,
        "Limit": 20
      }
    },

    // ✅ Role Management Routes
    {
      "DownStreamPathTemplate": "/api/roles/{everything}",
      "DownStreamSchema": "http",
      "DownStreamHostAndPorts": [
        {
          "Host": "auth-api",
          "Port": 80
        }
      ],
      "UpStreamPathTemplate": "/gateway/roles/{everything}",
      "UpStreamHttpMethod": ["GET", "POST", "PUT", "DELETE"],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "JwtBearer",
        "AllowedScopes": []
      },
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "10s",
        "PeriodTimespan": 10,
        "Limit": 15
      }
    }
  ]
}
```

### Business Microservice Routes
```json
{
  "Routes": [
    // ✅ Purchase Orders
    {
      "DownStreamPathTemplate": "/api/purchase-orders/{everything}",
      "DownStreamSchema": "http",
      "DownStreamHostAndPorts": [
        {
          "Host": "purchase-order-api",
          "Port": 8080
        }
      ],
      "UpStreamPathTemplate": "/gateway/purchase-orders/{everything}",
      "UpStreamHttpMethod": ["GET", "POST", "PUT", "DELETE"],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "JwtBearer",
        "AllowedScopes": []
      },
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "10s",
        "PeriodTimespan": 10,
        "Limit": 30
      },
      "LoadBalancerOptions": {
        "Type": "RoundRobin"
      }
    },

    // ✅ Suppliers
    {
      "DownStreamPathTemplate": "/api/suppliers/{everything}",
      "DownStreamSchema": "http",
      "DownStreamHostAndPorts": [
        {
          "Host": "purchase-order-api",
          "Port": 8080
        }
      ],
      "UpStreamPathTemplate": "/gateway/suppliers/{everything}",
      "UpStreamHttpMethod": ["GET", "POST", "PUT", "DELETE"],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "JwtBearer",
        "AllowedScopes": []
      },
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "10s",
        "PeriodTimespan": 10,
        "Limit": 25
      }
    },

    // ✅ Products
    {
      "DownStreamPathTemplate": "/api/products/{everything}",
      "DownStreamSchema": "http",
      "DownStreamHostAndPorts": [
        {
          "Host": "purchase-order-api", 
          "Port": 8080
        }
      ],
      "UpStreamPathTemplate": "/gateway/products/{everything}",
      "UpStreamHttpMethod": ["GET", "POST", "PUT", "DELETE"],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "JwtBearer",
        "AllowedScopes": []
      },
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "10s",
        "PeriodTimespan": 10,
        "Limit": 50
      }
    }
  ]
}
```

### Integration/Middleware Routes
```json
{
  "Routes": [
    // ✅ External Integration Routes
    {
      "DownStreamPathTemplate": "/api/integrations/lobster/{everything}",
      "DownStreamSchema": "http",
      "DownStreamHostAndPorts": [
        {
          "Host": "middleware",
          "Port": 8085
        }
      ],
      "UpStreamPathTemplate": "/gateway/integrations/lobster/{everything}",
      "UpStreamHttpMethod": ["POST", "GET"],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "JwtBearer",
        "AllowedScopes": []
      },
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "60s",
        "PeriodTimespan": 60,
        "Limit": 10
      }
    },

    // ✅ Data Synchronization
    {
      "DownStreamPathTemplate": "/api/sync/{everything}",
      "DownStreamSchema": "http",
      "DownStreamHostAndPorts": [
        {
          "Host": "middleware",
          "Port": 8085
        }
      ],
      "UpStreamPathTemplate": "/gateway/sync/{everything}",
      "UpStreamHttpMethod": ["POST"],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "JwtBearer",
        "AllowedScopes": ["admin"]
      },
      "RateLimitOptions": {
        "ClientWhiteList": [],
        "EnableRateLimiting": true,
        "Period": "300s",
        "PeriodTimespan": 300,
        "Limit": 5
      }
    }
  ]
}
```

## Rate Limiting Patterns

### Rate Limit Configuration by Resource Type
```json
{
  "RateLimitOptions": {
    "ClientWhiteList": [],
    "EnableRateLimiting": true,
    
    // ✅ Authentication endpoints - Restrictivo
    "Period": "60s",
    "PeriodTimespan": 60,
    "Limit": 5,
    
    // ✅ Read operations - Más permisivo
    // "Period": "10s",
    // "PeriodTimespan": 10, 
    // "Limit": 100,
    
    // ✅ Write operations - Moderado
    // "Period": "10s",
    // "PeriodTimespan": 10,
    // "Limit": 30,
    
    // ✅ External integrations - Muy restrictivo
    // "Period": "300s",
    // "PeriodTimespan": 300,
    // "Limit": 10
  }
}
```

### Rate Limit by Operation Type
```json
{
  "Routes": [
    // ✅ GET operations (lectura)
    {
      "UpStreamHttpMethod": ["GET"],
      "RateLimitOptions": {
        "Period": "10s",
        "PeriodTimespan": 10,
        "Limit": 100
      }
    },
    
    // ✅ POST/PUT operations (escritura)
    {
      "UpStreamHttpMethod": ["POST", "PUT"],
      "RateLimitOptions": {
        "Period": "10s", 
        "PeriodTimespan": 10,
        "Limit": 30
      }
    },
    
    // ✅ DELETE operations (eliminación)
    {
      "UpStreamHttpMethod": ["DELETE"],
      "RateLimitOptions": {
        "Period": "60s",
        "PeriodTimespan": 60,
        "Limit": 10
      }
    }
  ]
}
```

## Global Configuration

### Global Settings
```json
{
  "GlobalConfiguration": {
    // ✅ Base URL for the gateway
    "BaseUrl": "https://api.caroz.com",
    
    // ✅ Service discovery (if using)
    "ServiceDiscoveryProvider": {
      "Host": "consul",
      "Port": 8500,
      "Type": "Consul"
    },
    
    // ✅ Rate limiting global settings
    "RateLimitOptions": {
      "DisableRateLimitHeaders": false,
      "QuotaExceededMessage": "API rate limit exceeded. Please try again later.",
      "HttpStatusCode": 429,
      "ClientIdHeader": "ClientId"
    },
    
    // ✅ Load balancer default settings
    "LoadBalancerOptions": {
      "Type": "RoundRobin",
      "Key": "",
      "Expiry": 0
    }
  }
}
```

## Program.cs Configuration

### Startup Configuration
```csharp
// ✅ CORRECTO: Configuración completa de Ocelot
using Ocelot.DependencyInjection;
using Ocelot.Middleware;
using Ocelot.Cache.CacheManager;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;
using Serilog;

var builder = WebApplication.CreateBuilder(args);

// ✅ Serilog Configuration
builder.Host.UseSerilog((context, configuration) =>
    configuration.ReadFrom.Configuration(context.Configuration));

// ✅ JWT Authentication
var jwtKey = builder.Configuration["Jwt:Key"];
var jwtIssuer = builder.Configuration["Jwt:Issuer"];
var jwtAudience = builder.Configuration["Jwt:Audience"];

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer("JwtBearer", options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = jwtIssuer,
            ValidAudience = jwtAudience,
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(jwtKey!)),
            ClockSkew = TimeSpan.Zero
        };
        
        // ✅ Event handlers para logging
        options.Events = new JwtBearerEvents
        {
            OnAuthenticationFailed = context =>
            {
                Log.Warning("JWT Authentication failed: {Error}", context.Exception.Message);
                return Task.CompletedTask;
            },
            OnTokenValidated = context =>
            {
                Log.Information("JWT Token validated for user: {User}", 
                    context.Principal?.Identity?.Name ?? "Unknown");
                return Task.CompletedTask;
            }
        };
    });

// ✅ Authorization
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("AdminOnly", policy =>
        policy.RequireClaim("role", "Admin"));
    
    options.AddPolicy("UserOrAdmin", policy =>
        policy.RequireClaim("role", "User", "Admin"));
});

// ✅ CORS Configuration
builder.Services.AddCors(options =>
{
    options.AddPolicy("CarozCorsPolicy", policy =>
    {
        policy.WithOrigins(
            "http://localhost:4200",  // Angular dev server
            "https://caroz.com",      // Production frontend
            "https://app.caroz.com"   // Production app
        )
        .AllowAnyMethod()
        .AllowAnyHeader()
        .AllowCredentials();
    });
});

// ✅ Ocelot Configuration
builder.Configuration.AddJsonFile("ocelot.json", optional: false, reloadOnChange: true);

builder.Services.AddOcelot(builder.Configuration)
    .AddCacheManager(x =>
    {
        x.WithSerializer(typeof(JsonCacheSerializer));
        x.WithRedis(builder.Configuration.GetConnectionString("Redis"));
    });

// ✅ Health Checks
builder.Services.AddHealthChecks()
    .AddCheck("self", () => HealthCheckResult.Healthy())
    .AddUrlGroup(new Uri($"{builder.Configuration["Services:AuthApi"]}/health"), 
                 name: "auth-api", 
                 failureStatus: HealthStatus.Degraded)
    .AddUrlGroup(new Uri($"{builder.Configuration["Services:PurchaseOrderApi"]}/health"), 
                 name: "purchase-order-api", 
                 failureStatus: HealthStatus.Degraded);

var app = builder.Build();

// ✅ Pipeline Configuration
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}

// ✅ Logging middleware
app.UseSerilogRequestLogging(options =>
{
    options.MessageTemplate = "HTTP {RequestMethod} {RequestPath} responded {StatusCode} in {Elapsed:0.0000} ms";
    options.EnrichDiagnosticContext = (diagnosticContext, httpContext) =>
    {
        diagnosticContext.Set("RequestHost", httpContext.Request.Host.Value);
        diagnosticContext.Set("RequestScheme", httpContext.Request.Scheme);
        diagnosticContext.Set("UserAgent", httpContext.Request.Headers["User-Agent"].FirstOrDefault());
    };
});

// ✅ CORS before authentication
app.UseCors("CarozCorsPolicy");

// ✅ Security headers
app.Use(async (context, next) =>
{
    context.Response.Headers.Add("X-Content-Type-Options", "nosniff");
    context.Response.Headers.Add("X-Frame-Options", "DENY");
    context.Response.Headers.Add("X-XSS-Protection", "1; mode=block");
    context.Response.Headers.Add("Strict-Transport-Security", "max-age=31536000; includeSubDomains");
    await next();
});

app.UseAuthentication();
app.UseAuthorization();

// ✅ Health checks endpoint
app.MapHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

// ✅ Ocelot middleware
await app.UseOcelot();

app.Run();
```

## Custom Middleware

### Request Logging Middleware
```csharp
// ✅ CORRECTO: Middleware personalizado para logging
public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestLoggingMiddleware> _logger;

    public RequestLoggingMiddleware(RequestDelegate next, ILogger<RequestLoggingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var stopwatch = Stopwatch.StartNew();
        
        // ✅ Log request
        _logger.LogInformation("Incoming request: {Method} {Path} from {RemoteIp}",
            context.Request.Method,
            context.Request.Path,
            context.Connection.RemoteIpAddress);

        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Request failed: {Method} {Path}", 
                context.Request.Method, 
                context.Request.Path);
            throw;
        }
        finally
        {
            stopwatch.Stop();
            
            // ✅ Log response
            _logger.LogInformation("Request completed: {Method} {Path} - {StatusCode} in {ElapsedMilliseconds}ms",
                context.Request.Method,
                context.Request.Path,
                context.Response.StatusCode,
                stopwatch.ElapsedMilliseconds);
        }
    }
}
```

### Rate Limit Exceeded Middleware
```csharp
// ✅ CORRECTO: Middleware para manejar rate limiting
public class RateLimitMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RateLimitMiddleware> _logger;

    public RateLimitMiddleware(RequestDelegate next, ILogger<RateLimitMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        await _next(context);

        // ✅ Handle rate limit exceeded
        if (context.Response.StatusCode == 429)
        {
            _logger.LogWarning("Rate limit exceeded for {RemoteIp} on {Path}",
                context.Connection.RemoteIpAddress,
                context.Request.Path);

            context.Response.ContentType = "application/json";
            
            var response = new
            {
                message = "API rate limit exceeded. Please try again later.",
                statusCode = 429,
                timestamp = DateTime.UtcNow,
                path = context.Request.Path.Value
            };

            await context.Response.WriteAsync(JsonSerializer.Serialize(response));
        }
    }
}
```

## Configuration Management

### appsettings.json Structure
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Ocelot": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  
  "ConnectionStrings": {
    "Redis": "localhost:6379"
  },
  
  "Services": {
    "AuthApi": "http://auth-api:80",
    "PurchaseOrderApi": "http://purchase-order-api:8080",
    "MiddlewareApi": "http://middleware:8085"
  },
  
  "Jwt": {
    "Key": "your-super-secret-jwt-key-here-minimum-256-bits",
    "Issuer": "CarozApiGateway",
    "Audience": "CarozClients",
    "ExpiryMinutes": 60
  },
  
  "RateLimit": {
    "EnableIpRateLimiting": true,
    "EnableEndpointRateLimiting": false,
    "IpWhitelist": [],
    "GeneralRules": [
      {
        "Endpoint": "*",
        "Period": "10s",
        "Limit": 100
      }
    ]
  },
  
  "Serilog": {
    "Using": ["Serilog.Sinks.Console", "Serilog.Sinks.Elasticsearch"],
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "System": "Warning"
      }
    },
    "WriteTo": [
      {
        "Name": "Console",
        "Args": {
          "outputTemplate": "[{Timestamp:yyyy-MM-dd HH:mm:ss} {Level:u3}] {Message:lj} {Properties:j}{NewLine}{Exception}"
        }
      },
      {
        "Name": "Elasticsearch",
        "Args": {
          "nodeUris": "http://elasticsearch:9200",
          "indexFormat": "caroz-gateway-logs-{0:yyyy.MM.dd}",
          "autoRegisterTemplate": true
        }
      }
    ]
  }
}
```

## Health Checks Configuration

### Health Check Extensions
```csharp
// ✅ CORRECTO: Health checks personalizados
public static class HealthCheckExtensions
{
    public static IServiceCollection AddCarozHealthChecks(this IServiceCollection services, IConfiguration configuration)
    {
        services.AddHealthChecks()
            // ✅ Self check
            .AddCheck("gateway-self", () => HealthCheckResult.Healthy("Gateway is running"))
            
            // ✅ Database checks
            .AddNpgSql(
                connectionString: configuration.GetConnectionString("DefaultConnection")!,
                name: "postgresql",
                failureStatus: HealthStatus.Degraded,
                tags: new[] { "db", "sql", "postgresql" })
            
            // ✅ Redis check
            .AddRedis(
                connectionString: configuration.GetConnectionString("Redis")!,
                name: "redis",
                failureStatus: HealthStatus.Degraded,
                tags: new[] { "cache", "redis" })
            
            // ✅ Downstream services
            .AddUrlGroup(
                uri: new Uri($"{configuration["Services:AuthApi"]}/health"),
                name: "auth-service",
                failureStatus: HealthStatus.Degraded,
                tags: new[] { "service", "auth" })
            
            .AddUrlGroup(
                uri: new Uri($"{configuration["Services:PurchaseOrderApi"]}/health"),
                name: "purchase-order-service", 
                failureStatus: HealthStatus.Degraded,
                tags: new[] { "service", "purchase-order" });

        return services;
    }
}
```

## Error Handling

### Global Error Handler
```csharp
// ✅ CORRECTO: Manejo global de errores
public class GlobalErrorHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<GlobalErrorHandlingMiddleware> _logger;

    public GlobalErrorHandlingMiddleware(RequestDelegate next, ILogger<GlobalErrorHandlingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An unhandled exception occurred during request processing");
            await HandleExceptionAsync(context, ex);
        }
    }

    private static async Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        context.Response.ContentType = "application/json";
        
        var response = exception switch
        {
            UnauthorizedAccessException => new ErrorResponse
            {
                StatusCode = StatusCodes.Status401Unauthorized,
                Message = "Unauthorized access",
                Details = "Invalid or expired authentication token"
            },
            ArgumentException => new ErrorResponse
            {
                StatusCode = StatusCodes.Status400BadRequest,
                Message = "Bad request",
                Details = exception.Message
            },
            TimeoutException => new ErrorResponse
            {
                StatusCode = StatusCodes.Status408RequestTimeout,
                Message = "Request timeout",
                Details = "The downstream service took too long to respond"
            },
            _ => new ErrorResponse
            {
                StatusCode = StatusCodes.Status500InternalServerError,
                Message = "Internal server error",
                Details = "An unexpected error occurred"
            }
        };

        context.Response.StatusCode = response.StatusCode;
        await context.Response.WriteAsync(JsonSerializer.Serialize(response));
    }
}

public class ErrorResponse
{
    public int StatusCode { get; set; }
    public string Message { get; set; } = string.Empty;
    public string Details { get; set; } = string.Empty;
    public DateTime Timestamp { get; set; } = DateTime.UtcNow;
}
```
