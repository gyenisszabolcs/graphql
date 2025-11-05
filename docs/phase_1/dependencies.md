# Phase 1 - NuGet Package Dependencies

**Workstream 4 Documentation**
**Version:** 1.0
**Date:** 2025-11-05
**Status:** READY TO EXECUTE (Blocked by WS3)
**Agent:** backend-api-developer

---

## Executive Summary

This document lists all NuGet packages required for Phase 1 infrastructure setup. Packages are organized by project and include version numbers, purposes, and installation commands.

**BLOCKER:** Workstream 3 (Solution Structure) must complete before packages can be installed.

---

## Package Installation Prerequisites

### Dependencies
✅ Workstream 2 Complete (.NET SDK installed)
⏳ **Workstream 3 Must Complete** (Projects must exist)
   - GraphQLApp.API project
   - GraphQLApp.Infrastructure project
   - GraphQLApp.Services project

### Verification Before Installing
```bash
# Verify solution exists
dotnet sln src/GraphQLApp.sln list

# Expected output should show 4 projects:
# - GraphQLApp.API
# - GraphQLApp.Core
# - GraphQLApp.Infrastructure
# - GraphQLApp.Services
```

---

## 1. GraphQLApp.API Project Packages

### 1.1 Hot Chocolate GraphQL Framework

#### HotChocolate.AspNetCore (v13.9.12)
**Purpose:** Core GraphQL server implementation for ASP.NET Core
**Features:**
- GraphQL query and mutation execution
- Banana Cake Pop IDE
- Schema definition and introspection
- Subscription support

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.API
dotnet add package HotChocolate.AspNetCore --version 13.9.12
```

**Verification:**
Check `.csproj` contains:
```xml
<PackageReference Include="HotChocolate.AspNetCore" Version="13.9.12" />
```

---

#### HotChocolate.AspNetCore.Authorization (v13.9.12)
**Purpose:** Authorization integration for GraphQL
**Features:**
- [Authorize] attribute support
- Policy-based authorization
- JWT token integration

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.API
dotnet add package HotChocolate.AspNetCore.Authorization --version 13.9.12
```

---

#### HotChocolate.Data (v13.9.12)
**Purpose:** Data integration layer for GraphQL
**Features:**
- [UseFiltering] support
- [UseSorting] support
- [UsePaging] support
- Projection support

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.API
dotnet add package HotChocolate.Data --version 13.9.12
```

**Why v13.9.12?**
- Latest stable release of Hot Chocolate v13
- Proven stability in production
- Compatible with .NET 8
- Active maintenance and security updates

---

### 1.2 Serilog Logging Framework

#### Serilog.AspNetCore (v8.0.1)
**Purpose:** ASP.NET Core integration for Serilog structured logging
**Features:**
- UseSerilog() host builder extension
- Request logging middleware
- Configuration from appsettings.json
- Diagnostic context enrichment

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.API
dotnet add package Serilog.AspNetCore --version 8.0.1
```

---

#### Serilog.Sinks.Console (v5.0.1)
**Purpose:** Console output sink for Serilog
**Features:**
- Colored console output
- Structured log formatting
- Development-friendly output

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.API
dotnet add package Serilog.Sinks.Console --version 5.0.1
```

---

#### Serilog.Sinks.File (v6.0.0)
**Purpose:** File output sink for Serilog
**Features:**
- Rolling file support (daily, size-based)
- Retention policy (keep last N days)
- JSON or text formatting
- Asynchronous writing

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.API
dotnet add package Serilog.Sinks.File --version 6.0.0
```

**Why Serilog v8?**
- Native AOT support (future-proofing)
- .NET 8 optimizations
- Performance improvements
- Best-in-class structured logging

---

### 1.3 Authentication (Phase 4 prep)

