# Phase 1 - Infrastructure and Architecture
## Product Requirements Document

**Version:** 1.0
**Date:** 2025-11-05
**Phase:** 1 of 10
**Status:** In Progress
**Document Owner:** Product Requirements Architect

---

## Executive Summary

Phase 1 establishes the foundational infrastructure and development environment for the GraphQL API project. This phase focuses on:
- Validating connectivity to existing SQL Server database (dev_graphql)
- Setting up .NET 8 development environment
- Creating solution and project structure
- Installing required dependencies
- Configuring logging and configuration management

**Success Criteria:** A fully operational development environment with verified database connectivity, proper project structure, and working logging infrastructure.

**Timeline:** 3-4 days
**Dependencies:** Phase 0 (completed)
**Blocks:** Phase 2 (Database Integration) cannot start until Phase 1 completes

---

## Problem Statement

Currently, the project has documentation and planning completed (Phase 0), but no executable code or infrastructure. Developers cannot begin API development without:
1. Verified access to the existing SQL Server database
2. Properly configured development environment
3. Project structure following .NET best practices
4. Dependency management (NuGet packages)
5. Configuration system for credentials and settings
6. Logging infrastructure for debugging and monitoring

**This phase solves:** The "Day 1" setup problem, enabling all developers to start productive work with consistent tooling and verified access to required resources.

---

## User Personas

### Persona 1: Backend Developer (Primary)
- **Name:** Alex (Senior .NET Developer)
- **Goals:** Start building GraphQL API immediately, write clean testable code
- **Pain Points:** Manual environment setup is error-prone, missing dependencies block progress
- **Needs:** Step-by-step setup guide, automated scripts where possible, verified database access
- **Technical Proficiency:** Expert in C#/.NET, familiar with GraphQL, comfortable with SQL
- **Quote:** "I don't want to waste a day fighting with environment setup - just let me code"

### Persona 2: DevOps Engineer (Primary)
- **Name:** Jordan (Infrastructure Specialist)
- **Goals:** Ensure consistent environments across dev/test/prod, automate deployment
- **Pain Points:** Configuration drift, secrets leaking to git, unclear dependencies
- **Needs:** Clear separation of secrets, documented infrastructure, repeatable setup
- **Technical Proficiency:** Expert in Windows Server/IIS, SQL Server, CI/CD pipelines
- **Quote:** "Security and repeatability are non-negotiable from day one"

### Persona 3: Technical Architect (Secondary)
- **Name:** Morgan (Solution Architect)
- **Goals:** Ensure project structure supports future scalability and maintainability
- **Pain Points:** Poor initial structure leads to technical debt later
- **Needs:** Clean separation of concerns, testable architecture, documented decisions
- **Technical Proficiency:** Expert in system design, .NET architecture patterns
- **Quote:** "Get the structure right now, or pay for it with interest later"

---

## Phase 1 User Stories

### Epic 1: Database Connectivity

#### Story 1.1: SQL Server Connection Validation
**As a** backend developer
**I want to** verify connectivity to the SQL Server (10.10.10.69) and dev_graphql database
**So that** I know I can access the data layer before writing any code

**Acceptance Criteria:**
- [ ] GIVEN the SQL Server is at 10.10.10.69
- [ ] WHEN I test the connection with provided credentials
- [ ] THEN the connection succeeds without errors
- [ ] AND I can query the INFORMATION_SCHEMA to list tables
- [ ] AND I receive a list containing CIKK, GYARTO, PARTNER, USERS tables

**Technical Considerations:**
- Use SQL Server Management Studio (SSMS) or sqlcmd for initial testing
- Document exact server version and database collation
- Test both read and write permissions
- Verify network connectivity (firewall rules, VPN if needed)

**Definition of Done:**
- Connection test results documented
- All 4 tables (CIKK, GYARTO, PARTNER, USERS) confirmed present
- Table schemas extracted and documented
- Connectivity documented in `docs/phase_1/database_connectivity.md`

---

#### Story 1.2: Database Schema Documentation
**As a** backend developer
**I want to** have complete documentation of existing table schemas
**So that** I can create accurate C# entity models and understand data relationships

**Acceptance Criteria:**
- [ ] GIVEN the dev_graphql database
- [ ] WHEN I query the schema metadata
- [ ] THEN I receive complete column definitions for CIKK table (name, type, length, nullable, primary key)
- [ ] AND I receive complete column definitions for GYARTO table
- [ ] AND I receive complete column definitions for PARTNER table
- [ ] AND I receive complete column definitions for USERS table
- [ ] AND I document all foreign key relationships
- [ ] AND I document all existing indexes

**Technical Considerations:**
- Use T-SQL queries on INFORMATION_SCHEMA views
- Capture exact data types (NVARCHAR length, INT vs BIGINT, etc.)
- Document nullable vs NOT NULL constraints
- Identify primary keys and foreign keys
- List existing indexes (for performance planning)

**Definition of Done:**
- Schema documentation created at `docs/phase_1/database_schema.md`
- ER diagram created showing relationships
- Index list documented
- No assumptions - all field types verified from actual schema

**Priority:** HIGH (blocks entity model creation in Phase 2)

---

### Epic 2: Development Environment Setup

#### Story 2.1: .NET 8 SDK Installation
**As a** backend developer
**I want to** have .NET 8 SDK installed and verified
**So that** I can build and run .NET 8 applications

**Acceptance Criteria:**
- [ ] GIVEN a Windows development machine
- [ ] WHEN I run `dotnet --version`
- [ ] THEN the output shows version 8.0.x
- [ ] AND I can run `dotnet new webapi` successfully
- [ ] AND I can run `dotnet build` on a test project without errors

**Technical Considerations:**
- Download from https://dotnet.microsoft.com/download/dotnet/8.0
- Choose the SDK (not just runtime)
- Verify PATH environment variable updated
- Test in both CMD and PowerShell

**Definition of Done:**
- .NET 8 SDK installed
- Version verified with `dotnet --version`
- Test project created and built successfully
- Installation documented in `docs/phase_1/environment_setup.md`

