# 🚨 **ANÁLISIS CRÍTICO DE SEGURIDAD - MUSSIKON DATABASE**

## ⚠️ **ADVERTENCIA CRÍTICA DE SEGURIDAD**

Este documento identifica **vulnerabilidades potenciales críticas** y **baches de seguridad** que podrían comprometer la integridad de la aplicación MussikOn. **NO IMPLEMENTAR EN PRODUCCIÓN** sin resolver todas las vulnerabilidades identificadas.

---

## 🚨 **VULNERABILIDADES CRÍTICAS IDENTIFICADAS**

### **🔴 CRÍTICO - Ataques de Fuerza Bruta**
```sql
-- VULNERABILIDAD: No hay rate limiting en nivel de base de datos
-- IMPACTO: Ataques de fuerza bruta en login, registro, y operaciones sensibles
-- EXPLOTACIÓN: Scripts automatizados pueden sobrecargar el sistema

-- SOLUCIÓN: Implementar rate limiting en aplicación + base de datos
CREATE TABLE public.rate_limit_attempts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ip_address INET NOT NULL,
    action_type VARCHAR(100) NOT NULL,
    attempts_count INTEGER DEFAULT 1,
    first_attempt TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    last_attempt TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    is_blocked BOOLEAN DEFAULT FALSE,
    blocked_until TIMESTAMP WITH TIME ZONE,
    
    CONSTRAINT valid_attempts CHECK (attempts_count > 0)
);

-- Trigger para bloquear IPs después de múltiples intentos
CREATE OR REPLACE FUNCTION check_rate_limit()
RETURNS TRIGGER AS $$
BEGIN
    -- Bloquear IP después de 5 intentos fallidos en 15 minutos
    IF EXISTS (
        SELECT 1 FROM public.rate_limit_attempts 
        WHERE ip_address = inet_client_addr() 
        AND action_type = 'login_failed'
        AND attempts_count >= 5
        AND last_attempt > NOW() - INTERVAL '15 minutes'
    ) THEN
        RAISE EXCEPTION 'IP address blocked due to multiple failed attempts';
    END IF;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### **🔴 CRÍTICO - Inyección de SQL en Funciones Personalizadas**
```sql
-- VULNERABILIDAD: Funciones que construyen queries dinámicamente
-- IMPACTO: Ejecución de código SQL malicioso
-- EXPLOTACIÓN: Input malicioso en parámetros de función

-- SOLUCIÓN: Usar parámetros preparados y validación estricta
CREATE OR REPLACE FUNCTION search_requests_safe(
    search_term TEXT,
    location_lat DECIMAL DEFAULT NULL,
    location_lng DECIMAL DEFAULT NULL,
    radius_km INTEGER DEFAULT 50
)
RETURNS TABLE (
    id UUID,
    title VARCHAR(200),
    description TEXT,
    distance_km DECIMAL
) AS $$
BEGIN
    -- Validación estricta de parámetros
    IF search_term IS NULL OR length(trim(search_term)) < 2 THEN
        RAISE EXCEPTION 'Invalid search term';
    END IF;
    
    IF location_lat IS NOT NULL AND (location_lat < -90 OR location_lat > 90) THEN
        RAISE EXCEPTION 'Invalid latitude';
    END IF;
    
    IF location_lng IS NOT NULL AND (location_lng < -180 OR location_lng > 180) THEN
        RAISE EXCEPTION 'Invalid longitude';
    END IF;
    
    IF radius_km < 1 OR radius_km > 1000 THEN
        RAISE EXCEPTION 'Invalid radius';
    END IF;
    
    -- Query seguro con parámetros
    RETURN QUERY
    SELECT 
        mr.id,
        mr.title,
        mr.description,
        CASE 
            WHEN location_lat IS NOT NULL AND location_lng IS NOT NULL THEN
                (6371 * acos(cos(radians(location_lat)) * cos(radians(mr.venue_latitude)) * 
                 cos(radians(mr.venue_longitude) - radians(location_lng)) + 
                 sin(radians(location_lat)) * sin(radians(mr.venue_latitude))))
            ELSE NULL
        END AS distance_km
    FROM public.musician_requests mr
    WHERE mr.status = 'active'
    AND mr.visibility = 'public'
    AND mr.event_date >= CURRENT_DATE
    AND (
        search_term IS NULL OR
        mr.title ILIKE '%' || search_term || '%' OR
        mr.description ILIKE '%' || search_term || '%'
    )
    AND (
        location_lat IS NULL OR location_lng IS NULL OR
        (mr.venue_latitude IS NOT NULL AND mr.venue_longitude IS NOT NULL AND
         (6371 * acos(cos(radians(location_lat)) * cos(radians(mr.venue_latitude)) * 
          cos(radians(mr.venue_longitude) - radians(location_lng)) + 
          sin(radians(location_lat)) * sin(radians(mr.venue_latitude)))) <= radius_km)
    )
    ORDER BY 
        CASE WHEN location_lat IS NOT NULL THEN distance_km END ASC,
        mr.created_at DESC;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### **🔴 CRÍTICO - Elevación de Privilegios en Políticas RLS**
