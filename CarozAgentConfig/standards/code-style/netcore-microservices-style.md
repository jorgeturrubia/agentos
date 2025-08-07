# .NET Core Microservices Style Guide - CarozProject

## Clean Architecture Structure

### Project Organization
```
src/
├── Caroz.Backend.PurchaseOrder/              # API Layer
│   ├── Controllers/                          # REST API endpoints
│   ├── Program.cs                           # Application entry point
│   ├── appsettings.json                     # Configuration
│   └── Caroz.Backend.PurchaseOrder.csproj   
├── Caroz.Backend.PurchaseOrder.Application/  # Application Layer
│   ├── DTOs/                                # Data Transfer Objects
│   ├── Interfaces/                          # Application interfaces
│   ├── Services/                            # Application services
│   ├── Validators/                          # FluentValidation validators
│   └── Mappings/                            # AutoMapper profiles
├── Caroz.Backend.PurchaseOrder.Domain/       # Domain Layer
│   ├── Entities/                            # Domain entities
│   ├── Repositories/                        # Repository interfaces
│   ├── Exceptions/                          # Domain exceptions
│   └── ValueObjects/                        # Domain value objects
└── Caroz.Backend.PurchaseOrder.Infrastructure/ # Infrastructure Layer
    ├── Data/                                # EF Core DbContext
    ├── Repositories/                        # Repository implementations
    ├── Configurations/                      # EF Entity configurations
    └── Migrations/                          # EF Core migrations
```

## Naming Conventions

### Controllers
```csharp
// ✅ CORRECTO: Controller naming
[ApiController]
[Route("api/[controller]")]
public class PurchaseOrdersController : ControllerBase
{
    // ✅ Actions con nombres claros y específicos
    [HttpGet]
    public async Task<ActionResult<IEnumerable<PurchaseOrderDto>>> GetPurchaseOrdersAsync()
    
    [HttpGet("{id:guid}")]
    public async Task<ActionResult<PurchaseOrderDto>> GetPurchaseOrderByIdAsync(Guid id)
    
    [HttpPost]
    public async Task<ActionResult<PurchaseOrderDto>> CreatePurchaseOrderAsync(CreatePurchaseOrderDto dto)
    
    [HttpPut("{id:guid}")]
    public async Task<ActionResult<PurchaseOrderDto>> UpdatePurchaseOrderAsync(Guid id, UpdatePurchaseOrderDto dto)
    
    [HttpDelete("{id:guid}")]
    public async Task<ActionResult> DeletePurchaseOrderAsync(Guid id)
}
```

### Entities (Domain Layer)
```csharp
// ✅ CORRECTO: Entity with Data Annotations
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

[Table("PurchaseOrders")]
public class PurchaseOrder
{
    [Key]
    public Guid Id { get; set; }
    
    [Required]
    [MaxLength(50)]
    public string OrderNumber { get; set; } = string.Empty;
    
    [Required]
    [Column(TypeName = "decimal(18,2)")]
    public decimal TotalAmount { get; set; }
    
    [Required]
    public DateTime CreatedDate { get; set; }
    
    [MaxLength(500)]
    public string? Notes { get; set; }
    
    // ✅ Navigation properties
    public Guid SupplierId { get; set; }
    [ForeignKey(nameof(SupplierId))]
    public virtual Supplier Supplier { get; set; } = null!;
    
    public virtual ICollection<PurchaseOrderLine> Lines { get; set; } = new List<PurchaseOrderLine>();
}
```

### DTOs (Application Layer)
```csharp
// ✅ CORRECTO: DTO naming and structure
public record PurchaseOrderDto(
    Guid Id,
    string OrderNumber,
    decimal TotalAmount,
    DateTime CreatedDate,
    string? Notes,
    SupplierDto Supplier,
    IReadOnlyList<PurchaseOrderLineDto> Lines
);

public record CreatePurchaseOrderDto(
    [Required] string OrderNumber,
    [Required] [Range(0.01, double.MaxValue)] decimal TotalAmount,
    string? Notes,
    [Required] Guid SupplierId,
    ICollection<CreatePurchaseOrderLineDto> Lines
);

public record UpdatePurchaseOrderDto(
    [Required] string OrderNumber,
    [Required] [Range(0.01, double.MaxValue)] decimal TotalAmount,
    string? Notes,
    ICollection<UpdatePurchaseOrderLineDto> Lines
);
```

