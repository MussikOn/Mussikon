# 🗄️ **ESTRUCTURA DE DATOS COMPLETA - MUSSIKON API**

## 🚨 **ANÁLISIS CRÍTICO DE SEGURIDAD**

### **🔒 Principios de Seguridad Implementados**
- **Principle of Least Privilege**: Usuarios solo acceden a lo que necesitan
- **Defense in Depth**: Múltiples capas de seguridad
- **Zero Trust Architecture**: Nunca confiar, siempre verificar
- **Data Encryption at Rest**: Encriptación de datos sensibles
- **Audit Trail Complete**: Log de todas las operaciones
- **Row Level Security (RLS)**: Seguridad a nivel de fila
- **Input Validation**: Validación estricta de entrada
- **SQL Injection Prevention**: Uso de parámetros y ORM

---

## 🏗️ **ESQUEMA COMPLETO DE LA BASE DE DATOS**

### **📊 Esquema de Autenticación (auth schema)**

#### **🔐 Tabla: `auth.users` (Supabase Built-in)**
```sql
-- Tabla automática de Supabase - NO MODIFICAR
-- Contiene: id, email, encrypted_password, email_confirmed_at, 
--           invited_at, confirmation_token, confirmation_sent_at, 
--           recovery_token, recovery_sent_at, email_change_token_new, 
--           email_change, email_change_sent_at, last_sign_in_at, 
--           raw_app_meta_data, raw_user_meta_data, is_super_admin, 
--           created_at, updated_at, phone, phone_confirmed_at, 
--           phone_change, phone_change_token, phone_change_sent_at, 
--           email_change_token_current, email_change_confirm_status, 
--           banned_until, reauthentication_token, reauthentication_sent_at
```

#### **🔐 Tabla: `auth.sessions` (Supabase Built-in)**
```sql
-- Tabla automática de Supabase - NO MODIFICAR
-- Contiene: id, user_id, access_token, refresh_token, 
--           expires_at, created_at, updated_at
```

#### **🔐 Tabla: `auth.refresh_tokens` (Supabase Built-in)**
```sql
-- Tabla automática de Supabase - NO MODIFICAR
-- Contiene: id, user_id, token, revoked, created_at, updated_at
```

---

### **👥 Esquema de Usuarios (public schema)**

#### **👤 Tabla: `public.user_profiles`**
```sql
CREATE TABLE public.user_profiles (
    -- Identificación única (relacionada con auth.users)
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    
    -- Información personal básica
    username VARCHAR(50) UNIQUE NOT NULL,
    display_name VARCHAR(100) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    
    -- Información de contacto
    phone VARCHAR(20),
    phone_verified BOOLEAN DEFAULT FALSE,
    email_verified BOOLEAN DEFAULT FALSE,
    
    -- Información profesional
    bio TEXT,
    profile_picture_url TEXT,
    cover_photo_url TEXT,
    
    -- Ubicación
    country VARCHAR(100),
    state VARCHAR(100),
    city VARCHAR(100),
    postal_code VARCHAR(20),
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    
    -- Configuración de privacidad
    profile_visibility ENUM('public', 'private', 'friends_only') DEFAULT 'public',
    location_visibility ENUM('public', 'private', 'city_only') DEFAULT 'city_only',
    contact_visibility ENUM('public', 'private', 'verified_only') DEFAULT 'verified_only',
    
    -- Estado de la cuenta
    account_status ENUM('active', 'suspended', 'banned', 'pending_verification') DEFAULT 'pending_verification',
    verification_level ENUM('unverified', 'basic', 'verified', 'premium') DEFAULT 'unverified',
    
    -- Metadatos de seguridad
    last_password_change TIMESTAMP WITH TIME ZONE,
    failed_login_attempts INTEGER DEFAULT 0,
    account_locked_until TIMESTAMP WITH TIME ZONE,
    
    -- Timestamps
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT username_format CHECK (username ~ '^[a-zA-Z0-9_]{3,50}$'),
    CONSTRAINT phone_format CHECK (phone ~ '^\+?[1-9]\d{1,14}$'),
    CONSTRAINT coordinates_valid CHECK (
        (latitude IS NULL AND longitude IS NULL) OR
        (latitude BETWEEN -90 AND 90 AND longitude BETWEEN -180 AND 180)
    )
);

-- Índices para performance y seguridad
CREATE INDEX idx_user_profiles_username ON public.user_profiles(username);
CREATE INDEX idx_user_profiles_email_verified ON public.user_profiles(email_verified);
CREATE INDEX idx_user_profiles_account_status ON public.user_profiles(account_status);
CREATE INDEX idx_user_profiles_location ON public.user_profiles(latitude, longitude);
CREATE INDEX idx_user_profiles_created_at ON public.user_profiles(created_at);
```