```sql
-- VULNERABILIDAD: Políticas RLS que pueden ser bypassed
-- IMPACTO: Usuarios acceden a datos que no deberían
-- EXPLOTACIÓN: Manipulación de contexto de autenticación

-- SOLUCIÓN: Políticas más restrictivas y validación adicional
-- Política más restrictiva para user_profiles
CREATE POLICY "Strict user profile access" ON public.user_profiles
    FOR SELECT USING (
        -- Usuario puede ver su propio perfil
        (auth.uid() = id) OR
        -- O perfiles públicos de usuarios activos
        (profile_visibility = 'public' AND 
         account_status = 'active' AND
         verification_level IN ('verified', 'premium'))
    );

-- Política para actualizaciones con validación adicional
CREATE POLICY "Strict profile update" ON public.user_profiles
    FOR UPDATE USING (
        auth.uid() = id AND
        account_status = 'active' AND
        -- Prevenir cambios críticos sin verificación
        (NEW.email_verified = OLD.email_verified OR 
         NEW.phone_verified = OLD.phone_verified)
    ) WITH CHECK (
        auth.uid() = id AND
        account_status = 'active' AND
        -- Validaciones adicionales
        length(trim(NEW.display_name)) >= 2 AND
        length(trim(NEW.first_name)) >= 2 AND
        length(trim(NEW.last_name)) >= 2
    );
```

---

## 🚨 **BACHES DE SEGURIDAD IDENTIFICADOS**

### **🟡 ALTO - Fuga de Información en Logs de Auditoría**
```sql
-- PROBLEMA: Los logs de auditoría pueden contener datos sensibles
-- RIESGO: Información personal expuesta en logs

-- SOLUCIÓN: Función de logging seguro
CREATE OR REPLACE FUNCTION secure_audit_log(
    p_action_type VARCHAR(100),
    p_table_name VARCHAR(100),
    p_record_id UUID,
    p_old_values JSONB DEFAULT NULL,
    p_new_values JSONB DEFAULT NULL
)
RETURNS VOID AS $$
DECLARE
    sanitized_old JSONB;
    sanitized_new JSONB;
BEGIN
    -- Sanitizar datos sensibles en old_values
    IF p_old_values IS NOT NULL THEN
        sanitized_old := p_old_values;
        -- Remover campos sensibles
        sanitized_old := sanitized_old - 'password';
        sanitized_old := sanitized_old - 'phone';
        sanitized_old := sanitized_old - 'email';
        sanitized_old := sanitized_old - 'session_token';
        sanitized_old := sanitized_old - 'push_token';
    END IF;
    
    -- Sanitizar datos sensibles en new_values
    IF p_new_values IS NOT NULL THEN
        sanitized_new := p_new_values;
        -- Remover campos sensibles
        sanitized_new := sanitized_new - 'password';
        sanitized_new := sanitized_new - 'phone';
        sanitized_new := sanitized_new - 'email';
        sanitized_new := sanitized_new - 'session_token';
        sanitized_new := sanitized_new - 'push_token';
    END IF;
    
    -- Insertar log sanitizado
    INSERT INTO public.audit_logs (
        user_id, action_type, table_name, record_id,
        old_values, new_values, ip_address, user_agent
    ) VALUES (
        auth.uid(), p_action_type, p_table_name, p_record_id,
        sanitized_old, sanitized_new,
        inet_client_addr(), 
        current_setting('request.headers')::json->>'user-agent'
    );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### **🟡 ALTO - Ataques de Timing en Comparaciones de Tokens**
```sql
-- PROBLEMA: Comparaciones de strings pueden revelar información
-- RIESGO: Ataques de timing para adivinar tokens

