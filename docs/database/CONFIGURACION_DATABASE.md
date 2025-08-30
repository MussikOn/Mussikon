# ⚙️ **CONFIGURACIÓN DE BASE DE DATOS - MUSSIKON**

## 📋 **Índice de Configuración de Base de Datos**

### **🔧 Configuración del Entorno**
- [Variables de Entorno](./CONFIGURACION_DATABASE.md#variables-de-entorno) - Configuración del sistema
- [Supabase](./CONFIGURACION_DATABASE.md#supabase) - Configuración de Supabase
- [Conexiones](./CONFIGURACION_DATABASE.md#conexiones) - Strings de conexión

### **🚀 Despliegue y DevOps**
- [Migrations](./CONFIGURACION_DATABASE.md#migrations) - Sistema de migraciones
- [Backup](./CONFIGURACION_DATABASE.md#backup) - Estrategia de respaldo
- [CI/CD](./CONFIGURACION_DATABASE.md#cicd) - Pipeline de integración continua

---

## 🎯 **Propósito de la Configuración de Base de Datos**

### **Objetivo Principal**
Establecer la configuración completa del entorno de base de datos PostgreSQL en Supabase para MussikOn, asegurando seguridad, performance y facilidad de mantenimiento.

### **Principios Clave**
- **Seguridad**: Row Level Security y políticas granulares
- **Performance**: Índices optimizados y queries eficientes
- **Escalabilidad**: Preparada para crecer con el negocio
- **Mantenibilidad**: Esquemas claros y documentados

---

## 🔧 **Variables de Entorno**

### **📁 Archivo .env**
```bash
# Supabase Configuration
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_ANON_KEY=your-anon-key-here
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key-here
SUPABASE_DB_PASSWORD=your-database-password

# Database Connection
DATABASE_URL=postgresql://postgres:your-password@db.your-project.supabase.co:5432/postgres
DATABASE_HOST=db.your-project.supabase.co
DATABASE_PORT=5432
DATABASE_NAME=postgres
DATABASE_USER=postgres

# Security
JWT_SECRET=your-jwt-secret-key-here
ENCRYPTION_KEY=your-encryption-key-here

# Backup
BACKUP_S3_BUCKET=mussikon-database-backups
BACKUP_S3_REGION=us-east-1
BACKUP_RETENTION_DAYS=30

# Monitoring
ENABLE_QUERY_LOGGING=true
ENABLE_PERFORMANCE_MONITORING=true
```

---

## 🗄️ **Supabase**

### **📁 supabase/config.toml**
```toml
[api]
enabled = true
port = 54321
schemas = ["public", "storage", "graphql_public"]
extra_search_path = ["public", "extensions"]
max_rows = 1000

[db]
port = 54322
shadow_port = 54320
major_version = 15

[db.pooler]
enabled = true
port = 54329
pool_mode = "transaction"
default_pool_size = 15
max_client_conn = 100

[realtime]
enabled = true
port = 54323

[storage]
enabled = true
port = 54324
file_size_limit = "50MiB"

[edge_functions]
enabled = true
port = 54325

[analytics]
enabled = true
port = 54326
```

### **📁 supabase/migrations/001_initial_schema.sql**
```sql
-- Habilitar extensiones necesarias
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";
CREATE EXTENSION IF NOT EXISTS "postgis";

-- Crear tipos enumerados
CREATE TYPE user_role AS ENUM ('musician', 'organizer', 'admin', 'moderator');
CREATE TYPE user_status AS ENUM ('active', 'inactive', 'suspended', 'banned');
CREATE TYPE request_status AS ENUM ('open', 'in_review', 'assigned', 'completed', 'cancelled', 'expired');

-- Crear tablas principales
CREATE TABLE user_profiles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID UNIQUE NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    role user_role NOT NULL DEFAULT 'musician',
    status user_status NOT NULL DEFAULT 'active',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Habilitar RLS
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;

-- Crear políticas de RLS
CREATE POLICY "Users can view own profile" ON user_profiles
    FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can update own profile" ON user_profiles
    FOR UPDATE USING (auth.uid() = user_id);
```

---

## 🔗 **Conexiones**

### **📡 Connection String para .NET Core**
```csharp
// appsettings.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=db.your-project.supabase.co;Database=postgres;Username=postgres;Password=your-password;Port=5432;SSL Mode=Require;Trust Server Certificate=true;Pooling=true;MinPoolSize=5;MaxPoolSize=20;Connection Lifetime=300;Command Timeout=30"
  }
}
```

### **📱 Connection String para React Native**
```typescript
// services/supabase.ts
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = process.env.EXPO_PUBLIC_SUPABASE_URL!;
const supabaseAnonKey = process.env.EXPO_PUBLIC_SUPABASE_ANON_KEY!;

export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
  auth: {
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: false
  },
  db: {
    schema: 'public'
  },
  realtime: {
    params: {
      eventsPerSecond: 10
    }
  }
});
```

---

## 🔄 **Migrations**

### **📁 supabase/migrations/002_musician_requests.sql**
```sql
-- Crear tabla de solicitudes de músicos
CREATE TABLE musician_requests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organizer_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    instrument VARCHAR(100) NOT NULL,
    budget_min DECIMAL(10,2) NOT NULL,
    budget_max DECIMAL(10,2) NOT NULL,
    event_date TIMESTAMP WITH TIME ZONE NOT NULL,
    location JSONB NOT NULL,
    status request_status NOT NULL DEFAULT 'open',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Crear índices para performance
CREATE INDEX idx_musician_requests_status ON musician_requests(status);
CREATE INDEX idx_musician_requests_event_date ON musician_requests(event_date);
CREATE INDEX idx_musician_requests_location ON musician_requests USING GIN(location);

-- Habilitar RLS
ALTER TABLE musician_requests ENABLE ROW LEVEL SECURITY;

-- Políticas de RLS
CREATE POLICY "Anyone can view open requests" ON musician_requests
    FOR SELECT USING (status = 'open');

CREATE POLICY "Organizers can manage own requests" ON musician_requests
    FOR ALL USING (auth.uid() = organizer_id);
```

---

## 💾 **Backup**

### **📁 scripts/backup.sh**
```bash
#!/bin/bash

# Configuración
BACKUP_DIR="/backups"
DATE=$(date +%Y%m%d_%H%M%S)
DB_NAME="postgres"
DB_HOST="db.your-project.supabase.co"
DB_USER="postgres"

# Crear backup
pg_dump -h $DB_HOST -U $DB_USER -d $DB_NAME --format=custom --verbose > "$BACKUP_DIR/mussikon_$DATE.backup"

# Comprimir backup
gzip "$BACKUP_DIR/mussikon_$DATE.backup"

# Subir a S3 (opcional)
aws s3 cp "$BACKUP_DIR/mussikon_$DATE.backup.gz" "s3://$BACKUP_S3_BUCKET/"

# Limpiar backups antiguos (mantener solo 30 días)
find $BACKUP_DIR -name "*.backup.gz" -mtime +30 -delete

echo "Backup completado: mussikon_$DATE.backup.gz"
```

---

## 🔄 **CI/CD**

### **📁 .github/workflows/database-migrate.yml**
```yaml
name: Database Migration

on:
  push:
    branches: [ main ]
    paths: [ 'supabase/migrations/**' ]

jobs:
  migrate:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Supabase CLI
      uses: supabase/setup-cli@v1
      with:
        version: latest
    
    - name: Run Migrations
      run: |
        supabase db push --db-url ${{ secrets.DATABASE_URL }}
      env:
        SUPABASE_ACCESS_TOKEN: ${{ secrets.SUPABASE_ACCESS_TOKEN }}
        DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [Supabase Documentation](https://supabase.com/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Supabase CLI](https://supabase.com/docs/reference/cli)

### **📖 Documentación Relacionada**
- [Arquitectura de Base de Datos](./ARQUITECTURA_DATABASE.md)
- [Stack Tecnológico de Base de Datos](./STACK_TECNOLOGICO_DATABASE.md)
- [Estructura de Datos](./ESTRUCTURA_DATOS_COMPLETA.md)

---

*Esta configuración proporciona una base sólida para el desarrollo, testing y despliegue de la base de datos PostgreSQL en Supabase de MussikOn.*
