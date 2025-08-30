# ⚙️ **CONFIGURACIÓN DEL BACKEND - MUSSIKON**

## 📋 **Índice de Configuración**

### **🔧 Configuración del Entorno**
- [Variables de Entorno](./CONFIGURACION.md#variables-de-entorno) - Configuración del sistema
- [Dependencias](./CONFIGURACION.md#dependencias) - Paquetes NuGet y servicios
- [Base de Datos](./CONFIGURACION.md#base-de-datos) - Configuración de Supabase

### **🚀 Despliegue y DevOps**
- [Docker](./CONFIGURACION.md#docker) - Contenedores y orquestación
- [Azure](./CONFIGURACION.md#azure) - Configuración en la nube
- [CI/CD](./CONFIGURACION.md#cicd) - Pipeline de integración continua

---

## 🎯 **Propósito de la Configuración**

### **Objetivo Principal**
Establecer la configuración completa del entorno de desarrollo, testing y producción para el backend .NET Core de MussikOn, asegurando consistencia y facilidad de despliegue.

### **Principios Clave**
- **Configuración Centralizada**: Todas las configuraciones en un lugar
- **Seguridad**: Variables sensibles protegidas
- **Flexibilidad**: Diferentes configuraciones por ambiente
- **Automatización**: Configuración automática del entorno

---

## 🔧 **Variables de Entorno**

### **📁 Archivo appsettings.json**
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "Microsoft.EntityFrameworkCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "Host=db.supabase.co;Database=postgres;Username=postgres;Password={password};Port=5432;SSL Mode=Require;Trust Server Certificate=true",
    "RedisConnection": "localhost:6379"
  },
  "JwtSettings": {
    "SecretKey": "{JWT_SECRET_KEY}",
    "Issuer": "MussikOn",
    "Audience": "MussikOnUsers",
    "ExpirationMinutes": 60,
    "RefreshTokenExpirationDays": 7
  },
  "SupabaseSettings": {
    "Url": "https://{PROJECT_ID}.supabase.co",
    "AnonKey": "{SUPABASE_ANON_KEY}",
    "ServiceRoleKey": "{SUPABASE_SERVICE_ROLE_KEY}"
  },
  "EmailSettings": {
    "SmtpServer": "smtp.gmail.com",
    "SmtpPort": 587,
    "Username": "{EMAIL_USERNAME}",
    "Password": "{EMAIL_PASSWORD}",
    "EnableSsl": true
  },
  "SignalRSettings": {
    "EnableDetailedErrors": false,
    "EnableMessagePack": true,
    "ClientTimeoutInterval": 30,
    "KeepAliveInterval": 15
  },
  "CorsSettings": {
    "AllowedOrigins": [
      "https://mussikon.com",
      "https://admin.mussikon.com",
      "exp://localhost:8081"
    ],
    "AllowedMethods": ["GET", "POST", "PUT", "DELETE"],
    "AllowedHeaders": ["*"],
    "AllowCredentials": true
  },
  "RateLimiting": {
    "PermitLimit": 100,
    "Window": 60,
    "SegmentsPerWindow": 10
  }
}
```

### **🔐 Archivo .env (Desarrollo Local)**
```bash
# Base de Datos
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
DATABASE_CONNECTION_STRING=Host=db.supabase.co;Database=postgres;Username=postgres;Password=your-password;Port=5432;SSL Mode=Require;Trust Server Certificate=true

# JWT
JWT_SECRET_KEY=your-super-secret-jwt-key-here-minimum-32-characters
JWT_ISSUER=MussikOn
JWT_AUDIENCE=MussikOnUsers
JWT_EXPIRATION_MINUTES=60
JWT_REFRESH_EXPIRATION_DAYS=7

# Email
EMAIL_USERNAME=your-email@gmail.com
EMAIL_PASSWORD=your-app-password
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587

# Redis
REDIS_CONNECTION_STRING=localhost:6379

# Azure (Producción)
AZURE_CONNECTION_STRING=your-azure-connection-string
AZURE_STORAGE_ACCOUNT=your-storage-account
AZURE_STORAGE_KEY=your-storage-key

# Logging
LOG_LEVEL=Information
LOG_FILE_PATH=logs/mussikon-api.log

# API
API_VERSION=v1
API_TITLE=MussikOn API
API_DESCRIPTION=API para el sistema de solicitudes de músicos
API_CONTACT_EMAIL=api@mussikon.com
API_CONTACT_NAME=MussikOn Team
```

### **🌍 Configuración por Ambiente**
```csharp
// Program.cs
var environment = app.Environment.EnvironmentName;

if (environment == "Development")
{
    app.UseDeveloperExceptionPage();
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "MussikOn API v1");
        c.RoutePrefix = string.Empty;
    });
}
else
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}
```

---

## 📦 **Dependencias**

### **🔧 Paquetes NuGet Principales**
```xml
<ItemGroup>
  <!-- ASP.NET Core -->
  <PackageReference Include="Microsoft.AspNetCore.App" />
  <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="8.0.0" />
  <PackageReference Include="Microsoft.AspNetCore.SignalR" Version="1.1.0" />
  <PackageReference Include="Microsoft.AspNetCore.Cors" Version="2.2.0" />
  
  <!-- Entity Framework Core -->
  <PackageReference Include="Microsoft.EntityFrameworkCore" Version="8.0.0" />
  <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.0" />
  <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="8.0.0" />
  <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="8.0.0" />
  <PackageReference Include="Npgsql.EntityFrameworkCore.PostgreSQL" Version="8.0.0" />
  
  <!-- Validación y Mapeo -->
  <PackageReference Include="FluentValidation.AspNetCore" Version="11.3.0" />
  <PackageReference Include="AutoMapper.Extensions.Microsoft.DependencyInjection" Version="12.0.1" />
  
  <!-- Logging y Monitoreo -->
  <PackageReference Include="Serilog.AspNetCore" Version="8.0.0" />
  <PackageReference Include="Serilog.Sinks.File" Version="5.0.0" />
  <PackageReference Include="Serilog.Sinks.Console" Version="5.0.0" />
  
  <!-- Testing -->
  <PackageReference Include="Microsoft.NET.Test.Sdk" Version="17.8.0" />
  <PackageReference Include="xunit" Version="2.6.0" />
  <PackageReference Include="xunit.runner.visualstudio" Version="2.5.0" />
  <PackageReference Include="Moq" Version="4.20.69" />
  <PackageReference Include="FluentAssertions" Version="6.12.0" />
  
  <!-- Seguridad -->
  <PackageReference Include="Microsoft.AspNetCore.RateLimiting" Version="8.0.0" />
  <PackageReference Include="BCrypt.Net-Next" Version="4.0.3" />
  
  <!-- Utilidades -->
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
  <PackageReference Include="System.IdentityModel.Tokens.Jwt" Version="7.3.1" />
