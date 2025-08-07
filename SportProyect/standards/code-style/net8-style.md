# .NET 8 Style Guide para SportProyect

## Arquitectura y Estructura del Proyecto

### Clean Architecture con .NET 8

```
src/
├── SportProyect.Domain/           // Entidades, Value Objects, Domain Events
├── SportProyect.Application/      // Use Cases, DTOs, Interfaces, CQRS
├── SportProyect.Infrastructure/   // EF Core, Supabase Client, External Services
└── SportProyect.WebAPI/          // Controllers, Middleware, Program.cs
```

## Program.cs Configuration con Supabase

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add Supabase Client
builder.Services.AddSingleton<Supabase.Client>(provider =>
{
    var url = builder.Configuration["Supabase:Url"] 
        ?? throw new InvalidOperationException("Supabase URL not configured");
    var key = builder.Configuration["Supabase:AnonKey"] 
        ?? throw new InvalidOperationException("Supabase Anon Key not configured");
    
    var options = new SupabaseOptions
    {
        AutoRefreshToken = true,
        AutoConnectRealtime = true
    };
    
    return new Supabase.Client(url, key, options);
});

// JWT Configuration para Supabase
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        var supabaseUrl = builder.Configuration["Supabase:Url"];
        var jwtSecret = builder.Configuration["Supabase:JwtSecret"]; // Base64 encoded
        
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidIssuer = $"{supabaseUrl}/auth/v1",
            ValidateAudience = true,
            ValidAudience = "authenticated",
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(
                Convert.FromBase64String(jwtSecret)
            ),
            ClockSkew = TimeSpan.Zero
        };
    });

// Add Authorization with Policies
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("RequireAuthenticated", policy =>
        policy.RequireAuthenticatedUser());
    
    options.AddPolicy("RequireAdmin", policy =>
        policy.RequireClaim("role", "admin"));
});

// Add services
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Register application services
builder.Services.AddApplication();
builder.Services.AddInfrastructure(builder.Configuration);

var app = builder.Build();
```

## Entity Framework Core con Supabase PostgreSQL

### DbContext Configuration

```csharp
public class SportDbContext : DbContext
{
    public SportDbContext(DbContextOptions<SportDbContext> options)
        : base(options)
    {
    }

    public DbSet<User> Users => Set<User>();
    public DbSet<Team> Teams => Set<Team>();
    public DbSet<Match> Matches => Set<Match>();
    public DbSet<Player> Players => Set<Player>();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            optionsBuilder.UseNpgsql(
                connectionString,
                npgsqlOptions => npgsqlOptions
                    .EnableRetryOnFailure(
                        maxRetryCount: 3,
                        maxRetryDelay: TimeSpan.FromSeconds(30),
                        errorCodesToAdd: null)
                    .UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery)
            );
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Apply all configurations from assembly
        modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
        
        // Global query filters
        modelBuilder.Entity<User>()
            .HasQueryFilter(u => !u.IsDeleted);
            
        // Value conversions
        modelBuilder.Entity<Match>()
            .Property(m => m.Status)
            .HasConversion<string>();
            
        // Configure Supabase auth.users relationship
        modelBuilder.Entity<User>()
            .Property(u => u.SupabaseUserId)
            .HasColumnName("supabase_user_id");
    }
}
```

### Entity Configuration Example

```csharp
public class UserConfiguration : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.ToTable("users");
        
        builder.HasKey(u => u.Id);
        
        builder.Property(u => u.Email)
            .IsRequired()
            .HasMaxLength(255);
            
        builder.Property(u => u.Username)
            .IsRequired()
            .HasMaxLength(50);
            
        builder.HasIndex(u => u.Email)
            .IsUnique();
            
        builder.HasIndex(u => u.SupabaseUserId)
            .IsUnique();
            
        // Relationship with Supabase auth
        builder.Property(u => u.SupabaseUserId)
            .IsRequired();
    }
}
```

## Service Pattern con Dependency Injection

### Interface Definition

```csharp
public interface IUserService
{
    Task<Result<UserDto>> GetUserAsync(Guid id, CancellationToken ct = default);
    Task<Result<UserDto>> GetUserBySupabaseIdAsync(string supabaseId, CancellationToken ct = default);
    Task<Result<UserDto>> CreateUserAsync(CreateUserDto dto, CancellationToken ct = default);
    Task<Result<UserDto>> UpdateUserAsync(Guid id, UpdateUserDto dto, CancellationToken ct = default);
    Task<Result> DeleteUserAsync(Guid id, CancellationToken ct = default);
}
```

### Service Implementation

```csharp
public class UserService : IUserService
{
    private readonly SportDbContext _context;
    private readonly Supabase.Client _supabase;
    private readonly ILogger<UserService> _logger;
    private readonly IMapper _mapper;