**Priority:** HIGH (blocks all .NET development)

---

#### Story 2.2: VS Code Extensions Installation
**As a** backend developer
**I want to** have required VS Code extensions installed
**So that** I have IntelliSense, debugging, and GraphQL support

**Acceptance Criteria:**
- [ ] GIVEN VS Code is installed
- [ ] WHEN I install the C# Dev Kit extension
- [ ] THEN I have C# syntax highlighting and IntelliSense
- [ ] AND WHEN I install the GraphQL extension
- [ ] THEN I have GraphQL syntax highlighting
- [ ] AND WHEN I open a .cs file
- [ ] THEN I see IntelliSense suggestions

**Required Extensions:**
1. **C# Dev Kit** (microsoft.csdevkit) - C# development
2. **GraphQL: Language Feature Support** (graphql.vscode-graphql) - GraphQL syntax
3. **C#** (ms-dotnettools.csharp) - Base C# support
4. **NuGet Package Manager** (jmrog.vscode-nuget-package-manager) - Easy package management

**Optional but Recommended:**
- **GitLens** - Enhanced git capabilities
- **Error Lens** - Inline error display
- **Prettier** - Code formatting

**Technical Considerations:**
- Install via Extensions panel or CLI (`code --install-extension`)
- Restart VS Code after installation
- Configure C# extension for .NET 8

**Definition of Done:**
- All required extensions installed
- Test file created with working IntelliSense
- Extension list documented in `docs/phase_1/environment_setup.md`

**Priority:** MEDIUM (improves developer experience but not blocking)

---

#### Story 2.3: Workspace Configuration
**As a** backend developer
**I want to** have a VS Code workspace configured for the project
**So that** all team members have consistent settings

**Acceptance Criteria:**
- [ ] GIVEN the project root directory
- [ ] WHEN I create a `.vscode/settings.json` file
- [ ] THEN it contains C# formatting rules
- [ ] AND it contains recommended extensions
- [ ] AND it excludes bin/obj folders from search
- [ ] AND it configures the integrated terminal

**Workspace Settings to Include:**
```json
{
  "files.exclude": {
    "**/bin": true,
    "**/obj": true
  },
  "omnisharp.enableRoslynAnalyzers": true,
  "omnisharp.enableEditorConfigSupport": true,
  "[csharp]": {
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.organizeImports": true
    }
  }
}
```

**Definition of Done:**
- `.vscode/settings.json` created
- `.vscode/extensions.json` with recommended extensions
- `.vscode/launch.json` for debugging (template)
- Workspace committed to git
- Documented in `docs/phase_1/environment_setup.md`

**Priority:** MEDIUM

---

### Epic 3: Solution and Project Structure

#### Story 3.1: .NET Solution Creation
**As a** technical architect
**I want to** create a .NET solution with proper project organization
**So that** the codebase is maintainable, testable, and follows clean architecture

**Acceptance Criteria:**
- [ ] GIVEN the project root directory (d:\Development\graphql)
- [ ] WHEN I run `dotnet new sln -n GraphQLApp`
- [ ] THEN a GraphQLApp.sln file is created
- [ ] AND the solution can be opened in VS Code
- [ ] AND the solution is ready to accept projects

**Technical Considerations:**
- Use descriptive solution name (GraphQLApp)
- Create in src/ subdirectory for organization
- Follow .NET solution best practices

