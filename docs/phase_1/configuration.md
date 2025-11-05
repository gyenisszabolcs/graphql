# Phase 1 - Configuration Management

**Workstream 5 Documentation**
**Version:** 1.0
**Date:** 2025-11-05
**Status:** READY TO EXECUTE (Blocked by WS3)
**Agent:** backend-api-developer

---

## Executive Summary

This document details the secure configuration management system for the GraphQL API application. The system uses ASP.NET Core's layered configuration with explicit separation between committed templates and local secrets.

**BLOCKER:** Workstream 3 (Solution Structure) must complete before configuration files can be created.

---

## Security First: The Golden Rules

### 🔴 NEVER COMMIT THESE:
- `appsettings.Local.json` - Contains actual credentials
- Any file with real passwords, connection strings, or API keys
- Any file matching `*.Local.json` pattern

### ✅ ALWAYS COMMIT THESE:
- `appsettings.json` - With placeholder values only
- `appsettings.Local.json.template` - Shows required structure
- Documentation files

### Verification Command:
```bash
# Before committing, ALWAYS run:
git status

# Should NOT see:
# - appsettings.Local.json
# - Any file with "secret" or "password" in real values
```

---

## Configuration Architecture

### Configuration Layers (Loaded in Order)

1. **appsettings.json** (Base configuration, committed)
   - Placeholder connection strings
   - Default settings
   - Public configuration
   - Template for all environments

2. **appsettings.{Environment}.json** (Environment-specific, committed or not)
   - Development environment: Can be committed with safe values
   - Production environment: Should NOT be committed (contains production URLs)

3. **appsettings.Local.json** (Developer secrets, NEVER committed)
   - Real SQL Server credentials
   - Real JWT secret keys
   - Developer-specific settings
   - Overrides all previous layers

4. **Environment Variables** (Production deployment)
   - Used in IIS, Docker, Kubernetes
   - Highest priority override
   - Format: `ConnectionStrings__DefaultConnection`

5. **User Secrets** (Optional alternative to Local.json)
   - Stored outside project directory
   - Command: `dotnet user-secrets set "key" "value"`
   - Good for sensitive development credentials

---

## 1. appsettings.json (Base Configuration)

