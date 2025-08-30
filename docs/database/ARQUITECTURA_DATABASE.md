# 🗄️ **ARQUITECTURA DE BASE DE DATOS - MUSSIKON**

## 📋 **Índice de la Arquitectura de Base de Datos**

### **🏗️ Arquitectura General**
- [Modelo de Datos](./ARQUITECTURA_DATABASE.md#modelo-de-datos) - Entidades y relaciones
- [Patrones de Diseño](./ARQUITECTURA_DATABASE.md#patrones-de-diseño) - Patrones de base de datos
- [Estructura de Tablas](./ARQUITECTURA_DATABASE.md#estructura-de-tablas) - Organización de esquemas

### **🔧 Implementación Técnica**
- [Supabase](./ARQUITECTURA_DATABASE.md#supabase) - Configuración y servicios
- [Seguridad](./ARQUITECTURA_DATABASE.md#seguridad) - RLS y políticas
- [Performance](./ARQUITECTURA_DATABASE.md#performance) - Optimización y índices

---

## 🎯 **Propósito de la Arquitectura de Base de Datos**

### **Objetivo Principal**
Implementar una base de datos PostgreSQL robusta y escalable que soporte el **Sistema de Solicitudes de Músicos** con funcionalidades avanzadas de seguridad, real-time y APIs automáticas.

### **Principios Clave**
- **Normalización**: Estructura normalizada hasta 3FN/BCNF
- **Seguridad**: Row Level Security (RLS) y políticas granulares
- **Performance**: Índices optimizados y queries eficientes
- **Escalabilidad**: Preparada para crecer con el negocio
- **Mantenibilidad**: Esquemas claros y documentados

---

## 🏗️ **Modelo de Datos**

### **📊 Diagrama de Entidades**
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   user_profiles │    │ musician_requests│    │ request_apps    │
├─────────────────┤    ├──────────────────┤    ├─────────────────┤
│ id (PK)         │    │ id (PK)          │    │ id (PK)         │
│ user_id (FK)    │    │ organizer_id(FK) │    │ request_id (FK) │
│ name            │    │ title            │    │ musician_id(FK) │
│ email           │    │ description      │    │ status          │
│ role            │    │ instrument       │    │ applied_at      │
│ status          │    │ budget_min       │    │ accepted_at     │
│ created_at      │    │ budget_max       │    │ created_at      │
│ updated_at      │    │ event_date       │    │ updated_at      │
└─────────────────┘    │ location         │    └─────────────────┘
                       │ status           │
                       │ created_at       │
                       │ updated_at       │
                       └──────────────────┘
```

### **🔗 Relaciones Principales**
- **User Profiles** ↔ **Musician Requests** (1:N)
- **Musician Requests** ↔ **Request Applications** (1:N)
- **User Profiles** ↔ **Request Applications** (1:N)
- **User Profiles** ↔ **Conversations** (1:N)
- **Conversations** ↔ **Messages** (1:N)

---

## 🗄️ **Estructura de Tablas**

### **👥 Gestión de Usuarios**
```sql
-- Tabla principal de perfiles de usuario
CREATE TABLE user_profiles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20),
    role user_role NOT NULL DEFAULT 'musician',
    status user_status NOT NULL DEFAULT 'active',
    profile_image_url TEXT,
    bio TEXT,
    location JSONB, -- {city, state, country, coordinates}
    instruments TEXT[], -- Array de instrumentos
    experience_years INTEGER DEFAULT 0,
    hourly_rate DECIMAL(10,2),
    availability JSONB, -- Horarios disponibles
    verification_status verification_status DEFAULT 'pending',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabla de roles de usuario
CREATE TYPE user_role AS ENUM (
    'musician',
    'organizer',
    'admin',
    'moderator'
);

-- Tabla de estados de usuario
CREATE TYPE user_status AS ENUM (
    'active',
    'inactive',
    'suspended',
    'banned'
);

-- Tabla de estados de verificación
CREATE TYPE verification_status AS ENUM (
    'pending',
    'verified',
    'rejected'
);
```

### **🎵 Sistema de Solicitudes (CORE)**
```sql
-- Tabla principal de solicitudes de músicos
CREATE TABLE musician_requests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organizer_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    instrument VARCHAR(100) NOT NULL,
    genre VARCHAR(100),
    budget_min DECIMAL(10,2) NOT NULL,
    budget_max DECIMAL(10,2) NOT NULL,
    event_date TIMESTAMP WITH TIME ZONE NOT NULL,
    duration_hours INTEGER DEFAULT 1,
    location JSONB NOT NULL, -- {address, city, coordinates}
    requirements TEXT[], -- Array de requisitos
    status request_status NOT NULL DEFAULT 'open',
    priority request_priority DEFAULT 'normal',
    max_applications INTEGER DEFAULT 10,
    application_deadline TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabla de estados de solicitud
CREATE TYPE request_status AS ENUM (
    'open',
    'in_review',
    'assigned',
    'completed',
    'cancelled',
    'expired'
);

-- Tabla de prioridades de solicitud
CREATE TYPE request_priority AS ENUM (
    'low',
    'normal',
    'high',
    'urgent'
);

-- Tabla de aplicaciones a solicitudes
CREATE TABLE request_applications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    request_id UUID NOT NULL REFERENCES musician_requests(id) ON DELETE CASCADE,
    musician_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    status application_status NOT NULL DEFAULT 'pending',
    proposed_rate DECIMAL(10,2),
    message TEXT,
    availability_confirmed BOOLEAN DEFAULT FALSE,
    applied_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    accepted_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    UNIQUE(request_id, musician_id)
);

-- Tabla de estados de aplicación
CREATE TYPE application_status AS ENUM (
    'pending',
    'accepted',
    'rejected',
    'withdrawn'
);
```

### **💬 Sistema de Comunicación**
```sql
-- Tabla de conversaciones
CREATE TABLE conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    request_id UUID REFERENCES musician_requests(id) ON DELETE CASCADE,
    organizer_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    musician_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    status conversation_status DEFAULT 'active',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    UNIQUE(request_id, organizer_id, musician_id)
);

-- Tabla de estados de conversación
CREATE TYPE conversation_status AS ENUM (
    'active',
    'archived',
    'blocked'
);

-- Tabla de mensajes
CREATE TABLE messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    conversation_id UUID NOT NULL REFERENCES conversations(id) ON DELETE CASCADE,
    sender_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    content TEXT NOT NULL,
    message_type message_type DEFAULT 'text',
    read_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabla de tipos de mensaje
CREATE TYPE message_type AS ENUM (
    'text',
    'image',
    'file',
    'system'
);
```

### **🔔 Sistema de Notificaciones**
```sql
-- Tabla de notificaciones
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    type notification_type NOT NULL,
    title VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    data JSONB, -- Datos adicionales específicos del tipo
    read_at TIMESTAMP WITH TIME ZONE,
    sent_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabla de tipos de notificación
CREATE TYPE notification_type AS ENUM (
    'request_update',
    'new_application',
    'application_accepted',
    'application_rejected',
    'new_message',
    'payment_received',
    'system_alert'
);
```

### **📊 Auditoría y Logs**
```sql
-- Tabla de logs de auditoría
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES user_profiles(id),
    action VARCHAR(100) NOT NULL,
    table_name VARCHAR(100) NOT NULL,
    record_id UUID,
    old_values JSONB,
    new_values JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tabla de logs de actividad de usuario
CREATE TABLE user_activity_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    activity_type VARCHAR(100) NOT NULL,
    description TEXT,
    metadata JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

---

## 🔧 **Patrones de Diseño**

### **🏗️ Patrones de Base de Datos**
- **Normalización**: Estructura normalizada hasta 3FN/BCNF
- **Soft Delete**: Marcado lógico de eliminación
- **Audit Trail**: Registro completo de cambios
- **Polymorphic Associations**: Relaciones flexibles
- **Materialized Views**: Vistas materializadas para analytics

### **📱 Patrones de Seguridad**
- **Row Level Security (RLS)**: Seguridad a nivel de fila
- **Column Level Security**: Seguridad a nivel de columna
- **Data Encryption**: Encriptación de datos sensibles
- **Access Control**: Control granular de acceso
- **Audit Logging**: Registro de todas las operaciones

---

## ⚙️ **Supabase**

### **🔧 Configuración de Supabase**
```typescript
// lib/supabase.ts
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL!;
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!;

export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
  auth: {
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: true,
  },
  realtime: {
    params: {
      eventsPerSecond: 10,
    },
  },
});
```

### **🔌 Servicios de Supabase**
- **Database**: PostgreSQL 15+ con APIs automáticas
- **Auth**: Sistema de autenticación integrado
- **Storage**: Almacenamiento de archivos
- **Edge Functions**: Lógica de negocio en el edge
- **Real-time**: Suscripciones en tiempo real

---

## 🔒 **Seguridad**

### **🛡️ Row Level Security (RLS)**
```sql
-- Habilitar RLS en todas las tablas
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE musician_requests ENABLE ROW LEVEL SECURITY;
ALTER TABLE request_applications ENABLE ROW LEVEL SECURITY;
ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;

-- Política: Usuarios pueden ver su propio perfil
CREATE POLICY "Users can view own profile" ON user_profiles
    FOR SELECT USING (auth.uid() = user_id);

-- Política: Usuarios pueden editar su propio perfil
CREATE POLICY "Users can edit own profile" ON user_profiles
    FOR UPDATE USING (auth.uid() = user_id);

-- Política: Organizadores pueden crear solicitudes
CREATE POLICY "Organizers can create requests" ON musician_requests
    FOR INSERT WITH CHECK (
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE id = auth.uid() AND role = 'organizer'
        )
    );

-- Política: Usuarios pueden ver solicitudes públicas
CREATE POLICY "Users can view public requests" ON musician_requests
    FOR SELECT USING (status IN ('open', 'in_review'));

-- Política: Músicos pueden aplicar a solicitudes
CREATE POLICY "Musicians can apply to requests" ON request_applications
    FOR INSERT WITH CHECK (
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE id = auth.uid() AND role = 'musician'
        )
    );
```

### **🔐 Autenticación y Autorización**
```sql
-- Función para verificar rol de usuario
CREATE OR REPLACE FUNCTION check_user_role(required_role user_role)
RETURNS BOOLEAN AS $$
BEGIN
    RETURN EXISTS (
        SELECT 1 FROM user_profiles 
        WHERE user_id = auth.uid() AND role = required_role
    );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Función para verificar propiedad de recurso
CREATE OR REPLACE FUNCTION is_resource_owner(resource_table TEXT, resource_id UUID)
RETURNS BOOLEAN AS $$
BEGIN
    EXECUTE format('
        SELECT EXISTS (
            SELECT 1 FROM %I 
            WHERE id = $1 AND organizer_id = $2
        )', resource_table)
    INTO result
    USING resource_id, auth.uid();
    
    RETURN result;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## ⚡ **Performance**

### **📊 Estrategias de Optimización**
- **Índices Compuestos**: Para queries complejas
- **Índices Parciales**: Para datos filtrados
- **Índices de Expresión**: Para funciones y cálculos
- **Partitioning**: Particionamiento por fecha
- **Connection Pooling**: Gestión eficiente de conexiones

### **🔍 Índices Optimizados**
```sql
-- Índices para búsqueda de solicitudes
CREATE INDEX idx_musician_requests_status_date ON musician_requests(status, event_date);
CREATE INDEX idx_musician_requests_location ON musician_requests USING GIN(location);
CREATE INDEX idx_musician_requests_instrument ON musician_requests(instrument);
CREATE INDEX idx_musician_requests_budget ON musician_requests(budget_min, budget_max);

-- Índices para búsqueda de usuarios
CREATE INDEX idx_user_profiles_role_status ON user_profiles(role, status);
CREATE INDEX idx_user_profiles_location ON user_profiles USING GIN(location);
CREATE INDEX idx_user_profiles_instruments ON user_profiles USING GIN(instruments);

-- Índices para aplicaciones
CREATE INDEX idx_request_applications_status ON request_applications(status);
CREATE INDEX idx_request_applications_request_musician ON request_applications(request_id, musician_id);

-- Índices para mensajes
CREATE INDEX idx_messages_conversation_date ON messages(conversation_id, created_at);
CREATE INDEX idx_messages_sender ON messages(sender_id);

-- Índices para notificaciones
CREATE INDEX idx_notifications_user_unread ON notifications(user_id, read_at) WHERE read_at IS NULL;
CREATE INDEX idx_notifications_type_date ON notifications(type, created_at);
```

### **📈 Materialized Views para Analytics**
```sql
-- Vista materializada para estadísticas de solicitudes
CREATE MATERIALIZED VIEW mv_request_stats AS
SELECT 
    DATE_TRUNC('day', created_at) as date,
    COUNT(*) as total_requests,
    COUNT(CASE WHEN status = 'open' THEN 1 END) as open_requests,
    COUNT(CASE WHEN status = 'completed' THEN 1 END) as completed_requests,
    AVG(budget_max - budget_min) as avg_budget_range
FROM musician_requests
GROUP BY DATE_TRUNC('day', created_at)
ORDER BY date DESC;

-- Vista materializada para estadísticas de usuarios
CREATE MATERIALIZED VIEW mv_user_stats AS
SELECT 
    role,
    COUNT(*) as total_users,
    COUNT(CASE WHEN status = 'active' THEN 1 END) as active_users,
    AVG(experience_years) as avg_experience
FROM user_profiles
GROUP BY role;

-- Función para refrescar vistas materializadas
CREATE OR REPLACE FUNCTION refresh_analytics_views()
RETURNS VOID AS $$
BEGIN
    REFRESH MATERIALIZED VIEW mv_request_stats;
    REFRESH MATERIALIZED VIEW mv_user_stats;
END;
$$ LANGUAGE plpgsql;
```

---

## 🔄 **Real-time y Notificaciones**

### **📡 Suscripciones en Tiempo Real**
```sql
-- Función para notificar cambios en solicitudes
CREATE OR REPLACE FUNCTION notify_request_change()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        PERFORM pg_notify(
            'request_changes',
            json_build_object(
                'operation', 'INSERT',
                'request_id', NEW.id,
                'organizer_id', NEW.organizer_id
            )::text
        );
    ELSIF TG_OP = 'UPDATE' THEN
        PERFORM pg_notify(
            'request_changes',
            json_build_object(
                'operation', 'UPDATE',
                'request_id', NEW.id,
                'organizer_id', NEW.organizer_id,
                'old_status', OLD.status,
                'new_status', NEW.status
            )::text
        );
    ELSIF TG_OP = 'DELETE' THEN
        PERFORM pg_notify(
            'request_changes',
            json_build_object(
                'operation', 'DELETE',
                'request_id', OLD.id,
                'organizer_id', OLD.organizer_id
            )::text
        );
    END IF;
    
    RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql;

-- Trigger para notificaciones de solicitudes
CREATE TRIGGER trigger_notify_request_change
    AFTER INSERT OR UPDATE OR DELETE ON musician_requests
    FOR EACH ROW EXECUTE FUNCTION notify_request_change();
```

---

## 🧪 **Testing y Calidad**

### **📊 Estrategia de Testing**
- **pgTAP**: Framework de testing para PostgreSQL
- **Data Validation**: Validación de integridad de datos
- **Performance Testing**: Testing de rendimiento de queries
- **Security Testing**: Testing de políticas RLS

### **🔧 Herramientas de Testing**
```sql
-- Función para testing de políticas RLS
CREATE OR REPLACE FUNCTION test_rls_policies()
RETURNS TABLE(test_name TEXT, passed BOOLEAN) AS $$
BEGIN
    -- Test 1: Usuario puede ver su propio perfil
    RETURN QUERY
    SELECT 
        'User can view own profile'::TEXT,
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE user_id = auth.uid()
        );
    
    -- Test 2: Usuario no puede ver perfiles de otros
    RETURN QUERY
    SELECT 
        'User cannot view other profiles'::TEXT,
        NOT EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE user_id != auth.uid()
        );
    
    -- Más tests...
END;
$$ LANGUAGE plpgsql;
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Supabase Documentation](https://supabase.com/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Row Level Security Guide](https://supabase.com/docs/guides/auth/row-level-security)

### **📖 Documentación Relacionada**
- [Stack Tecnológico Database](./STACK_TECNOLOGICO_DATABASE.md)
- [Estructura de Datos Completa](./ESTRUCTURA_DATOS_COMPLETA.md)
- [Análisis Crítico de Seguridad](./ANALISIS_SEGURIDAD_CRITICO.md)

---

*Esta arquitectura de base de datos proporciona una base sólida para el Sistema de Solicitudes de Músicos, con funcionalidades avanzadas de seguridad, performance y escalabilidad.*