    public UserService(
        SportDbContext context, 
        Supabase.Client supabase,
        ILogger<UserService> logger,
        IMapper mapper)
    {
        _context = context;
        _supabase = supabase;
        _logger = logger;
        _mapper = mapper;
    }

    public async Task<Result<UserDto>> GetUserAsync(Guid id, CancellationToken ct = default)
    {
        try
        {
            var user = await _context.Users
                .AsNoTracking()
                .FirstOrDefaultAsync(u => u.Id == id, ct);

            if (user == null)
            {
                return Result<UserDto>.Failure("User not found");
            }

            return Result<UserDto>.Success(_mapper.Map<UserDto>(user));
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting user {UserId}", id);
            return Result<UserDto>.Failure("An error occurred while retrieving the user");
        }
    }

    public async Task<Result<UserDto>> CreateUserAsync(CreateUserDto dto, CancellationToken ct = default)
    {
        using var transaction = await _context.Database.BeginTransactionAsync(ct);
        
        try
        {
            // Create user in Supabase Auth
            var authResponse = await _supabase.Auth.SignUp(dto.Email, dto.Password);
            
            if (authResponse?.User == null)
            {
                return Result<UserDto>.Failure("Failed to create auth user");
            }

            // Create user in our database
            var user = new User
            {
                Email = dto.Email,
                Username = dto.Username,
                SupabaseUserId = authResponse.User.Id,
                CreatedAt = DateTime.UtcNow
            };

            _context.Users.Add(user);
            await _context.SaveChangesAsync(ct);
            
            await transaction.CommitAsync(ct);

            return Result<UserDto>.Success(_mapper.Map<UserDto>(user));
        }
        catch (Exception ex)
        {
            await transaction.RollbackAsync(ct);
            _logger.LogError(ex, "Error creating user");
            return Result<UserDto>.Failure("An error occurred while creating the user");
        }
    }
}
```

## CQRS con MediatR

### Command Example

```csharp
public record CreateMatchCommand : IRequest<Result<MatchDto>>
{
    public required Guid HomeTeamId { get; init; }
    public required Guid AwayTeamId { get; init; }
    public required DateTime ScheduledDate { get; init; }
    public string? Location { get; init; }
}

public class CreateMatchCommandHandler : IRequestHandler<CreateMatchCommand, Result<MatchDto>>
{
    private readonly SportDbContext _context;
    private readonly IMapper _mapper;
    private readonly ILogger<CreateMatchCommandHandler> _logger;

    public CreateMatchCommandHandler(
        SportDbContext context,
        IMapper mapper,
        ILogger<CreateMatchCommandHandler> logger)
    {
        _context = context;
        _mapper = mapper;
        _logger = logger;
    }

    public async Task<Result<MatchDto>> Handle(
        CreateMatchCommand request, 
        CancellationToken cancellationToken)
    {
        try
        {
            var match = new Match
            {
                HomeTeamId = request.HomeTeamId,
                AwayTeamId = request.AwayTeamId,
                ScheduledDate = request.ScheduledDate,
                Location = request.Location,
                Status = MatchStatus.Scheduled,
                CreatedAt = DateTime.UtcNow
            };

            _context.Matches.Add(match);
            await _context.SaveChangesAsync(cancellationToken);

            _logger.LogInformation("Match created with Id: {MatchId}", match.Id);

            return Result<MatchDto>.Success(_mapper.Map<MatchDto>(match));
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error creating match");
            return Result<MatchDto>.Failure("Failed to create match");
        }
    }
}
```

### Query Example

```csharp
public record GetMatchesByDateQuery : IRequest<Result<List<MatchDto>>>
{
    public required DateTime StartDate { get; init; }
    public required DateTime EndDate { get; init; }
}