</ItemGroup>
```

### **🔌 Configuración de Servicios**
```csharp
// Program.cs
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["JwtSettings:Issuer"],
            ValidAudience = builder.Configuration["JwtSettings:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["JwtSettings:SecretKey"]))
        };
    });

builder.Services.AddSignalR();
builder.Services.AddAutoMapper(typeof(Program));
builder.Services.AddFluentValidationAutoValidation();
builder.Services.AddRateLimiter(options =>
{
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(context =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: context.User.Identity?.Name ?? context.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = int.Parse(builder.Configuration["RateLimiting:PermitLimit"]),
                Window = TimeSpan.FromSeconds(int.Parse(builder.Configuration["RateLimiting:Window"]))
            }));
});
```

---

## 🗄️ **Base de Datos**

### **🔗 Configuración de Supabase**
```csharp
// Infrastructure/Data/ApplicationDbContext.cs
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            var connectionString = Environment.GetEnvironmentVariable("DATABASE_CONNECTION_STRING");
            optionsBuilder.UseNpgsql(connectionString);
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);
        
        // Configuración de entidades
        modelBuilder.Entity<UserProfile>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Email).IsRequired().HasMaxLength(255);
            entity.HasIndex(e => e.Email).IsUnique();
        });

        modelBuilder.Entity<MusicianRequest>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Title).IsRequired().HasMaxLength(255);
            entity.Property(e => e.BudgetMin).HasColumnType("decimal(10,2)");
            entity.Property(e => e.BudgetMax).HasColumnType("decimal(10,2)");
        });

        // Configuración de relaciones
        modelBuilder.Entity<MusicianRequest>()
            .HasOne(r => r.Organizer)
            .WithMany(u => u.OrganizedRequests)
            .HasForeignKey(r => r.OrganizerId)
            .OnDelete(DeleteBehavior.Cascade);

        modelBuilder.Entity<RequestApplication>()
            .HasOne(a => a.Request)
            .WithMany(r => r.Applications)
            .HasForeignKey(a => a.RequestId)
            .OnDelete(DeleteBehavior.Cascade);
    }

    public DbSet<UserProfile> UserProfiles { get; set; }
    public DbSet<MusicianRequest> MusicianRequests { get; set; }
    public DbSet<RequestApplication> RequestApplications { get; set; }
    public DbSet<Conversation> Conversations { get; set; }
    public DbSet<Message> Messages { get; set; }
    public DbSet<Notification> Notifications { get; set; }
    public DbSet<AuditLog> AuditLogs { get; set; }
}
```

### **🔐 Configuración de RLS (Row Level Security)**
```sql
-- Habilitar RLS en todas las tablas
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE musician_requests ENABLE ROW LEVEL SECURITY;
ALTER TABLE request_applications ENABLE ROW LEVEL SECURITY;
ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;
ALTER TABLE messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;
ALTER TABLE audit_logs ENABLE ROW LEVEL SECURITY;