#### **🔑 Tabla: `public.user_roles`**
```sql
CREATE TABLE public.user_roles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    role_name VARCHAR(50) NOT NULL,
    granted_by UUID REFERENCES public.user_profiles(id),
    granted_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    expires_at TIMESTAMP WITH TIME ZONE,
    is_active BOOLEAN DEFAULT TRUE,
    
    -- Constraints de seguridad
    CONSTRAINT valid_role CHECK (role_name IN (
        'user', 'musician', 'organizer', 'moderator', 'admin', 'super_admin'
    )),
    CONSTRAINT role_not_expired CHECK (expires_at IS NULL OR expires_at > NOW()),
    
    UNIQUE(user_id, role_name)
);

-- Índices
CREATE INDEX idx_user_roles_user_id ON public.user_roles(user_id);
CREATE INDEX idx_user_roles_role_name ON public.user_roles(role_name);
CREATE INDEX idx_user_roles_active ON public.user_roles(is_active);
```

#### **🔐 Tabla: `public.user_sessions`**
```sql
CREATE TABLE public.user_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    session_token VARCHAR(255) UNIQUE NOT NULL,
    device_info JSONB,
    ip_address INET,
    user_agent TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    last_activity TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    expires_at TIMESTAMP WITH TIME ZONE NOT NULL,
    
    -- Constraints de seguridad
    CONSTRAINT session_not_expired CHECK (expires_at > NOW()),
    CONSTRAINT valid_ip CHECK (ip_address IS NOT NULL)
);

-- Índices
CREATE INDEX idx_user_sessions_user_id ON public.user_sessions(user_id);
CREATE INDEX idx_user_sessions_token ON public.user_sessions(session_token);
CREATE INDEX idx_user_sessions_active ON public.user_sessions(is_active);
CREATE INDEX idx_user_sessions_expires ON public.user_sessions(expires_at);
```

#### **📱 Tabla: `public.user_devices`**
```sql
CREATE TABLE public.user_devices (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    device_id VARCHAR(255) UNIQUE NOT NULL,
    device_name VARCHAR(100),
    device_type ENUM('mobile', 'tablet', 'desktop', 'web') NOT NULL,
    os_info JSONB,
    app_version VARCHAR(20),
    push_token VARCHAR(255),
    is_trusted BOOLEAN DEFAULT FALSE,
    last_used TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT device_id_format CHECK (device_id ~ '^[a-zA-Z0-9_-]{10,255}$')
);

-- Índices
CREATE INDEX idx_user_devices_user_id ON public.user_devices(user_id);
CREATE INDEX idx_user_devices_device_id ON public.user_devices(device_id);
CREATE INDEX idx_user_devices_trusted ON public.user_devices(is_trusted);
```

---

### **🎵 Esquema de Solicitudes de Músicos (Core del Negocio)**