public class GetMatchesByDateQueryHandler : IRequestHandler<GetMatchesByDateQuery, Result<List<MatchDto>>>
{
    private readonly SportDbContext _context;
    private readonly IMapper _mapper;

    public GetMatchesByDateQueryHandler(SportDbContext context, IMapper mapper)
    {
        _context = context;
        _mapper = mapper;
    }

    public async Task<Result<List<MatchDto>>> Handle(
        GetMatchesByDateQuery request, 
        CancellationToken cancellationToken)
    {
        var matches = await _context.Matches
            .AsNoTracking()
            .Include(m => m.HomeTeam)
            .Include(m => m.AwayTeam)
            .Where(m => m.ScheduledDate >= request.StartDate && 
                       m.ScheduledDate <= request.EndDate)
            .OrderBy(m => m.ScheduledDate)
            .ToListAsync(cancellationToken);

        var matchDtos = _mapper.Map<List<MatchDto>>(matches);
        return Result<List<MatchDto>>.Success(matchDtos);
    }
}
```

## Controller Pattern con Minimal APIs

### Traditional Controller

```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize]
public class TeamsController : ControllerBase
{
    private readonly IMediator _mediator;
    private readonly ILogger<TeamsController> _logger;

    public TeamsController(IMediator mediator, ILogger<TeamsController> logger)
    {
        _mediator = mediator;
        _logger = logger;
    }

    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof(TeamDto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetTeam(Guid id, CancellationToken ct)
    {
        var result = await _mediator.Send(new GetTeamByIdQuery { Id = id }, ct);
        
        return result.IsSuccess 
            ? Ok(result.Value) 
            : NotFound(result.Error);
    }

    [HttpPost]
    [ProducesResponseType(typeof(TeamDto), StatusCodes.Status201Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> CreateTeam(
        [FromBody] CreateTeamCommand command, 
        CancellationToken ct)
    {
        var result = await _mediator.Send(command, ct);
        
        return result.IsSuccess 
            ? CreatedAtAction(nameof(GetTeam), new { id = result.Value.Id }, result.Value)
            : BadRequest(result.Error);
    }
}
```

### Minimal API Alternative

```csharp
public static class TeamEndpoints
{
    public static void MapTeamEndpoints(this WebApplication app)
    {
        var teams = app.MapGroup("/api/teams")
            .RequireAuthorization()
            .WithTags("Teams");

        teams.MapGet("/{id:guid}", GetTeam)
            .WithName("GetTeam")
            .Produces<TeamDto>()
            .Produces(StatusCodes.Status404NotFound);

        teams.MapPost("/", CreateTeam)
            .WithName("CreateTeam")
            .Produces<TeamDto>(StatusCodes.Status201Created)
            .Produces(StatusCodes.Status400BadRequest);
    }

    private static async Task<IResult> GetTeam(
        Guid id, 
        IMediator mediator, 
        CancellationToken ct)
    {
        var result = await mediator.Send(new GetTeamByIdQuery { Id = id }, ct);
        
        return result.IsSuccess 
            ? Results.Ok(result.Value) 
            : Results.NotFound(result.Error);
    }

    private static async Task<IResult> CreateTeam(
        CreateTeamCommand command,
        IMediator mediator,
        CancellationToken ct)
    {
        var result = await mediator.Send(command, ct);
        
        return result.IsSuccess 
            ? Results.CreatedAtRoute("GetTeam", new { id = result.Value.Id }, result.Value)
            : Results.BadRequest(result.Error);
    }
}
```

## Error Handling y Result Pattern

```csharp
public class Result<T>
{
    public bool IsSuccess { get; }
    public T Value { get; }
    public string Error { get; }

    protected Result(bool isSuccess, T value, string error)
    {
        IsSuccess = isSuccess;
        Value = value;
        Error = error;
    }

    public static Result<T> Success(T value) => new(true, value, null);
    public static Result<T> Failure(string error) => new(false, default, error);
}

public class Result
{
    public bool IsSuccess { get; }
    public string Error { get; }

    protected Result(bool isSuccess, string error)
    {
        IsSuccess = isSuccess;
        Error = error;
    }

    public static Result Success() => new(true, null);
    public static Result Failure(string error) => new(false, error);
}
```

## Global Exception Handling Middleware

```csharp
public class GlobalExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<GlobalExceptionHandlingMiddleware> _logger;

    public GlobalExceptionHandlingMiddleware(
        RequestDelegate next,
        ILogger<GlobalExceptionHandlingMiddleware> logger)
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
            _logger.LogError(ex, "An unhandled exception occurred");
            await HandleExceptionAsync(context, ex);
        }
    }

