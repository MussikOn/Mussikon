# 🚀 **BACKEND - MUSSIKON API**

## 📋 **Índice General del Backend**

### **🏗️ Arquitectura y Tecnologías**
- [Stack Tecnológico Detallado](./STACK_TECNOLOGICO.md) - Stack completo de .NET Core
- [Arquitectura del Sistema](./ARQUITECTURA.md) - Patrones y estructura del backend
- [Sistema de Solicitudes](./SOLICITUDES_MUSICOS.md) - **CORE DEL NEGOCIO**
- [Configuración del Entorno](./CONFIGURACION.md) - Setup y variables de entorno

### **📅 Sprints y Desarrollo**
- [Etapas de Desarrollo](./ETAPAS_DESARROLLO.md) - Plan completo por etapas
- [Guía del MVP](./GUIA_MVP.md) - Desarrollo día a día del MVP
- [Sprints del Backend](./SPRINTS_BACKEND.md) - Sprints específicos para backend

### **👥 Historias de Usuario**
- [Historias de Usuario Organizadas](./HISTORIAS_USUARIO.md) - Todas las US del backend
- [API Endpoints](./API_ENDPOINTS.md) - Documentación de APIs
- [Autenticación y Seguridad](./AUTENTICACION.md) - JWT + Identity

### **🧪 Testing y Calidad**
- [Estrategia de Testing](./TESTING.md) - xUnit + Moq + FluentAssertions
- [Código de Calidad](./CALIDAD_CODIGO.md) - Estándares y métricas
- [Performance y Optimización](./PERFORMANCE.md) - Caching y optimizaciones

### **🚀 DevOps y Despliegue**
- [CI/CD Pipeline](./CI_CD.md) - GitHub Actions
- [Docker y Contenedores](./DOCKER.md) - Containerización
- [Monitoreo y Logs](./MONITOREO.md) - Observabilidad

### **📚 Recursos y Referencias**
- [Documentación .NET Core](./REFERENCIAS_DOTNET.md) - Enlaces oficiales
- [Patrones de Diseño](./PATRONES_DISENO.md) - Arquitectura limpia
- [Troubleshooting](./TROUBLESHOOTING.md) - Problemas comunes

---

## 🎯 **Estado Actual del Backend**

### **✅ Tecnologías Confirmadas**
- **Runtime**: .NET 8.0+
- **Framework**: ASP.NET Core Web API
- **Base de Datos**: SQL Server / PostgreSQL
- **ORM**: Entity Framework Core 8.0+
- **Autenticación**: JWT + ASP.NET Identity
- **Comunicación**: SignalR (reemplaza Socket.IO)
- **Testing**: xUnit + Moq + FluentAssertions
- **Documentación**: Swagger/OpenAPI

### **📊 Métricas del Backend**
- **Cobertura de Testing**: Objetivo 80%+
- **Performance**: Response time < 200ms
- **Seguridad**: OWASP Top 10 compliance
- **Documentación**: 100% de APIs documentadas

---

## 🎵 **Sistema Core: Solicitudes de Músicos**

### **🏗️ Arquitectura del Sistema**
```
┌─────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ Controllers │  │ Middleware  │  │   DTOs/ViewModels   │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │   Services  │  │ Validators  │  │   AutoMapper        │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     DOMAIN LAYER                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │   Entities  │  │  Interfaces │  │   Domain Services   │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  INFRASTRUCTURE LAYER                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │   Repos     │  │   External  │  │   Configuration     │ │
│  │             │  │   Services  │  │                     │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### **📁 Estructura de Carpetas del Proyecto**
```
MussikOn.API/
├── 📁 Controllers/           # Controladores de la API
│   ├── AuthController.cs
│   ├── MusicianRequestController.cs    # 🎯 CORE
│   ├── MusicianController.cs
│   ├── UserController.cs
│   └── ChatController.cs
├── 📁 Middleware/            # Middleware personalizado
│   ├── ExceptionMiddleware.cs
│   ├── LoggingMiddleware.cs
│   └── PerformanceMiddleware.cs
├── 📁 DTOs/                  # Data Transfer Objects
│   ├── Auth/
│   ├── MusicianRequest/      # 🎯 CORE
│   ├── Musician/
│   └── User/
├── 📁 Services/              # Lógica de negocio
│   ├── Interfaces/
│   ├── MusicianRequestService.cs      # 🎯 CORE
│   ├── MusicianService.cs
│   ├── AuthService.cs
│   └── ChatService.cs
├── 📁 Validators/            # Validaciones con FluentValidation
│   ├── MusicianRequestValidators.cs   # 🎯 CORE
│   ├── MusicianValidators.cs
│   └── AuthValidators.cs
├── 📁 Domain/                # Entidades y lógica de dominio
│   ├── Entities/
│   │   ├── MusicianRequest.cs        # 🎯 CORE
│   │   ├── Musician.cs
│   │   └── User.cs
│   ├── Interfaces/
│   ├── Enums/
│   └── Exceptions/
├── 📁 Infrastructure/        # Implementaciones de infraestructura
│   ├── Data/
│   ├── Repositories/
│   │   ├── MusicianRequestRepository.cs    # 🎯 CORE
│   │   ├── MusicianRepository.cs
│   │   └── UserRepository.cs
│   ├── ExternalServices/
│   └── Configuration/
├── 📁 Tests/                 # Tests unitarios y de integración
│   ├── Unit/
│   ├── Integration/
│   └── Fixtures/
└── 📁 Configuration/         # Configuración de la aplicación
    ├── appsettings.json
    ├── appsettings.Development.json
    └── appsettings.Production.json