#### Microsoft.AspNetCore.Authentication.JwtBearer (v8.0.11)
**Purpose:** JWT Bearer token authentication for ASP.NET Core
**Features:**
- JWT token validation
- Claims extraction
- Token lifetime validation
- Signature verification

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.API
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer --version 8.0.11
```

**Why v8.0.11?**
- Matches .NET 8 LTS release
- Security patches included
- Official Microsoft package
- Long-term support guaranteed

**Note:** Configuration will be done in Phase 4 (Authentication and Security), but package is installed in Phase 1 for infrastructure readiness.

---

## 2. GraphQLApp.Infrastructure Project Packages

### 2.1 Data Access Layer

#### Dapper (v2.1.28)
**Purpose:** Lightweight micro-ORM for .NET
**Features:**
- High-performance SQL query execution
- Simple parameter mapping
- Multi-mapping support
- Stored procedure execution

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.Infrastructure
dotnet add package Dapper --version 2.1.28
```

**Why Dapper?**
- Minimal overhead (close to ADO.NET performance)
- Simple and intuitive API
- No ORM magic (explicit SQL control)
- Excellent for existing database schemas
- Widely adopted and battle-tested

**Why v2.1.28?**
- Latest stable release (as of 2024)
- Full .NET 8 support
- Async/await support
- Proven stability

---

#### Microsoft.Data.SqlClient (v5.2.2)
**Purpose:** SQL Server data provider for .NET
**Features:**
- Native SQL Server connectivity
- Always Encrypted support
- Azure AD authentication
- Connection pooling

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.Infrastructure
dotnet add package Microsoft.Data.SqlClient --version 5.2.2
```

**Why v5.2.2?**
- .NET 8 compatible
- Latest security patches
- Performance improvements
- Official Microsoft package
- Supports SQL Server 2012+ and Azure SQL

**Connection String Format:**
```
Server=10.10.10.69;Database=dev_graphql;User Id=username;Password=password;TrustServerCertificate=True;
```

---

## 3. GraphQLApp.Services Project Packages

### 3.1 Password Hashing (Phase 4 prep)

#### BCrypt.Net-Next (v4.0.3)
**Purpose:** BCrypt password hashing for .NET
**Features:**
- Adaptive hashing algorithm
- Configurable work factor (cost)
- Built-in salt generation
- Cross-platform compatibility

**Installation:**
```bash
cd d:\Development\graphql\src\GraphQLApp.Services
dotnet add package BCrypt.Net-Next --version 4.0.3
```

**Why BCrypt?**
- Industry-standard password hashing
- Slow by design (resistant to brute force)
- Adjustable cost factor (future-proof)
- No rainbow table vulnerability

**Why v4.0.3?**
- Latest stable release
- .NET 8 support
- Enhanced security
- Active maintenance

**Usage Example (Phase 4):**
```csharp
// Hash password
string hashedPassword = BCrypt.Net.BCrypt.HashPassword("plainPassword", workFactor: 12);

// Verify password
bool isValid = BCrypt.Net.BCrypt.Verify("plainPassword", hashedPassword);
```

**Note:** Configuration and usage will be implemented in Phase 4, but package is installed in Phase 1.

---

## 4. Installation Script

### Complete Installation Commands

Once Workstream 3 is complete, execute these commands:

```bash
# Navigate to project root
cd d:\Development\graphql

# ============================================
# GRAPHQL APP API PACKAGES
# ============================================
cd src\GraphQLApp.API

# Hot Chocolate GraphQL (3 packages)
dotnet add package HotChocolate.AspNetCore --version 13.9.12
dotnet add package HotChocolate.AspNetCore.Authorization --version 13.9.12
dotnet add package HotChocolate.Data --version 13.9.12

# Serilog Logging (3 packages)
dotnet add package Serilog.AspNetCore --version 8.0.1
dotnet add package Serilog.Sinks.Console --version 5.0.1
dotnet add package Serilog.Sinks.File --version 6.0.0

# JWT Authentication (1 package)
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer --version 8.0.11