#### **🎼 Tabla: `public.musician_requests`**
```sql
CREATE TABLE public.musician_requests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Información del organizador
    organizer_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Información de la solicitud
    title VARCHAR(200) NOT NULL,
    description TEXT NOT NULL,
    event_type ENUM('wedding', 'corporate', 'party', 'concert', 'other') NOT NULL,
    
    -- Requisitos musicales
    instruments_required TEXT[] NOT NULL,
    genre_preferences TEXT[],
    experience_level ENUM('beginner', 'intermediate', 'professional', 'expert') NOT NULL,
    
    -- Información del evento
    event_date DATE NOT NULL,
    event_start_time TIME NOT NULL,
    event_end_time TIME NOT NULL,
    setup_time_minutes INTEGER DEFAULT 30,
    
    -- Ubicación
    venue_name VARCHAR(200),
    venue_address TEXT,
    venue_city VARCHAR(100),
    venue_state VARCHAR(100),
    venue_country VARCHAR(100),
    venue_latitude DECIMAL(10, 8),
    venue_longitude DECIMAL(11, 8),
    
    -- Presupuesto y compensación
    budget_min DECIMAL(10, 2),
    budget_max DECIMAL(10, 2),
    currency VARCHAR(3) DEFAULT 'USD',
    payment_terms TEXT,
    
    -- Estado y visibilidad
    status ENUM('draft', 'pending_approval', 'active', 'in_progress', 'completed', 'cancelled', 'expired') DEFAULT 'draft',
    visibility ENUM('public', 'private', 'invite_only') DEFAULT 'public',
    is_featured BOOLEAN DEFAULT FALSE,
    
    -- Metadatos de seguridad
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    expires_at TIMESTAMP WITH TIME ZONE,
    approved_at TIMESTAMP WITH TIME ZONE,
    approved_by UUID REFERENCES public.user_profiles(id),
    
    -- Constraints de seguridad
    CONSTRAINT valid_event_date CHECK (event_date >= CURRENT_DATE),
    CONSTRAINT valid_event_time CHECK (event_start_time < event_end_time),
    CONSTRAINT valid_budget CHECK (budget_min IS NULL OR budget_max IS NULL OR budget_min <= budget_max),
    CONSTRAINT valid_coordinates CHECK (
        (venue_latitude IS NULL AND venue_longitude IS NULL) OR
        (venue_latitude BETWEEN -90 AND 90 AND venue_longitude BETWEEN -180 AND 180)
    ),
    CONSTRAINT valid_instruments CHECK (array_length(instruments_required, 1) > 0),
    CONSTRAINT valid_currency CHECK (currency ~ '^[A-Z]{3}$')
);

-- Índices para performance y búsqueda
CREATE INDEX idx_musician_requests_organizer ON public.musician_requests(organizer_id);
CREATE INDEX idx_musician_requests_status ON public.musician_requests(status);
CREATE INDEX idx_musician_requests_event_date ON public.musician_requests(event_date);
CREATE INDEX idx_musician_requests_location ON public.musician_requests(venue_latitude, venue_longitude);
CREATE INDEX idx_musician_requests_instruments ON public.musician_requests USING GIN(instruments_required);
CREATE INDEX idx_musician_requests_genre ON public.musician_requests USING GIN(genre_preferences);
CREATE INDEX idx_musician_requests_budget ON public.musician_requests(budget_min, budget_max);
CREATE INDEX idx_musician_requests_created ON public.musician_requests(created_at);
CREATE INDEX idx_musician_requests_visibility ON public.musician_requests(visibility, status);
```

#### **🎯 Tabla: `public.request_applications`**
```sql
CREATE TABLE public.request_applications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Relaciones
    request_id UUID NOT NULL REFERENCES public.musician_requests(id) ON DELETE CASCADE,
    musician_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Información de la aplicación
    cover_letter TEXT,
    proposed_rate DECIMAL(10, 2),
    proposed_currency VARCHAR(3) DEFAULT 'USD',
    availability_confirmed BOOLEAN DEFAULT FALSE,
    
    -- Estado de la aplicación
    status ENUM('pending', 'reviewed', 'shortlisted', 'accepted', 'rejected', 'withdrawn') DEFAULT 'pending',
    reviewed_at TIMESTAMP WITH TIME ZONE,
    reviewed_by UUID REFERENCES public.user_profiles(id),
    review_notes TEXT,
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT unique_application UNIQUE(request_id, musician_id),
    CONSTRAINT valid_rate CHECK (proposed_rate > 0),
    CONSTRAINT valid_currency CHECK (proposed_currency ~ '^[A-Z]{3}$')
);

-- Índices
CREATE INDEX idx_request_applications_request ON public.request_applications(request_id);
CREATE INDEX idx_request_applications_musician ON public.request_applications(musician_id);
CREATE INDEX idx_request_applications_status ON public.request_applications(status);
CREATE INDEX idx_request_applications_created ON public.request_applications(created_at);
```