```

---

## 🎯 **Funcionalidades Core del MVP**

### **✅ Sistema de Autenticación**
- **JWT + ASP.NET Identity** para autenticación robusta
- **8 roles de usuario** diferenciados (SuperAdmin, Admin, Moderador, Organizador, Músico, Event Creator, Musician, User)
- **Refresh tokens** automáticos
- **Middleware de autorización** granular por roles

### **✅ Sistema de Solicitudes de Músicos** ⭐ **CORE FEATURE**
- **CRUD completo** de solicitudes musicales
- **Estados del ciclo de vida**: Pendiente, En Revisión, Asignada, Completada, Cancelada
- **Validaciones robustas** con FluentValidation
- **Notificaciones en tiempo real** vía SignalR
- **Sistema de matching** inteligente con algoritmos de scoring

### **✅ Sistema de Matching Musical**
- **Algoritmo de scoring** para músicos disponibles
- **Filtros avanzados** por instrumento, género, ubicación, precio
- **Detección de conflictos** de calendario
- **Cálculo automático** de tarifas sugeridas
- **Estado online/offline** en tiempo real

### **✅ Chat en Tiempo Real**
- **SignalR Hubs** para comunicación bidireccional
- **Persistencia** de mensajes en base de datos
- **Notificaciones push** para mensajes importantes
- **Historial completo** de conversaciones

---

## 🔐 **Sistema de Autenticación y Autorización**

### **JWT Token Structure**
```json
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "sub": "user-id",
    "email": "user@example.com",
    "role": "Organizador",
    "permissions": ["read:requests", "write:requests", "read:musicians"],
    "iat": 1640995200,
    "exp": 1641081600
  }
}
```

### **Roles y Permisos**
```csharp
public enum UserRole
{
    SuperAdmin,      // Acceso total al sistema
    Admin,           // Administración general
    Moderador,       // Moderación de contenido
    Organizador,     // Creación y gestión de solicitudes
    Musico,          // Músico profesional
    EventCreator,    // Creador de eventos
    Musician,        // Músico registrado
    User             // Usuario básico
}

public enum Permission
{
    // Solicitudes de Músicos
    ReadRequests, WriteRequests, AcceptRequests, CancelRequests,
    // Músicos
    ReadMusicians, WriteMusicians, SearchMusicians,
    // Chat
    ReadMessages, WriteMessages, DeleteMessages,
    // Usuarios
    ReadUsers, WriteUsers, DeleteUsers
}
```

---

## 📊 **Patrones de Diseño Implementados**

### **🏭 Repository Pattern**
```csharp
public interface IMusicianRequestRepository
{
    Task<MusicianRequest> GetByIdAsync(Guid id);
    Task<IEnumerable<MusicianRequest>> GetAllAsync();
    Task<IEnumerable<MusicianRequest>> GetAvailableForMusiciansAsync();
    Task<MusicianRequest> AddAsync(MusicianRequest entity);
    Task UpdateAsync(MusicianRequest entity);
    Task DeleteAsync(Guid id);
}
```

### **⚙️ Service Layer Pattern**
```csharp
public interface IMusicianRequestService
{
    Task<MusicianRequestDto> CreateAsync(CreateMusicianRequestDto request);
    Task<MusicianRequestDto> GetByIdAsync(Guid id);
    Task<IEnumerable<MusicianRequestDto>> GetAvailableForMusiciansAsync();
    Task<MusicianRequestDto> AcceptByMusicianAsync(Guid requestId, Guid musicianId);
    Task<MusicianRequestDto> UpdateStatusAsync(Guid id, RequestStatus status);
}
```

### **🔄 Unit of Work Pattern**
```csharp
public interface IUnitOfWork : IDisposable
{
    IMusicianRequestRepository MusicianRequests { get; }
    IMusicianRepository Musicians { get; }
    IUserRepository Users { get; }
    Task<int> SaveChangesAsync();
}
```

---

## 🧪 **Estrategia de Testing**

### **📝 Tests Unitarios**
```csharp
[Fact]
public async Task CreateMusicianRequest_WithValidData_ShouldReturnSuccess()
{
    // Arrange
    var mockRepository = new Mock<IMusicianRequestRepository>();
    var mockService = new Mock<IMusicianRequestService>();
    var mockMapper = new Mock<IMapper>();
    
    var request = new CreateMusicianRequestDto
    {
        EventType = "Boda",
        Date = DateTime.Now.AddDays(30),
        Instrument = "Piano",
        Budget = 500,
        Location = "Madrid, España"
    };
    
    // Act
    var result = await _controller.Create(request);
    
    // Assert
    Assert.IsType<CreatedAtActionResult>(result);
}
```

### **🔗 Tests de Integración**
```csharp
[Fact]
public async Task CreateMusicianRequest_EndToEnd_ShouldWork()
{
    // Arrange
    var client = _factory.CreateClient();
    var token = await GetAuthToken(client);
    client.DefaultRequestHeaders.Authorization = 
        new AuthenticationHeaderValue("Bearer", token);
    
    // Act
    var response = await client.PostAsJsonAsync("/api/musician-requests", request);
    
    // Assert
    response.EnsureSuccessStatusCode();
}
```

---

## 🚀 **Configuración y Deployment**

### **⚙️ appsettings.json**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MussikOnDB;Trusted_Connection=true;"
  },
  "JwtSettings": {
    "SecretKey": "your-secret-key-here",
    "Issuer": "MussikOn",
    "Audience": "MussikOnUsers",
    "ExpirationMinutes": 60
  },
  "SignalR": {
    "EnableDetailedErrors": true,
    "ClientTimeoutInterval": "00:00:30",
    "KeepAliveInterval": "00:00:15"
  },
  "Redis": {
    "ConnectionString": "localhost:6379"
  }
}
```