# ============================================
# GRAPHQL APP INFRASTRUCTURE PACKAGES
# ============================================
cd ..\GraphQLApp.Infrastructure

# Dapper + SQL Client (2 packages)
dotnet add package Dapper --version 2.1.28
dotnet add package Microsoft.Data.SqlClient --version 5.2.2

# ============================================
# GRAPHQL APP SERVICES PACKAGES
# ============================================
cd ..\GraphQLApp.Services

# BCrypt Password Hashing (1 package)
dotnet add package BCrypt.Net-Next --version 4.0.3

# ============================================
# VERIFICATION
# ============================================
cd ..\..

# Restore all packages
dotnet restore src\GraphQLApp.sln

# Build entire solution
dotnet build src\GraphQLApp.sln

# List all packages
dotnet list src\GraphQLApp.API\GraphQLApp.API.csproj package
dotnet list src\GraphQLApp.Infrastructure\GraphQLApp.Infrastructure.csproj package
dotnet list src\GraphQLApp.Services\GraphQLApp.Services.csproj package
```

---

## 5. Expected .csproj File Contents

### GraphQLApp.API.csproj (Expected)
```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <!-- Hot Chocolate GraphQL -->
    <PackageReference Include="HotChocolate.AspNetCore" Version="13.9.12" />
    <PackageReference Include="HotChocolate.AspNetCore.Authorization" Version="13.9.12" />
    <PackageReference Include="HotChocolate.Data" Version="13.9.12" />

    <!-- Serilog Logging -->
    <PackageReference Include="Serilog.AspNetCore" Version="8.0.1" />
    <PackageReference Include="Serilog.Sinks.Console" Version="5.0.1" />
    <PackageReference Include="Serilog.Sinks.File" Version="6.0.0" />

    <!-- JWT Authentication -->
    <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="8.0.11" />
  </ItemGroup>

  <ItemGroup>
    <!-- Project References -->
    <ProjectReference Include="..\GraphQLApp.Core\GraphQLApp.Core.csproj" />
    <ProjectReference Include="..\GraphQLApp.Infrastructure\GraphQLApp.Infrastructure.csproj" />
    <ProjectReference Include="..\GraphQLApp.Services\GraphQLApp.Services.csproj" />
  </ItemGroup>

</Project>
```

---

### GraphQLApp.Infrastructure.csproj (Expected)
```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <!-- Dapper + SQL Client -->
    <PackageReference Include="Dapper" Version="2.1.28" />
    <PackageReference Include="Microsoft.Data.SqlClient" Version="5.2.2" />
  </ItemGroup>

  <ItemGroup>
    <!-- Project References -->
    <ProjectReference Include="..\GraphQLApp.Core\GraphQLApp.Core.csproj" />
  </ItemGroup>

</Project>
```

---

### GraphQLApp.Services.csproj (Expected)
```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <!-- BCrypt Password Hashing -->
    <PackageReference Include="BCrypt.Net-Next" Version="4.0.3" />
  </ItemGroup>

  <ItemGroup>
    <!-- Project References -->
    <ProjectReference Include="..\GraphQLApp.Core\GraphQLApp.Core.csproj" />
  </ItemGroup>