-- SOLUCIÓN: Función de comparación segura
CREATE OR REPLACE FUNCTION secure_token_compare(
    token1 TEXT,
    token2 TEXT
)
RETURNS BOOLEAN AS $$
DECLARE
    result BOOLEAN;
    dummy BOOLEAN;
    i INTEGER;
BEGIN
    -- Comparación segura contra timing attacks
    result := TRUE;
    
    -- Siempre hacer la misma cantidad de operaciones
    FOR i IN 1..64 LOOP
        IF i <= length(token1) AND i <= length(token2) THEN
            IF substring(token1, i, 1) != substring(token2, i, 1) THEN
                result := FALSE;
            END IF;
        ELSE
            -- Operación dummy para mantener timing consistente
            dummy := (i % 2 = 0);
        END IF;
    END LOOP;
    
    RETURN result;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### **🟡 ALTO - Ataques de Enumeration en IDs de Usuario**
```sql
-- PROBLEMA: UUIDs secuenciales pueden ser adivinados
-- RIESGO: Enumeración de usuarios del sistema

-- SOLUCIÓN: UUIDs aleatorios y rate limiting por IP
-- Ya implementado con gen_random_uuid(), pero agregar protección adicional

CREATE TABLE public.user_enumeration_protection (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ip_address INET NOT NULL,
    user_id_attempts UUID[] DEFAULT '{}',
    attempt_count INTEGER DEFAULT 0,
    first_attempt TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    last_attempt TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    is_blocked BOOLEAN DEFAULT FALSE,
    blocked_until TIMESTAMP WITH TIME ZONE
);

-- Función para verificar si una IP está bloqueada por enumeration
CREATE OR REPLACE FUNCTION check_enumeration_protection(
    p_user_id UUID
)
RETURNS BOOLEAN AS $$
BEGIN
    -- Bloquear IP si intenta acceder a más de 10 usuarios únicos en 1 hora
    IF EXISTS (
        SELECT 1 FROM public.user_enumeration_protection
        WHERE ip_address = inet_client_addr()
        AND array_length(user_id_attempts, 1) >= 10
        AND last_attempt > NOW() - INTERVAL '1 hour'
    ) THEN
        RAISE EXCEPTION 'IP blocked due to potential user enumeration attack';
    END IF;
    
    -- Registrar intento
    INSERT INTO public.user_enumeration_protection (
        ip_address, user_id_attempts, attempt_count, last_attempt
    ) VALUES (
        inet_client_addr(), 
        ARRAY[p_user_id], 
        1, 
        NOW()
    )
    ON CONFLICT (ip_address) DO UPDATE SET
        user_id_attempts = CASE 
            WHEN NOT (p_user_id = ANY(user_enumeration_protection.user_id_attempts))
            THEN user_enumeration_protection.user_id_attempts || p_user_id
            ELSE user_enumeration_protection.user_id_attempts
        END,
        attempt_count = user_enumeration_protection.attempt_count + 1,
        last_attempt = NOW();
    
    RETURN TRUE;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 🚨 **MEDIDAS DE SEGURIDAD ADICIONALES CRÍTICAS**

### **🔒 Encriptación de Datos Sensibles**
```sql
-- Función para encriptar datos sensibles
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Función para encriptar datos con clave específica
CREATE OR REPLACE FUNCTION encrypt_sensitive_data(
    data TEXT,
    encryption_key TEXT
)
RETURNS TEXT AS $$
BEGIN
    -- Validar que la clave tenga la longitud correcta
    IF length(encryption_key) != 32 THEN
        RAISE EXCEPTION 'Encryption key must be exactly 32 characters';
    END IF;
    
    -- Encriptar usando AES-256
    RETURN encode(
        encrypt_iv(
            data::bytea,
            encryption_key::bytea,
            decode('000102030405060708090A0B0C0D0E0F', 'hex'),
            'aes-cbc'
        ),
        'base64'
    );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Función para desencriptar datos
CREATE OR REPLACE FUNCTION decrypt_sensitive_data(
    encrypted_data TEXT,
    encryption_key TEXT
)
RETURNS TEXT AS $$
BEGIN
    -- Validar que la clave tenga la longitud correcta
    IF length(encryption_key) != 32 THEN
        RAISE EXCEPTION 'Encryption key must be exactly 32 characters';
    END IF;
    
    -- Desencriptar usando AES-256
    RETURN convert_from(
        decrypt_iv(
            decode(encrypted_data, 'base64'),
            encryption_key::bytea,
            decode('000102030405060708090A0B0C0D0E0F', 'hex'),
            'aes-cbc'
        ),
        'utf8'
    );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### **🔒 Validación de Entrada Estricta**