**Definition of Done:**
- GraphQLApp.sln created in `d:\Development\graphql\src\`
- Solution opens without errors in VS Code
- Documented in project structure guide

**Priority:** HIGH (blocks all project creation)

---

#### Story 3.2: API Project Creation
**As a** technical architect
**I want to** create the main API project (GraphQLApp.API)
**So that** we have the entry point for the GraphQL server

**Acceptance Criteria:**
- [ ] GIVEN the solution exists
- [ ] WHEN I create a new webapi project named GraphQLApp.API
- [ ] THEN the project is created in src/GraphQLApp.API/
- [ ] AND the project is added to the solution
- [ ] AND I can build the project successfully
- [ ] AND I can run the project and see the default API response

**Technical Considerations:**
- Use `dotnet new webapi -n GraphQLApp.API`
- Set target framework to net8.0
- Remove default WeatherForecast controller (clean slate)
- Configure to run on https://localhost:5001 (HTTPS)

**Directory Structure:**
```
src/GraphQLApp.API/
├── Controllers/
├── GraphQL/
│   ├── Queries/
│   ├── Mutations/
│   ├── Types/
│   └── Filters/
├── Middleware/
├── Program.cs
├── appsettings.json
└── GraphQLApp.API.csproj
```

**Definition of Done:**
- GraphQLApp.API project created
- Added to solution
- Builds without errors
- Runs on HTTPS port 5001
- Default controllers removed

**Priority:** HIGH (critical path)

---

#### Story 3.3: Core Library Project Creation
**As a** technical architect
**I want to** create the Core library project (GraphQLApp.Core)
**So that** we have a place for domain models, interfaces, and DTOs (no dependencies)

**Acceptance Criteria:**
- [ ] GIVEN the solution exists
- [ ] WHEN I create a classlib project named GraphQLApp.Core
- [ ] THEN the project is created in src/GraphQLApp.Core/
- [ ] AND the project is added to the solution
- [ ] AND the project has NO dependencies (pure domain layer)
- [ ] AND I can build the project successfully

**Technical Considerations:**
- Use `dotnet new classlib -n GraphQLApp.Core`
- This is the innermost layer (Clean Architecture)
- Should NOT reference any other projects
- Contains only POCOs, interfaces, enums, DTOs

**Directory Structure:**
```
src/GraphQLApp.Core/
├── Entities/
│   ├── User.cs
│   ├── Cikk.cs
│   ├── Gyarto.cs
│   └── Partner.cs
├── Interfaces/
│   ├── IUserRepository.cs
│   ├── ICikkRepository.cs
│   └── ...
├── DTOs/
│   ├── LoginRequest.cs
│   └── LoginResponse.cs
└── GraphQLApp.Core.csproj
```

**Definition of Done:**
- GraphQLApp.Core project created
- Added to solution
- Folder structure created (Entities/, Interfaces/, DTOs/)
- Builds without errors
- Zero external dependencies (verified in .csproj)

**Priority:** HIGH (critical path)

---

#### Story 3.4: Infrastructure Library Project Creation
**As a** technical architect
**I want to** create the Infrastructure library project (GraphQLApp.Infrastructure)
**So that** we have a place for data access implementations (Dapper, repositories)

**Acceptance Criteria:**
- [ ] GIVEN the solution exists
- [ ] WHEN I create a classlib project named GraphQLApp.Infrastructure
- [ ] THEN the project is created in src/GraphQLApp.Infrastructure/
- [ ] AND the project is added to the solution
- [ ] AND the project references GraphQLApp.Core
- [ ] AND I can build the project successfully

**Technical Considerations:**
- Use `dotnet new classlib -n GraphQLApp.Infrastructure`
- Add project reference to Core: `dotnet add reference ../GraphQLApp.Core/GraphQLApp.Core.csproj`
- This layer implements interfaces from Core

**Directory Structure:**
```
src/GraphQLApp.Infrastructure/
├── Data/
│   └── DapperContext.cs
├── Repositories/
│   ├── UserRepository.cs
│   ├── CikkRepository.cs
│   ├── GyartoRepository.cs
│   ├── PartnerRepository.cs
│   └── StoredProcedureExecutor.cs
└── GraphQLApp.Infrastructure.csproj
```

**Definition of Done:**
- GraphQLApp.Infrastructure project created
- Added to solution
- References GraphQLApp.Core
- Folder structure created (Data/, Repositories/)
- Builds without errors

**Priority:** HIGH (critical path)

---

#### Story 3.5: Services Library Project Creation
**As a** technical architect
**I want to** create the Services library project (GraphQLApp.Services)
**So that** we have a place for business logic (AuthService, JwtTokenService)

**Acceptance Criteria:**
- [ ] GIVEN the solution exists
- [ ] WHEN I create a classlib project named GraphQLApp.Services
- [ ] THEN the project is created in src/GraphQLApp.Services/
- [ ] AND the project is added to the solution
- [ ] AND the project references GraphQLApp.Core
- [ ] AND I can build the project successfully

**Technical Considerations:**
- Use `dotnet new classlib -n GraphQLApp.Services`
- Add project reference to Core
- Contains application services (not domain services)

**Directory Structure:**
```
src/GraphQLApp.Services/
├── AuthService.cs
├── JwtTokenService.cs
└── GraphQLApp.Services.csproj
```

**Definition of Done:**
- GraphQLApp.Services project created
- Added to solution
- References GraphQLApp.Core
- Builds without errors

**Priority:** MEDIUM (needed for Phase 4)

---

#### Story 3.6: Project Reference Configuration
**As a** technical architect
**I want to** configure all project references correctly
**So that** dependency flow follows clean architecture (API → Services → Infrastructure → Core)

**Acceptance Criteria:**
- [ ] GIVEN all projects are created
- [ ] WHEN I configure references
- [ ] THEN GraphQLApp.API references Core, Infrastructure, Services
- [ ] AND GraphQLApp.Services references Core only
- [ ] AND GraphQLApp.Infrastructure references Core only
- [ ] AND GraphQLApp.Core references nothing (zero dependencies)
- [ ] AND the entire solution builds successfully

**Dependency Graph:**
```
GraphQLApp.API
    ├─> GraphQLApp.Core
    ├─> GraphQLApp.Infrastructure
    └─> GraphQLApp.Services

GraphQLApp.Services
    └─> GraphQLApp.Core

GraphQLApp.Infrastructure
    └─> GraphQLApp.Core

GraphQLApp.Core
    (no dependencies)