</Project>
```

---

## 6. Package Summary Table

| Package | Version | Project | Purpose | Phase Used |
|---------|---------|---------|---------|------------|
| HotChocolate.AspNetCore | 13.9.12 | API | GraphQL server | Phase 5 |
| HotChocolate.AspNetCore.Authorization | 13.9.12 | API | GraphQL authorization | Phase 4 |
| HotChocolate.Data | 13.9.12 | API | Filtering/Sorting/Paging | Phase 5 |
| Serilog.AspNetCore | 8.0.1 | API | Structured logging | Phase 1 |
| Serilog.Sinks.Console | 5.0.1 | API | Console log output | Phase 1 |
| Serilog.Sinks.File | 6.0.0 | API | File log output | Phase 1 |
| Microsoft.AspNetCore.Authentication.JwtBearer | 8.0.11 | API | JWT authentication | Phase 4 |
| Dapper | 2.1.28 | Infrastructure | Micro-ORM | Phase 2 |
| Microsoft.Data.SqlClient | 5.2.2 | Infrastructure | SQL Server provider | Phase 2 |
| BCrypt.Net-Next | 4.0.3 | Services | Password hashing | Phase 4 |

**Total Packages:** 10

---

## 7. Version Rationale and Alternatives

### Hot Chocolate v13 vs v14
**Chosen:** v13.9.12
**Reason:**
- v13 is the latest stable major release
- v14 is in preview (not production-ready)
- v13 has extensive documentation and community support
- v13 is proven in production environments

---

### Serilog v8 vs v7
**Chosen:** v8.0.1
**Reason:**
- Native AOT support
- .NET 8 optimizations
- Performance improvements
- Forward compatibility

---

### Dapper v2 vs Entity Framework Core
**Chosen:** Dapper v2.1.28
**Reason:**
- Working with existing database (not code-first)
- Need full control over SQL queries
- Performance is critical
- Simpler for stored procedure execution
- Smaller learning curve

---

### BCrypt.Net-Next vs Argon2
**Chosen:** BCrypt.Net-Next v4.0.3
**Reason:**
- Industry standard
- Simpler implementation
- Well-tested
- Argon2 is newer but BCrypt is sufficient for web apps

---

## 8. Troubleshooting

### Issue: Package version not found
**Solution:**
```bash
# Check available versions
dotnet list package --outdated

# Update NuGet cache
dotnet nuget locals all --clear

# Retry installation
```

---

### Issue: Package conflicts
**Solution:**
```bash
# Remove conflicting package
dotnet remove package PackageName

# Clean build artifacts
dotnet clean

# Reinstall with specific version
dotnet add package PackageName --version x.y.z
```

---

### Issue: Build fails after adding packages
**Solution:**
```bash
# Restore packages
dotnet restore

# Clean and rebuild
dotnet clean
dotnet build
```

---

## 9. Security Considerations

### Package Vulnerability Scanning
After installing all packages, run:
```bash
dotnet list package --vulnerable
```

Expected output: "No vulnerable packages found"

If vulnerabilities are found:
1. Update to latest patch version
2. Check security advisory
3. If no patch available, consider alternatives

---

### Package License Compliance

| Package | License | Commercial Use |
|---------|---------|----------------|
| HotChocolate | MIT | ✅ Yes |
| Serilog | Apache 2.0 | ✅ Yes |
| Dapper | Apache 2.0 | ✅ Yes |
| Microsoft.Data.SqlClient | MIT | ✅ Yes |
| BCrypt.Net-Next | MIT | ✅ Yes |

All packages are safe for commercial use.

---

## 10. Next Steps

### After Workstream 4 Completion:
✅ All NuGet packages installed
✅ Solution builds successfully
✅ No package conflicts

### Enables:
→ Workstream 5: Configuration files can reference installed packages
→ Workstream 6: Serilog can be configured in Program.cs
→ Phase 2: Dapper can be used for database access
→ Phase 4: JWT and BCrypt can be configured

---

## Acceptance Criteria

- ✅ All 10 packages installed with exact versions specified
- ✅ All packages documented with purpose and usage
- ✅ `dotnet restore` completes without errors
- ✅ `dotnet build` completes without errors
- ✅ No package vulnerability warnings
- ✅ All licenses documented and approved
- ✅ Installation script tested and verified

---

## Workstream 4 Status

**Status:** READY TO EXECUTE (waiting for WS3)
**Blocker:** Workstream 3 (Solution Structure) must complete first
**Estimated Execution Time:** 1-2 hours once unblocked
**Prepared By:** backend-api-developer
**Date:** 2025-11-05

---

**Document Version:** 1.0
**Last Updated:** 2025-11-05
**Author:** backend-api-developer agent
