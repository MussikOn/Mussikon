# 🎵 **SISTEMA DE SOLICITUDES DE MÚSICOS - BACKEND**

## 🎯 **Visión General**

El **Sistema de Solicitudes de Músicos** es el **corazón de MussikOn**. Permite a los organizadores de eventos crear solicitudes para contratar músicos, y a los músicos profesionales encontrar y aceptar estas oportunidades de trabajo.

### **🎵 Propósito Principal**
Resolver la dificultad para encontrar y contratar músicos profesionales para eventos, facilitando la conexión directa entre talento musical y oportunidades laborales.

---

## 🏗️ **Arquitectura del Sistema**

### **📁 Estructura de Entidades**
```
┌─────────────────────────────────────────────────────────────┐
│                    DOMAIN LAYER                            │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              MusicianRequest                        │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ │   │
│  │  │     Id      │  │   Status    │  │   Created   │ │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘ │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ │   │
│  │  │ EventType   │  │   Date      │  │   Time      │ │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘ │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ │   │
│  │  │ Location    │  │ Instrument  │  │   Budget    │ │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  INFRASTRUCTURE LAYER                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ Repository  │  │   Service   │  │   Controller       │ │
│  │             │  │             │  │                     │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### **🔄 Flujo de Datos**
```
1. Organizador crea solicitud → 2. Sistema valida datos → 3. Persiste en BD → 4. Notifica músicos → 5. Músicos aplican → 6. Organizador selecciona → 7. Estado actualizado
```

---

## 🏗️ **Entidades del Dominio**

### **🎵 MusicianRequest Entity**
```csharp
public class MusicianRequest : BaseEntity
{
    // Propiedades básicas
    public string EventType { get; set; }           // Boda, Cumpleaños, Corporativo, etc.
    public DateTime Date { get; set; }              // Fecha del evento
    public TimeSpan Time { get; set; }              // Hora del evento
    public string Location { get; set; }            // Ubicación del evento
    public string Instrument { get; set; }          // Instrumento requerido
    public decimal Budget { get; set; }             // Presupuesto disponible
    public string Comments { get; set; }            // Comentarios adicionales
    
    // Estados del ciclo de vida
    public RequestStatus Status { get; set; }       // Pendiente, En Revisión, Asignada, etc.
    public Guid? AssignedMusicianId { get; set; }  // Músico asignado (si aplica)
    
    // Relaciones
    public Guid UserId { get; set; }                // Organizador que creó la solicitud
    public virtual User User { get; set; }          // Navegación al usuario
    public virtual Musician AssignedMusician { get; set; }  // Navegación al músico
    
    // Auditoría
    public DateTime CreatedAt { get; set; }
    public DateTime UpdatedAt { get; set; }
    public string CreatedBy { get; set; }
    public string UpdatedBy { get; set; }
}
```

### **🎵 RequestStatus Enum**
```csharp
public enum RequestStatus
{
    Pendiente,      // Solicitud creada, esperando aplicaciones
    EnRevisión,     // Organizador evaluando candidatos
    Asignada,       // Músico seleccionado y confirmado
    Completada,     // Evento realizado exitosamente
    Cancelada,      // Solicitud cancelada por organizador
    Expirada        // Solicitud expirada por tiempo
}
```

### **🎵 EventType Enum**
```csharp
public enum EventType
{
    Boda,           // Ceremonias de boda
    Cumpleaños,     // Celebraciones de cumpleaños
    Corporativo,    // Eventos empresariales
    Concierto,      // Presentaciones musicales
    Festival,       // Festivales musicales
    Culto,          // Servicios religiosos
    Graduación,     // Ceremonias de graduación
    Aniversario,    // Celebraciones de aniversario
    Otro            // Otros tipos de eventos
}
```

---

## 🏗️ **DTOs y ViewModels**

### **📝 CreateMusicianRequestDto**
```csharp
public class CreateMusicianRequestDto
{
    [Required]
    [StringLength(100)]
    public string EventType { get; set; }
    
    [Required]
    public DateTime Date { get; set; }
    
    [Required]
    public TimeSpan Time { get; set; }
    
    [Required]
    [StringLength(200)]
    public string Location { get; set; }
    