```

**Technical Considerations:**
- Use `dotnet add reference <path>` for each
- Verify no circular dependencies
- Run `dotnet build` at solution level

**Definition of Done:**
- All project references configured
- Dependency graph follows clean architecture
- Solution builds without errors
- Reference diagram documented in `docs/phase_1/architecture.md`

**Priority:** HIGH (critical path)

---

### Epic 4: NuGet Package Installation

#### Story 4.1: Hot Chocolate GraphQL Packages
**As a** backend developer
**I want to** install Hot Chocolate NuGet packages
**So that** I can build GraphQL APIs

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.API project
- [ ] WHEN I install HotChocolate.AspNetCore (v13.9+)
- [ ] THEN the package is added to the .csproj file
- [ ] AND I install HotChocolate.AspNetCore.Authorization
- [ ] AND I install HotChocolate.Data (for filtering/sorting/paging)
- [ ] AND the project builds successfully with the packages

**Packages to Install:**
```bash
cd src/GraphQLApp.API
dotnet add package HotChocolate.AspNetCore --version 13.9.12
dotnet add package HotChocolate.AspNetCore.Authorization --version 13.9.12
dotnet add package HotChocolate.Data --version 13.9.12
```

**Technical Considerations:**
- Use consistent version numbers across Hot Chocolate packages
- Version 13.9.12 is latest stable as of Nov 2025
- Authorization package needed for [Authorize] attributes
- Data package enables filtering, sorting, paging

**Definition of Done:**
- All Hot Chocolate packages installed
- Versions are consistent (all 13.9.12)
- Project builds without errors
- Package list documented in `docs/phase_1/dependencies.md`

**Priority:** HIGH (critical for Phase 5)

---

#### Story 4.2: Dapper Data Access Packages
**As a** backend developer
**I want to** install Dapper and SQL Server client packages
**So that** I can access the SQL Server database

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.Infrastructure project
- [ ] WHEN I install Dapper (v2.1+)
- [ ] THEN the package is added to the .csproj file
- [ ] AND I install Microsoft.Data.SqlClient (v5.1+)
- [ ] AND the project builds successfully

**Packages to Install:**
```bash
cd src/GraphQLApp.Infrastructure
dotnet add package Dapper --version 2.1.28
dotnet add package Microsoft.Data.SqlClient --version 5.2.2
```

**Technical Considerations:**
- Dapper is a micro-ORM for high performance
- Microsoft.Data.SqlClient is the modern SQL Server driver
- Avoid System.Data.SqlClient (deprecated)

**Definition of Done:**
- Dapper and Microsoft.Data.SqlClient installed
- Project builds without errors
- Package versions documented

**Priority:** HIGH (critical for Phase 2)

---

#### Story 4.3: Serilog Logging Packages
**As a** backend developer
**I want to** install Serilog packages
**So that** I can implement structured logging

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.API project
- [ ] WHEN I install Serilog.AspNetCore
- [ ] THEN the package is added to the .csproj file
- [ ] AND I install Serilog.Sinks.Console
- [ ] AND I install Serilog.Sinks.File
- [ ] AND the project builds successfully

**Packages to Install:**
```bash
cd src/GraphQLApp.API
dotnet add package Serilog.AspNetCore --version 8.0.1
dotnet add package Serilog.Sinks.Console --version 5.0.1
dotnet add package Serilog.Sinks.File --version 6.0.0
```

**Technical Considerations:**
- Serilog.AspNetCore includes core Serilog + ASP.NET integration
- Console sink for development debugging
- File sink for production logging
- Rolling file configuration needed (separate story)

**Definition of Done:**
- All Serilog packages installed
- Project builds without errors
- Package versions documented

**Priority:** HIGH (needed for Story 6.1)

---

#### Story 4.4: JWT Authentication Packages
**As a** backend developer
**I want to** install JWT Bearer authentication packages
**So that** I can implement token-based authentication (in Phase 4)

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.API project
- [ ] WHEN I install Microsoft.AspNetCore.Authentication.JwtBearer
- [ ] THEN the package is added to the .csproj file
- [ ] AND the project builds successfully

**Packages to Install:**
```bash
cd src/GraphQLApp.API
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer --version 8.0.11
```

**Technical Considerations:**
- Version should match .NET version (8.0.x)
- Configuration happens in Phase 4
- Installing now to avoid dependency issues later

**Definition of Done:**
- JWT Bearer package installed
- Project builds without errors
- Noted for Phase 4 usage

**Priority:** MEDIUM (not used until Phase 4)

---

#### Story 4.5: BCrypt Password Hashing Package
**As a** backend developer
**I want to** install BCrypt.Net for password hashing
**So that** I can securely hash passwords (in Phase 4)

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.Services project
- [ ] WHEN I install BCrypt.Net-Next
- [ ] THEN the package is added to the .csproj file
- [ ] AND the project builds successfully

**Packages to Install:**
```bash
cd src/GraphQLApp.Services
dotnet add package BCrypt.Net-Next --version 4.0.3
```

**Technical Considerations:**
- Use BCrypt.Net-Next (actively maintained fork)
- Avoid old BCrypt.Net (unmaintained)
- Work factor of 12 is recommended for security

**Definition of Done:**
- BCrypt.Net-Next installed
- Project builds without errors
- Usage example documented for Phase 4

**Priority:** MEDIUM (not used until Phase 4)

---

### Epic 5: Configuration Management

#### Story 5.1: Base appsettings.json Creation
**As a** backend developer
**I want to** create appsettings.json without secrets
**So that** I can configure the application safely for version control

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.API project
- [ ] WHEN I create appsettings.json
- [ ] THEN it contains ConnectionStrings section with placeholder
- [ ] AND it contains Serilog configuration
- [ ] AND it contains JwtSettings section (template)
- [ ] AND it does NOT contain actual passwords or secrets
- [ ] AND the file is committed to git

**appsettings.json Template:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=10.10.10.69;Database=dev_graphql;User Id=YOUR_USERNAME;Password=YOUR_PASSWORD;TrustServerCertificate=True;Encrypt=True;"
  },
  "JwtSettings": {
    "SecretKey": "YOUR_SECRET_KEY_MIN_32_CHARACTERS",
    "Issuer": "GraphQLApp",
    "Audience": "GraphQLApp",
    "ExpiryMinutes": 60
  },
  "Serilog": {
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "System": "Warning"
      }
    },
    "WriteTo": [
      {
        "Name": "Console"
      },
      {
        "Name": "File",
        "Args": {
          "path": "logs/log-.txt",
          "rollingInterval": "Day",
          "retainedFileCountLimit": 7
        }
      }
    ]
  },
  "AllowedHosts": "*"
}
```

**Technical Considerations:**
- Use placeholder values (YOUR_USERNAME, YOUR_PASSWORD)
- Include TrustServerCertificate=True for internal SQL Server
- Configure log retention (7 days)
- Set minimum log levels appropriately

**Definition of Done:**
- appsettings.json created with placeholders
- File committed to git
- No secrets in the file (verified)
- Documented in `docs/phase_1/configuration.md`

**Priority:** HIGH (critical path)

---

#### Story 5.2: Local Settings File Creation
**As a** backend developer
**I want to** create appsettings.Local.json for my actual credentials
**So that** I can run the application locally without exposing secrets

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.API project
- [ ] WHEN I create appsettings.Local.json
- [ ] THEN it contains my actual SQL Server credentials
- [ ] AND it contains a real JWT secret key (min 32 characters)
- [ ] AND the file is NOT committed to git (.gitignore)
- [ ] AND I can run the application and connect to the database