    private static async Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        context.Response.ContentType = "application/json";
        var response = context.Response;

        var errorResponse = new ErrorResponse
        {
            Timestamp = DateTime.UtcNow,
            TraceId = Activity.Current?.Id ?? context.TraceIdentifier
        };

        switch (exception)
        {
            case ValidationException ex:
                response.StatusCode = StatusCodes.Status400BadRequest;
                errorResponse.Message = "Validation failed";
                errorResponse.Details = ex.Errors.Select(e => e.ErrorMessage).ToList();
                break;
                
            case NotFoundException ex:
                response.StatusCode = StatusCodes.Status404NotFound;
                errorResponse.Message = ex.Message;
                break;
                
            case UnauthorizedException ex:
                response.StatusCode = StatusCodes.Status401Unauthorized;
                errorResponse.Message = "Unauthorized";
                break;
                
            default:
                response.StatusCode = StatusCodes.Status500InternalServerError;
                errorResponse.Message = "An error occurred while processing your request";
                break;
        }

        var jsonResponse = JsonSerializer.Serialize(errorResponse);
        await response.WriteAsync(jsonResponse);
    }
}

public class ErrorResponse
{
    public string Message { get; set; }
    public List<string> Details { get; set; } = new();
    public DateTime Timestamp { get; set; }
    public string TraceId { get; set; }
}
```

## Validation con FluentValidation

```csharp
public class CreateTeamCommandValidator : AbstractValidator<CreateTeamCommand>
{
    public CreateTeamCommandValidator()
    {
        RuleFor(x => x.Name)
            .NotEmpty().WithMessage("Team name is required")
            .MaximumLength(100).WithMessage("Team name must not exceed 100 characters")
            .Matches(@"^[a-zA-Z0-9\s]+$").WithMessage("Team name can only contain letters, numbers and spaces");

        RuleFor(x => x.City)
            .NotEmpty().WithMessage("City is required")
            .MaximumLength(50).WithMessage("City must not exceed 50 characters");

        RuleFor(x => x.Founded)
            .LessThanOrEqualTo(DateTime.UtcNow.Year)
            .WithMessage("Founded year cannot be in the future")
            .GreaterThan(1850)
            .WithMessage("Founded year must be after 1850");

        RuleFor(x => x.Stadium)
            .MaximumLength(100).WithMessage("Stadium name must not exceed 100 characters")
            .When(x => !string.IsNullOrEmpty(x.Stadium));
    }
}
```

## AutoMapper Configuration

```csharp
public class MappingProfile : Profile
{
    public MappingProfile()
    {
        // User mappings
        CreateMap<User, UserDto>()
            .ForMember(dest => dest.FullName, 
                opt => opt.MapFrom(src => $"{src.FirstName} {src.LastName}"));
        
        CreateMap<CreateUserDto, User>()
            .ForMember(dest => dest.Id, opt => opt.Ignore())
            .ForMember(dest => dest.CreatedAt, opt => opt.Ignore());

        // Team mappings
        CreateMap<Team, TeamDto>();
        CreateMap<Team, TeamDetailDto>()
            .ForMember(dest => dest.PlayerCount, 
                opt => opt.MapFrom(src => src.Players.Count));
        
        // Match mappings
        CreateMap<Match, MatchDto>()
            .ForMember(dest => dest.HomeTeamName, 
                opt => opt.MapFrom(src => src.HomeTeam.Name))
            .ForMember(dest => dest.AwayTeamName, 
                opt => opt.MapFrom(src => src.AwayTeam.Name));
    }
}
```

## Supabase Realtime Integration

```csharp
public class RealtimeService : IRealtimeService
{
    private readonly Supabase.Client _supabase;
    private readonly ILogger<RealtimeService> _logger;

