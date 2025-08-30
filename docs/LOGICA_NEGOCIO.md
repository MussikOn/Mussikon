# 📋 **LÓGICA DE NEGOCIO - MUSSIKON API**

## 🎯 **Enfoque: Sistema de Solicitudes de Músicos (100% MVP)**

La **Lógica de Negocio** de MussikOn se centra exclusivamente en el **Sistema de Solicitudes de Músicos**, que es el corazón de la plataforma. Este documento define todas las reglas, validaciones y flujos de negocio necesarios para conectar músicos profesionales con organizadores de eventos.

---

## 🎵 **CONCEPTOS FUNDAMENTALES**

### **👥 Roles de Usuario**
- **Organizador**: Crea solicitudes para contratar músicos
- **Músico**: Acepta solicitudes y proporciona servicios musicales
- **Admin**: Gestiona usuarios y supervisa la plataforma
- **Moderador**: Modera contenido y resuelve disputas

### **📋 Tipos de Solicitudes**
- **Eventos Privados**: Bodas, cumpleaños, eventos corporativos
- **Eventos Públicos**: Conciertos, festivales, presentaciones
- **Sesiones de Estudio**: Grabaciones, colaboraciones musicales
- **Clases y Workshops**: Educación musical, mentorías

### **🎯 Estados de Solicitud**
- **Pendiente**: Solicitud creada, esperando aplicaciones
- **En Revisión**: Backend revisa detalles y presupuesto
- **Asignada**: Músico seleccionado y confirmado
- **Completada**: Evento realizado exitosamente
- **Cancelada**: Solicitud cancelada por organizador o músico
- **Expirada**: Solicitud expirada por tiempo

---

## 🔄 **FLUJOS DE NEGOCIO PRINCIPALES**

### **🎵 Flujo 1: Creación de Solicitud**
```
1. Organizador inicia sesión
2. Completa formulario de solicitud
3. Sistema valida datos de entrada
4. Sistema crea solicitud con estado "Pendiente"
5. Sistema notifica a músicos disponibles
6. Solicitud aparece en lista de disponibles
```

**Reglas de Negocio**:
- Solo usuarios con rol "Organizador" pueden crear solicitudes
- Fecha del evento debe ser futura (mínimo 24 horas)
- Presupuesto mínimo: $50, máximo: $10,000
- Todos los campos obligatorios deben estar completos

### **🎵 Flujo 2: Aceptación de Solicitud**
```
1. Músico ve solicitud disponible
2. Músico revisa detalles y tarifa
3. Backend verifica disponibilidad de calendario
4. Músico acepta la solicitud
5. Sistema cambia estado a "Asignada"
6. Sistema notifica al organizador
7. Sistema bloquea solicitud para otros músicos
```

**Reglas de Negocio**:
- Solo músicos con rol "Músico" pueden aceptar solicitudes
- Músico debe estar disponible en la fecha y hora del evento
- No se puede aceptar solicitudes ya asignadas
- Sistema previene doble asignación automáticamente

### **🎵 Flujo 3: Gestión de Estados**
```
1. Organizador puede cambiar estado de "Asignada" a "En Revisión"
2. Organizador puede cambiar estado a "Completada" después del evento
3. Organizador puede cancelar solicitudes "Pendientes"
4. Sistema puede expirar solicitudes automáticamente
5. Músico puede rechazar solicitud asignada (estado "Pendiente")
```

**Reglas de Negocio**:
- Solo el organizador puede cambiar estados de sus solicitudes
- Cambios de estado generan notificaciones automáticas
- Solicitudes expiran automáticamente después de 30 días
- Cancelaciones requieren justificación opcional

---

## ✅ **VALIDACIONES DE NEGOCIO**

### **📅 Validaciones de Fecha y Tiempo**
```csharp
// Fecha del evento debe ser futura
RuleFor(x => x.Date)
    .GreaterThan(DateTime.Today.AddDays(1))
    .WithMessage("La fecha del evento debe ser al menos 24 horas en el futuro");

// Hora del evento debe estar en rango válido
RuleFor(x => x.Time)
    .Must(time => time >= TimeSpan.FromHours(6) && time <= TimeSpan.FromHours(23))
    .WithMessage("La hora del evento debe estar entre 6:00 AM y 11:00 PM");
```