**appsettings.Local.json Example:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=10.10.10.69;Database=dev_graphql;User Id=actual_user;Password=actual_password;TrustServerCertificate=True;Encrypt=True;"
  },
  "JwtSettings": {
    "SecretKey": "ThisIsMySecretKeyForDevelopmentMin32Chars123!"
  }
}
```

**Technical Considerations:**
- This file overrides appsettings.json values
- ASP.NET Core merges configs in order: appsettings.json → appsettings.{Environment}.json → appsettings.Local.json
- File should be in .gitignore
- Each developer has their own copy

**Definition of Done:**
- appsettings.Local.json.template created and committed (as example)
- appsettings.Local.json created locally (NOT committed)
- .gitignore updated to exclude appsettings.Local.json
- Developer can run app and connect to DB
- Documented in `docs/phase_1/configuration.md`

**Priority:** HIGH (critical path)

---

#### Story 5.3: .gitignore Update
**As a** DevOps engineer
**I want to** update .gitignore to exclude sensitive files
**So that** secrets are never committed to version control

**Acceptance Criteria:**
- [ ] GIVEN the project root .gitignore
- [ ] WHEN I add patterns for sensitive files
- [ ] THEN appsettings.Local.json is excluded
- [ ] AND bin/ and obj/ folders are excluded
- [ ] AND logs/ folder is excluded
- [ ] AND .vs/ folder is excluded
- [ ] AND *.user files are excluded

**.gitignore Additions:**
```gitignore
# .NET build artifacts
**/bin/
**/obj/
**/out/

# User-specific files
*.user
*.suo
*.userosscache
*.sln.docstates

# Visual Studio
.vs/
.vscode/settings.json

# Secrets and local configuration
**/appsettings.Local.json
appsettings.*.Local.json

# Logs
logs/
*.log

# Database
*.db
*.db-shm
*.db-wal
```

**Technical Considerations:**
- Use ** for recursive matching
- Exclude IDE-specific files
- Exclude all local config variants
- Keep .vscode/extensions.json (team shared)

**Definition of Done:**
- .gitignore updated with all exclusions
- Verified that appsettings.Local.json is not tracked
- Changes committed to git
- Documented in `docs/phase_1/configuration.md`

**Priority:** HIGH (security critical)

---

#### Story 5.4: Environment Variable Configuration
**As a** DevOps engineer
**I want to** document how to use environment variables for configuration
**So that** production deployments can inject secrets securely

**Acceptance Criteria:**
- [ ] GIVEN production deployment requirements
- [ ] WHEN I document environment variable usage
- [ ] THEN I provide examples for ConnectionStrings__DefaultConnection
- [ ] AND I provide examples for JwtSettings__SecretKey
- [ ] AND I explain the double-underscore syntax
- [ ] AND I document IIS Application Settings usage

**Environment Variable Examples:**
```bash
# PowerShell (Development)
$env:ConnectionStrings__DefaultConnection = "Server=10.10.10.69;Database=dev_graphql;User Id=user;Password=pass;TrustServerCertificate=True;Encrypt=True;"
$env:JwtSettings__SecretKey = "ProductionSecretKeyMin32CharsLongSecure!"

# IIS Application Settings (Production)
# Navigate to: IIS Manager → Site → Configuration Editor → system.webServer/aspNetCore → environmentVariables
# Add:
# Name: ConnectionStrings__DefaultConnection
# Value: Server=...;Database=...;User Id=...;Password=...

# Name: JwtSettings__SecretKey
# Value: <secure-random-key>
```

**Technical Considerations:**
- ASP.NET Core uses double underscore (__) for hierarchical config
- Environment variables override appsettings.json
- IIS stores env vars in applicationHost.config (encrypted)
- Azure App Service uses Application Settings

**Definition of Done:**
- Environment variable usage documented in `docs/phase_1/configuration.md`
- Examples provided for dev and prod
- IIS setup instructions included
- Security notes added (key length, randomness)

**Priority:** MEDIUM (needed for Phase 9 deployment)

---

### Epic 6: Logging Infrastructure

#### Story 6.1: Serilog Configuration in Program.cs
**As a** backend developer
**I want to** configure Serilog in Program.cs
**So that** all application logs are captured with structured logging

**Acceptance Criteria:**
- [ ] GIVEN the GraphQLApp.API project with Serilog packages installed
- [ ] WHEN I configure Serilog in Program.cs
- [ ] THEN logs are written to console in development
- [ ] AND logs are written to rolling files in logs/
- [ ] AND log levels are configurable via appsettings.json
- [ ] AND I can see HTTP request logs
- [ ] AND structured properties are captured

**Program.cs Configuration:**
```csharp
using Serilog;

var builder = WebApplication.CreateBuilder(args);

// Configure Serilog
builder.Host.UseSerilog((context, services, configuration) => configuration
    .ReadFrom.Configuration(context.Configuration)
    .ReadFrom.Services(services)
    .Enrich.FromLogContext()
    .WriteTo.Console()
    .WriteTo.File(
        path: "logs/log-.txt",
        rollingInterval: RollingInterval.Day,
        retainedFileCountLimit: 7,
        outputTemplate: "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level:u3}] {Message:lj}{NewLine}{Exception}")
);

// Add services
builder.Services.AddControllers();
// ... other services

var app = builder.Build();

// Use Serilog request logging
app.UseSerilogRequestLogging();

// ... middleware pipeline

app.Run();
```

**Technical Considerations:**
- UseSerilog replaces default ILogger
- ReadFrom.Configuration reads from appsettings.json
- Enrich.FromLogContext enables structured properties
- RollingInterval.Day creates new log file daily
- retainedFileCountLimit prevents disk space issues

**Definition of Done:**
- Serilog configured in Program.cs
- Console logging works (verified by running app)
- File logging works (logs/ folder created)
- Rolling daily logs verified
- Old logs deleted after 7 days (configurable)
- Test log entries created and verified
- Documented in `docs/phase_1/logging.md`

**Priority:** HIGH (critical for debugging)

---

#### Story 6.2: Structured Logging Examples
**As a** backend developer
**I want to** have examples of structured logging
**So that** I know how to log effectively throughout the application

**Acceptance Criteria:**
- [ ] GIVEN Serilog is configured
- [ ] WHEN I create example log statements
- [ ] THEN I can log with structured properties
- [ ] AND I can log exceptions properly
- [ ] AND I can log at different levels (Debug, Info, Warning, Error)
- [ ] AND I can see the structured data in log files

**Example Code (for documentation):**
```csharp
// Inject ILogger into class
public class UserRepository
{
    private readonly ILogger<UserRepository> _logger;