```sql
-- Función para validar y sanitizar HTML
CREATE OR REPLACE FUNCTION sanitize_html(
    input_text TEXT
)
RETURNS TEXT AS $$
BEGIN
    -- Remover tags HTML peligrosos
    input_text := regexp_replace(input_text, '<script[^>]*>.*?</script>', '', 'gi');
    input_text := regexp_replace(input_text, '<iframe[^>]*>.*?</iframe>', '', 'gi');
    input_text := regexp_replace(input_text, '<object[^>]*>.*?</object>', '', 'gi');
    input_text := regexp_replace(input_text, '<embed[^>]*>.*?</embed>', '', 'gi');
    
    -- Remover atributos peligrosos
    input_text := regexp_replace(input_text, 'on\w+\s*=', '', 'gi');
    input_text := regexp_replace(input_text, 'javascript:', '', 'gi');
    input_text := regexp_replace(input_text, 'vbscript:', '', 'gi');
    
    -- Limitar longitud
    IF length(input_text) > 10000 THEN
        RAISE EXCEPTION 'Input text too long (max 10000 characters)';
    END IF;
    
    RETURN input_text;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Función para validar archivos
CREATE OR REPLACE FUNCTION validate_file_upload(
    file_name TEXT,
    file_size BIGINT,
    allowed_extensions TEXT[],
    max_size_bytes BIGINT
)
RETURNS BOOLEAN AS $$
DECLARE
    file_extension TEXT;
BEGIN
    -- Validar tamaño
    IF file_size > max_size_bytes THEN
        RAISE EXCEPTION 'File size exceeds maximum allowed size';
    END IF;
    
    -- Extraer extensión
    file_extension := lower(split_part(file_name, '.', -1));
    
    -- Validar extensión
    IF NOT (file_extension = ANY(allowed_extensions)) THEN
        RAISE EXCEPTION 'File extension not allowed';
    END IF;
    
    -- Validar nombre del archivo
    IF NOT (file_name ~ '^[a-zA-Z0-9._-]+$') THEN
        RAISE EXCEPTION 'Invalid file name';
    END IF;
    
    RETURN TRUE;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 🚨 **CHECKLIST DE SEGURIDAD CRÍTICA**

### **🔴 CRÍTICO - Implementar Inmediatamente**
- [ ] **Rate Limiting**: Bloquear IPs después de múltiples intentos fallidos
- [ ] **Validación de Entrada**: Sanitizar y validar todo input del usuario
- [ ] **Encriptación**: Encriptar datos sensibles en reposo
- [ ] **Auditoría Segura**: Sanitizar logs para evitar fuga de información
- [ ] **Políticas RLS**: Reforzar políticas con validaciones adicionales

### **🟡 ALTO - Implementar en Próxima Iteración**
- [ ] **Protección contra Enumeration**: Detectar ataques de enumeración de usuarios
- [ ] **Comparación Segura**: Implementar comparaciones seguras contra timing attacks
- [ ] **Validación de Archivos**: Verificar tipos y tamaños de archivos
- [ ] **Sanitización HTML**: Limpiar contenido HTML malicioso
- [ ] **Monitoreo de Actividad**: Alertas para actividades sospechosas

### **🟡 MEDIO - Implementar en Fase 2**
- [ ] **Machine Learning**: Detección automática de patrones sospechosos
- [ ] **Análisis de Comportamiento**: Perfiles de usuario para detectar anomalías
- [ ] **Backup Encriptado**: Backup de datos con encriptación adicional
- [ ] **Recuperación de Desastres**: Plan de recuperación completo
- [ ] **Testing de Penetración**: Tests automatizados de seguridad

---

## 🚨 **VULNERABILIDADES ESPECÍFICAS DEL DOMINIO**

### **🎵 Ataques Específicos de la Aplicación Musical**

#### **🔴 CRÍTICO - Manipulación de Solicitudes**
```sql
-- VULNERABILIDAD: Usuarios pueden modificar solicitudes de otros
-- RIESGO: Sabotaje de eventos musicales

-- SOLUCIÓN: Validación estricta de propiedad
CREATE OR REPLACE FUNCTION validate_request_ownership(
    request_id UUID,
    user_id UUID
)
RETURNS BOOLEAN AS $$
DECLARE
    is_owner BOOLEAN;
    request_status VARCHAR(50);