### **💰 Validaciones de Presupuesto**
```csharp
// Presupuesto debe estar en rango válido
RuleFor(x => x.Budget)
    .GreaterThan(49.99m)
    .LessThanOrEqualTo(10000.00m)
    .WithMessage("El presupuesto debe estar entre $50 y $10,000");

// Presupuesto debe ser múltiplo de $10 para eventos corporativos
RuleFor(x => x.Budget)
    .Must((request, budget) => 
        request.EventType != "Corporativo" || budget % 10 == 0)
    .WithMessage("El presupuesto para eventos corporativos debe ser múltiplo de $10");
```

### **📍 Validaciones de Ubicación**
```csharp
// Ubicación debe tener formato válido
RuleFor(x => x.Location)
    .NotEmpty()
    .MaximumLength(200)
    .Matches(@"^[a-zA-Z0-9\s,.-]+$")
    .WithMessage("La ubicación contiene caracteres no válidos");

// Ubicación debe incluir ciudad y país
RuleFor(x => x.Location)
    .Must(location => location.Contains(","))
    .WithMessage("La ubicación debe incluir ciudad y país separados por coma");
```

### **🎼 Validaciones de Instrumento**
```csharp
// Instrumento debe estar en lista de instrumentos válidos
RuleFor(x => x.Instrument)
    .Must(instrument => ValidInstruments.Contains(instrument))
    .WithMessage("El instrumento especificado no es válido");

// Instrumento debe coincidir con el rol del músico
RuleFor(x => x.Instrument)
    .Must((request, instrument) => 
        request.OrganizerRole == "Professional" || 
        !ProfessionalInstruments.Contains(instrument))
    .WithMessage("Este instrumento requiere un organizador profesional");
```

---

## 🧮 **ALGORITMOS DE MATCHING**

### **🎯 Algoritmo de Scoring Básico**
```csharp
public class MusicianScoringAlgorithm
{
    public decimal CalculateScore(Musician musician, MusicianRequest request)
    {
        decimal score = 0;
        
        // Score por instrumento (50 puntos)
        if (musician.Instruments.Contains(request.Instrument))
            score += 50;
        
        // Score por ubicación (30 puntos)
        score += CalculateLocationScore(musician.Location, request.Location);
        
        // Score por presupuesto (20 puntos)
        score += CalculateBudgetScore(musician.HourlyRate, request.Budget);
        
        // Bonus por experiencia (hasta 10 puntos)
        score += Math.Min(musician.YearsOfExperience, 10);
        
        // Bonus por calificación (hasta 10 puntos)
        score += Math.Min(musician.AverageRating, 10);
        
        return Math.Min(score, 100); // Máximo 100 puntos
    }
}
```

### **📍 Cálculo de Score de Ubicación**
```csharp
private decimal CalculateLocationScore(string musicianLocation, string requestLocation)
{
    var distance = CalculateDistance(musicianLocation, requestLocation);
    
    if (distance <= 10) return 30;      // Misma ciudad
    if (distance <= 50) return 25;      // Ciudad cercana
    if (distance <= 100) return 20;     // Región
    if (distance <= 200) return 15;     // Estado/Provincia
    if (distance <= 500) return 10;     // País
    return 0;                           // Muy lejos
}
```

### **💰 Cálculo de Score de Presupuesto**
```csharp
private decimal CalculateBudgetScore(decimal hourlyRate, decimal budget)
{
    var estimatedHours = CalculateEstimatedHours(budget);
    var totalCost = hourlyRate * estimatedHours;
    
    if (totalCost <= budget * 0.8m) return 20;      // Muy económico
    if (totalCost <= budget * 0.9m) return 18;      // Económico
    if (totalCost <= budget) return 15;              // Dentro del presupuesto
    if (totalCost <= budget * 1.1m) return 10;      // Ligeramente sobre presupuesto
    if (totalCost <= budget * 1.2m) return 5;       // Sobre presupuesto
    return 0;                                        // Muy sobre presupuesto
}
```

---

## 🔒 **REGLAS DE SEGURIDAD Y AUTORIZACIÓN**