    public UserRepository(ILogger<UserRepository> logger)
    {
        _logger = logger;
    }

    public async Task<User> GetUserByIdAsync(int userId)
    {
        _logger.LogInformation("Fetching user with ID: {UserId}", userId);

        try
        {
            // Database call
            var user = await _dapperContext.QueryFirstOrDefaultAsync<User>(
                "SELECT * FROM USERS WHERE USERCODE = @UserId",
                new { UserId = userId }
            );

            if (user == null)
            {
                _logger.LogWarning("User not found: {UserId}", userId);
                return null;
            }

            _logger.LogDebug("User fetched successfully: {@User}", user);
            return user;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error fetching user {UserId}", userId);
            throw;
        }
    }
}
```

**Structured Logging Best Practices:**
1. **Use message templates:** `"User {UserId} logged in"` not `$"User {userId} logged in"`
2. **Use @ for complex objects:** `{@User}` serializes the entire object
3. **Log at appropriate levels:**
   - Debug: Detailed diagnostic info
   - Information: General flow of application
   - Warning: Unexpected but handled situations
   - Error: Errors and exceptions
   - Critical: System-level failures
4. **Always log exceptions:** `_logger.LogError(ex, "Message")`
5. **Include correlation IDs:** For request tracing

**Definition of Done:**
- Logging examples documented in `docs/phase_1/logging.md`
- Best practices guide created
- Sample repository class with logging created
- Test logs verified in console and file
- Log levels tested (Debug, Info, Warning, Error)

**Priority:** MEDIUM (best practice documentation)

---

#### Story 6.3: Log File Management
**As a** DevOps engineer
**I want to** configure log retention and rotation
**So that** logs don't fill up disk space

**Acceptance Criteria:**
- [ ] GIVEN Serilog file sink is configured
- [ ] WHEN logs are written daily
- [ ] THEN new log file is created each day with format: log-20251105.txt
- [ ] AND logs older than 7 days are automatically deleted
- [ ] AND log files are in logs/ directory (excluded from git)
- [ ] AND production can override retention via config

**Configuration in appsettings.json:**
```json
{
  "Serilog": {
    "WriteTo": [
      {
        "Name": "File",
        "Args": {
          "path": "logs/log-.txt",
          "rollingInterval": "Day",
          "retainedFileCountLimit": 7,
          "fileSizeLimitBytes": 104857600,
          "rollOnFileSizeLimit": true,
          "shared": false,
          "flushToDiskInterval": 1
        }
      }
    ]
  }
}
```

**Technical Considerations:**
- rollingInterval: Day creates daily files
- retainedFileCountLimit: 7 keeps last 7 days
- fileSizeLimitBytes: 100 MB limit per file
- rollOnFileSizeLimit: true creates new file if size exceeded
- shared: false for better performance
- flushToDiskInterval: 1 second (ensure logs are written)

**Definition of Done:**
- Log rotation configured for daily files
- Retention set to 7 days
- File size limit set to 100 MB
- logs/ folder in .gitignore
- Configuration documented in `docs/phase_1/logging.md`
- Tested: created log file, verified rotation

**Priority:** MEDIUM (operational requirement)

---

## MVP Scope Definition

### Must-Have for Phase 1 (MVP)

**Infrastructure Essentials:**
1. SQL Server connection verified to dev_graphql database
2. Database schema fully documented (all 4 tables)
3. .NET 8 SDK installed and verified
4. VS Code with C# Dev Kit extension

**Project Structure Essentials:**
5. GraphQLApp.sln created with 4 projects:
   - GraphQLApp.API (webapi)
   - GraphQLApp.Core (classlib)
   - GraphQLApp.Infrastructure (classlib)
   - GraphQLApp.Services (classlib)
6. Project references configured correctly
7. Solution builds without errors

**Dependencies Essentials:**
8. Hot Chocolate packages installed (API project)
9. Dapper + SQL Client packages installed (Infrastructure project)
10. Serilog packages installed (API project)

**Configuration Essentials:**
11. appsettings.json created (no secrets, committed)
12. appsettings.Local.json template created
13. .gitignore updated to exclude secrets
14. Developer can run application locally

**Logging Essentials:**
15. Serilog configured in Program.cs
16. Console logging verified
17. File logging verified (logs/ directory)

**Acceptance Criteria for Phase 1 Complete:**
- [ ] A developer can clone the repo
- [ ] Run `dotnet restore` successfully
- [ ] Run `dotnet build` successfully (all projects)
- [ ] Create their own appsettings.Local.json with credentials
- [ ] Run `dotnet run --project src/GraphQLApp.API` successfully
- [ ] See logs in console
- [ ] See logs in logs/ folder
- [ ] Verify solution structure matches specification
- [ ] All documentation is present in docs/phase_1/

---

### Nice-to-Have (Deferred)

**Can wait for later phases:**
1. Full VS Code workspace extensions (optional extensions)
2. EditorConfig for code style consistency
3. Unit test projects (Phase 7)
4. Docker configuration (future)
5. GitHub Actions CI/CD (Phase 9)
6. Database connection pooling optimization (Phase 2)
7. Advanced Serilog sinks (Application Insights, Seq) - optional

**Explicitly Excluded from Phase 1:**
1. Any Entity model creation (Phase 2)
2. Any repository implementation (Phase 2)
3. Any GraphQL schema definition (Phase 5)
4. Any authentication code (Phase 4)
5. Any frontend code (Phase 6)
6. Any database table creation (database already exists)
7. IIS deployment (Phase 9)

---

## Technical Requirements Summary

### Performance Requirements
- **Build time:** Solution should build in < 30 seconds on modern hardware
- **Startup time:** Application should start in < 5 seconds
- **Log write performance:** Logs should not block application (async writes)

### Security Requirements
- **No secrets in git:** All credential files in .gitignore
- **Password complexity:** JWT secret must be min 32 characters
- **SQL connection:** Use encrypted connection (TrustServerCertificate for internal)
- **File permissions:** Log files should not be world-readable in production

### Scalability Requirements
- **Log rotation:** Automatic cleanup prevents disk space issues
- **Modular architecture:** Clean separation enables independent scaling later
- **Connection pooling:** SQL Server connection pooling enabled by default

### Integration Requirements
- **Database compatibility:** SQL Server 2019+
- **.NET version:** .NET 8 LTS
- **IDE support:** Must work in VS Code and Visual Studio 2022
- **OS support:** Windows 10+, Windows Server 2019+

### Data Requirements
- **Database:** dev_graphql (existing, DO NOT create)
- **Tables:** CIKK, GYARTO, PARTNER, USERS (existing, DO NOT modify)
- **Credentials:** Stored in appsettings.Local.json (git-ignored)
- **Connection string format:** Microsoft.Data.SqlClient format

---

## Success Metrics

### Leading Indicators (During Phase 1)
1. **Developer velocity:** Days to complete environment setup
   - Target: < 1 hour for experienced .NET dev
   - Measure: Time from clone to running app

2. **Build success rate:** Percentage of successful builds
   - Target: 100% on clean checkout
   - Measure: CI build results (future)

3. **Documentation completeness:** Percentage of stories with documentation
   - Target: 100%
   - Measure: Manual checklist review

### Lagging Indicators (Post Phase 1)
1. **Developer onboarding time:** How long to get new dev productive
   - Target: < 2 hours (setup + orientation)
   - Measure: Survey new team members

2. **Configuration errors:** Number of "can't connect to DB" issues
   - Target: 0 (after following setup guide)
   - Measure: Support tickets, Slack questions

3. **Log coverage:** Percentage of components with logging
   - Target: 100% of repositories and services
   - Measure: Code review in later phases

4. **Technical debt:** Number of "TODO" items from shortcuts
   - Target: 0 (do it right the first time)
   - Measure: Code search for TODO comments

---

## Risks and Assumptions

### Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| SQL Server connectivity issues (firewall, VPN) | Medium | High | Test connection early (Story 1.1), document network requirements |
| Version conflicts in NuGet packages | Low | Medium | Use specific version numbers, document all versions |
| .NET 8 SDK installation issues on older Windows | Low | Medium | Document minimum OS requirements, provide troubleshooting |
| Secrets accidentally committed to git | Medium | High | .gitignore before creating secrets, git hook for detection |
| Log disk space issues in production | Low | Low | Configure retention policy, monitor disk usage |
| Developer machine compatibility (Mac/Linux) | Medium | Low | Focus on Windows first, document cross-platform later |

### Assumptions

1. **Infrastructure Assumptions:**
   - SQL Server at 10.10.10.69 is accessible and operational
   - dev_graphql database exists with tables CIKK, GYARTO, PARTNER, USERS
   - Developers have Windows machines (or WSL2)
   - Developers have admin rights to install .NET SDK

2. **Database Assumptions:**
   - Database schema will not change during Phase 1
   - Read/write permissions are available for dev user
   - Network latency to SQL Server is < 50ms (LAN)
   - Database collation is compatible with NVARCHAR

3. **Team Assumptions:**
   - Developers are familiar with C# and .NET
   - Developers have used Git before
   - Developers have access to project repository
   - Team has agreed on coding standards (to be documented)

4. **Tooling Assumptions:**
   - VS Code is acceptable (not requiring Visual Studio 2022)
   - Windows 10+ is the development OS
   - Internet access for NuGet package downloads
   - GitHub is accessible for repository operations

---

## Dependencies

### Phase 1 Depends On:
- **Phase 0 (Completed):** Documentation, architecture planning, requirements

### Phases That Depend On Phase 1:
- **Phase 2 (Database Integration):** Needs project structure, Dapper packages, database connection
- **Phase 3 (Backend API Basics):** Needs all projects created, references configured
- **Phase 4 (Authentication):** Needs Services project, JWT packages
- **Phase 5 (GraphQL API):** Needs Hot Chocolate packages, logging infrastructure
- **All subsequent phases** depend on Phase 1 completion

### External Dependencies:
- SQL Server availability at 10.10.10.69
- NuGet.org package availability
- GitHub.com repository access
- Internet connectivity for downloads

---

## Quality Assurance Checklist

Before marking Phase 1 as complete, verify:

**Database Connectivity:**
- [ ] SQL Server connection test successful
- [ ] All 4 tables (CIKK, GYARTO, PARTNER, USERS) confirmed
- [ ] Schema metadata extracted and documented
- [ ] Sample query executed successfully

**Development Environment:**
- [ ] .NET 8 SDK installed (`dotnet --version` = 8.0.x)
- [ ] VS Code with C# Dev Kit extension working
- [ ] IntelliSense functioning in .cs files
- [ ] Can build and run a test project

**Project Structure:**
- [ ] GraphQLApp.sln exists in src/
- [ ] All 4 projects created (API, Core, Infrastructure, Services)
- [ ] Project references configured correctly
- [ ] `dotnet build` succeeds at solution level
- [ ] No circular dependencies

**NuGet Packages:**
- [ ] Hot Chocolate packages installed in API project
- [ ] Dapper packages installed in Infrastructure project
- [ ] Serilog packages installed in API project
- [ ] JWT packages installed in API project
- [ ] BCrypt package installed in Services project
- [ ] All packages restore successfully

**Configuration:**
- [ ] appsettings.json created (no secrets)
- [ ] appsettings.Local.json template created
- [ ] .gitignore excludes appsettings.Local.json
- [ ] Developer has working appsettings.Local.json locally
- [ ] Application can read configuration

**Logging:**
- [ ] Serilog configured in Program.cs
- [ ] Console logging verified (run app, see logs)
- [ ] File logging verified (logs/ folder exists, log file created)
- [ ] Log rotation configured (daily, 7 day retention)
- [ ] Test log entries created at multiple levels

**Documentation:**
- [ ] docs/phase_1/database_connectivity.md exists
- [ ] docs/phase_1/database_schema.md exists
- [ ] docs/phase_1/environment_setup.md exists
- [ ] docs/phase_1/architecture.md exists
- [ ] docs/phase_1/dependencies.md exists
- [ ] docs/phase_1/configuration.md exists
- [ ] docs/phase_1/logging.md exists
- [ ] docs/phase_1/phase_1_summary.md exists

**Git Repository:**
- [ ] All code committed to git
- [ ] No secrets in git history
- [ ] .gitignore working correctly
- [ ] README.md updated with Phase 1 status

**Team Readiness:**
- [ ] At least one developer has completed full setup
- [ ] Setup instructions validated by second developer
- [ ] Common issues documented in troubleshooting section
- [ ] All team members have access to repository

---

## Phase 1 Completion Criteria

Phase 1 is considered **COMPLETE** when:

1. All 6 epics have all stories marked as done
2. All items in Quality Assurance Checklist are checked
3. A second developer can successfully:
   - Clone the repository
   - Follow setup instructions
   - Run the application
   - See logs
   - Build the solution
4. Documentation is reviewed and approved
5. Phase 1 summary document is created and committed

**Sign-off Required From:**
- Technical Architect (architecture and project structure)
- Backend Developer (development environment and dependencies)
- DevOps Engineer (configuration and security)
- Product Owner (acceptance of deliverables)

---

## Next Steps (Phase 2 Preview)

Once Phase 1 is complete, Phase 2 will focus on:

1. **Database Schema Reverse Engineering:**
   - Query INFORMATION_SCHEMA for exact column definitions
   - Document data types, lengths, nullable, constraints

2. **Entity Model Creation:**
   - Create C# classes for Cikk, Gyarto, Partner, User
   - Match exact field names and types from database

3. **AUTH Table Creation:**
   - Create new AUTH table for authentication
   - Seed with initial admin user

4. **Repository Implementation:**
   - Implement UserRepository, CikkRepository, etc.
   - Use Dapper for data access
   - Test CRUD operations

5. **Stored Procedure Development:**
   - Create GetCikkekByGyarto stored procedure
   - Create GetStatisztika stored procedure
   - Add StoredProcedureExecutor implementation

---

## Appendix

### A. Useful Commands Reference

**Solution and Project Management:**
```bash
# Create solution
dotnet new sln -n GraphQLApp