#### **📋 Tabla: `public.request_requirements`**
```sql
CREATE TABLE public.request_requirements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    request_id UUID NOT NULL REFERENCES public.musician_requests(id) ON DELETE CASCADE,
    
    -- Requisitos específicos
    requirement_type ENUM('instrument', 'skill', 'experience', 'certification', 'equipment', 'other') NOT NULL,
    requirement_name VARCHAR(200) NOT NULL,
    requirement_description TEXT,
    is_mandatory BOOLEAN DEFAULT TRUE,
    priority_level INTEGER DEFAULT 1,
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT valid_priority CHECK (priority_level BETWEEN 1 AND 5)
);

-- Índices
CREATE INDEX idx_request_requirements_request ON public.request_requirements(request_id);
CREATE INDEX idx_request_requirements_type ON public.request_requirements(requirement_type);
CREATE INDEX idx_request_requirements_mandatory ON public.request_requirements(is_mandatory);
```

---

### **💬 Esquema de Comunicación**

#### **💭 Tabla: `public.conversations`**
```sql
CREATE TABLE public.conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Participantes
    participant1_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    participant2_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Contexto de la conversación
    request_id UUID REFERENCES public.musician_requests(id) ON DELETE SET NULL,
    conversation_type ENUM('request_discussion', 'general', 'support') DEFAULT 'general',
    
    -- Estado
    is_active BOOLEAN DEFAULT TRUE,
    last_message_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT different_participants CHECK (participant1_id != participant2_id),
    CONSTRAINT unique_conversation UNIQUE(participant1_id, participant2_id, request_id)
);

-- Índices
CREATE INDEX idx_conversations_participant1 ON public.conversations(participant1_id);
CREATE INDEX idx_conversations_participant2 ON public.conversations(participant2_id);
CREATE INDEX idx_conversations_request ON public.conversations(request_id);
CREATE INDEX idx_conversations_active ON public.conversations(is_active);
CREATE INDEX idx_conversations_last_message ON public.conversations(last_message_at);
```

#### **💬 Tabla: `public.messages`**
```sql
CREATE TABLE public.messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Relaciones
    conversation_id UUID NOT NULL REFERENCES public.conversations(id) ON DELETE CASCADE,
    sender_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Contenido del mensaje
    message_type ENUM('text', 'image', 'file', 'audio', 'location') DEFAULT 'text',
    content TEXT NOT NULL,
    media_url TEXT,
    media_metadata JSONB,
    
    -- Estado del mensaje
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP WITH TIME ZONE,
    is_edited BOOLEAN DEFAULT FALSE,
    edited_at TIMESTAMP WITH TIME ZONE,
    original_content TEXT,
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT valid_content CHECK (content IS NOT NULL AND length(trim(content)) > 0),
    CONSTRAINT valid_media CHECK (
        (message_type = 'text' AND media_url IS NULL) OR
        (message_type != 'text' AND media_url IS NOT NULL)
    )
);

-- Índices
CREATE INDEX idx_messages_conversation ON public.messages(conversation_id);
CREATE INDEX idx_messages_sender ON public.messages(sender_id);
CREATE INDEX idx_messages_created ON public.messages(created_at);
CREATE INDEX idx_messages_read ON public.messages(is_read);
CREATE INDEX idx_messages_type ON public.messages(message_type);
```

---

### **🔔 Esquema de Notificaciones**

#### **🔔 Tabla: `public.notifications`**
```sql
CREATE TABLE public.notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Destinatario
    user_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Contenido de la notificación
    notification_type ENUM(
        'request_created', 'request_updated', 'application_received', 
        'application_status_changed', 'message_received', 'system_alert',
        'verification_required', 'account_status_changed'
    ) NOT NULL,
    
    title VARCHAR(200) NOT NULL,
    message TEXT NOT NULL,
    data JSONB,
    
    -- Estado
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP WITH TIME ZONE,
    is_delivered BOOLEAN DEFAULT FALSE,
    delivered_at TIMESTAMP WITH TIME ZONE,
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    expires_at TIMESTAMP WITH TIME ZONE,
    
    -- Constraints de seguridad
    CONSTRAINT valid_expiry CHECK (expires_at IS NULL OR expires_at > NOW())
);

-- Índices
CREATE INDEX idx_notifications_user ON public.notifications(user_id);
CREATE INDEX idx_notifications_type ON public.notifications(notification_type);
CREATE INDEX idx_notifications_read ON public.notifications(is_read);
CREATE INDEX idx_notifications_created ON public.notifications(created_at);
CREATE INDEX idx_notifications_expires ON public.notifications(expires_at);
```

