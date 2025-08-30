# 🎯 **SISTEMA DE SOLICITUDES BACKEND - MUSSIKON**

## 📋 **Índice del Sistema de Solicitudes Backend**

### **🏗️ Arquitectura del Sistema**
- [Controladores](./SOLICITUDES_BACKEND.md#controladores) - API endpoints
- [Servicios](./SOLICITUDES_BACKEND.md#servicios) - Lógica de negocio
- [Modelos y DTOs](./SOLICITUDES_BACKEND.md#modelos-y-dtos) - Estructura de datos

### **🔧 Implementación Técnica**
- [Validaciones](./SOLICITUDES_BACKEND.md#validaciones) - FluentValidation
- [Mapeo](./SOLICITUDES_BACKEND.md#mapeo) - AutoMapper
- [Testing](./SOLICITUDES_BACKEND.md#testing) - Estrategia de testing

---

## 🎯 **Propósito del Sistema de Solicitudes Backend**

### **Objetivo Principal**
Implementar la funcionalidad completa del sistema de solicitudes de músicos en el backend .NET Core, proporcionando APIs robustas, seguras y escalables para la gestión de solicitudes, aplicaciones y comunicación.

### **Funcionalidades Clave**
- **CRUD de Solicitudes**: Crear, leer, actualizar y eliminar solicitudes
- **Sistema de Aplicaciones**: Gestión de postulaciones de músicos
- **Búsqueda Avanzada**: Filtros, paginación y ordenamiento
- **Notificaciones**: Sistema de alertas en tiempo real
- **Validaciones**: Reglas de negocio y validaciones de datos

---

## 🏗️ **Controladores**

### **🎮 Controlador de Solicitudes**
```csharp
// Controllers/MusicianRequestsController.cs
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using MussikOn.Application.DTOs;
using MussikOn.Application.Services;
using MussikOn.Domain.Entities;
using System.Security.Claims;

[ApiController]
[Route("api/[controller]")]
[Authorize]
public class MusicianRequestsController : ControllerBase
{
    private readonly IMusicianRequestService _requestService;
    private readonly ILogger<MusicianRequestsController> _logger;

    public MusicianRequestsController(
        IMusicianRequestService requestService,
        ILogger<MusicianRequestsController> logger)
    {
        _requestService = requestService;
        _logger = logger;
    }

    /// <summary>
    /// Obtiene todas las solicitudes con filtros y paginación
    /// </summary>
    [HttpGet]
    [AllowAnonymous]
    public async Task<ActionResult<PaginatedResult<MusicianRequestDto>>> GetRequests(
        [FromQuery] RequestSearchDto searchDto)
    {
        try
        {
            var result = await _requestService.SearchRequestsAsync(searchDto);
            return Ok(result);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al buscar solicitudes");
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Obtiene una solicitud específica por ID
    /// </summary>
    [HttpGet("{id:guid}")]
    [AllowAnonymous]
    public async Task<ActionResult<MusicianRequestDto>> GetRequest(Guid id)
    {
        try
        {
            var request = await _requestService.GetRequestByIdAsync(id);
            if (request == null)
                return NotFound();

            return Ok(request);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al obtener solicitud {RequestId}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Crea una nueva solicitud
    /// </summary>
    [HttpPost]
    public async Task<ActionResult<MusicianRequestDto>> CreateRequest(
        [FromBody] CreateMusicianRequestDto createDto)
    {
        try
        {
            var userId = GetCurrentUserId();
            var request = await _requestService.CreateRequestAsync(createDto, userId);
            
            return CreatedAtAction(
                nameof(GetRequest), 
                new { id = request.Id }, 
                request);
        }
        catch (ValidationException ex)
        {
            return BadRequest(ex.Message);
        }
        catch (UnauthorizedAccessException)
        {
            return Forbid();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al crear solicitud");
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Actualiza una solicitud existente
    /// </summary>
    [HttpPut("{id:guid}")]
    public async Task<ActionResult<MusicianRequestDto>> UpdateRequest(
        Guid id, 
        [FromBody] UpdateMusicianRequestDto updateDto)
    {
        try
        {
            var userId = GetCurrentUserId();
            var request = await _requestService.UpdateRequestAsync(id, updateDto, userId);
            
            return Ok(request);
        }
        catch (ValidationException ex)
        {
            return BadRequest(ex.Message);
        }
        catch (UnauthorizedAccessException)
        {
            return Forbid();
        }
        catch (NotFoundException)
        {
            return NotFound();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al actualizar solicitud {RequestId}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Elimina una solicitud
    /// </summary>
    [HttpDelete("{id:guid}")]
    public async Task<ActionResult> DeleteRequest(Guid id)
    {
        try
        {
            var userId = GetCurrentUserId();
            await _requestService.DeleteRequestAsync(id, userId);
            
            return NoContent();
        }
        catch (UnauthorizedAccessException)
        {
            return Forbid();
        }
        catch (NotFoundException)
        {
            return NotFound();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al eliminar solicitud {RequestId}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Marca una solicitud como destacada
    /// </summary>
    [HttpPatch("{id:guid}/feature")]
    [Authorize(Roles = "Admin,Moderator")]
    public async Task<ActionResult<MusicianRequestDto>> FeatureRequest(Guid id)
    {
        try
        {
            var request = await _requestService.ToggleFeaturedAsync(id);
            return Ok(request);
        }
        catch (NotFoundException)
        {
            return NotFound();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al marcar solicitud como destacada {RequestId}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Obtiene las solicitudes del usuario autenticado
    /// </summary>
    [HttpGet("my-requests")]
    public async Task<ActionResult<IEnumerable<MusicianRequestDto>>> GetMyRequests()
    {
        try
        {
            var userId = GetCurrentUserId();
            var requests = await _requestService.GetRequestsByUserAsync(userId);
            
            return Ok(requests);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al obtener solicitudes del usuario {UserId}", GetCurrentUserId());
            return StatusCode(500, "Error interno del servidor");
        }
    }

    private Guid GetCurrentUserId()
    {
        var userIdClaim = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (Guid.TryParse(userIdClaim, out var userId))
            return userId;
        
        throw new UnauthorizedAccessException("Usuario no autenticado");
    }
}
```

### **📝 Controlador de Aplicaciones**
```csharp
// Controllers/RequestApplicationsController.cs
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using MussikOn.Application.DTOs;
using MussikOn.Application.Services;

[ApiController]
[Route("api/requests/{requestId:guid}/applications")]
[Authorize]
public class RequestApplicationsController : ControllerBase
{
    private readonly IRequestApplicationService _applicationService;
    private readonly ILogger<RequestApplicationsController> _logger;

    public RequestApplicationsController(
        IRequestApplicationService applicationService,
        ILogger<RequestApplicationsController> logger)
    {
        _applicationService = applicationService;
        _logger = logger;
    }

    /// <summary>
    /// Obtiene todas las aplicaciones de una solicitud
    /// </summary>
    [HttpGet]
    public async Task<ActionResult<IEnumerable<RequestApplicationDto>>> GetApplications(
        Guid requestId)
    {
        try
        {
            var userId = GetCurrentUserId();
            var applications = await _applicationService.GetApplicationsByRequestAsync(requestId, userId);
            
            return Ok(applications);
        }
        catch (UnauthorizedAccessException)
        {
            return Forbid();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al obtener aplicaciones de solicitud {RequestId}", requestId);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Crea una nueva aplicación
    /// </summary>
    [HttpPost]
    public async Task<ActionResult<RequestApplicationDto>> CreateApplication(
        Guid requestId, 
        [FromBody] CreateApplicationDto createDto)
    {
        try
        {
            var userId = GetCurrentUserId();
            var application = await _applicationService.CreateApplicationAsync(requestId, createDto, userId);
            
            return CreatedAtAction(
                nameof(GetApplication), 
                new { requestId, id = application.Id }, 
                application);
        }
        catch (ValidationException ex)
        {
            return BadRequest(ex.Message);
        }
        catch (NotFoundException)
        {
            return NotFound();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al crear aplicación para solicitud {RequestId}", requestId);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Obtiene una aplicación específica
    /// </summary>
    [HttpGet("{id:guid}")]
    public async Task<ActionResult<RequestApplicationDto>> GetApplication(
        Guid requestId, 
        Guid id)
    {
        try
        {
            var userId = GetCurrentUserId();
            var application = await _applicationService.GetApplicationByIdAsync(id, userId);
            
            if (application == null)
                return NotFound();

            return Ok(application);
        }
        catch (UnauthorizedAccessException)
        {
            return Forbid();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al obtener aplicación {ApplicationId}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Acepta una aplicación
    /// </summary>
    [HttpPatch("{id:guid}/accept")]
    public async Task<ActionResult<RequestApplicationDto>> AcceptApplication(
        Guid requestId, 
        Guid id)
    {
        try
        {
            var userId = GetCurrentUserId();
            var application = await _applicationService.AcceptApplicationAsync(id, userId);
            
            return Ok(application);
        }
        catch (UnauthorizedAccessException)
        {
            return Forbid();
        }
        catch (NotFoundException)
        {
            return NotFound();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al aceptar aplicación {ApplicationId}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    /// <summary>
    /// Rechaza una aplicación
    /// </summary>
    [HttpPatch("{id:guid}/reject")]
    public async Task<ActionResult<RequestApplicationDto>> RejectApplication(
        Guid requestId, 
        Guid id, 
        [FromBody] RejectApplicationDto rejectDto)
    {
        try
        {
            var userId = GetCurrentUserId();
            var application = await _applicationService.RejectApplicationAsync(id, rejectDto, userId);
            
            return Ok(application);
        }
        catch (UnauthorizedAccessException)
        {
            return Forbid();
        }
        catch (NotFoundException)
        {
            return NotFound();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al rechazar aplicación {ApplicationId}", id);
            return StatusCode(500, "Error interno del servidor");
        }
    }

    private Guid GetCurrentUserId()
    {
        var userIdClaim = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (Guid.TryParse(userIdClaim, out var userId))
            return userId;
        
        throw new UnauthorizedAccessException("Usuario no autenticado");
    }
}
```

---

## 🔧 **Servicios**

### **⚙️ Servicio de Solicitudes**
```csharp
// Services/IMusicianRequestService.cs
public interface IMusicianRequestService
{
    Task<PaginatedResult<MusicianRequestDto>> SearchRequestsAsync(RequestSearchDto searchDto);
    Task<MusicianRequestDto> GetRequestByIdAsync(Guid id);
    Task<MusicianRequestDto> CreateRequestAsync(CreateMusicianRequestDto createDto, Guid userId);
    Task<MusicianRequestDto> UpdateRequestAsync(Guid id, UpdateMusicianRequestDto updateDto, Guid userId);
    Task DeleteRequestAsync(Guid id, Guid userId);
    Task<MusicianRequestDto> ToggleFeaturedAsync(Guid id);
    Task<IEnumerable<MusicianRequestDto>> GetRequestsByUserAsync(Guid userId);
}

// Services/MusicianRequestService.cs
public class MusicianRequestService : IMusicianRequestService
{
    private readonly IMusicianRequestRepository _requestRepository;
    private readonly IUserProfileRepository _userRepository;
    private readonly IMapper _mapper;
    private readonly IValidator<CreateMusicianRequestDto> _createValidator;
    private readonly IValidator<UpdateMusicianRequestDto> _updateValidator;
    private readonly ILogger<MusicianRequestService> _logger;
    private readonly INotificationService _notificationService;

    public MusicianRequestService(
        IMusicianRequestRepository requestRepository,
        IUserProfileRepository userRepository,
        IMapper mapper,
        IValidator<CreateMusicianRequestDto> createValidator,
        IValidator<UpdateMusicianRequestDto> updateValidator,
        ILogger<MusicianRequestService> logger,
        INotificationService notificationService)
    {
        _requestRepository = requestRepository;
        _userRepository = userRepository;
        _mapper = mapper;
        _createValidator = createValidator;
        _updateValidator = updateValidator;
        _logger = logger;
        _notificationService = notificationService;
    }

    public async Task<PaginatedResult<MusicianRequestDto>> SearchRequestsAsync(RequestSearchDto searchDto)
    {
        try
        {
            var searchCriteria = new RequestSearchCriteria
            {
                SearchTerm = searchDto.SearchTerm,
                Instrument = searchDto.Instrument,
                Location = searchDto.Location,
                BudgetMin = searchDto.BudgetMin,
                BudgetMax = searchDto.BudgetMax,
                DateFrom = searchDto.DateFrom,
                DateTo = searchDto.DateTo,
                Status = searchDto.Status,
                Page = searchDto.Page,
                PageSize = searchDto.PageSize,
                SortBy = searchDto.SortBy,
                SortDirection = searchDto.SortDirection
            };

            var (requests, totalCount) = await _requestRepository.SearchAsync(searchCriteria);
            
            var requestDtos = _mapper.Map<IEnumerable<MusicianRequestDto>>(requests);
            
            return new PaginatedResult<MusicianRequestDto>
            {
                Data = requestDtos,
                TotalCount = totalCount,
                Page = searchDto.Page,
                PageSize = searchDto.PageSize,
                TotalPages = (int)Math.Ceiling((double)totalCount / searchDto.PageSize)
            };
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error en búsqueda de solicitudes");
            throw;
        }
    }

    public async Task<MusicianRequestDto> GetRequestByIdAsync(Guid id)
    {
        var request = await _requestRepository.GetByIdAsync(id);
        if (request == null)
            return null;

        // Incrementar contador de vistas
        await _requestRepository.IncrementViewCountAsync(id);
        
        return _mapper.Map<MusicianRequestDto>(request);
    }

    public async Task<MusicianRequestDto> CreateRequestAsync(CreateMusicianRequestDto createDto, Guid userId)
    {
        // Validar DTO
        var validationResult = await _createValidator.ValidateAsync(createDto);
        if (!validationResult.IsValid)
        {
            var errors = string.Join(", ", validationResult.Errors.Select(e => e.ErrorMessage));
            throw new ValidationException(errors);
        }

        // Verificar que el usuario existe y es organizador
        var user = await _userRepository.GetByIdAsync(userId);
        if (user == null || user.Role != UserRole.Organizer)
        {
            throw new UnauthorizedAccessException("Solo los organizadores pueden crear solicitudes");
        }

        // Crear entidad
        var request = _mapper.Map<MusicianRequest>(createDto);
        request.OrganizerId = userId;
        request.CreatedBy = userId;
        request.Status = RequestStatus.Open;
        request.CreatedAt = DateTime.UtcNow;

        // Guardar en base de datos
        var createdRequest = await _requestRepository.AddAsync(request);
        
        // Notificar a músicos interesados
        await _notificationService.NotifyMusiciansOfNewRequestAsync(createdRequest);
        
        _logger.LogInformation("Solicitud creada: {RequestId} por usuario {UserId}", 
            createdRequest.Id, userId);

        return _mapper.Map<MusicianRequestDto>(createdRequest);
    }

    public async Task<MusicianRequestDto> UpdateRequestAsync(Guid id, UpdateMusicianRequestDto updateDto, Guid userId)
    {
        // Validar DTO
        var validationResult = await _updateValidator.ValidateAsync(updateDto);
        if (!validationResult.IsValid)
        {
            var errors = string.Join(", ", validationResult.Errors.Select(e => e.ErrorMessage));
            throw new ValidationException(errors);
        }

        // Obtener solicitud existente
        var existingRequest = await _requestRepository.GetByIdAsync(id);
        if (existingRequest == null)
            throw new NotFoundException("Solicitud no encontrada");

        // Verificar permisos
        if (existingRequest.OrganizerId != userId)
        {
            var user = await _userRepository.GetByIdAsync(userId);
            if (user?.Role != UserRole.Admin && user?.Role != UserRole.Moderator)
            {
                throw new UnauthorizedAccessException("No tienes permisos para actualizar esta solicitud");
            }
        }

        // Actualizar campos
        _mapper.Map(updateDto, existingRequest);
        existingRequest.UpdatedBy = userId;
        existingRequest.UpdatedAt = DateTime.UtcNow;

        // Guardar cambios
        var updatedRequest = await _requestRepository.UpdateAsync(existingRequest);
        
        _logger.LogInformation("Solicitud actualizada: {RequestId} por usuario {UserId}", 
            id, userId);

        return _mapper.Map<MusicianRequestDto>(updatedRequest);
    }

    public async Task DeleteRequestAsync(Guid id, Guid userId)
    {
        var request = await _requestRepository.GetByIdAsync(id);
        if (request == null)
            throw new NotFoundException("Solicitud no encontrada");

        // Verificar permisos
        if (request.OrganizerId != userId)
        {
            var user = await _userRepository.GetByIdAsync(userId);
            if (user?.Role != UserRole.Admin)
            {
                throw new UnauthorizedAccessException("No tienes permisos para eliminar esta solicitud");
            }
        }

        // Verificar que no tenga aplicaciones aceptadas
        if (request.Status == RequestStatus.Assigned || request.Status == RequestStatus.InProgress)
        {
            throw new ValidationException("No se puede eliminar una solicitud que ya tiene un músico asignado");
        }

        await _requestRepository.DeleteAsync(id);
        
        _logger.LogInformation("Solicitud eliminada: {RequestId} por usuario {UserId}", 
            id, userId);
    }

    public async Task<MusicianRequestDto> ToggleFeaturedAsync(Guid id)
    {
        var request = await _requestRepository.GetByIdAsync(id);
        if (request == null)
            throw new NotFoundException("Solicitud no encontrada");

        request.IsFeatured = !request.IsFeatured;
        request.UpdatedAt = DateTime.UtcNow;

        var updatedRequest = await _requestRepository.UpdateAsync(request);
        
        _logger.LogInformation("Solicitud {RequestId} marcada como destacada: {IsFeatured}", 
            id, request.IsFeatured);

        return _mapper.Map<MusicianRequestDto>(updatedRequest);
    }

    public async Task<IEnumerable<MusicianRequestDto>> GetRequestsByUserAsync(Guid userId)
    {
        var requests = await _requestRepository.GetByOrganizerIdAsync(userId);
        return _mapper.Map<IEnumerable<MusicianRequestDto>>(requests);
    }
}
```

---

## 📊 **Modelos y DTOs**

### **🔍 DTOs de Búsqueda**
```csharp
// DTOs/RequestSearchDto.cs
public class RequestSearchDto
{
    public string? SearchTerm { get; set; }
    public string? Instrument { get; set; }
    public string? Location { get; set; }
    public decimal? BudgetMin { get; set; }
    public decimal? BudgetMax { get; set; }
    public DateTime? DateFrom { get; set; }
    public DateTime? DateTo { get; set; }
    public RequestStatus? Status { get; set; }
    public int Page { get; set; } = 1;
    public int PageSize { get; set; } = 20;
    public string? SortBy { get; set; } = "created_at";
    public SortDirection SortDirection { get; set; } = SortDirection.Descending;
}

public enum SortDirection
{
    Ascending,
    Descending
}

// DTOs/CreateMusicianRequestDto.cs
public class CreateMusicianRequestDto
{
    [Required]
    [StringLength(255)]
    public string Title { get; set; } = string.Empty;
    
    [Required]
    [StringLength(2000)]
    public string Description { get; set; } = string.Empty;
    
    [Required]
    [StringLength(100)]
    public string Instrument { get; set; } = string.Empty;
    
    [StringLength(100)]
    public string? Genre { get; set; }
    
    [Required]
    [Range(1, 10000)]
    public decimal BudgetMin { get; set; }
    
    [Required]
    [Range(1, 10000)]
    public decimal BudgetMax { get; set; }
    
    [Required]
    [Range(1, 48)]
    public int DurationHours { get; set; }
    
    [Required]
    public DateTime EventDate { get; set; }
    
    [Required]
    public LocationDto Location { get; set; } = new();
    
    public List<string>? Requirements { get; set; }
    
    [StringLength(100)]
    public string? PreferredStyle { get; set; }
    
    public ExperienceLevel ExperienceLevel { get; set; } = ExperienceLevel.Any;
    
    public List<string>? Tags { get; set; }
}

// DTOs/UpdateMusicianRequestDto.cs
public class UpdateMusicianRequestDto
{
    [StringLength(255)]
    public string? Title { get; set; }
    
    [StringLength(2000)]
    public string? Description { get; set; }
    
    [StringLength(100)]
    public string? Instrument { get; set; }
    
    [StringLength(100)]
    public string? Genre { get; set; }
    
    [Range(1, 10000)]
    public decimal? BudgetMin { get; set; }
    
    [Range(1, 10000)]
    public decimal? BudgetMax { get; set; }
    
    [Range(1, 48)]
    public int? DurationHours { get; set; }
    
    public DateTime? EventDate { get; set; }
    
    public LocationDto? Location { get; set; }
    
    public List<string>? Requirements { get; set; }
    
    [StringLength(100)]
    public string? PreferredStyle { get; set; }
    
    public ExperienceLevel? ExperienceLevel { get; set; }
    
    public List<string>? Tags { get; set; }
    
    public RequestStatus? Status { get; set; }
}

// DTOs/LocationDto.cs
public class LocationDto
{
    [Required]
    [StringLength(500)]
    public string Address { get; set; } = string.Empty;
    
    [Required]
    [StringLength(100)]
    public string City { get; set; } = string.Empty;
    
    [Required]
    [StringLength(100)]
    public string State { get; set; } = string.Empty;
    
    [Required]
    [StringLength(100)]
    public string Country { get; set; } = string.Empty;
    
    [Required]
    [Range(-90, 90)]
    public double Latitude { get; set; }
    
    [Required]
    [Range(-180, 180)]
    public double Longitude { get; set; }
}
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [ASP.NET Core Documentation](https://docs.microsoft.com/en-us/aspnet/core/)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [FluentValidation](https://fluentvalidation.net/)

### **📖 Documentación Relacionada**
- [Arquitectura del Backend](./ARQUITECTURA.md)
- [Stack Tecnológico](./STACK_TECNOLOGICO.md)
- [Configuración](./CONFIGURACION.md)

---

*Este sistema de solicitudes backend proporciona una base sólida para la funcionalidad core de MussikOn, asegurando APIs robustas, seguras y escalables.*