# Create projects
dotnet new webapi -n GraphQLApp.API
dotnet new classlib -n GraphQLApp.Core
dotnet new classlib -n GraphQLApp.Infrastructure
dotnet new classlib -n GraphQLApp.Services

# Add projects to solution
dotnet sln add src/GraphQLApp.API/GraphQLApp.API.csproj
dotnet sln add src/GraphQLApp.Core/GraphQLApp.Core.csproj
dotnet sln add src/GraphQLApp.Infrastructure/GraphQLApp.Infrastructure.csproj
dotnet sln add src/GraphQLApp.Services/GraphQLApp.Services.csproj

# Add project references
dotnet add src/GraphQLApp.API/GraphQLApp.API.csproj reference src/GraphQLApp.Core/GraphQLApp.Core.csproj
dotnet add src/GraphQLApp.API/GraphQLApp.API.csproj reference src/GraphQLApp.Infrastructure/GraphQLApp.Infrastructure.csproj
dotnet add src/GraphQLApp.API/GraphQLApp.API.csproj reference src/GraphQLApp.Services/GraphQLApp.Services.csproj
dotnet add src/GraphQLApp.Infrastructure/GraphQLApp.Infrastructure.csproj reference src/GraphQLApp.Core/GraphQLApp.Core.csproj
dotnet add src/GraphQLApp.Services/GraphQLApp.Services.csproj reference src/GraphQLApp.Core/GraphQLApp.Core.csproj