    public RealtimeService(Supabase.Client supabase, ILogger<RealtimeService> logger)
    {
        _supabase = supabase;
        _logger = logger;
    }

    public async Task SubscribeToMatchUpdates(Guid matchId, Action<Match> onUpdate)
    {
        var channel = _supabase.Realtime.Channel("match-updates");
        
        channel
            .On(ListenType.PostgresChanges, 
                "matches", 
                ListenFilter.Eq("id", matchId.ToString()),
                (sender, change) =>
                {
                    if (change.Payload?.Data?.NewRecord != null)
                    {
                        var match = change.Payload.Data.NewRecord.ToObject<Match>();
                        onUpdate(match);
                    }
                });

        await channel.Subscribe();
        _logger.LogInformation("Subscribed to realtime updates for match {MatchId}", matchId);
    }

    public async Task UnsubscribeFromMatchUpdates(string channelName)
    {
        await _supabase.Realtime.Remove(_supabase.Realtime.Channel(channelName));
        _logger.LogInformation("Unsubscribed from channel {ChannelName}", channelName);
    }
}
```

## Unit Testing Pattern

```csharp
public class UserServiceTests
{
    private readonly Mock<SportDbContext> _mockContext;
    private readonly Mock<Supabase.Client> _mockSupabase;
    private readonly Mock<ILogger<UserService>> _mockLogger;
    private readonly IMapper _mapper;
    private readonly UserService _userService;

    public UserServiceTests()
    {
        _mockContext = new Mock<SportDbContext>();
        _mockSupabase = new Mock<Supabase.Client>();
        _mockLogger = new Mock<ILogger<UserService>>();
        
        var config = new MapperConfiguration(cfg => cfg.AddProfile<MappingProfile>());
        _mapper = config.CreateMapper();

        _userService = new UserService(
            _mockContext.Object,
            _mockSupabase.Object,
            _mockLogger.Object,
            _mapper);
    }

    [Fact]
    public async Task GetUserAsync_WhenUserExists_ReturnsSuccessResult()
    {
        // Arrange
        var userId = Guid.NewGuid();
        var user = new User 
        { 
            Id = userId, 
            Email = "test@example.com",
            Username = "testuser"
        };

        _mockContext.Setup(x => x.Users)
            .Returns(GetQueryableMockDbSet(new List<User> { user }));

        // Act
        var result = await _userService.GetUserAsync(userId);

        // Assert
        result.IsSuccess.Should().BeTrue();
        result.Value.Should().NotBeNull();
        result.Value.Email.Should().Be(user.Email);
    }

    private static DbSet<T> GetQueryableMockDbSet<T>(List<T> sourceList) where T : class
    {
        var queryable = sourceList.AsQueryable();
        var dbSet = new Mock<DbSet<T>>();
        
        dbSet.As<IQueryable<T>>().Setup(m => m.Provider).Returns(queryable.Provider);
        dbSet.As<IQueryable<T>>().Setup(m => m.Expression).Returns(queryable.Expression);
        dbSet.As<IQueryable<T>>().Setup(m => m.ElementType).Returns(queryable.ElementType);
        dbSet.As<IQueryable<T>>().Setup(m => m.GetEnumerator()).Returns(() => queryable.GetEnumerator());
        
        return dbSet.Object;
    }
}
```

## Logging Best Practices

```csharp
public class MatchService : IMatchService
{
    private readonly ILogger<MatchService> _logger;

