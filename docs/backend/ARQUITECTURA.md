# 🏗️ **ARQUITECTURA DEL SISTEMA - BACKEND MUSSIKON**

## 📋 **Índice de la Arquitectura**

### **🏗️ Arquitectura General**
- [Clean Architecture](./ARQUITECTURA.md#clean-architecture) - Principios y capas
- [Patrones de Diseño](./ARQUITECTURA.md#patrones-de-diseño) - SOLID y patrones aplicados
- [Estructura de Carpetas](./ARQUITECTURA.md#estructura-de-carpetas) - Organización del proyecto

### **🔧 Implementación Técnica**
- [Configuración](./ARQUITECTURA.md#configuración) - Variables de entorno y settings
- [Dependencias](./ARQUITECTURA.md#dependencias) - Paquetes NuGet y servicios
- [Middleware](./ARQUITECTURA.md#middleware) - Pipeline de request/response

---

## 🎯 **Propósito de la Arquitectura**

### **Objetivo Principal**
Implementar una arquitectura limpia y escalable que permita el desarrollo eficiente del **Sistema de Solicitudes de Músicos** siguiendo principios SOLID y patrones de diseño probados.

### **Principios Clave**
- **Separación de Responsabilidades**: Cada capa tiene una responsabilidad específica
- **Independencia de Frameworks**: La lógica de negocio no depende de ASP.NET Core
- **Testabilidad**: Arquitectura diseñada para testing unitario y de integración
- **Escalabilidad**: Preparada para crecer con el negocio
- **Mantenibilidad**: Código limpio y fácil de mantener

---

## 🏗️ **Clean Architecture**

### **📊 Diagrama de Capas**
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
│   ├── UserController.cs
│   └── NotificationController.cs
├── 📁 Middleware/            # Middleware personalizado
│   ├── ExceptionMiddleware.cs
│   ├── LoggingMiddleware.cs
│   └── ValidationMiddleware.cs
├── 📁 DTOs/                  # Data Transfer Objects
│   ├── Requests/
│   ├── Responses/
│   └── ViewModels/
├── 📁 Services/              # Servicios de aplicación
│   ├── MusicianRequestService.cs
│   ├── UserService.cs
│   ├── NotificationService.cs
│   └── MatchingService.cs
├── 📁 Validators/            # Validaciones con FluentValidation
│   ├── MusicianRequestValidator.cs
│   ├── UserValidator.cs
│   └── CustomValidators/
├── 📁 AutoMapper/            # Configuración de mapeo
│   └── MappingProfiles.cs
├── 📁 Domain/                # Entidades y lógica de dominio
│   ├── Entities/
│   │   ├── User.cs
│   │   ├── MusicianRequest.cs
│   │   ├── MusicianProfile.cs
│   │   └── Notification.cs
│   ├── Interfaces/
│   │   ├── IMusicianRequestRepository.cs
│   │   ├── IUserRepository.cs
│   │   └── INotificationService.cs
│   ├── Services/
│   │   ├── MusicianMatchingService.cs
│   │   └── NotificationDomainService.cs
│   └── ValueObjects/
│       ├── Location.cs
│       ├── Money.cs
│       └── TimeRange.cs
├── 📁 Infrastructure/        # Implementación de infraestructura
│   ├── Data/
│   │   ├── ApplicationDbContext.cs
│   │   ├── Repositories/
│   │   └── Migrations/
│   ├── External/
│   │   ├── EmailService.cs
│   │   ├── PaymentService.cs
│   │   └── StorageService.cs
│   └── Configuration/
│       ├── DatabaseSettings.cs
│       ├── JwtSettings.cs
│       └── AppSettings.cs
├── 📁 Hubs/                  # SignalR Hubs para tiempo real
│   ├── NotificationHub.cs
│   └── ChatHub.cs
├── 📁 Extensions/            # Extensiones y helpers
│   ├── ServiceCollectionExtensions.cs
│   ├── ApplicationBuilderExtensions.cs
│   └── ClaimsPrincipalExtensions.cs
└── 📁 Program.cs             # Punto de entrada de la aplicación
```

---

## 🔧 **Patrones de Diseño**

### **🏗️ Patrones Arquitectónicos**
- **Clean Architecture**: Separación clara de responsabilidades
- **Repository Pattern**: Abstracción del acceso a datos
- **Unit of Work**: Gestión de transacciones
- **Service Layer**: Lógica de aplicación
- **Factory Pattern**: Creación de objetos complejos
- **Observer Pattern**: Notificaciones en tiempo real

### **📱 Patrones de Comunicación**
- **SignalR**: Comunicación en tiempo real
- **REST API**: Endpoints HTTP estándar
- **Event-Driven**: Eventos para notificaciones
- **CQRS**: Separación de comandos y consultas (futuro)

---

## ⚙️ **Configuración**

### **🔧 Variables de Entorno**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=...;Database=MussikOn;...",
    "RedisConnection": "localhost:6379"
  },
  "JwtSettings": {
    "SecretKey": "your-secret-key-here",
    "Issuer": "MussikOn",
    "Audience": "MussikOnUsers",
    "ExpirationMinutes": 60,
    "RefreshTokenExpirationDays": 7
  },
  "DatabaseSettings": {
    "Provider": "SqlServer", // o "PostgreSQL"
    "ConnectionTimeout": 30,
    "CommandTimeout": 30
  },
  "SignalRSettings": {
    "EnableDetailedErrors": false,
    "EnableMessagePack": true
  }
}
```

### **🔌 Dependencias Principales**
```xml
<PackageReference Include="Microsoft.AspNetCore.App" />
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="8.0.0" />
<PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="8.0.0" />
<PackageReference Include="Microsoft.AspNetCore.SignalR" Version="1.1.0" />
<PackageReference Include="FluentValidation.AspNetCore" Version="11.3.0" />
<PackageReference Include="AutoMapper.Extensions.Microsoft.DependencyInjection" Version="12.0.1" />
<PackageReference Include="Serilog.AspNetCore" Version="8.0.0" />
```

---

## 🔄 **Middleware Pipeline**

### **📊 Flujo de Request**
```
Request → Logging → Authentication → Authorization → Validation → Controller → Response
```

### **🔧 Middleware Configurado**
```csharp
app.UseMiddleware<ExceptionMiddleware>();
app.UseMiddleware<LoggingMiddleware>();
app.UseMiddleware<RequestValidationMiddleware>();

app.UseAuthentication();
app.UseAuthorization();
app.UseSignalR();
```

---

## 🧪 **Testing y Calidad**

### **📊 Cobertura Objetivo**
- **Unit Tests**: 80%+
- **Integration Tests**: 60%+
- **End-to-End Tests**: 40%+

### **🔧 Herramientas de Testing**
- **xUnit**: Framework de testing
- **Moq**: Mocking framework
- **FluentAssertions**: Assertions legibles
- **TestContainers**: Testing con base de datos real

---

## 🚀 **Escalabilidad y Performance**

### **📈 Estrategias de Escalabilidad**
- **Horizontal Scaling**: Múltiples instancias de la API
- **Database Sharding**: Particionamiento de datos por región
- **Caching**: Redis para datos frecuentemente accedidos
- **Async/Await**: Operaciones asíncronas para mejor throughput

### **⚡ Optimizaciones de Performance**
- **Connection Pooling**: Gestión eficiente de conexiones a BD
- **Query Optimization**: Índices y queries optimizados
- **Response Caching**: Cache de respuestas HTTP
- **Compression**: Gzip para respuestas grandes

---

## 🔒 **Seguridad**

### **🛡️ Medidas de Seguridad**
- **JWT Authentication**: Tokens seguros con refresh
- **Role-Based Access Control**: Permisos granulares
- **Input Validation**: Validación estricta de entrada
- **SQL Injection Prevention**: Entity Framework Core
- **HTTPS Only**: Forzar conexiones seguras
- **Rate Limiting**: Protección contra ataques DDoS

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Clean Architecture by Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [ASP.NET Core Documentation](https://docs.microsoft.com/en-us/aspnet/core/)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [SignalR Documentation](https://docs.microsoft.com/en-us/aspnet/core/signalr)

### **📖 Documentación Relacionada**
- [Stack Tecnológico](./STACK_TECNOLOGICO.md)
- [Sistema de Solicitudes](./SOLICITUDES_MUSICOS.md)
- [Configuración del Entorno](./CONFIGURACION.md)

---

*Esta arquitectura proporciona una base sólida para el desarrollo del Sistema de Solicitudes de Músicos, siguiendo las mejores prácticas de Clean Architecture y patrones de diseño probados.*