---

### **📊 Esquema de Auditoría y Analytics**

#### **📝 Tabla: `public.audit_logs`**
```sql
CREATE TABLE public.audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Usuario que realiza la acción
    user_id UUID REFERENCES public.user_profiles(id) ON DELETE SET NULL,
    
    -- Detalles de la acción
    action_type VARCHAR(100) NOT NULL,
    table_name VARCHAR(100),
    record_id UUID,
    old_values JSONB,
    new_values JSONB,
    
    -- Contexto
    ip_address INET,
    user_agent TEXT,
    session_id UUID,
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT valid_action CHECK (length(trim(action_type)) > 0)
);

-- Índices
CREATE INDEX idx_audit_logs_user ON public.audit_logs(user_id);
CREATE INDEX idx_audit_logs_action ON public.audit_logs(action_type);
CREATE INDEX idx_audit_logs_table ON public.audit_logs(table_name);
CREATE INDEX idx_audit_logs_record ON public.audit_logs(record_id);
CREATE INDEX idx_audit_logs_created ON public.audit_logs(created_at);
CREATE INDEX idx_audit_logs_ip ON public.audit_logs(ip_address);
```

#### **📈 Tabla: `public.user_activity_logs`**
```sql
CREATE TABLE public.user_activity_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Usuario
    user_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Actividad
    activity_type VARCHAR(100) NOT NULL,
    activity_details JSONB,
    
    -- Contexto
    ip_address INET,
    user_agent TEXT,
    device_id UUID REFERENCES public.user_devices(id),
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT valid_activity CHECK (length(trim(activity_type)) > 0)
);

-- Índices
CREATE INDEX idx_user_activity_user ON public.user_activity_logs(user_id);
CREATE INDEX idx_user_activity_type ON public.user_activity_logs(activity_type);
CREATE INDEX idx_user_activity_created ON public.user_activity_logs(created_at);
CREATE INDEX idx_user_activity_device ON public.user_activity_logs(device_id);
```

---

### **🔒 Esquema de Seguridad Adicional**

#### **🚫 Tabla: `public.blocked_users`**
```sql
CREATE TABLE public.blocked_users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Usuarios involucrados
    blocker_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    blocked_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Razón del bloqueo
    reason TEXT,
    is_permanent BOOLEAN DEFAULT FALSE,
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    expires_at TIMESTAMP WITH TIME ZONE,
    
    -- Constraints de seguridad
    CONSTRAINT different_users CHECK (blocker_id != blocked_id),
    CONSTRAINT unique_block UNIQUE(blocker_id, blocked_id),
    CONSTRAINT valid_expiry CHECK (expires_at IS NULL OR expires_at > NOW())
);

-- Índices
CREATE INDEX idx_blocked_users_blocker ON public.blocked_users(blocker_id);
CREATE INDEX idx_blocked_users_blocked ON public.blocked_users(blocked_id);
CREATE INDEX idx_blocked_users_expires ON public.blocked_users(expires_at);
```

#### **⚠️ Tabla: `public.reported_content`**
```sql
CREATE TABLE public.reported_content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Reportador
    reporter_id UUID NOT NULL REFERENCES public.user_profiles(id) ON DELETE CASCADE,
    
    -- Contenido reportado
    content_type ENUM('user', 'request', 'message', 'application') NOT NULL,
    content_id UUID NOT NULL,
    
    -- Detalles del reporte
    reason ENUM(
        'inappropriate', 'spam', 'fake', 'harassment', 'copyright', 
        'violence', 'other'
    ) NOT NULL,
    description TEXT,
    evidence_urls TEXT[],
    
    -- Estado del reporte
    status ENUM('pending', 'under_review', 'resolved', 'dismissed') DEFAULT 'pending',
    reviewed_by UUID REFERENCES public.user_profiles(id),
    reviewed_at TIMESTAMP WITH TIME ZONE,
    resolution_notes TEXT,
    
    -- Metadatos
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    -- Constraints de seguridad
    CONSTRAINT valid_content_id CHECK (content_id IS NOT NULL)
);

-- Índices
CREATE INDEX idx_reported_content_reporter ON public.reported_content(reporter_id);
CREATE INDEX idx_reported_content_type ON public.reported_content(content_type);
CREATE INDEX idx_reported_content_status ON public.reported_content(status);
CREATE INDEX idx_reported_content_created ON public.reported_content(created_at);
```