BEGIN
    -- Verificar propiedad y estado
    SELECT 
        (organizer_id = user_id),
        status
    INTO is_owner, request_status
    FROM public.musician_requests
    WHERE id = request_id;
    
    -- Solo permitir modificaciones si es el propietario y la solicitud no está en progreso
    IF NOT is_owner THEN
        RAISE EXCEPTION 'User is not the owner of this request';
    END IF;
    
    IF request_status IN ('in_progress', 'completed') THEN
        RAISE EXCEPTION 'Cannot modify request in progress or completed';
    END IF;
    
    RETURN TRUE;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

#### **🔴 CRÍTICO - Ataques de Spam en Aplicaciones**
```sql
-- VULNERABILIDAD: Músicos pueden enviar múltiples aplicaciones
-- RIESGO: Spam y acoso a organizadores

-- SOLUCIÓN: Límite de aplicaciones por usuario
CREATE OR REPLACE FUNCTION check_application_limit(
    musician_id UUID,
    request_id UUID
)
RETURNS BOOLEAN AS $$
DECLARE
    application_count INTEGER;
    recent_applications INTEGER;
BEGIN
    -- Verificar que no haya aplicado ya a esta solicitud
    IF EXISTS (
        SELECT 1 FROM public.request_applications
        WHERE musician_id = check_application_limit.musician_id
        AND request_id = check_application_limit.request_id
    ) THEN
        RAISE EXCEPTION 'User has already applied to this request';
    END IF;
    
    -- Verificar límite de aplicaciones por día
    SELECT COUNT(*) INTO recent_applications
    FROM public.request_applications
    WHERE musician_id = check_application_limit.musician_id
    AND created_at > CURRENT_DATE;
    
    IF recent_applications >= 20 THEN
        RAISE EXCEPTION 'Daily application limit reached (20 applications per day)';
    END IF;
    
    -- Verificar límite total de aplicaciones activas
    SELECT COUNT(*) INTO application_count
    FROM public.request_applications ra
    JOIN public.musician_requests mr ON ra.request_id = mr.id
    WHERE ra.musician_id = check_application_limit.musician_id
    AND ra.status IN ('pending', 'reviewed', 'shortlisted')
    AND mr.status IN ('active', 'in_progress');
    
    IF application_count >= 50 THEN
        RAISE EXCEPTION 'Maximum active applications limit reached (50 applications)';
    END IF;
    
    RETURN TRUE;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 🚨 **RECOMENDACIONES FINALES CRÍTICAS**

### **🔴 NO IMPLEMENTAR EN PRODUCCIÓN SIN:**
1. **Testing Exhaustivo**: Tests de penetración completos
2. **Auditoría de Seguridad**: Revisión por expertos en seguridad
3. **Monitoreo Continuo**: Sistema de alertas 24/7
4. **Plan de Respuesta**: Protocolo para incidentes de seguridad
5. **Backup y Recuperación**: Estrategia robusta de DR

### **🟡 IMPLEMENTAR EN ORDEN DE PRIORIDAD:**
1. **Rate Limiting y Bloqueo de IPs**
2. **Validación y Sanitización de Input**
3. **Encriptación de Datos Sensibles**
4. **Políticas RLS Reforzadas**
5. **Auditoría Segura**

### **🟡 MONITOREAR CONTINUAMENTE:**
1. **Logs de Auditoría**: Buscar patrones sospechosos
2. **Métricas de Performance**: Detectar ataques DoS
3. **Actividad de Usuarios**: Identificar comportamientos anómalos
4. **Accesos a Datos**: Verificar violaciones de políticas RLS
5. **Cambios en la Base**: Detectar modificaciones no autorizadas

---

## 🚨 **CONCLUSIÓN CRÍTICA**

La estructura de datos propuesta **NO ES SEGURA PARA PRODUCCIÓN** sin implementar todas las medidas de seguridad adicionales identificadas. 

**Riesgos Críticos Identificados:**
- Ataques de fuerza bruta sin rate limiting
- Posible inyección de SQL en funciones personalizadas
- Elevación de privilegios en políticas RLS
- Fuga de información en logs de auditoría
- Ataques de timing en comparaciones de tokens
- Enumeration attacks en IDs de usuario

**Acción Requerida:**
Implementar **TODAS** las medidas de seguridad adicionales antes de cualquier deployment en producción.

---

*Este documento debe ser revisado y actualizado regularmente para mantener la seguridad de la aplicación MussikOn.*