## Repository Pattern Implementation

### Repository Interface (Domain Layer)
```csharp
// ✅ CORRECTO: Repository interface in Domain
public interface IPurchaseOrderRepository
{
    Task<PurchaseOrder?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default);
    Task<IEnumerable<PurchaseOrder>> GetAllAsync(CancellationToken cancellationToken = default);
    Task<PurchaseOrder> AddAsync(PurchaseOrder entity, CancellationToken cancellationToken = default);
    Task UpdateAsync(PurchaseOrder entity, CancellationToken cancellationToken = default);
    Task DeleteAsync(Guid id, CancellationToken cancellationToken = default);
    Task<bool> ExistsAsync(Guid id, CancellationToken cancellationToken = default);
    Task<IEnumerable<PurchaseOrder>> GetBySupplierAsync(Guid supplierId, CancellationToken cancellationToken = default);
    Task<int> SaveChangesAsync(CancellationToken cancellationToken = default);
}
```

### Repository Implementation (Infrastructure Layer)
```csharp
// ✅ CORRECTO: Repository implementation
public class PurchaseOrderRepository : IPurchaseOrderRepository
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<PurchaseOrderRepository> _logger;

    public PurchaseOrderRepository(
        ApplicationDbContext context,
        ILogger<PurchaseOrderRepository> logger)
    {
        _context = context;
        _logger = logger;
    }

    public async Task<PurchaseOrder?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default)
    {
        return await _context.PurchaseOrders
            .Include(po => po.Supplier)
            .Include(po => po.Lines)
            .ThenInclude(line => line.Product)
            .FirstOrDefaultAsync(po => po.Id == id, cancellationToken);
    }

    public async Task<IEnumerable<PurchaseOrder>> GetAllAsync(CancellationToken cancellationToken = default)
    {
        return await _context.PurchaseOrders
            .Include(po => po.Supplier)
            .Include(po => po.Lines)
            .OrderByDescending(po => po.CreatedDate)
            .ToListAsync(cancellationToken);
    }

    public async Task<PurchaseOrder> AddAsync(PurchaseOrder entity, CancellationToken cancellationToken = default)
    {
        var entry = await _context.PurchaseOrders.AddAsync(entity, cancellationToken);
        return entry.Entity;
    }

    public async Task UpdateAsync(PurchaseOrder entity, CancellationToken cancellationToken = default)
    {
        _context.PurchaseOrders.Update(entity);
        await Task.CompletedTask;
    }

    public async Task DeleteAsync(Guid id, CancellationToken cancellationToken = default)
    {
        var entity = await _context.PurchaseOrders.FindAsync(new object[] { id }, cancellationToken);
        if (entity != null)
        {
            _context.PurchaseOrders.Remove(entity);
        }
    }

    public async Task<bool> ExistsAsync(Guid id, CancellationToken cancellationToken = default)
    {
        return await _context.PurchaseOrders.AnyAsync(po => po.Id == id, cancellationToken);
    }

    public async Task<IEnumerable<PurchaseOrder>> GetBySupplierAsync(Guid supplierId, CancellationToken cancellationToken = default)
    {
        return await _context.PurchaseOrders
            .Where(po => po.SupplierId == supplierId)
            .Include(po => po.Lines)
            .OrderByDescending(po => po.CreatedDate)
            .ToListAsync(cancellationToken);
    }

    public async Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        return await _context.SaveChangesAsync(cancellationToken);
    }
}
```

## Service Layer (Application Layer)