### **👥 Control de Acceso por Rol**
```csharp
public enum Permission
{
    // Solicitudes de Músicos
    ReadRequests, WriteRequests, AcceptRequests, CancelRequests,
    
    // Músicos
    ReadMusicians, WriteMusicians, SearchMusicians,
    
    // Chat
    ReadMessages, WriteMessages, DeleteMessages,
    
    // Usuarios
    ReadUsers, WriteUsers, DeleteUsers,
    
    // Administración
    ManageRoles, ManagePermissions, ViewAnalytics
}

public class RolePermissionMatrix
{
    public static Dictionary<string, List<Permission>> GetRolePermissions()
    {
        return new Dictionary<string, List<Permission>>
        {
            ["SuperAdmin"] = Enum.GetValues<Permission>().ToList(),
            ["Admin"] = new List<Permission> { ReadRequests, WriteRequests, ReadMusicians, ReadUsers, ViewAnalytics },
            ["Organizador"] = new List<Permission> { ReadRequests, WriteRequests, CancelRequests, ReadMusicians, ReadMessages, WriteMessages },
            ["Músico"] = new List<Permission> { ReadRequests, AcceptRequests, ReadMessages, WriteMessages },
            ["User"] = new List<Permission> { ReadRequests, ReadMessages }
        };
    }
}
```

### **🛡️ Validaciones de Seguridad**
```csharp
public class SecurityValidationRules
{
    // Usuario solo puede acceder a sus propias solicitudes
    public static bool CanAccessRequest(Guid userId, MusicianRequest request)
    {
        return request.UserId == userId || 
               request.AssignedMusicianId == userId ||
               IsAdmin(userId);
    }
    
    // Solo organizadores pueden crear solicitudes
    public static bool CanCreateRequest(Guid userId, string userRole)
    {
        return userRole == "Organizador" || 
               userRole == "Admin" || 
               userRole == "SuperAdmin";
    }
    
    // Solo músicos pueden aceptar solicitudes
    public static bool CanAcceptRequest(Guid userId, string userRole)
    {
        return userRole == "Músico" || 
               userRole == "Admin" || 
               userRole == "SuperAdmin";
    }
}
```

---

## 📊 **REGLAS DE AUDITORÍA Y LOGGING**

### **📝 Eventos de Auditoría**
```csharp
public enum AuditEventType
{
    // Solicitudes
    RequestCreated,
    RequestUpdated,
    RequestStatusChanged,
    RequestAccepted,
    RequestCancelled,
    
    // Usuarios
    UserRegistered,
    UserLoggedIn,
    UserRoleChanged,
    UserProfileUpdated,
    
    // Sistema
    PermissionDenied,
    SecurityViolation,
    SystemError
}

public class AuditLogEntry
{
    public Guid Id { get; set; }
    public DateTime Timestamp { get; set; }
    public string UserId { get; set; }
    public string UserEmail { get; set; }
    public string UserRole { get; set; }
    public AuditEventType EventType { get; set; }
    public string Description { get; set; }
    public string IpAddress { get; set; }
    public string UserAgent { get; set; }
    public Dictionary<string, object> Metadata { get; set; }
}
```

### **🔍 Logging de Operaciones Críticas**
```csharp
public class CriticalOperationLogger
{
    public static void LogRequestCreation(MusicianRequest request, string userId)
    {
        _logger.LogInformation(
            "Musician request created. ID: {RequestId}, User: {UserId}, EventType: {EventType}, Date: {Date}, Budget: {Budget}",
            request.Id, userId, request.EventType, request.Date, request.Budget);
    }
    
    public static void LogRequestAcceptance(Guid requestId, Guid musicianId, string musicianEmail)
    {
        _logger.LogInformation(
            "Musician request accepted. RequestID: {RequestId}, MusicianID: {MusicianId}, MusicianEmail: {MusicianEmail}",
            requestId, musicianId, musicianEmail);
    }
    
    public static void LogSecurityViolation(string userId, string action, string reason)
    {
        _logger.LogWarning(
            "Security violation detected. User: {UserId}, Action: {Action}, Reason: {Reason}",
            userId, action, reason);
    }
}
```

---

## 🚫 **RESTRICCIONES Y LIMITACIONES**