# Build and run
dotnet restore
dotnet build
dotnet run --project src/GraphQLApp.API
```

**NuGet Package Management:**
```bash
# Add packages
dotnet add package HotChocolate.AspNetCore --version 13.9.12
dotnet add package Dapper --version 2.1.28
dotnet add package Serilog.AspNetCore --version 8.0.1

# List packages
dotnet list package

# Update packages
dotnet add package PackageName --version x.x.x
```

**Database Connection Test:**
```bash
# Using sqlcmd (if installed)
sqlcmd -S 10.10.10.69 -d dev_graphql -U your_username -P your_password -Q "SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES"
```

### B. Troubleshooting Guide

**Issue: "dotnet command not found"**
- Solution: Install .NET 8 SDK, restart terminal, verify PATH

**Issue: "Cannot connect to SQL Server"**
- Check network connectivity: `ping 10.10.10.69`
- Verify firewall rules
- Check SQL Server authentication mode (mixed mode)
- Verify credentials in appsettings.Local.json

**Issue: "Package restore failed"**
- Clear NuGet cache: `dotnet nuget locals all --clear`
- Retry: `dotnet restore`
- Check internet connection

**Issue: "Logs not appearing in file"**
- Check logs/ folder exists
- Check file permissions
- Verify Serilog configuration in appsettings.json
- Check if flushToDiskInterval is set

**Issue: "Secrets committed to git"**
- Remove from git history: `git rm --cached appsettings.Local.json`
- Add to .gitignore
- Rotate compromised secrets immediately

### C. Reference Links

- [.NET 8 Download](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Hot Chocolate Documentation](https://chillicream.com/docs/hotchocolate/v13)
- [Dapper Documentation](https://github.com/DapperLib/Dapper)
- [Serilog Documentation](https://serilog.net/)
- [ASP.NET Core Configuration](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/)
- [SQL Server Connection Strings](https://www.connectionstrings.com/sql-server/)

---

**Document End**

**Phase 1 PRD Version:** 1.0
**Last Updated:** 2025-11-05
**Next Review:** Upon Phase 1 completion