### Service Interface
```csharp
// ✅ CORRECTO: Service interface
public interface IPurchaseOrderService
{
    Task<PurchaseOrderDto?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default);
    Task<IEnumerable<PurchaseOrderDto>> GetAllAsync(CancellationToken cancellationToken = default);
    Task<PurchaseOrderDto> CreateAsync(CreatePurchaseOrderDto dto, CancellationToken cancellationToken = default);
    Task<PurchaseOrderDto> UpdateAsync(Guid id, UpdatePurchaseOrderDto dto, CancellationToken cancellationToken = default);
    Task<bool> DeleteAsync(Guid id, CancellationToken cancellationToken = default);
    Task<IEnumerable<PurchaseOrderDto>> GetBySupplierAsync(Guid supplierId, CancellationToken cancellationToken = default);
}
```

### Service Implementation
```csharp
// ✅ CORRECTO: Service implementation with logging and validation
public class PurchaseOrderService : IPurchaseOrderService
{
    private readonly IPurchaseOrderRepository _repository;
    private readonly IMapper _mapper;
    private readonly IValidator<CreatePurchaseOrderDto> _createValidator;
    private readonly IValidator<UpdatePurchaseOrderDto> _updateValidator;
    private readonly ILogger<PurchaseOrderService> _logger;

    public PurchaseOrderService(
        IPurchaseOrderRepository repository,
        IMapper mapper,
        IValidator<CreatePurchaseOrderDto> createValidator,
        IValidator<UpdatePurchaseOrderDto> updateValidator,
        ILogger<PurchaseOrderService> logger)
    {
        _repository = repository;
        _mapper = mapper;
        _createValidator = createValidator;
        _updateValidator = updateValidator;
        _logger = logger;
    }

    public async Task<PurchaseOrderDto?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default)
    {
        _logger.LogInformation("Retrieving purchase order with ID: {PurchaseOrderId}", id);
        
        var entity = await _repository.GetByIdAsync(id, cancellationToken);
        if (entity == null)
        {
            _logger.LogWarning("Purchase order with ID {PurchaseOrderId} not found", id);
            return null;
        }

        return _mapper.Map<PurchaseOrderDto>(entity);
    }

    public async Task<PurchaseOrderDto> CreateAsync(CreatePurchaseOrderDto dto, CancellationToken cancellationToken = default)
    {
        _logger.LogInformation("Creating new purchase order: {OrderNumber}", dto.OrderNumber);

        // ✅ Validation
        var validationResult = await _createValidator.ValidateAsync(dto, cancellationToken);
        if (!validationResult.IsValid)
        {
            throw new ValidationException(validationResult.Errors);
        }

        // ✅ Mapping and creation
        var entity = _mapper.Map<PurchaseOrder>(dto);
        entity.Id = Guid.NewGuid();
        entity.CreatedDate = DateTime.UtcNow;

        var createdEntity = await _repository.AddAsync(entity, cancellationToken);
        await _repository.SaveChangesAsync(cancellationToken);

        _logger.LogInformation("Purchase order created successfully with ID: {PurchaseOrderId}", createdEntity.Id);

        return _mapper.Map<PurchaseOrderDto>(createdEntity);
    }

    // ✅ Implementación similar para Update, Delete, etc.
}
```

## Controller Implementation