-- Políticas de RLS
CREATE POLICY "Users can view own profile" ON user_profiles
    FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can update own profile" ON user_profiles
    FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Organizers can create requests" ON musician_requests
    FOR INSERT WITH CHECK (auth.uid() = organizer_id);

CREATE POLICY "Anyone can view open requests" ON musician_requests
    FOR SELECT USING (status = 'open');

CREATE POLICY "Organizers can manage own requests" ON musician_requests
    FOR ALL USING (auth.uid() = organizer_id);
```

---

## 🐳 **Docker**

### **📁 Dockerfile**
```dockerfile
# Multi-stage build para optimizar el tamaño de la imagen
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["MussikOn.API/MussikOn.API.csproj", "MussikOn.API/"]
COPY ["MussikOn.Domain/MussikOn.Domain.csproj", "MussikOn.Domain/"]
COPY ["MussikOn.Infrastructure/MussikOn.Infrastructure.csproj", "MussikOn.Infrastructure/"]
RUN dotnet restore "MussikOn.API/MussikOn.API.csproj"
COPY . .
WORKDIR "/src/MussikOn.API"
RUN dotnet build "MussikOn.API.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "MussikOn.API.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "MussikOn.API.dll"]
```

### **📁 docker-compose.yml**
```yaml
version: '3.8'

services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:80"
      - "5001:443"
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - DATABASE_CONNECTION_STRING=Host=db;Database=mussikon;Username=postgres;Password=password
      - REDIS_CONNECTION_STRING=redis:6379
    depends_on:
      - db
      - redis
    volumes:
      - ./logs:/app/logs

  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=mussikon
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

---

## ☁️ **Azure**

### **🔧 Configuración de App Service**
```json
{
  "name": "mussikon-api",
  "type": "Microsoft.Web/sites",
  "apiVersion": "2021-02-01",
  "location": "East US",
  "properties": {
    "serverFarmId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Web/serverfarms/mussikon-plan",
    "siteConfig": {
      "netFrameworkVersion": "v8.0",
      "appSettings": [
        {
          "name": "ASPNETCORE_ENVIRONMENT",
          "value": "Production"
        },
        {
          "name": "DATABASE_CONNECTION_STRING",
          "value": "@Microsoft.KeyVault(SecretUri=https://mussikon-kv.vault.azure.net/secrets/DatabaseConnectionString/)"
        },
        {
          "name": "JWT_SECRET_KEY",
          "value": "@Microsoft.KeyVault(SecretUri=https://mussikon-kv.vault.azure.net/secrets/JwtSecretKey/)"
        }
      ],
      "connectionStrings": [
        {
          "name": "DefaultConnection",
          "type": "PostgreSQL",
          "connectionString": "@Microsoft.KeyVault(SecretUri=https://mussikon-kv.vault.azure.net/secrets/DatabaseConnectionString/)"
        }
      ]
    }
  }
}
```