**File:** `d:\Development\graphql\src\GraphQLApp.API\appsettings.json`
**Status:** To be committed to git
**Purpose:** Base configuration with placeholder values

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=10.10.10.69;Database=dev_graphql;User Id=YOUR_USERNAME;Password=YOUR_PASSWORD;TrustServerCertificate=True;Connection Timeout=30;",
    "Description": "Replace YOUR_USERNAME and YOUR_PASSWORD in appsettings.Local.json"
  },
  "JwtSettings": {
    "SecretKey": "YOUR_SECRET_KEY_MINIMUM_32_CHARACTERS_REQUIRED_FOR_SECURITY",
    "Issuer": "GraphQLApp",
    "Audience": "GraphQLApp.Client",
    "ExpirationMinutes": 60,
    "Description": "Replace YOUR_SECRET_KEY with a real secret in appsettings.Local.json"
  },
  "Serilog": {
    "Using": ["Serilog.Sinks.Console", "Serilog.Sinks.File"],
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "Microsoft.AspNetCore": "Warning",
        "Microsoft.EntityFrameworkCore": "Warning",
        "System": "Warning"
      }
    },
    "WriteTo": [
      {
        "Name": "Console",
        "Args": {
          "outputTemplate": "[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj} {Properties:j}{NewLine}{Exception}"
        }
      },
      {
        "Name": "File",
        "Args": {
          "path": "logs/log-.txt",
          "rollingInterval": "Day",
          "retainedFileCountLimit": 7,
          "outputTemplate": "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level:u3}] {Message:lj} {Properties:j}{NewLine}{Exception}",
          "fileSizeLimitBytes": 104857600,
          "rollOnFileSizeLimit": true
        }
      }
    ],
    "Enrich": ["FromLogContext", "WithMachineName", "WithThreadId"]
  },
  "AllowedHosts": "*",
  "GraphQL": {
    "EnableIntrospection": true,
    "EnableGraphQLVoyager": true,
    "MaxExecutionDepth": 15,
    "MaxOperationComplexity": 200,
    "EnableTracing": true
  },
  "Cors": {
    "AllowedOrigins": [
      "http://localhost:3000",
      "http://localhost:5173"
    ],
    "AllowedMethods": ["GET", "POST"],
    "AllowedHeaders": ["Content-Type", "Authorization"],
    "AllowCredentials": true
  }
}
```

### Key Sections Explained:

#### ConnectionStrings
- `Server=10.10.10.69` - SQL Server address (committed, not secret)
- `Database=dev_graphql` - Database name (committed, not secret)
- `YOUR_USERNAME` - **PLACEHOLDER** - Replace in Local.json
- `YOUR_PASSWORD` - **PLACEHOLDER** - Replace in Local.json
- `TrustServerCertificate=True` - Required for self-signed certs
- `Connection Timeout=30` - 30-second timeout

#### JwtSettings
- `SecretKey` - **PLACEHOLDER** - Must be replaced with 32+ char secret
- `Issuer` - Token issuer (our application name)
- `Audience` - Token audience (our frontend)
- `ExpirationMinutes` - Token lifetime (60 minutes = 1 hour)

#### Serilog
- Console output for development visibility
- File output for persistent logs
- Rolling interval: Daily (creates log-20251105.txt)
- Retention: 7 days
- File size limit: 100MB per file

---

## 2. appsettings.Local.json.template (Template for Developers)

**File:** `d:\Development\graphql\src\GraphQLApp.API\appsettings.Local.json.template`
**Status:** To be committed to git
**Purpose:** Shows structure, developers copy to `appsettings.Local.json`

```json
{
  "_README": "Copy this file to appsettings.Local.json and fill in real values. DO NOT commit appsettings.Local.json!",
  "ConnectionStrings": {
    "DefaultConnection": "Server=10.10.10.69;Database=dev_graphql;User Id=YOUR_SQL_USERNAME;Password=YOUR_SQL_PASSWORD;TrustServerCertificate=True;Connection Timeout=30;"
  },
  "JwtSettings": {
    "SecretKey": "GENERATE_A_STRONG_SECRET_KEY_MIN_32_CHARS_USE_pwgen_OR_openssl",
    "_Example": "Run: openssl rand -base64 32"
  }
}
```

### Instructions for Developers:
1. Copy `appsettings.Local.json.template` to `appsettings.Local.json`
2. Replace `YOUR_SQL_USERNAME` with your actual SQL Server username
3. Replace `YOUR_SQL_PASSWORD` with your actual SQL Server password
4. Generate a strong JWT secret (see below)
5. Save file
6. Verify file is in `.gitignore` (should not show in `git status`)

---

## 3. appsettings.Local.json (Actual Developer Secrets)

**File:** `d:\Development\graphql\src\GraphQLApp.API\appsettings.Local.json`
**Status:** NEVER commit this file (must be in .gitignore)
**Purpose:** Contains actual credentials for local development

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=10.10.10.69;Database=dev_graphql;User Id=sa;Password=RealPassword123!;TrustServerCertificate=True;Connection Timeout=30;"
  },
  "JwtSettings": {
    "SecretKey": "a3k8f9d2j1k5l6m7n8o9p0q1r2s3t4u5v6w7x8y9z0"
  }
}
```

**⚠️ CRITICAL:** This file must be in `.gitignore`!

---

## 4. Generating Secure JWT Secret Keys

### Option 1: PowerShell (Windows)
```powershell
# Generate 32-byte random string (base64 encoded = 44 chars)
$bytes = New-Object byte[] 32
[Security.Cryptography.RandomNumberGenerator]::Create().GetBytes($bytes)
[Convert]::ToBase64String($bytes)

# Example output: "a3k8f9d2j1k5l6m7n8o9p0q1r2s3t4u5v6w7x8y9z0A="
```

---

### Option 2: OpenSSL (Linux/Mac/Windows with Git Bash)
```bash
openssl rand -base64 32
# Example output: "xK8fN2jL5mO7pQ1rS3tU5vW7xY9zA0bC2dE4fG6hI8="
```

---

### Option 3: Online Generator (Use with Caution)
- Visit: https://generate-secret.vercel.app/32
- Generate key
- Copy immediately
- Use only for development (not production)

---

### Option 4: Manual (Not Recommended)
- Type 32+ random characters
- Mix uppercase, lowercase, numbers, symbols
- Example: `Kx9#mL2$pN8*qR5&sT3@vW7!xY4%zA1^bC6`

---

## 5. .gitignore Configuration

**File:** `d:\Development\graphql\.gitignore`
**Status:** Already updated by Workstream 1 (DevOps agent)

Verify these lines exist:
```gitignore
# Local configuration with secrets (Phase 1 requirement)
appsettings.Local.json
appsettings.*.Local.json

# Environment variable files
.env
.env.local
.env.development
.env.production

# Credentials and secrets files
*credentials*.json
*secrets*.json

# Logs
[Ll]og/
[Ll]ogs/
*.log
```

---

## 6. Environment Variables (Production Deployment)