    [Required]
    [StringLength(100)]
    public string Instrument { get; set; }
    
    [Required]
    [Range(0, 10000)]
    public decimal Budget { get; set; }
    
    [StringLength(500)]
    public string Comments { get; set; }
}
```

### **📝 MusicianRequestDto**
```csharp
public class MusicianRequestDto
{
    public Guid Id { get; set; }
    public string EventType { get; set; }
    public DateTime Date { get; set; }
    public TimeSpan Time { get; set; }
    public string Location { get; set; }
    public string Instrument { get; set; }
    public decimal Budget { get; set; }
    public string Comments { get; set; }
    public RequestStatus Status { get; set; }
    public Guid? AssignedMusicianId { get; set; }
    public string OrganizerName { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime UpdatedAt { get; set; }
}
```

### **📝 UpdateMusicianRequestDto**
```csharp
public class UpdateMusicianRequestDto
{
    [StringLength(100)]
    public string EventType { get; set; }
    
    public DateTime? Date { get; set; }
    public TimeSpan? Time { get; set; }
    
    [StringLength(200)]
    public string Location { get; set; }
    
    [StringLength(100)]
    public string Instrument { get; set; }
    
    [Range(0, 10000)]
    public decimal? Budget { get; set; }
    
    [StringLength(500)]
    public string Comments { get; set; }
}
```

---

## 🏗️ **Validaciones con FluentValidation**

### **✅ MusicianRequestValidator**
```csharp
public class CreateMusicianRequestValidator : AbstractValidator<CreateMusicianRequestDto>
{
    public CreateMusicianRequestValidator()
    {
        RuleFor(x => x.EventType)
            .NotEmpty().WithMessage("El tipo de evento es requerido")
            .MaximumLength(100).WithMessage("El tipo de evento no puede exceder 100 caracteres");
        
        RuleFor(x => x.Date)
            .NotEmpty().WithMessage("La fecha del evento es requerida")
            .GreaterThan(DateTime.Today).WithMessage("La fecha del evento debe ser futura");
        
        RuleFor(x => x.Time)
            .NotEmpty().WithMessage("La hora del evento es requerida");
        
        RuleFor(x => x.Location)
            .NotEmpty().WithMessage("La ubicación del evento es requerida")
            .MaximumLength(200).WithMessage("La ubicación no puede exceder 200 caracteres");
        
        RuleFor(x => x.Instrument)
            .NotEmpty().WithMessage("El instrumento es requerido")
            .MaximumLength(100).WithMessage("El instrumento no puede exceder 100 caracteres");
        
        RuleFor(x => x.Budget)
            .GreaterThan(0).WithMessage("El presupuesto debe ser mayor a 0")
            .LessThanOrEqualTo(10000).WithMessage("El presupuesto no puede exceder 10,000");
        
        RuleFor(x => x.Comments)
            .MaximumLength(500).WithMessage("Los comentarios no pueden exceder 500 caracteres");
    }
}
```

---

## 🏗️ **Servicios de Negocio**

### **⚙️ IMusicianRequestService Interface**
```csharp
public interface IMusicianRequestService
{
    // Operaciones CRUD básicas
    Task<MusicianRequestDto> CreateAsync(CreateMusicianRequestDto request, Guid userId);
    Task<MusicianRequestDto> GetByIdAsync(Guid id);
    Task<IEnumerable<MusicianRequestDto>> GetAllAsync();
    Task<MusicianRequestDto> UpdateAsync(Guid id, UpdateMusicianRequestDto request, Guid userId);
    Task DeleteAsync(Guid id, Guid userId);
    
    // Operaciones específicas del negocio
    Task<IEnumerable<MusicianRequestDto>> GetAvailableForMusiciansAsync();
    Task<IEnumerable<MusicianRequestDto>> GetByUserIdAsync(Guid userId);
    Task<MusicianRequestDto> AcceptByMusicianAsync(Guid requestId, Guid musicianId);
    Task<MusicianRequestDto> UpdateStatusAsync(Guid id, RequestStatus status, Guid userId);
    