### **🗄️ Configuración de Azure Key Vault**
```bash
# Crear Key Vault
az keyvault create --name mussikon-kv --resource-group mussikon-rg --location eastus

# Agregar secretos
az keyvault secret set --vault-name mussikon-kv --name "DatabaseConnectionString" --value "Host=db.supabase.co;Database=postgres;Username=postgres;Password=password;Port=5432;SSL Mode=Require;Trust Server Certificate=true"

az keyvault secret set --vault-name mussikon-kv --name "JwtSecretKey" --value "your-super-secret-jwt-key-here-minimum-32-characters"

# Configurar políticas de acceso
az keyvault set-policy --name mussikon-kv --spn {app-service-principal-id} --secret-permissions get list
```

---

## 🔄 **CI/CD**

### **📁 GitHub Actions (.github/workflows/deploy.yml)**
```yaml
name: Deploy to Azure

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  AZURE_WEBAPP_NAME: mussikon-api
  AZURE_WEBAPP_PACKAGE_PATH: '.'

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '8.0.x'
    
    - name: Restore dependencies
      run: dotnet restore
    
    - name: Build
      run: dotnet build --no-restore --configuration Release
    
    - name: Test
      run: dotnet test --no-build --verbosity normal --configuration Release
    
    - name: Publish
      run: dotnet publish --no-build --configuration Release --output ${{env.AZURE_WEBAPP_PACKAGE_PATH}}/myapp
    
    - name: Upload artifact for deployment job
      uses: actions/upload-artifact@v3
      with:
        name: .net-app
        path: ${{env.AZURE_WEBAPP_PACKAGE_PATH}}/myapp

  deploy:
    runs-on: ubuntu-latest
    needs: build-and-test
    environment:
      name: 'Production'
      url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}
    
    steps:
    - name: Download artifact from build job
      uses: actions/download-artifact@v3
      with:
        name: .net-app
    
    - name: Deploy to Azure Web App
      id: deploy-to-webapp
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        slot-name: 'Production'
        package: ${{env.AZURE_WEBAPP_PACKAGE_PATH}}/myapp
```

### **🔧 Configuración de Azure DevOps**
```yaml
# azure-pipelines.yml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  solution: '**/*.sln'
  buildPlatform: 'Any CPU'
  buildConfiguration: 'Release'

stages:
- stage: Build
  jobs:
  - job: Build
    steps:
    - task: DotNetCoreCLI@2
      inputs:
        command: 'restore'
        projects: '$(solution)'
        feedsToUse: 'select'
    
    - task: DotNetCoreCLI@2
      inputs:
        command: 'build'
        projects: '$(solution)'
        arguments: '--configuration $(buildConfiguration)'
    
    - task: DotNetCoreCLI@2
      inputs:
        command: 'test'
        projects: '**/*Tests/*.csproj'
        arguments: '--configuration $(buildConfiguration)'
    
    - task: DotNetCoreCLI@2
      inputs:
        command: 'publish'
        publishWebProjects: true
        arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'
        zipAfterPublish: true
    
    - task: PublishBuildArtifacts@1
      inputs:
        pathToPublish: '$(Build.ArtifactStagingDirectory)'
        artifactName: 'drop'

- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: Deploy
    environment: 'production'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureWebApp@1
            inputs:
              azureSubscription: 'Azure Subscription'
              appName: 'mussikon-api'
              package: '$(Pipeline.Workspace)/drop/**/*.zip'
```

---

## 📚 **Recursos y Referencias**

### **🔗 Enlaces Oficiales**
- [.NET 8 Documentation](https://docs.microsoft.com/en-us/dotnet/)
- [ASP.NET Core Configuration](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/)

### **📖 Documentación Relacionada**
- [Arquitectura del Backend](./ARQUITECTURA.md)
- [Stack Tecnológico](./STACK_TECNOLOGICO.md)
- [Sistema de Solicitudes](./SOLICITUDES_MUSICOS.md)

---

*Esta configuración proporciona una base sólida para el desarrollo, testing y despliegue del backend .NET Core de MussikOn, asegurando consistencia y facilidad de mantenimiento.*