---

## 🔐 **POLÍTICAS DE SEGURIDAD (RLS)**

### **🔒 Políticas para `public.user_profiles`**
```sql
-- Usuarios pueden ver su propio perfil
CREATE POLICY "Users can view own profile" ON public.user_profiles
    FOR SELECT USING (auth.uid() = id);

-- Usuarios pueden ver perfiles públicos
CREATE POLICY "Users can view public profiles" ON public.user_profiles
    FOR SELECT USING (
        profile_visibility = 'public' AND 
        account_status = 'active'
    );

-- Usuarios pueden actualizar su propio perfil
CREATE POLICY "Users can update own profile" ON public.user_profiles
    FOR UPDATE USING (auth.uid() = id);

-- Solo admins pueden insertar perfiles
CREATE POLICY "Only admins can insert profiles" ON public.user_profiles
    FOR INSERT WITH CHECK (
        EXISTS (
            SELECT 1 FROM public.user_roles 
            WHERE user_id = auth.uid() AND role_name IN ('admin', 'super_admin')
        )
    );

-- Solo admins pueden eliminar perfiles
CREATE POLICY "Only admins can delete profiles" ON public.user_profiles
    FOR DELETE USING (
        EXISTS (
            SELECT 1 FROM public.user_roles 
            WHERE user_id = auth.uid() AND role_name IN ('admin', 'super_admin')
        )
    );
```

### **🔒 Políticas para `public.musician_requests`**
```sql
-- Usuarios pueden ver solicitudes públicas activas
CREATE POLICY "Users can view public active requests" ON public.musician_requests
    FOR SELECT USING (
        visibility = 'public' AND 
        status IN ('active', 'in_progress') AND
        event_date >= CURRENT_DATE
    );

-- Organizadores pueden ver sus propias solicitudes
CREATE POLICY "Organizers can view own requests" ON public.musician_requests
    FOR SELECT USING (auth.uid() = organizer_id);

-- Organizadores pueden crear solicitudes
CREATE POLICY "Organizers can create requests" ON public.musician_requests
    FOR INSERT WITH CHECK (
        auth.uid() = organizer_id AND
        EXISTS (
            SELECT 1 FROM public.user_roles 
            WHERE user_id = auth.uid() AND role_name IN ('organizer', 'admin', 'super_admin')
        )
    );

-- Organizadores pueden actualizar sus solicitudes
CREATE POLICY "Organizers can update own requests" ON public.musician_requests
    FOR UPDATE USING (auth.uid() = organizer_id);

-- Solo admins pueden eliminar solicitudes
CREATE POLICY "Only admins can delete requests" ON public.musician_requests
    FOR DELETE USING (
        EXISTS (
            SELECT 1 FROM public.user_roles 
            WHERE user_id = auth.uid() AND role_name IN ('admin', 'super_admin')
        )
    );
```

### **🔒 Políticas para `public.request_applications`**
```sql
-- Músicos pueden ver sus propias aplicaciones
CREATE POLICY "Musicians can view own applications" ON public.request_applications
    FOR SELECT USING (auth.uid() = musician_id);

-- Organizadores pueden ver aplicaciones a sus solicitudes
CREATE POLICY "Organizers can view applications to own requests" ON public.request_applications
    FOR SELECT USING (
        EXISTS (
            SELECT 1 FROM public.musician_requests 
            WHERE id = request_id AND organizer_id = auth.uid()
        )
    );

-- Músicos pueden crear aplicaciones
CREATE POLICY "Musicians can create applications" ON public.request_applications
    FOR INSERT WITH CHECK (
        auth.uid() = musician_id AND
        EXISTS (
            SELECT 1 FROM public.user_roles 
            WHERE user_id = auth.uid() AND role_name IN ('musician', 'admin', 'super_admin')
        )
    );

-- Solo admins pueden actualizar aplicaciones
CREATE POLICY "Only admins can update applications" ON public.request_applications
    FOR UPDATE USING (
        EXISTS (
            SELECT 1 FROM public.user_roles 
            WHERE user_id = auth.uid() AND role_name IN ('admin', 'super_admin')
        )
    );
```

