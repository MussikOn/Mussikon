# 🎯 **SISTEMA DE SOLICITUDES BASE DE DATOS - MUSSIKON**

## 📋 **Índice del Sistema de Solicitudes de Base de Datos**

### **🏗️ Diseño de Base de Datos**
- [Esquema de Tablas](./SOLICITUDES_DATABASE.md#esquema-de-tablas) - Estructura de datos
- [Relaciones y Constraints](./SOLICITUDES_DATABASE.md#relaciones-y-constraints) - Integridad referencial
- [Índices y Performance](./SOLICITUDES_DATABASE.md#índices-y-performance) - Optimización

### **🔧 Implementación Técnica**
- [Políticas RLS](./SOLICITUDES_DATABASE.md#políticas-rls) - Row Level Security
- [Funciones y Triggers](./SOLICITUDES_DATABASE.md#funciones-y-triggers) - Lógica de negocio
- [APIs y Endpoints](./SOLICITUDES_DATABASE.md#apis-y-endpoints) - Supabase Functions

---

## 🎯 **Propósito del Sistema de Solicitudes de Base de Datos**

### **Objetivo Principal**
Implementar la estructura de base de datos completa para el sistema de solicitudes de músicos en Supabase PostgreSQL, asegurando seguridad, performance y escalabilidad para el manejo de todas las operaciones del sistema.

### **Funcionalidades Clave**
- **Gestión de Solicitudes**: Almacenamiento y consulta eficiente
- **Seguridad de Datos**: Row Level Security y encriptación
- **Performance**: Índices optimizados y queries eficientes
- **Escalabilidad**: Estructura preparada para crecimiento
- **Auditoría**: Trazabilidad completa de cambios

---

## 🏗️ **Esquema de Tablas**

### **📊 Tabla Principal de Solicitudes**
```sql
-- Tabla principal de solicitudes de músicos
CREATE TABLE musician_requests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Información básica
    title VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    instrument VARCHAR(100) NOT NULL,
    genre VARCHAR(100),
    
    -- Presupuesto y duración
    budget_min DECIMAL(10,2) NOT NULL CHECK (budget_min > 0),
    budget_max DECIMAL(10,2) NOT NULL CHECK (budget_max >= budget_min),
    duration_hours INTEGER NOT NULL CHECK (duration_hours > 0),
    
    -- Fechas
    event_date TIMESTAMP WITH TIME ZONE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    expires_at TIMESTAMP WITH TIME ZONE,
    
    -- Ubicación
    location JSONB NOT NULL,
    coordinates POINT,
    
    -- Estado y moderación
    status request_status NOT NULL DEFAULT 'open',
    priority priority_level DEFAULT 'normal',
    is_featured BOOLEAN DEFAULT false,
    is_urgent BOOLEAN DEFAULT false,
    
    -- Organizador
    organizer_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    
    -- Requisitos y preferencias
    requirements TEXT[],
    preferred_style VARCHAR(100),
    experience_level experience_level DEFAULT 'any',
    
    -- Metadatos
    tags TEXT[],
    views_count INTEGER DEFAULT 0,
    applications_count INTEGER DEFAULT 0,
    
    -- Moderación
    moderation_status moderation_status DEFAULT 'pending',
    moderation_notes TEXT,
    moderated_by UUID REFERENCES user_profiles(id),
    moderated_at TIMESTAMP WITH TIME ZONE,
    
    -- Auditoría
    created_by UUID NOT NULL REFERENCES user_profiles(id),
    updated_by UUID REFERENCES user_profiles(id),
    
    -- Constraints adicionales
    CONSTRAINT valid_event_date CHECK (event_date > NOW()),
    CONSTRAINT valid_budget_range CHECK (budget_max >= budget_min),
    CONSTRAINT valid_duration CHECK (duration_hours BETWEEN 1 AND 48)
);

-- Comentarios para documentación
COMMENT ON TABLE musician_requests IS 'Solicitudes de músicos para eventos';
COMMENT ON COLUMN musician_requests.location IS 'JSON con dirección, ciudad, estado, país y coordenadas';
COMMENT ON COLUMN musician_requests.coordinates IS 'Punto geográfico para búsquedas espaciales';
COMMENT ON COLUMN musician_requests.requirements IS 'Array de requisitos específicos para el músico';
COMMENT ON COLUMN musician_requests.tags IS 'Tags para categorización y búsqueda';
```

### **📝 Tabla de Aplicaciones**
```sql
-- Tabla de aplicaciones de músicos a solicitudes
CREATE TABLE request_applications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Relaciones
    request_id UUID NOT NULL REFERENCES musician_requests(id) ON DELETE CASCADE,
    musician_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    
    -- Propuesta del músico
    proposed_rate DECIMAL(10,2) NOT NULL CHECK (proposed_rate > 0),
    message TEXT NOT NULL,
    availability_confirmed BOOLEAN NOT NULL DEFAULT false,
    
    -- Estado de la aplicación
    status application_status NOT NULL DEFAULT 'pending',
    
    -- Fechas
    applied_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    reviewed_at TIMESTAMP WITH TIME ZONE,
    accepted_at TIMESTAMP WITH TIME ZONE,
    rejected_at TIMESTAMP WITH TIME ZONE,
    
    -- Feedback y calificaciones
    organizer_rating INTEGER CHECK (organizer_rating BETWEEN 1 AND 5),
    organizer_feedback TEXT,
    musician_rating INTEGER CHECK (musician_rating BETWEEN 1 AND 5),
    musician_feedback TEXT,
    
    -- Metadatos
    is_favorite BOOLEAN DEFAULT false,
    notes TEXT,
    
    -- Auditoría
    created_by UUID NOT NULL REFERENCES user_profiles(id),
    updated_by UUID REFERENCES user_profiles(id),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints
    CONSTRAINT unique_musician_request UNIQUE (request_id, musician_id),
    CONSTRAINT valid_rating CHECK (organizer_rating IS NULL OR (organizer_rating BETWEEN 1 AND 5)),
    CONSTRAINT valid_musician_rating CHECK (musician_rating IS NULL OR (musician_rating BETWEEN 1 AND 5))
);

-- Comentarios
COMMENT ON TABLE request_applications IS 'Aplicaciones de músicos a solicitudes';
COMMENT ON COLUMN request_applications.proposed_rate IS 'Tarifa propuesta por el músico';
COMMENT ON COLUMN request_applications.message IS 'Mensaje de presentación del músico';
```

### **💬 Tabla de Conversaciones**
```sql
-- Tabla de conversaciones entre organizadores y músicos
CREATE TABLE conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Participantes
    request_id UUID NOT NULL REFERENCES musician_requests(id) ON DELETE CASCADE,
    organizer_id UUID NOT NULL REFERENCES user_profiles(id),
    musician_id UUID NOT NULL REFERENCES user_profiles(id),
    
    -- Estado de la conversación
    status conversation_status DEFAULT 'active',
    is_archived BOOLEAN DEFAULT false,
    
    -- Metadatos
    last_message_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    message_count INTEGER DEFAULT 0,
    
    -- Fechas
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints
    CONSTRAINT unique_conversation UNIQUE (request_id, organizer_id, musician_id)
);

-- Tabla de mensajes
CREATE TABLE messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Relaciones
    conversation_id UUID NOT NULL REFERENCES conversations(id) ON DELETE CASCADE,
    sender_id UUID NOT NULL REFERENCES user_profiles(id),
    
    -- Contenido del mensaje
    content TEXT NOT NULL,
    message_type message_type DEFAULT 'text',
    attachment_url TEXT,
    
    -- Estado del mensaje
    is_read BOOLEAN DEFAULT false,
    read_at TIMESTAMP WITH TIME ZONE,
    
    -- Fechas
    sent_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Metadatos
    reply_to_id UUID REFERENCES messages(id),
    is_edited BOOLEAN DEFAULT false,
    edited_at TIMESTAMP WITH TIME ZONE,
    
    -- Auditoría
    created_by UUID NOT NULL REFERENCES user_profiles(id)
);

-- Comentarios
COMMENT ON TABLE conversations IS 'Conversaciones entre organizadores y músicos';
COMMENT ON TABLE messages IS 'Mensajes individuales en las conversaciones';
```

---

## 🔗 **Relaciones y Constraints**

### **🔐 Tipos Enumerados**
```sql
-- Tipos enumerados para el sistema
CREATE TYPE request_status AS ENUM (
    'draft',           -- Borrador
    'open',            -- Abierta
    'in_review',       -- En revisión
    'assigned',        -- Asignada
    'in_progress',     -- En progreso
    'completed',       -- Completada
    'cancelled',       -- Cancelada
    'expired'          -- Expirada
);

CREATE TYPE application_status AS ENUM (
    'pending',         -- Pendiente
    'reviewed',        -- Revisada
    'accepted',        -- Aceptada
    'rejected',        -- Rechazada
    'withdrawn'        -- Retirada
);

CREATE TYPE conversation_status AS ENUM (
    'active',          -- Activa
    'paused',          -- Pausada
    'closed',           -- Cerrada
    'archived'         -- Archivada
);

CREATE TYPE message_type AS ENUM (
    'text',            -- Texto
    'image',           -- Imagen
    'audio',           -- Audio
    'file',            -- Archivo
    'system'           -- Mensaje del sistema
);

CREATE TYPE priority_level AS ENUM (
    'low',             -- Baja
    'normal',          -- Normal
    'high',            -- Alta
    'urgent'           -- Urgente
);

CREATE TYPE experience_level AS ENUM (
    'beginner',        -- Principiante
    'intermediate',    -- Intermedio
    'advanced',        -- Avanzado
    'professional',    -- Profesional
    'any'              -- Cualquiera
);

CREATE TYPE moderation_status AS ENUM (
    'pending',         -- Pendiente
    'approved',        -- Aprobada
    'rejected',        -- Rechazada
    'flagged',         -- Marcada para revisión
    'under_review'     -- En revisión
);
```

### **🔒 Constraints de Integridad**
```sql
-- Constraints adicionales para integridad de datos
ALTER TABLE musician_requests 
ADD CONSTRAINT check_budget_range 
CHECK (budget_max >= budget_min * 1.1); -- El máximo debe ser al menos 10% mayor al mínimo

ALTER TABLE musician_requests 
ADD CONSTRAINT check_event_date_future 
CHECK (event_date > NOW() + INTERVAL '1 hour'); -- El evento debe ser al menos 1 hora en el futuro

ALTER TABLE request_applications 
ADD CONSTRAINT check_proposed_rate_range 
CHECK (
    proposed_rate >= (
        SELECT budget_min FROM musician_requests WHERE id = request_id
    ) AND 
    proposed_rate <= (
        SELECT budget_max FROM musician_requests WHERE id = request_id
    )
);

-- Trigger para actualizar contadores
CREATE OR REPLACE FUNCTION update_request_counters()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        -- Incrementar contador de aplicaciones
        UPDATE musician_requests 
        SET applications_count = applications_count + 1 
        WHERE id = NEW.request_id;
        
        -- Incrementar contador de mensajes
        UPDATE conversations 
        SET message_count = message_count + 1 
        WHERE id = NEW.conversation_id;
        
    ELSIF TG_OP = 'DELETE' THEN
        -- Decrementar contador de aplicaciones
        UPDATE musician_requests 
        SET applications_count = applications_count - 1 
        WHERE id = OLD.request_id;
        
        -- Decrementar contador de mensajes
        UPDATE conversations 
        SET message_count = message_count - 1 
        WHERE id = OLD.conversation_id;
    END IF;
    
    RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql;

-- Aplicar triggers
CREATE TRIGGER trigger_update_request_counters
    AFTER INSERT OR DELETE ON request_applications
    FOR EACH ROW EXECUTE FUNCTION update_request_counters();

CREATE TRIGGER trigger_update_message_counters
    AFTER INSERT OR DELETE ON messages
    FOR EACH ROW EXECUTE FUNCTION update_request_counters();
```

---

## 📈 **Índices y Performance**

### **🚀 Índices Optimizados**
```sql
-- Índices para búsquedas frecuentes
CREATE INDEX idx_musician_requests_status ON musician_requests(status);
CREATE INDEX idx_musician_requests_instrument ON musician_requests(instrument);
CREATE INDEX idx_musician_requests_event_date ON musician_requests(event_date);
CREATE INDEX idx_musician_requests_organizer ON musician_requests(organizer_id);
CREATE INDEX idx_musician_requests_location ON musician_requests USING GIN(location);
CREATE INDEX idx_musician_requests_coordinates ON musician_requests USING GIST(coordinates);
CREATE INDEX idx_musician_requests_budget ON musician_requests(budget_min, budget_max);
CREATE INDEX idx_musician_requests_created_at ON musician_requests(created_at DESC);
CREATE INDEX idx_musician_requests_featured ON musician_requests(is_featured) WHERE is_featured = true;
CREATE INDEX idx_musician_requests_urgent ON musician_requests(is_urgent) WHERE is_urgent = true;

-- Índices para aplicaciones
CREATE INDEX idx_applications_request ON request_applications(request_id);
CREATE INDEX idx_applications_musician ON request_applications(musician_id);
CREATE INDEX idx_applications_status ON request_applications(status);
CREATE INDEX idx_applications_applied_at ON request_applications(applied_at DESC);

-- Índices para conversaciones
CREATE INDEX idx_conversations_request ON conversations(request_id);
CREATE INDEX idx_conversations_participants ON conversations(organizer_id, musician_id);
CREATE INDEX idx_conversations_last_message ON conversations(last_message_at DESC);

-- Índices para mensajes
CREATE INDEX idx_messages_conversation ON messages(conversation_id);
CREATE INDEX idx_messages_sender ON messages(sender_id);
CREATE INDEX idx_messages_sent_at ON messages(sent_at DESC);
CREATE INDEX idx_messages_unread ON messages(is_read) WHERE is_read = false;

-- Índices compuestos para queries complejas
CREATE INDEX idx_requests_search ON musician_requests 
USING GIN(to_tsvector('spanish', title || ' ' || description || ' ' || instrument));

CREATE INDEX idx_requests_location_status ON musician_requests(status, coordinates) 
WHERE status = 'open';

CREATE INDEX idx_applications_request_status ON request_applications(request_id, status);
```

### **⚡ Queries Optimizadas**
```sql
-- Query optimizada para búsqueda de solicitudes
CREATE OR REPLACE FUNCTION search_musician_requests(
    search_term TEXT DEFAULT '',
    instrument_filter TEXT DEFAULT '',
    location_filter TEXT DEFAULT '',
    budget_min_filter DECIMAL DEFAULT 0,
    budget_max_filter DECIMAL DEFAULT 999999,
    date_from TIMESTAMP WITH TIME ZONE DEFAULT NULL,
    date_to TIMESTAMP WITH TIME ZONE DEFAULT NULL,
    limit_count INTEGER DEFAULT 20,
    offset_count INTEGER DEFAULT 0
)
RETURNS TABLE (
    id UUID,
    title VARCHAR,
    description TEXT,
    instrument VARCHAR,
    budget_min DECIMAL,
    budget_max DECIMAL,
    event_date TIMESTAMP WITH TIME ZONE,
    location JSONB,
    organizer_name VARCHAR,
    applications_count INTEGER,
    distance_km DECIMAL
) AS $$
BEGIN
    RETURN QUERY
    SELECT 
        mr.id,
        mr.title,
        mr.description,
        mr.instrument,
        mr.budget_min,
        mr.budget_max,
        mr.event_date,
        mr.location,
        up.name as organizer_name,
        mr.applications_count,
        CASE 
            WHEN location_filter IS NOT NULL THEN
                ST_Distance(
                    mr.coordinates::geography,
                    ST_GeomFromText(location_filter)::geography
                ) / 1000
            ELSE NULL
        END as distance_km
    FROM musician_requests mr
    JOIN user_profiles up ON mr.organizer_id = up.id
    WHERE mr.status = 'open'
        AND (search_term = '' OR 
             to_tsvector('spanish', mr.title || ' ' || mr.description) @@ plainto_tsquery('spanish', search_term))
        AND (instrument_filter = '' OR mr.instrument = instrument_filter)
        AND mr.budget_min >= budget_min_filter
        AND mr.budget_max <= budget_max_filter
        AND (date_from IS NULL OR mr.event_date >= date_from)
        AND (date_to IS NULL OR mr.event_date <= date_to)
    ORDER BY 
        CASE WHEN search_term != '' THEN 
            ts_rank(to_tsvector('spanish', mr.title || ' ' || mr.description), plainto_tsquery('spanish', search_term))
        ELSE 0 END DESC,
        mr.is_featured DESC,
        mr.is_urgent DESC,
        mr.created_at DESC
    LIMIT limit_count
    OFFSET offset_count;
END;
$$ LANGUAGE plpgsql STABLE;

-- Query para estadísticas de solicitudes
CREATE OR REPLACE FUNCTION get_requests_statistics(
    date_from TIMESTAMP WITH TIME ZONE DEFAULT NOW() - INTERVAL '30 days',
    date_to TIMESTAMP WITH TIME ZONE DEFAULT NOW()
)
RETURNS TABLE (
    total_requests INTEGER,
    open_requests INTEGER,
    completed_requests INTEGER,
    cancelled_requests INTEGER,
    total_applications INTEGER,
    avg_applications_per_request DECIMAL,
    total_budget DECIMAL,
    avg_budget DECIMAL,
    top_instruments TEXT[],
    completion_rate DECIMAL
) AS $$
BEGIN
    RETURN QUERY
    WITH stats AS (
        SELECT 
            COUNT(*) as total_requests,
            COUNT(*) FILTER (WHERE status = 'open') as open_requests,
            COUNT(*) FILTER (WHERE status = 'completed') as completed_requests,
            COUNT(*) FILTER (WHERE status = 'cancelled') as cancelled_requests,
            SUM(budget_min + budget_max) / 2 as total_budget,
            AVG((budget_min + budget_max) / 2) as avg_budget
        FROM musician_requests
        WHERE created_at BETWEEN date_from AND date_to
    ),
    app_stats AS (
        SELECT 
            COUNT(*) as total_applications,
            AVG(app_count) as avg_applications_per_request
        FROM (
            SELECT COUNT(*) as app_count
            FROM request_applications ra
            JOIN musician_requests mr ON ra.request_id = mr.id
            WHERE mr.created_at BETWEEN date_from AND date_to
            GROUP BY ra.request_id
        ) app_counts
    ),
    instrument_stats AS (
        SELECT ARRAY_AGG(instrument ORDER BY count DESC LIMIT 5) as top_instruments
        FROM (
            SELECT instrument, COUNT(*) as count
            FROM musician_requests
            WHERE created_at BETWEEN date_from AND date_to
            GROUP BY instrument
            ORDER BY count DESC
        ) instrument_counts
    )
    SELECT 
        s.total_requests,
        s.open_requests,
        s.completed_requests,
        s.cancelled_requests,
        COALESCE(a.total_applications, 0),
        COALESCE(a.avg_applications_per_request, 0),
        s.total_budget,
        s.avg_budget,
        i.top_instruments,
        CASE 
            WHEN s.total_requests > 0 THEN 
                (s.completed_requests::DECIMAL / s.total_requests) * 100
            ELSE 0 
        END as completion_rate
    FROM stats s
    CROSS JOIN app_stats a
    CROSS JOIN instrument_stats i;
END;
$$ LANGUAGE plpgsql STABLE;
```

---

## 🔐 **Políticas RLS**

### **🛡️ Políticas de Seguridad**
```sql
-- Habilitar RLS en todas las tablas
ALTER TABLE musician_requests ENABLE ROW LEVEL SECURITY;
ALTER TABLE request_applications ENABLE ROW LEVEL SECURITY;
ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;

-- Políticas para musician_requests
CREATE POLICY "Anyone can view open requests" ON musician_requests
    FOR SELECT USING (status = 'open');

CREATE POLICY "Users can view own requests" ON musician_requests
    FOR SELECT USING (auth.uid() = organizer_id);

CREATE POLICY "Organizers can create requests" ON musician_requests
    FOR INSERT WITH CHECK (auth.uid() = organizer_id);

CREATE POLICY "Organizers can update own requests" ON musician_requests
    FOR UPDATE USING (auth.uid() = organizer_id);

CREATE POLICY "Organizers can delete own requests" ON musician_requests
    FOR DELETE USING (auth.uid() = organizer_id);

CREATE POLICY "Admins can manage all requests" ON musician_requests
    FOR ALL USING (
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE id = auth.uid() AND role = 'admin'
        )
    );

-- Políticas para request_applications
CREATE POLICY "Musicians can view own applications" ON request_applications
    FOR SELECT USING (auth.uid() = musician_id);

CREATE POLICY "Organizers can view applications to own requests" ON request_applications
    FOR SELECT USING (
        EXISTS (
            SELECT 1 FROM musician_requests 
            WHERE id = request_id AND organizer_id = auth.uid()
        )
    );

CREATE POLICY "Musicians can create applications" ON request_applications
    FOR INSERT WITH CHECK (auth.uid() = musician_id);

CREATE POLICY "Organizers can update applications to own requests" ON request_applications
    FOR UPDATE USING (
        EXISTS (
            SELECT 1 FROM musician_requests 
            WHERE id = request_id AND organizer_id = auth.uid()
        )
    );

-- Políticas para conversations
CREATE POLICY "Participants can view conversations" ON conversations
    FOR SELECT USING (
        auth.uid() = organizer_id OR auth.uid() = musician_id
    );

CREATE POLICY "Participants can create conversations" ON conversations
    FOR INSERT WITH CHECK (
        auth.uid() = organizer_id OR auth.uid() = musician_id
    );

-- Políticas para messages
CREATE POLICY "Participants can view messages" ON messages
    FOR SELECT USING (
        EXISTS (
            SELECT 1 FROM conversations c
            WHERE c.id = conversation_id 
            AND (c.organizer_id = auth.uid() OR c.musician_id = auth.uid())
        )
    );

CREATE POLICY "Participants can send messages" ON messages
    FOR INSERT WITH CHECK (
        EXISTS (
            SELECT 1 FROM conversations c
            WHERE c.id = conversation_id 
            AND (c.organizer_id = auth.uid() OR c.musician_id = auth.uid())
        )
    );
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Supabase Documentation](https://supabase.com/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [PostGIS Documentation](https://postgis.net/documentation/)

### **📖 Documentación Relacionada**
- [Arquitectura de Base de Datos](./ARQUITECTURA_DATABASE.md)
- [Stack Tecnológico de Base de Datos](./STACK_TECNOLOGICO_DATABASE.md)
- [Estructura de Datos](./ESTRUCTURA_DATOS_COMPLETA.md)

---

*Este sistema de solicitudes de base de datos proporciona una base sólida para el almacenamiento y gestión de datos de MussikOn, asegurando seguridad, performance y escalabilidad.*