    public async Task<Result<MatchDto>> CreateMatchAsync(CreateMatchDto dto)
    {
        using (_logger.BeginScope(new Dictionary<string, object>
        {
            ["HomeTeamId"] = dto.HomeTeamId,
            ["AwayTeamId"] = dto.AwayTeamId,
            ["ScheduledDate"] = dto.ScheduledDate
        }))
        {
            _logger.LogInformation("Creating new match between teams {HomeTeamId} and {AwayTeamId}", 
                dto.HomeTeamId, dto.AwayTeamId);

            try
            {
                // Create match logic...
                
                _logger.LogInformation("Match created successfully with Id: {MatchId}", match.Id);
                return Result<MatchDto>.Success(matchDto);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Failed to create match");
                return Result<MatchDto>.Failure("Failed to create match");
            }
        }
    }
}
```

## Health Checks

```csharp
public class DatabaseHealthCheck : IHealthCheck
{
    private readonly SportDbContext _context;

    public DatabaseHealthCheck(SportDbContext context)
    {
        _context = context;
    }

    public async Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default)
    {
        try
        {
            await _context.Database.CanConnectAsync(cancellationToken);
            return HealthCheckResult.Healthy("Database is accessible");
        }
        catch (Exception ex)
        {
            return HealthCheckResult.Unhealthy("Database is not accessible", ex);
        }
    }
}

public class SupabaseHealthCheck : IHealthCheck
{
    private readonly Supabase.Client _supabase;

    public SupabaseHealthCheck(Supabase.Client supabase)
    {
        _supabase = supabase;
    }

    public async Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default)
    {
        try
        {
            var session = _supabase.Auth.CurrentSession;
            return session != null 
                ? HealthCheckResult.Healthy("Supabase connection is healthy")
                : HealthCheckResult.Degraded("Supabase session is not active");
        }
        catch (Exception ex)
        {
            return HealthCheckResult.Unhealthy("Supabase connection failed", ex);
        }
    }
}

// In Program.cs
builder.Services.AddHealthChecks()
    .AddCheck<DatabaseHealthCheck>("database")
    .AddCheck<SupabaseHealthCheck>("supabase");
```

## Convenciones de Nomenclatura

- **Namespaces**: `SportProyect.Domain.Entities`, `SportProyect.Application.UseCases`
- **Clases**: PascalCase - `UserService`, `TeamRepository`
- **Interfaces**: Prefijo 'I' - `IUserService`, `ITeamRepository`
- **Métodos**: PascalCase - `GetUserAsync`, `CreateTeamAsync`
- **Parámetros y variables**: camelCase - `userId`, `teamName`
- **Constantes**: UPPER_CASE - `MAX_TEAM_SIZE`, `DEFAULT_TIMEOUT`
- **Propiedades**: PascalCase - `FirstName`, `IsActive`

## Configuración y Secretos

```csharp
// appsettings.json
{
  "Supabase": {
    "Url": "https://your-project.supabase.co",
    "AnonKey": "your-anon-key"
  },
  "ConnectionStrings": {
    "DefaultConnection": "Host=db.your-project.supabase.co;Database=postgres;Username=postgres;Password=your-password"
  }
}

// Use User Secrets for development
// dotnet user-secrets set "Supabase:JwtSecret" "your-base64-jwt-secret"
```

## Mejores Prácticas Específicas

1. **Siempre use async/await** para operaciones I/O
2. **Implemente IDisposable** cuando maneje recursos no administrados
3. **Use cancellation tokens** en todas las operaciones asíncronas
4. **Prefiera record types** para DTOs inmutables
5. **Use nullable reference types** habilitados globalmente
6. **Implemente logging estructurado** con correlación IDs
7. **Use transacciones** para operaciones que afecten múltiples entidades
8. **Cache estratégicamente** con IMemoryCache o Redis
9. **Valide entrada** en el punto de entrada (Controllers/Endpoints)
10. **Mantenga controllers delgados** - toda la lógica en servicios