### **📅 Límites de Tiempo**
- **Solicitudes**: Máximo 10 solicitudes activas por organizador
- **Aceptaciones**: Máximo 5 solicitudes aceptadas por músico simultáneamente
- **Expiración**: Solicitudes expiran automáticamente después de 30 días
- **Cancelación**: Solo se pueden cancelar solicitudes en estado "Pendiente"

### **💰 Límites de Presupuesto**
- **Mínimo**: $50 por solicitud
- **Máximo**: $10,000 por solicitud
- **Incrementos**: Múltiplos de $10 para eventos corporativos
- **Comisión**: 5% del presupuesto para la plataforma (futuro)

### **👥 Límites de Usuarios**
- **Solicitudes por día**: Máximo 5 por organizador
- **Búsquedas por día**: Máximo 100 por músico
- **Mensajes por conversación**: Máximo 100 por solicitud
- **Archivos adjuntos**: Máximo 5 por solicitud

---

## 🔄 **FLUJOS DE EXCEPCIÓN**

### **❌ Solicitud Rechazada por Músico**
```
1. Músico rechaza solicitud asignada
2. Sistema cambia estado a "Pendiente"
3. Sistema notifica al organizador
4. Sistema libera solicitud para otros músicos
5. Sistema registra motivo del rechazo
6. Sistema actualiza métricas del músico
```

### **❌ Cancelación de Solicitud por Organizador**
```
1. Organizador cancela solicitud
2. Sistema valida que esté en estado cancelable
3. Sistema cambia estado a "Cancelada"
4. Sistema notifica al músico asignado (si existe)
5. Sistema libera músico para otras solicitudes
6. Sistema registra motivo de cancelación
```

### **❌ Conflicto de Calendario**
```
1. Sistema detecta conflicto de calendario
2. Sistema previene aceptación de solicitud
3. Sistema notifica conflicto al músico
4. Sistema sugiere fechas alternativas
5. Sistema registra intento de conflicto
```

---

## 📈 **MÉTRICAS Y KPIs DEL NEGOCIO**

### **🎯 Métricas de Solicitudes**
- **Tasa de Creación**: Solicitudes creadas por día
- **Tasa de Aceptación**: Solicitudes aceptadas vs. creadas
- **Tiempo de Asignación**: Promedio desde creación hasta aceptación
- **Tasa de Cancelación**: Solicitudes canceladas vs. creadas

### **👥 Métricas de Usuarios**
- **Usuarios Activos**: Usuarios que usan la plataforma mensualmente
- **Retención**: Usuarios que regresan después de 30 días
- **Engagement**: Tiempo promedio de sesión
- **Satisfacción**: Calificaciones promedio de usuarios

### **💰 Métricas Financieras**
- **Presupuesto Promedio**: Promedio de presupuestos por solicitud
- **Valor Total**: Suma de todos los presupuestos activos
- **Distribución por Tipo**: Solicitudes por tipo de evento
- **Eficiencia de Matching**: Presupuesto asignado vs. solicitado

---

## 🎯 **RESUMEN DE LÓGICA DE NEGOCIO**

**MussikOn** implementa una **lógica de negocio robusta y bien definida** para el **Sistema de Solicitudes de Músicos**:

### **✅ Funcionalidades Core**
- **Creación de solicitudes** con validaciones completas
- **Sistema de matching** con algoritmos de scoring
- **Gestión de estados** del ciclo de vida completo
- **Comunicación en tiempo real** entre partes

### **🔒 Seguridad y Autorización**
- **Control de acceso granular** por roles y permisos
- **Validaciones de seguridad** en todas las operaciones
- **Auditoría completa** de acciones críticas
- **Prevención de conflictos** y violaciones

### **📊 Métricas y Monitoreo**
- **KPIs del negocio** bien definidos
- **Logging estructurado** de todas las operaciones
- **Métricas de performance** y satisfacción
- **Alertas automáticas** para eventos críticos

**Objetivo**: Crear un sistema confiable, seguro y eficiente que conecte músicos con organizadores de eventos de manera transparente y profesional.

---

*Última actualización: Diciembre 2024*
*Versión: v1.0.0*
*Enfoque: Lógica de Negocio para Sistema de Solicitudes de Músicos*