    // Operaciones de búsqueda y filtrado
    Task<IEnumerable<MusicianRequestDto>> SearchAsync(MusicianRequestSearchDto searchDto);
    Task<IEnumerable<MusicianRequestDto>> GetByStatusAsync(RequestStatus status);
    Task<IEnumerable<MusicianRequestDto>> GetByEventTypeAsync(string eventType);
}
```

### **⚙️ MusicianRequestService Implementation**
```csharp
public class MusicianRequestService : IMusicianRequestService
{
    private readonly IMusicianRequestRepository _repository;
    private readonly IMapper _mapper;
    private readonly ILogger<MusicianRequestService> _logger;
    private readonly IHubContext<NotificationHub> _hubContext;
    private readonly IUnitOfWork _unitOfWork;

    public MusicianRequestService(
        IMusicianRequestRepository repository,
        IMapper mapper,
        ILogger<MusicianRequestService> logger,
        IHubContext<NotificationHub> hubContext,
        IUnitOfWork unitOfWork)
    {
        _repository = repository;
        _mapper = mapper;
        _logger = logger;
        _hubContext = hubContext;
        _unitOfWork = unitOfWork;
    }

    public async Task<MusicianRequestDto> CreateAsync(CreateMusicianRequestDto request, Guid userId)
    {
        try
        {
            // Validar que el usuario tenga permisos para crear solicitudes
            if (!await HasPermissionToCreateRequestsAsync(userId))
            {
                throw new UnauthorizedAccessException("No tienes permisos para crear solicitudes");
            }

            // Crear la entidad
            var musicianRequest = new MusicianRequest
            {
                EventType = request.EventType,
                Date = request.Date,
                Time = request.Time,
                Location = request.Location,
                Instrument = request.Instrument,
                Budget = request.Budget,
                Comments = request.Comments,
                Status = RequestStatus.Pendiente,
                UserId = userId,
                CreatedAt = DateTime.UtcNow,
                UpdatedAt = DateTime.UtcNow,
                CreatedBy = userId.ToString(),
                UpdatedBy = userId.ToString()
            };

            // Persistir en base de datos
            var createdRequest = await _repository.AddAsync(musicianRequest);
            await _unitOfWork.SaveChangesAsync();

            // Notificar a músicos disponibles vía SignalR
            await NotifyAvailableMusiciansAsync(createdRequest);

            // Log de la operación
            _logger.LogInformation("Musician request created successfully. ID: {RequestId}, User: {UserId}", 
                createdRequest.Id, userId);

            return _mapper.Map<MusicianRequestDto>(createdRequest);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error creating musician request for user {UserId}", userId);
            throw;
        }
    }

    public async Task<MusicianRequestDto> AcceptByMusicianAsync(Guid requestId, Guid musicianId)
    {
        try
        {
            // Obtener la solicitud
            var request = await _repository.GetByIdAsync(requestId);
            if (request == null)
            {
                throw new NotFoundException($"Solicitud con ID {requestId} no encontrada");
            }

            // Validar que la solicitud esté disponible
            if (request.Status != RequestStatus.Pendiente)
            {
                throw new InvalidOperationException("La solicitud no está disponible para aceptar");
            }

            // Validar que el músico no tenga conflictos de calendario
            if (await HasCalendarConflictAsync(musicianId, request.Date, request.Time))
            {
                throw new InvalidOperationException("El músico tiene un conflicto de calendario");
            }

            // Asignar el músico y cambiar estado
            request.AssignedMusicianId = musicianId;
            request.Status = RequestStatus.Asignada;
            request.UpdatedAt = DateTime.UtcNow;
            request.UpdatedBy = musicianId.ToString();

            // Persistir cambios
            await _repository.UpdateAsync(request);
            await _unitOfWork.SaveChangesAsync();

            // Notificar al organizador vía SignalR
            await NotifyOrganizerAsync(request, musicianId);

            // Log de la operación
            _logger.LogInformation("Musician request {RequestId} accepted by musician {MusicianId}", 
                requestId, musicianId);

            return _mapper.Map<MusicianRequestDto>(request);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error accepting musician request {RequestId} by musician {MusicianId}", 
                requestId, musicianId);
            throw;
        }
    }