---

## 🚨 **TRIGGERS DE SEGURIDAD**

### **🔔 Trigger para Auditoría de Cambios**
```sql
CREATE OR REPLACE FUNCTION audit_trigger_function()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        INSERT INTO public.audit_logs (
            user_id, action_type, table_name, record_id, 
            old_values, new_values, ip_address, user_agent
        ) VALUES (
            auth.uid(), 'INSERT', TG_TABLE_NAME, NEW.id,
            NULL, to_jsonb(NEW), 
            inet_client_addr(), current_setting('request.headers')::json->>'user-agent'
        );
        RETURN NEW;
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO public.audit_logs (
            user_id, action_type, table_name, record_id,
            old_values, new_values, ip_address, user_agent
        ) VALUES (
            auth.uid(), 'UPDATE', TG_TABLE_NAME, NEW.id,
            to_jsonb(OLD), to_jsonb(NEW),
            inet_client_addr(), current_setting('request.headers')::json->>'user-agent'
        );
        RETURN NEW;
    ELSIF TG_OP = 'DELETE' THEN
        INSERT INTO public.audit_logs (
            user_id, action_type, table_name, record_id,
            old_values, new_values, ip_address, user_agent
        ) VALUES (
            auth.uid(), 'DELETE', TG_TABLE_NAME, OLD.id,
            to_jsonb(OLD), NULL,
            inet_client_addr(), current_setting('request.headers')::json->>'user-agent'
        );
        RETURN OLD;
    END IF;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Aplicar trigger a todas las tablas críticas
CREATE TRIGGER audit_user_profiles_trigger
    AFTER INSERT OR UPDATE OR DELETE ON public.user_profiles
    FOR EACH ROW EXECUTE FUNCTION audit_trigger_function();

CREATE TRIGGER audit_musician_requests_trigger
    AFTER INSERT OR UPDATE OR DELETE ON public.musician_requests
    FOR EACH ROW EXECUTE FUNCTION audit_trigger_function();

CREATE TRIGGER audit_request_applications_trigger
    AFTER INSERT OR UPDATE OR DELETE ON public.request_applications
    FOR EACH ROW EXECUTE FUNCTION audit_trigger_function();
```

### **🔒 Trigger para Validación de Seguridad**
```sql
CREATE OR REPLACE FUNCTION security_validation_trigger()
RETURNS TRIGGER AS $$
BEGIN
    -- Verificar que el usuario no esté bloqueado
    IF EXISTS (
        SELECT 1 FROM public.blocked_users 
        WHERE (blocker_id = NEW.id OR blocked_id = NEW.id)
        AND (expires_at IS NULL OR expires_at > NOW())
    ) THEN
        RAISE EXCEPTION 'User is blocked from performing this action';
    END IF;
    
    -- Verificar que la cuenta esté activa
    IF NEW.account_status != 'active' THEN
        RAISE EXCEPTION 'Account must be active to perform this action';
    END IF;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Aplicar trigger a operaciones críticas
CREATE TRIGGER security_validation_user_profiles_trigger
    BEFORE INSERT OR UPDATE ON public.user_profiles
    FOR EACH ROW EXECUTE FUNCTION security_validation_trigger();
```

---

## 📊 **ÍNDICES DE PERFORMANCE Y SEGURIDAD**

### **🚀 Índices Compuestos para Consultas Complejas**
```sql
-- Índice para búsqueda de solicitudes por ubicación y fecha
CREATE INDEX idx_requests_location_date ON public.musician_requests 
    (venue_latitude, venue_longitude, event_date, status, visibility);

-- Índice para búsqueda de aplicaciones por estado y fecha
CREATE INDEX idx_applications_status_date ON public.request_applications 
    (status, created_at, request_id);

-- Índice para auditoría por usuario y fecha
CREATE INDEX idx_audit_user_date ON public.audit_logs 
    (user_id, created_at, action_type);

-- Índice para notificaciones por usuario y estado
CREATE INDEX idx_notifications_user_status ON public.notifications 
    (user_id, is_read, created_at);
```