### **🐳 Docker Configuration**
```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["MussikOn.API/MussikOn.API.csproj", "MussikOn.API/"]
RUN dotnet restore "MussikOn.API/MussikOn.API.csproj"
COPY . .
WORKDIR "/src/MussikOn.API"
RUN dotnet build "MussikOn.API.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "MussikOn.API.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "MussikOn.API.dll"]
```

---

## 📈 **Métricas y Monitoreo**

### **📊 Health Checks**
```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks()
            .AddDbContextCheck<ApplicationDbContext>()
            .AddRedis(Configuration.GetConnectionString("Redis"))
            .AddUrlGroup(new Uri("https://api.stripe.com"), "Stripe API");
    }
}
```

### **📝 Logging Estructurado**
```csharp
public class MusicianRequestController : ControllerBase
{
    [HttpPost]
    public async Task<ActionResult<MusicianRequestDto>> Create(CreateMusicianRequestDto request)
    {
        using var scope = _logger.BeginScope(new Dictionary<string, object>
        {
            ["Action"] = "Create",
            ["Controller"] = "MusicianRequest",
            ["UserId"] = User.GetUserId(),
            ["EventType"] = request.EventType
        });
        
        _logger.LogInformation("Creating musician request for event type: {EventType}", request.EventType);
        var result = await _musicianRequestService.CreateAsync(request);
        _logger.LogInformation("Musician request created successfully with ID: {RequestId}", result.Id);
        
        return CreatedAtAction(nameof(GetById), new { id = result.Id }, result);
    }
}
```

---

## 🔒 **Seguridad y Compliance**

### **🛡️ OWASP Top 10 Compliance**
- **SQL Injection**: Entity Framework Core parameterized queries
- **Authentication**: JWT + Identity con refresh tokens
- **Authorization**: Role-based access control (RBAC)
- **Input Validation**: FluentValidation + model binding
- **Rate Limiting**: Implementado con middleware personalizado
- **CORS**: Configuración restrictiva por origen
- **HTTPS**: Forzado en producción
- **Logging**: Logs de auditoría para acciones sensibles

---

## 📚 **Referencias y Recursos**

### **🔗 Enlaces Oficiales**
- [.NET 8 Documentation](https://docs.microsoft.com/en-us/dotnet/)
- [ASP.NET Core Documentation](https://docs.microsoft.com/en-us/aspnet/core/)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [SignalR Documentation](https://docs.microsoft.com/en-us/aspnet/core/signalr/)

### **📖 Libros Recomendados**
- "Clean Architecture" - Robert C. Martin
- "Domain-Driven Design" - Eric Evans
- "Patterns of Enterprise Application Architecture" - Martin Fowler

---

## 🎯 **Próximos Pasos Recomendados**

1. **Configurar proyecto .NET Core** con Clean Architecture
2. **Conectar con base de datos** (SQL Server/PostgreSQL)
3. **Implementar autenticación JWT** con ASP.NET Identity
4. **Crear sistema de solicitudes** completo (MVP core)
5. **Configurar SignalR** para comunicación en tiempo real
6. **Testing exhaustivo** y documentación de endpoints

---

## 🔗 **Enlaces Rápidos**

- [📋 Sprint Actual](./SPRINTS_BACKEND.md#sprint-actual)
- [🎯 Sistema de Solicitudes](./SOLICITUDES_MUSICOS.md)
- [🐛 Issues Conocidos](./TROUBLESHOOTING.md)
- [📊 Métricas en Tiempo Real](./MONITOREO.md)
- [🚀 Deploy Status](./CI_CD.md#status-deploy)

---

*Última actualización: Diciembre 2024*
*Versión: v1.0.0*
*Enfoque: Sistema de Solicitudes de Músicos*