### IIS Configuration

**Format:** Use double underscores (`__`) to navigate hierarchy

```xml
<!-- In web.config -->
<aspNetCore processPath="dotnet" arguments=".\GraphQLApp.API.dll">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Production" />
    <environmentVariable name="ConnectionStrings__DefaultConnection" value="Server=prod-server;Database=prod_db;User Id=produser;Password=ProdPass123!;TrustServerCertificate=False;" />
    <environmentVariable name="JwtSettings__SecretKey" value="production_secret_key_32_chars_min" />
  </environmentVariables>
</aspNetCore>
```

---

### Windows Server - IIS Manager

1. Open IIS Manager
2. Select Application Pool: GraphQLAppPool
3. Right-click → Advanced Settings
4. Environment Variables section:
   ```
   Name: ConnectionStrings__DefaultConnection
   Value: Server=prod-server;Database=prod_db;...

   Name: JwtSettings__SecretKey
   Value: production_secret_key_32_chars_min
   ```

---

### Docker Compose

```yaml
services:
  graphql-api:
    image: graphql-api:latest
    ports:
      - "5000:80"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Server=sql-server;Database=graphql_db;User Id=docker_user;Password=DockerPass123!
      - JwtSettings__SecretKey=${JWT_SECRET}
    env_file:
      - .env.production
```

---

### Azure App Service

**Portal:**
1. Go to App Service → Configuration
2. Application Settings → New application setting
3. Add:
   - Name: `ConnectionStrings__DefaultConnection`
   - Value: `[connection string]`
   - Deployment slot setting: ✅

**Azure CLI:**
```bash
az webapp config appsettings set \
  --name graphql-api \
  --resource-group rg-graphql \
  --settings ConnectionStrings__DefaultConnection="Server=..."
```

---

## 7. Configuration Loading in Code

### Program.cs (ASP.NET Core)

```csharp
var builder = WebApplication.CreateBuilder(args);

// Configuration is automatically loaded in this order:
// 1. appsettings.json
// 2. appsettings.{Environment}.json
// 3. User Secrets (if Development)
// 4. Environment Variables
// 5. Command-line arguments

// Access configuration:
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
var jwtSecret = builder.Configuration["JwtSettings:SecretKey"];

// Or bind to strongly-typed class:
var jwtSettings = new JwtSettings();
builder.Configuration.GetSection("JwtSettings").Bind(jwtSettings);
```

---

### Strongly-Typed Configuration Classes

**File:** `src/GraphQLApp.Core/Configuration/JwtSettings.cs`
```csharp
namespace GraphQLApp.Core.Configuration;

public class JwtSettings
{
    public string SecretKey { get; set; } = string.Empty;
    public string Issuer { get; set; } = string.Empty;
    public string Audience { get; set; } = string.Empty;
    public int ExpirationMinutes { get; set; } = 60;
}
```

**Register in Program.cs:**
```csharp
builder.Services.Configure<JwtSettings>(
    builder.Configuration.GetSection("JwtSettings")
);

// Inject with IOptions<JwtSettings>:
public class JwtTokenService
{
    private readonly JwtSettings _jwtSettings;

    public JwtTokenService(IOptions<JwtSettings> jwtSettings)
    {
        _jwtSettings = jwtSettings.Value;
    }
}
```

---

## 8. Security Best Practices

### DO:
✅ Use `appsettings.Local.json` for development secrets
✅ Use environment variables for production secrets
✅ Generate strong, random JWT secrets (32+ characters)
✅ Rotate secrets periodically (every 90 days)
✅ Use different secrets for each environment
✅ Use Azure Key Vault or AWS Secrets Manager for production
✅ Audit configuration access logs

### DON'T:
❌ Commit secrets to git (even in private repos)
❌ Share `appsettings.Local.json` files
❌ Use weak or predictable secrets
❌ Hardcode secrets in source code
❌ Log sensitive configuration values
❌ Use same secrets across environments
❌ Store secrets in plain text files on servers

---

## 9. Testing Configuration

### Verify Configuration Loading

**File:** `src/GraphQLApp.API/Controllers/ConfigTestController.cs` (Development only)