### Complete Controller Example
```csharp
[ApiController]
[Route("api/[controller]")]
[Produces("application/json")]
public class PurchaseOrdersController : ControllerBase
{
    private readonly IPurchaseOrderService _service;
    private readonly ILogger<PurchaseOrdersController> _logger;

    public PurchaseOrdersController(
        IPurchaseOrderService service,
        ILogger<PurchaseOrdersController> logger)
    {
        _service = service;
        _logger = logger;
    }

    /// <summary>
    /// Retrieves all purchase orders
    /// </summary>
    /// <response code="200">Returns the list of purchase orders</response>
    [HttpGet]
    [ProducesResponseType(typeof(IEnumerable<PurchaseOrderDto>), StatusCodes.Status200OK)]
    public async Task<ActionResult<IEnumerable<PurchaseOrderDto>>> GetPurchaseOrdersAsync(CancellationToken cancellationToken)
    {
        try
        {
            var purchaseOrders = await _service.GetAllAsync(cancellationToken);
            return Ok(purchaseOrders);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving purchase orders");
            return StatusCode(StatusCodes.Status500InternalServerError, "An error occurred while retrieving purchase orders");
        }
    }

    /// <summary>
    /// Retrieves a specific purchase order by ID
    /// </summary>
    /// <param name="id">The purchase order ID</param>
    /// <response code="200">Returns the purchase order</response>
    /// <response code="404">If the purchase order is not found</response>
    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof(PurchaseOrderDto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<ActionResult<PurchaseOrderDto>> GetPurchaseOrderByIdAsync(
        Guid id, 
        CancellationToken cancellationToken)
    {
        try
        {
            var purchaseOrder = await _service.GetByIdAsync(id, cancellationToken);
            
            if (purchaseOrder == null)
            {
                return NotFound($"Purchase order with ID {id} not found");
            }

            return Ok(purchaseOrder);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving purchase order with ID: {PurchaseOrderId}", id);
            return StatusCode(StatusCodes.Status500InternalServerError, "An error occurred while retrieving the purchase order");
        }
    }

    /// <summary>
    /// Creates a new purchase order
    /// </summary>
    /// <param name="dto">The purchase order creation data</param>
    /// <response code="201">Returns the newly created purchase order</response>
    /// <response code="400">If the request is invalid</response>
    [HttpPost]
    [ProducesResponseType(typeof(PurchaseOrderDto), StatusCodes.Status201Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<ActionResult<PurchaseOrderDto>> CreatePurchaseOrderAsync(
        [FromBody] CreatePurchaseOrderDto dto, 
        CancellationToken cancellationToken)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }

        try
        {
            var createdPurchaseOrder = await _service.CreateAsync(dto, cancellationToken);
            
            return CreatedAtAction(
                nameof(GetPurchaseOrderByIdAsync), 
                new { id = createdPurchaseOrder.Id }, 
                createdPurchaseOrder);
        }
        catch (ValidationException ex)
        {
            return BadRequest(ex.Errors.Select(e => e.ErrorMessage));
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error creating purchase order");
            return StatusCode(StatusCodes.Status500InternalServerError, "An error occurred while creating the purchase order");
        }
    }
}
```

## DbContext Configuration

### ApplicationDbContext
```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
    {
    }

    public DbSet<PurchaseOrder> PurchaseOrders { get; set; }
    public DbSet<PurchaseOrderLine> PurchaseOrderLines { get; set; }
    public DbSet<Supplier> Suppliers { get; set; }
    public DbSet<Product> Products { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // ✅ Aplicar configuraciones desde Data Annotations automáticamente
        base.OnModelCreating(modelBuilder);

        // ✅ Configuraciones adicionales si son necesarias
        modelBuilder.Entity<PurchaseOrder>(entity =>
        {
            entity.HasIndex(e => e.OrderNumber).IsUnique();
        });

        modelBuilder.Entity<PurchaseOrderLine>(entity =>
        {
            entity.HasOne(e => e.PurchaseOrder)
                  .WithMany(e => e.Lines)
                  .HasForeignKey(e => e.PurchaseOrderId)
                  .OnDelete(DeleteBehavior.Cascade);
        });
    }
}
```

## Dependency Injection Configuration