    private async Task NotifyAvailableMusiciansAsync(MusicianRequest request)
    {
        try
        {
            await _hubContext.Clients.All.SendAsync("NewMusicianRequest", new
            {
                RequestId = request.Id,
                EventType = request.EventType,
                Date = request.Date,
                Location = request.Location,
                Instrument = request.Instrument,
                Budget = request.Budget
            });
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error notifying available musicians about new request {RequestId}", request.Id);
        }
    }

    private async Task NotifyOrganizerAsync(MusicianRequest request, Guid musicianId)
    {
        try
        {
            await _hubContext.Clients.User(request.UserId.ToString()).SendAsync("MusicianRequestAccepted", new
            {
                RequestId = request.Id,
                MusicianId = musicianId,
                Status = request.Status
            });
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error notifying organizer about accepted request {RequestId}", request.Id);
        }
    }
}
```

---

## 🏗️ **Repositorios**

### **🗄️ IMusicianRequestRepository Interface**
```csharp
public interface IMusicianRequestRepository
{
    // Operaciones básicas
    Task<MusicianRequest> GetByIdAsync(Guid id);
    Task<IEnumerable<MusicianRequest>> GetAllAsync();
    Task<MusicianRequest> AddAsync(MusicianRequest entity);
    Task UpdateAsync(MusicianRequest entity);
    Task DeleteAsync(Guid id);
    
    // Operaciones específicas del negocio
    Task<IEnumerable<MusicianRequest>> GetAvailableForMusiciansAsync();
    Task<IEnumerable<MusicianRequest>> GetByUserIdAsync(Guid userId);
    Task<IEnumerable<MusicianRequest>> GetByStatusAsync(RequestStatus status);
    Task<IEnumerable<MusicianRequest>> GetByEventTypeAsync(string eventType);
    Task<IEnumerable<MusicianRequest>> SearchAsync(MusicianRequestSearchDto searchDto);
    
    // Operaciones de conteo y estadísticas
    Task<int> GetCountByStatusAsync(RequestStatus status);
    Task<int> GetCountByUserIdAsync(Guid userId);
    Task<decimal> GetTotalBudgetByUserIdAsync(Guid userId);
}
```

### **🗄️ MusicianRequestRepository Implementation**
```csharp
public class MusicianRequestRepository : IMusicianRequestRepository
{
    private readonly ApplicationDbContext _context;

    public MusicianRequestRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<MusicianRequest> GetByIdAsync(Guid id)
    {
        return await _context.MusicianRequests
            .Include(r => r.User)
            .Include(r => r.AssignedMusician)
            .FirstOrDefaultAsync(r => r.Id == id);
    }

    public async Task<IEnumerable<MusicianRequest>> GetAvailableForMusiciansAsync()
    {
        return await _context.MusicianRequests
            .Include(r => r.User)
            .Where(r => r.Status == RequestStatus.Pendiente && r.Date >= DateTime.Today)
            .OrderBy(r => r.Date)
            .ThenBy(r => r.Time)
            .ToListAsync();
    }

    public async Task<IEnumerable<MusicianRequest>> GetByUserIdAsync(Guid userId)
    {
        return await _context.MusicianRequests
            .Include(r => r.AssignedMusician)
            .Where(r => r.UserId == userId)
            .OrderByDescending(r => r.CreatedAt)
            .ToListAsync();
    }

    public async Task<IEnumerable<MusicianRequest>> SearchAsync(MusicianRequestSearchDto searchDto)
    {
        var query = _context.MusicianRequests
            .Include(r => r.User)
            .Include(r => r.AssignedMusician)
            .AsQueryable();

        // Aplicar filtros
        if (!string.IsNullOrEmpty(searchDto.EventType))
        {
            query = query.Where(r => r.EventType.Contains(searchDto.EventType));
        }

        if (!string.IsNullOrEmpty(searchDto.Instrument))
        {
            query = query.Where(r => r.Instrument.Contains(searchDto.Instrument));
        }

        if (!string.IsNullOrEmpty(searchDto.Location))
        {
            query = query.Where(r => r.Location.Contains(searchDto.Location));
        }

        if (searchDto.MinBudget.HasValue)
        {
            query = query.Where(r => r.Budget >= searchDto.MinBudget.Value);
        }

        if (searchDto.MaxBudget.HasValue)
        {
            query = query.Where(r => r.Budget <= searchDto.MaxBudget.Value);
        }

        if (searchDto.DateFrom.HasValue)
        {
            query = query.Where(r => r.Date >= searchDto.DateFrom.Value);
        }

        if (searchDto.DateTo.HasValue)
        {
            query = query.Where(r => r.Date <= searchDto.DateTo.Value);
        }

        if (searchDto.Status.HasValue)
        {
            query = query.Where(r => r.Status == searchDto.Status.Value);
        }

        // Ordenar y paginar
        query = query.OrderByDescending(r => r.CreatedAt);

        if (searchDto.Page.HasValue && searchDto.PageSize.HasValue)
        {
            query = query.Skip((searchDto.Page.Value - 1) * searchDto.PageSize.Value)
                        .Take(searchDto.PageSize.Value);
        }

        return await query.ToListAsync();
    }
}
```

---

## 🏗️ **Controladores**

### **🎮 MusicianRequestController**
```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize]
public class MusicianRequestController : ControllerBase
{
    private readonly IMusicianRequestService _musicianRequestService;
    private readonly ILogger<MusicianRequestController> _logger;