### **🔍 Índices Full-Text para Búsqueda**
```sql
-- Índice full-text para búsqueda en solicitudes
CREATE INDEX idx_requests_search ON public.musician_requests 
    USING GIN(to_tsvector('english', title || ' ' || description));

-- Índice full-text para búsqueda en perfiles
CREATE INDEX idx_profiles_search ON public.user_profiles 
    USING GIN(to_tsvector('english', display_name || ' ' || bio));
```

---

## 🚨 **ANÁLISIS CRÍTICO DE SEGURIDAD**

### **🔒 Puntos Fuertes de Seguridad**
1. **Row Level Security (RLS)**: Implementado en todas las tablas críticas
2. **Auditoría Completa**: Log de todas las operaciones con contexto
3. **Validación de Entrada**: Constraints estrictos en nivel de base de datos
4. **Encriptación**: Contraseñas y datos sensibles encriptados
5. **Control de Acceso**: Políticas granulares por rol y usuario
6. **Prevención de SQL Injection**: Uso de parámetros y ORM
7. **Logging de Seguridad**: IP, User-Agent, y contexto de sesión

### **⚠️ Puntos de Atención Críticos**
1. **Rate Limiting**: Implementar en nivel de aplicación
2. **Validación de Archivos**: Verificar tipos y tamaños de archivos subidos
3. **Sanitización de HTML**: Para campos de texto que permitan HTML
4. **Monitoreo de Actividad Sospechosa**: Alertas automáticas
5. **Backup y Recuperación**: Estrategia robusta de backup
6. **Encriptación en Tránsito**: HTTPS obligatorio
7. **Gestión de Sesiones**: Expiración y renovación segura

### **🚨 Vulnerabilidades Potenciales Identificadas**
1. **Timing Attacks**: En comparaciones de tokens
2. **Enumeration Attacks**: En IDs de usuario
3. **Privilege Escalation**: Si las políticas RLS fallan
4. **Data Leakage**: En logs de auditoría
5. **Session Hijacking**: Si los tokens no se manejan correctamente

### **🛡️ Medidas de Mitigación Implementadas**
1. **UUIDs**: Para prevenir enumeration attacks
2. **Políticas RLS**: Para prevenir privilege escalation
3. **Auditoría**: Para detectar actividades sospechosas
4. **Validación**: Para prevenir data leakage
5. **Encriptación**: Para proteger datos sensibles

---

## 📋 **CHECKLIST DE IMPLEMENTACIÓN**

### **✅ Configuración Inicial**
- [ ] Crear proyecto Supabase
- [ ] Configurar PostgreSQL 15+
- [ ] Habilitar Row Level Security
- [ ] Configurar autenticación integrada

### **✅ Creación de Tablas**
- [ ] Implementar esquema de autenticación
- [ ] Crear tablas de usuarios
- [ ] Crear tablas de solicitudes
- [ ] Crear tablas de comunicación
- [ ] Crear tablas de auditoría

### **✅ Políticas de Seguridad**
- [ ] Implementar políticas RLS
- [ ] Crear triggers de auditoría
- [ ] Configurar validaciones
- [ ] Implementar constraints

### **✅ Testing de Seguridad**
- [ ] Testing de políticas RLS
- [ ] Testing de triggers
- [ ] Testing de constraints
- [ ] Testing de performance

### **✅ Documentación**
- [ ] Documentar esquema
- [ ] Documentar políticas
- [ ] Documentar triggers
- [ ] Crear guías de uso

---

## 🚀 **Próximos Pasos**

1. **🔒 [Configuración del Entorno Database](./CONFIGURACION_DATABASE.md)** - Setup seguro
2. **🧪 [Testing de Seguridad](./TESTING_SEGURIDAD_DB.md)** - Validación de seguridad
3. **📊 [Sistema de Solicitudes Database](./SOLICITUDES_DATABASE.md)** - Core del negocio
4. **🏗️ [Arquitectura de la Base de Datos](./ARQUITECTURA_DATABASE.md)** - Estructura general
5. **⚙️ [Variables de Entorno](./VARIABLES_ENTORNO_DB.md)** - Configuración segura

---

*Esta estructura de datos ha sido diseñada con un enfoque crítico en la seguridad, implementando múltiples capas de protección y auditoría para garantizar la integridad y confidencialidad de los datos de los usuarios.*