```csharp
#if DEBUG
using Microsoft.AspNetCore.Mvc;

namespace GraphQLApp.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class ConfigTestController : ControllerBase
{
    private readonly IConfiguration _configuration;

    public ConfigTestController(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    [HttpGet("test")]
    public IActionResult TestConfig()
    {
        // NEVER return actual secrets!
        return Ok(new
        {
            ConnectionStringConfigured = !string.IsNullOrEmpty(
                _configuration.GetConnectionString("DefaultConnection")
            ),
            JwtSecretConfigured = !string.IsNullOrEmpty(
                _configuration["JwtSettings:SecretKey"]
            ),
            JwtSecretLength = _configuration["JwtSettings:SecretKey"]?.Length ?? 0,
            JwtSecretIsPlaceholder = _configuration["JwtSettings:SecretKey"]
                ?.Contains("YOUR_SECRET_KEY") ?? false,
            Environment = _configuration["ASPNETCORE_ENVIRONMENT"]
        });
    }
}
#endif
```

**Test:**
```bash
dotnet run --project src/GraphQLApp.API
curl http://localhost:5000/api/configtest/test
```

**Expected Output:**
```json
{
  "connectionStringConfigured": true,
  "jwtSecretConfigured": true,
  "jwtSecretLength": 44,
  "jwtSecretIsPlaceholder": false,
  "environment": "Development"
}
```

---

## 10. Troubleshooting

### Issue: Configuration not loading
**Symptoms:** NullReferenceException when accessing config
**Solution:**
```bash
# Verify appsettings.Local.json exists
ls src/GraphQLApp.API/appsettings.Local.json

# Verify JSON is valid
# Use online validator or:
dotnet tool install -g dotnet-json-validator
dotnet json-validator src/GraphQLApp.API/appsettings.Local.json
```

---

### Issue: Connection string invalid
**Symptoms:** SqlException: Login failed
**Solution:**
```csharp
// Add logging in Program.cs
var connString = builder.Configuration.GetConnectionString("DefaultConnection");
builder.Logging.AddConsole();
Console.WriteLine($"Connection String Length: {connString?.Length ?? 0}");
Console.WriteLine($"Contains Placeholder: {connString?.Contains("YOUR_") ?? false}");

// DO NOT log the actual connection string!
```

---

### Issue: JWT secret too short
**Symptoms:** SecurityTokenException: IDX10603
**Solution:**
- JWT secrets must be minimum 32 characters (256 bits)
- Generate new secret using methods in section 4
- Update `appsettings.Local.json`

---

### Issue: appsettings.Local.json accidentally committed
**Solution:**
```bash
# If not pushed yet:
git reset HEAD src/GraphQLApp.API/appsettings.Local.json
git checkout -- src/GraphQLApp.API/appsettings.Local.json

# If already pushed:
git rm --cached src/GraphQLApp.API/appsettings.Local.json
git commit -m "Remove accidentally committed secrets"
git push

# IMPORTANT: Rotate all secrets immediately!
# Generate new SQL password, new JWT secret
```

---

## 11. Configuration Checklist

### Development Setup:
- [ ] `appsettings.json` created with placeholders
- [ ] `appsettings.Local.json.template` created
- [ ] `appsettings.Local.json` created (NOT committed)
- [ ] `.gitignore` includes `*.Local.json`
- [ ] JWT secret generated (32+ characters)
- [ ] SQL Server credentials added
- [ ] Configuration test endpoint works
- [ ] `git status` does NOT show `appsettings.Local.json`

### Production Setup (Phase 9):
- [ ] Environment variables configured in IIS
- [ ] Secrets stored in Azure Key Vault / AWS Secrets Manager
- [ ] Connection string uses production database
- [ ] JWT secret is production-grade (64+ characters)
- [ ] TrustServerCertificate=False (use real certs)
- [ ] HTTPS enforced
- [ ] Configuration access logged and audited

---

## 12. Next Steps

### After Workstream 5 Completion:
✅ Configuration system ready
✅ Secrets protected from git
✅ Developer can run application locally

### Enables:
→ Workstream 6: Serilog can read logging config from appsettings.json
→ Phase 2: Database connection string available
→ Phase 4: JWT authentication can be configured
→ Phase 9: Production deployment configuration documented

---

## Acceptance Criteria

- ✅ `appsettings.json` created with placeholders (committed)
- ✅ `appsettings.Local.json.template` created (committed)
- ✅ `appsettings.Local.json` in `.gitignore`
- ✅ Developer can run app with their Local.json
- ✅ No secrets visible in git log
- ✅ Configuration documentation complete
- ✅ JWT secret generation documented
- ✅ Environment variable format documented

---

## Workstream 5 Status

**Status:** READY TO EXECUTE (waiting for WS3)
**Blocker:** Workstream 3 Task 3.2 (API project must exist)
**Estimated Execution Time:** 2-3 hours once unblocked
**Prepared By:** backend-api-developer
**Date:** 2025-11-05

---

**Document Version:** 1.0
**Last Updated:** 2025-11-05
**Author:** backend-api-developer agent