    public MusicianRequestController(
        IMusicianRequestService musicianRequestService,
        ILogger<MusicianRequestController> logger)
    {
        _musicianRequestService = musicianRequestService;
        _logger = logger;
    }

    /// <summary>
    /// Crear una nueva solicitud de músico
    /// </summary>
    [HttpPost]
    [Authorize(Roles = "Organizador,Admin,SuperAdmin")]
    public async Task<ActionResult<MusicianRequestDto>> Create(CreateMusicianRequestDto request)
    {
        try
        {
            var userId = User.GetUserId();
            var result = await _musicianRequestService.CreateAsync(request, userId);
            
            return CreatedAtAction(nameof(GetById), new { id = result.Id }, result);
        }
        catch (UnauthorizedAccessException ex)
        {
            return Forbid();
        }
        catch (ValidationException ex)
        {
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error creating musician request");
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Obtener una solicitud por ID
    /// </summary>
    [HttpGet("{id}")]
    public async Task<ActionResult<MusicianRequestDto>> GetById(Guid id)
    {
        try
        {
            var result = await _musicianRequestService.GetByIdAsync(id);
            if (result == null)
            {
                return NotFound();
            }
            
            return Ok(result);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting musician request {Id}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Obtener solicitudes disponibles para músicos
    /// </summary>
    [HttpGet("available")]
    [AllowAnonymous]
    public async Task<ActionResult<IEnumerable<MusicianRequestDto>>> GetAvailable()
    {
        try
        {
            var result = await _musicianRequestService.GetAvailableForMusiciansAsync();
            return Ok(result);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting available musician requests");
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Aceptar una solicitud como músico
    /// </summary>
    [HttpPost("{id}/accept")]
    [Authorize(Roles = "Musico,Musician,Admin,SuperAdmin")]
    public async Task<ActionResult<MusicianRequestDto>> Accept(Guid id)
    {
        try
        {
            var musicianId = User.GetUserId();
            var result = await _musicianRequestService.AcceptByMusicianAsync(id, musicianId);
            
            return Ok(result);
        }
        catch (NotFoundException ex)
        {
            return NotFound(ex.Message);
        }
        catch (InvalidOperationException ex)
        {
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error accepting musician request {Id}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Actualizar el estado de una solicitud
    /// </summary>
    [HttpPut("{id}/status")]
    [Authorize(Roles = "Organizador,Admin,SuperAdmin")]
    public async Task<ActionResult<MusicianRequestDto>> UpdateStatus(Guid id, [FromBody] RequestStatus status)
    {
        try
        {
            var userId = User.GetUserId();
            var result = await _musicianRequestService.UpdateStatusAsync(id, status, userId);
            
            return Ok(result);
        }
        catch (UnauthorizedAccessException ex)
        {
            return Forbid();
        }
        catch (NotFoundException ex)
        {
            return NotFound(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error updating status for musician request {Id}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Buscar solicitudes con filtros
    /// </summary>
    [HttpGet("search")]
    public async Task<ActionResult<IEnumerable<MusicianRequestDto>>> Search([FromQuery] MusicianRequestSearchDto searchDto)
    {
        try
        {
            var result = await _musicianRequestService.SearchAsync(searchDto);
            return Ok(result);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error searching musician requests");
            return StatusCode(500, "Error interno del servidor");
        }
    }
}
```

---

## 🧪 **Testing del Sistema**

### **📝 Tests Unitarios del Servicio**
```csharp
public class MusicianRequestServiceTests
{
    private readonly Mock<IMusicianRequestRepository> _mockRepository;
    private readonly Mock<IMapper> _mockMapper;
    private readonly Mock<ILogger<MusicianRequestService>> _mockLogger;
    private readonly Mock<IHubContext<NotificationHub>> _mockHubContext;
    private readonly Mock<IUnitOfWork> _mockUnitOfWork;
    private readonly MusicianRequestService _service;

    public MusicianRequestServiceTests()
    {
        _mockRepository = new Mock<IMusicianRequestRepository>();
        _mockMapper = new Mock<IMapper>();
        _mockLogger = new Mock<ILogger<MusicianRequestService>>();
        _mockHubContext = new Mock<IHubContext<NotificationHub>>();
        _mockUnitOfWork = new Mock<IUnitOfWork>();
        
        _service = new MusicianRequestService(
            _mockRepository.Object,
            _mockMapper.Object,
            _mockLogger.Object,
            _mockHubContext.Object,
            _mockUnitOfWork.Object);
    }

    [Fact]
    public async Task CreateAsync_WithValidData_ShouldCreateRequest()
    {
        // Arrange
        var request = new CreateMusicianRequestDto
        {
            EventType = "Boda",
            Date = DateTime.Now.AddDays(30),
            Time = new TimeSpan(18, 0, 0),
            Location = "Madrid, España",
            Instrument = "Piano",
            Budget = 500,
            Comments = "Evento de boda elegante"
        };

        var userId = Guid.NewGuid();
        var expectedEntity = new MusicianRequest { Id = Guid.NewGuid() };
        var expectedDto = new MusicianRequestDto { Id = expectedEntity.Id };

        _mockRepository.Setup(r => r.AddAsync(It.IsAny<MusicianRequest>()))
            .ReturnsAsync(expectedEntity);
        _mockMapper.Setup(m => m.Map<MusicianRequestDto>(expectedEntity))
            .Returns(expectedDto);

        // Act
        var result = await _service.CreateAsync(request, userId);

        // Assert
        Assert.NotNull(result);
        Assert.Equal(expectedDto.Id, result.Id);
        _mockRepository.Verify(r => r.AddAsync(It.IsAny<MusicianRequest>()), Times.Once);
        _mockUnitOfWork.Verify(u => u.SaveChangesAsync(), Times.Once);
    }

    [Fact]
    public async Task AcceptByMusicianAsync_WithValidRequest_ShouldAcceptRequest()
    {
        // Arrange
        var requestId = Guid.NewGuid();
        var musicianId = Guid.NewGuid();
        var request = new MusicianRequest
        {
            Id = requestId,
            Status = RequestStatus.Pendiente,
            Date = DateTime.Now.AddDays(30),
            Time = new TimeSpan(18, 0, 0)
        };

        _mockRepository.Setup(r => r.GetByIdAsync(requestId))
            .ReturnsAsync(request);

        // Act
        var result = await _service.AcceptByMusicianAsync(requestId, musicianId);

        // Assert
        Assert.NotNull(result);
        Assert.Equal(RequestStatus.Asignada, result.Status);
        Assert.Equal(musicianId, result.AssignedMusicianId);
        _mockRepository.Verify(r => r.UpdateAsync(It.IsAny<MusicianRequest>()), Times.Once);
        _mockUnitOfWork.Verify(u => u.SaveChangesAsync(), Times.Once);
    }
}
```

---

## 🚀 **Configuración de SignalR**

### **🔌 NotificationHub**
```csharp
public class NotificationHub : Hub
{
    private readonly ILogger<NotificationHub> _logger;

    public NotificationHub(ILogger<NotificationHub> logger)
    {
        _logger = logger;
    }

    public override async Task OnConnectedAsync()
    {
        var userId = Context.User?.GetUserId();
        if (userId.HasValue)
        {
            await Groups.AddToGroupAsync(Context.ConnectionId, userId.Value.ToString());
            _logger.LogInformation("User {UserId} connected to hub", userId.Value);
        }
        
        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception exception)
    {
        var userId = Context.User?.GetUserId();
        if (userId.HasValue)
        {
            await Groups.RemoveFromGroupAsync(Context.ConnectionId, userId.Value.ToString());
            _logger.LogInformation("User {UserId} disconnected from hub", userId.Value);
        }
        
        await base.OnDisconnectedAsync(exception);
    }

    public async Task JoinMusicianGroup()
    {
        var userId = Context.User?.GetUserId();
        if (userId.HasValue)
        {
            await Groups.AddToGroupAsync(Context.ConnectionId, "musicians");
            _logger.LogInformation("User {UserId} joined musicians group", userId.Value);
        }
    }

    public async Task LeaveMusicianGroup()
    {
        var userId = Context.User?.GetUserId();
        if (userId.HasValue)
        {
            await Groups.RemoveFromGroupAsync(Context.ConnectionId, "musicians");
            _logger.LogInformation("User {UserId} left musicians group", userId.Value);
        }
    }
}
```

---

## 📊 **Métricas y Monitoreo**

### **📈 Health Checks Específicos**
```csharp
public class MusicianRequestHealthCheck : IHealthCheck
{
    private readonly IMusicianRequestRepository _repository;

    public MusicianRequestHealthCheck(IMusicianRequestRepository repository)
    {
        _repository = repository;
    }

    public async Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, CancellationToken cancellationToken = default)
    {
        try
        {
            var pendingCount = await _repository.GetCountByStatusAsync(RequestStatus.Pendiente);
            var assignedCount = await _repository.GetCountByStatusAsync(RequestStatus.Asignada);
            
            var data = new Dictionary<string, object>
            {
                ["PendingRequests"] = pendingCount,
                ["AssignedRequests"] = assignedCount,
                ["TotalActive"] = pendingCount + assignedCount
            };

            return HealthCheckResult.Healthy("Musician request system is healthy", data);
        }
        catch (Exception ex)
        {
            return HealthCheckResult.Unhealthy("Musician request system is unhealthy", ex);
        }
    }
}
```

---

## 🔒 **Seguridad y Validaciones**

### **🛡️ Middleware de Autorización**
```csharp
public class MusicianRequestAuthorizationHandler : AuthorizationHandler<MusicianRequestRequirement, MusicianRequest>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        MusicianRequestRequirement requirement,
        MusicianRequest resource)
    {
        var user = context.User;
        var userId = user.GetUserId();

        // SuperAdmin y Admin pueden acceder a todo
        if (user.IsInRole("SuperAdmin") || user.IsInRole("Admin"))
        {
            context.Succeed(requirement);
            return Task.CompletedTask;
        }

        // Organizador puede acceder a sus propias solicitudes
        if (user.IsInRole("Organizador") && resource.UserId == userId)
        {
            context.Succeed(requirement);
            return Task.CompletedTask;
        }

        // Músicos pueden ver solicitudes disponibles
        if (user.IsInRole("Musico") || user.IsInRole("Musician"))
        {
            if (requirement.Operation == MusicianRequestOperation.Read)
            {
                context.Succeed(requirement);
                return Task.CompletedTask;
            }
        }

        context.Fail();
        return Task.CompletedTask;
    }
}
```

---

## 📚 **Referencias y Recursos**

### **🔗 Enlaces Oficiales**
- [SignalR Documentation](https://docs.microsoft.com/en-us/aspnet/core/signalr/)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [FluentValidation](https://fluentvalidation.net/)
- [AutoMapper](https://automapper.org/)

### **📖 Patrones de Diseño**
- Repository Pattern
- Service Layer Pattern
- Unit of Work Pattern
- CQRS Pattern (para futuras implementaciones)

---

## 🎯 **Próximos Pasos**

1. **Implementar sistema de notificaciones** con SignalR
2. **Crear algoritmos de matching** avanzados
3. **Implementar sistema de reviews** y calificaciones
4. **Agregar analytics** y reportes
5. **Optimizar performance** con caching

---

*Última actualización: Diciembre 2024*
*Versión: v1.0.0*
*Enfoque: Sistema de Solicitudes de Músicos - Core del MVP*