### Program.cs Configuration
```csharp
var builder = WebApplication.CreateBuilder(args);

// ✅ Database Configuration
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("DefaultConnection")));

// ✅ Repository Pattern
builder.Services.AddScoped<IPurchaseOrderRepository, PurchaseOrderRepository>();
builder.Services.AddScoped<ISupplierRepository, SupplierRepository>();

// ✅ Application Services
builder.Services.AddScoped<IPurchaseOrderService, PurchaseOrderService>();
builder.Services.AddScoped<ISupplierService, SupplierService>();

// ✅ AutoMapper
builder.Services.AddAutoMapper(typeof(PurchaseOrderMappingProfile));

// ✅ FluentValidation
builder.Services.AddValidatorsFromAssemblyContaining<CreatePurchaseOrderDtoValidator>();

// ✅ Controllers
builder.Services.AddControllers()
    .AddFluentValidation(fv => fv.RegisterValidatorsFromAssemblyContaining<CreatePurchaseOrderDtoValidator>());

// ✅ Swagger/OpenAPI
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo 
    { 
        Title = "Purchase Order API", 
        Version = "v1" 
    });
    
    // Include XML comments
    var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
    var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
    c.IncludeXmlComments(xmlPath);
});

// ✅ Serilog
builder.Host.UseSerilog((context, configuration) =>
    configuration.ReadFrom.Configuration(context.Configuration));

var app = builder.Build();

// ✅ Configure pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseRouting();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

## Error Handling

### Global Exception Handler
```csharp
public class GlobalExceptionMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<GlobalExceptionMiddleware> _logger;

    public GlobalExceptionMiddleware(RequestDelegate next, ILogger<GlobalExceptionMiddleware> logger)
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
            _logger.LogError(ex, "An unexpected error occurred");
            await HandleExceptionAsync(context, ex);
        }
    }

    private static async Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        context.Response.ContentType = "application/json";

        var response = exception switch
        {
            ValidationException validationEx => new
            {
                StatusCode = StatusCodes.Status400BadRequest,
                Message = "Validation failed",
                Details = validationEx.Errors.Select(e => e.ErrorMessage)
            },
            KeyNotFoundException => new
            {
                StatusCode = StatusCodes.Status404NotFound,
                Message = "Resource not found"
            },
            UnauthorizedAccessException => new
            {
                StatusCode = StatusCodes.Status401Unauthorized,
                Message = "Unauthorized access"
            },
            _ => new
            {
                StatusCode = StatusCodes.Status500InternalServerError,
                Message = "An internal server error occurred"
            }
        };

        context.Response.StatusCode = response.StatusCode;
        await context.Response.WriteAsync(JsonSerializer.Serialize(response));
    }
}
```

## FluentValidation Example

### Validator Implementation
```csharp
public class CreatePurchaseOrderDtoValidator : AbstractValidator<CreatePurchaseOrderDto>
{
    public CreatePurchaseOrderDtoValidator()
    {
        RuleFor(x => x.OrderNumber)
            .NotEmpty().WithMessage("Order number is required")
            .MaximumLength(50).WithMessage("Order number cannot exceed 50 characters");

        RuleFor(x => x.TotalAmount)
            .GreaterThan(0).WithMessage("Total amount must be greater than zero");

        RuleFor(x => x.SupplierId)
            .NotEmpty().WithMessage("Supplier ID is required");

        RuleFor(x => x.Lines)
            .NotEmpty().WithMessage("At least one purchase order line is required")
            .Must(lines => lines.All(line => line.Quantity > 0))
            .WithMessage("All lines must have quantity greater than zero");

        RuleFor(x => x.Notes)
            .MaximumLength(500).WithMessage("Notes cannot exceed 500 characters");
    }
}
```

## AutoMapper Profile

### Mapping Configuration
```csharp
public class PurchaseOrderMappingProfile : Profile
{
    public PurchaseOrderMappingProfile()
    {
        // ✅ Entity to DTO
        CreateMap<PurchaseOrder, PurchaseOrderDto>()
            .ForMember(dest => dest.Lines, opt => opt.MapFrom(src => src.Lines));

        // ✅ Create DTO to Entity
        CreateMap<CreatePurchaseOrderDto, PurchaseOrder>()
            .ForMember(dest => dest.Id, opt => opt.Ignore())
            .ForMember(dest => dest.CreatedDate, opt => opt.Ignore())
            .ForMember(dest => dest.Supplier, opt => opt.Ignore());

        // ✅ Update DTO to Entity
        CreateMap<UpdatePurchaseOrderDto, PurchaseOrder>()
            .ForMember(dest => dest.Id, opt => opt.Ignore())
            .ForMember(dest => dest.CreatedDate, opt => opt.Ignore())
            .ForMember(dest => dest.SupplierId, opt => opt.Ignore())
            .ForMember(dest => dest.Supplier, opt => opt.Ignore());

        // ✅ Related entities
        CreateMap<PurchaseOrderLine, PurchaseOrderLineDto>();
        CreateMap<CreatePurchaseOrderLineDto, PurchaseOrderLine>();
        CreateMap<Supplier, SupplierDto>();
    }
}
```
