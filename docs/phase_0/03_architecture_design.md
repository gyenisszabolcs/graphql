# Phase 0 - Task 3: System Architecture Design

**Agent Role:** Technical Architect
**Date:** 2025-11-04
**Status:** ✅ COMPLETED

## Executive Summary

This document presents the comprehensive system architecture for the GraphQL API Application, including component design, technology stack justification, deployment architecture, and scalability considerations.

## 1. Architecture Overview

### 1.1 Architectural Style

**Selected Approach:** Layered Architecture with Repository Pattern

**Rationale:**
- ✅ Clear separation of concerns
- ✅ Testability through dependency injection
- ✅ Maintainability for team collaboration
- ✅ Proven pattern for .NET applications
- ✅ Supports future microservices migration if needed

### 1.2 High-Level Architecture Diagram

```
┌───────────────────────────────────────────────────────────────────────┐
│                          CLIENT LAYER                                  │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────────────┐  │
│  │  Webshop Client │  │  Vue.js Web UI   │  │  GraphQL Clients   │  │
│  │  (Primary API   │  │  (Admin Interface│  │  (Postman, cURL)   │  │
│  │   Consumer)     │  │   + Query Builder│  │                    │  │
│  └─────────────────┘  └──────────────────┘  └────────────────────┘  │
└───────────────────────────────────────────────────────────────────────┘
                                  │
                                  │ HTTPS + JWT
                                  ▼
┌───────────────────────────────────────────────────────────────────────┐
│                       PRESENTATION LAYER                               │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │              ASP.NET Core 8 Web API (GraphQL Host)              │ │
│  │  ┌──────────────────────────────────────────────────────────┐  │ │
│  │  │  Hot Chocolate GraphQL Middleware                         │  │ │
│  │  │  • /graphql endpoint (POST)                               │  │ │
│  │  │  • Banana Cake Pop UI (GET /graphql in dev)               │  │ │
│  │  │  • Schema introspection                                   │  │ │
│  │  │  • Query execution pipeline                               │  │ │
│  │  └──────────────────────────────────────────────────────────┘  │ │
│  │  ┌──────────────────────────────────────────────────────────┐  │ │
│  │  │  REST Controllers                                         │  │ │
│  │  │  • /api/auth/login (POST) - JWT token generation         │  │ │
│  │  └──────────────────────────────────────────────────────────┘  │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌───────────────────────────────────────────────────────────────────────┐
│                      MIDDLEWARE LAYER                                  │
│  ┌──────────────────┐ ┌──────────────────┐ ┌────────────────────┐   │
│  │  Authentication  │ │  Authorization   │ │  Error Handling    │   │
│  │  (JWT Bearer)    │ │  (Role-based)    │ │  (Global Filter)   │   │
│  └──────────────────┘ └──────────────────┘ └────────────────────┘   │
│  ┌──────────────────┐ ┌──────────────────┐ ┌────────────────────┐   │
│  │  Request Logging │ │  CORS Policy     │ │  Rate Limiting     │   │
│  │  (Serilog)       │ │  (Trusted Only)  │ │  (60 req/min)      │   │
│  └──────────────────┘ └──────────────────┘ └────────────────────┘   │
└───────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌───────────────────────────────────────────────────────────────────────┐
│                     APPLICATION LAYER                                  │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │                   GraphQL Resolvers                              │ │
│  │  ┌───────────────┐  ┌────────────────┐  ┌────────────────────┐ │ │
│  │  │  Queries      │  │  Mutations     │  │  DataLoaders       │ │ │
│  │  │  • GetCikkek  │  │  • CreateCikk  │  │  • GyartoDataLdr   │ │ │
│  │  │  • GetGyartok │  │  • UpdateCikk  │  │  (N+1 Prevention)  │ │ │
│  │  │  • GetPrtnrek │  │  • DeleteCikk  │  │                    │ │ │
│  │  │  • GetUsers   │  │  • CreateGyarto│  │                    │ │ │
│  │  └───────────────┘  └────────────────┘  └────────────────────┘ │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │                   Business Services                              │ │
│  │  • AuthService (Login, JWT generation)                          │ │
│  │  • JwtTokenService (Token creation/validation)                  │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌───────────────────────────────────────────────────────────────────────┐
│                    DATA ACCESS LAYER                                   │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │                Repository Pattern (Interfaces)                   │ │
│  │  • IUserRepository                                               │ │
│  │  • ICikkRepository                                               │ │
│  │  • IGyartoRepository                                             │ │
│  │  • IPartnerRepository                                            │ │
│  │  • IStoredProcedureExecutor                                      │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │           Repository Implementations (Dapper-based)              │ │
│  │  • UserRepository (CRUD + GetByUsername)                         │ │
│  │  • CikkRepository (CRUD + GetByCikkKod, GetByGyartoId)           │ │
│  │  • GyartoRepository (CRUD)                                       │ │
│  │  • PartnerRepository (CRUD + GetByTipus)                         │ │
│  │  • StoredProcedureExecutor (Dynamic SP execution)                │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │  DapperContext (Connection Factory)                              │ │
│  │  • CreateConnection() → SqlConnection                            │ │
│  │  • Connection string from configuration                          │ │
│  │  • Connection pooling (default: min 0, max 100)                  │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────┘
                                  │
                                  │ SQL Queries (Parameterized)
                                  ▼
┌───────────────────────────────────────────────────────────────────────┐
│                         DATA LAYER                                     │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │      Microsoft SQL Server 2019+ (10.10.10.69)                   │ │
│  │  ┌────────────────┐  ┌────────────────┐  ┌──────────────────┐  │ │
│  │  │  Dev Database  │  │  Prod Database │  │  Test Database   │  │ │
│  │  │  (DevDatabase) │  │  (ProdDatabase)│  │  (TestDatabase)  │  │ │
│  │  └────────────────┘  └────────────────┘  └──────────────────┘  │ │
│  │                                                                   │ │
│  │  Tables:                                                          │ │
│  │  • users                                                          │ │
│  │  • cikkek                                                         │ │
│  │  • gyartok                                                        │ │
│  │  • partnerek                                                      │ │
│  │                                                                   │ │
│  │  Stored Procedures:                                               │ │
│  │  • GetCikkekByGyarto                                              │ │
│  │  • GetStatisztika                                                 │ │
│  │                                                                   │ │
│  │  Indexes:                                                         │ │
│  │  • users.Username (UNIQUE)                                        │ │
│  │  • cikkek.CikkKod (UNIQUE)                                        │ │
│  │  • cikkek.GyartoId (FOREIGN KEY)                                  │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌───────────────────────────────────────────────────────────────────────┐
│                   CROSS-CUTTING CONCERNS                               │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐   │
│  │  Logging         │  │  Configuration   │  │  Dependency      │   │
│  │  (Serilog)       │  │  (appsettings)   │  │  Injection       │   │
│  │  • Console       │  │  • JSON files    │  │  (Built-in DI)   │   │
│  │  • File rolling  │  │  • Env variables │  │                  │   │
│  │  • Structured    │  │  • User secrets  │  │                  │   │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘   │
└───────────────────────────────────────────────────────────────────────┘
```

## 2. Technology Stack Justification

### 2.1 Backend Technologies

| Component | Technology | Version | Justification |
|-----------|-----------|---------|---------------|
| **Framework** | ASP.NET Core | 8.0 LTS | • Long-term support until 2026<br>• Native async/await performance<br>• Built-in DI container<br>• Cross-platform (future flexibility)<br>• Mature ecosystem |
| **GraphQL** | Hot Chocolate | 13.9+ | • Best-in-class .NET GraphQL library<br>• Excellent performance (schema stitching, DataLoader)<br>• Built-in Banana Cake Pop UI<br>• Strong type safety<br>• Active development and community |
| **ORM** | Dapper | 2.1+ | • Lightweight (no overhead)<br>• SQL control for complex queries<br>• Excellent performance (micro-ORM)<br>• Works well with stored procedures<br>• **Alternative:** EF Core 8 for migrations if team prefers |
| **Authentication** | JWT Bearer | Built-in | • Stateless (scalable)<br>• Industry standard<br>• Works across domains<br>• Easy client integration |
| **Logging** | Serilog | 3.1+ | • Structured logging<br>• Multiple sinks (console, file, seq)<br>• Rich context enrichment<br>• Excellent performance |
| **Password Hashing** | BCrypt.Net | 0.1.0+ | • Industry standard<br>• Adaptive work factor<br>• Salt included<br>• Resistance to rainbow tables |

### 2.2 Frontend Technologies

| Component | Technology | Version | Justification |
|-----------|-----------|---------|---------------|
| **Framework** | Vue.js | 3.4+ | • Gentle learning curve<br>• Reactive data binding<br>• Composition API for logic reuse<br>• Excellent tooling (Vite)<br>• Smaller bundle size than React |
| **Build Tool** | Vite | 5.0+ | • Lightning-fast HMR<br>• Native ES modules<br>• Optimized production builds<br>• Better DX than Webpack |
| **GraphQL Client** | @urql/vue | 4.0+ | • Lightweight alternative to Apollo<br>• Extensible architecture<br>• Normalized caching<br>• Great Vue integration |
| **State Management** | Pinia | 2.1+ | • Vue 3 recommended store<br>• Better TypeScript support than Vuex<br>• Devtools integration<br>• Modular design |
| **UI Framework** | Tailwind CSS | 3.4+ | • Utility-first approach<br>• Customizable design system<br>• Tree-shaking (small bundles)<br>• Responsive design built-in<br>• No runtime JavaScript |
| **HTTP Client** | Fetch API | Native | • No extra dependency<br>• Modern browser support<br>• Promise-based API |

### 2.3 Infrastructure

| Component | Technology | Justification |
|-----------|-----------|---------------|
| **Web Server** | IIS 10+ | • Organizational standard<br>• Windows authentication integration<br>• Mature monitoring tools<br>• HTTPS management via Windows Cert Store |
| **Database** | SQL Server 2019+ | • Existing infrastructure<br>• ACID compliance<br>• Excellent stored procedure support<br>• SQL Server Management Studio tooling |
| **Operating System** | Windows Server 2019+ | • IIS native environment<br>• Organizational standard<br>• Active Directory integration potential |

## 3. Component Design

### 3.1 Project Structure (Solution)

```
GraphQLApp.sln
│
├── src/
│   ├── GraphQLApp.API/               [Web API Host - ASP.NET Core 8]
│   │   ├── Controllers/
│   │   │   └── AuthController.cs     [REST endpoint for /api/auth/login]
│   │   ├── GraphQL/
│   │   │   ├── Queries/
│   │   │   │   ├── UserQueries.cs
│   │   │   │   ├── CikkQueries.cs
│   │   │   │   ├── GyartoQueries.cs
│   │   │   │   └── PartnerQueries.cs
│   │   │   ├── Mutations/
│   │   │   │   ├── UserMutations.cs
│   │   │   │   ├── CikkMutations.cs
│   │   │   │   ├── GyartoMutations.cs
│   │   │   │   └── PartnerMutations.cs
│   │   │   ├── Types/
│   │   │   │   ├── UserType.cs
│   │   │   │   ├── CikkType.cs
│   │   │   │   ├── GyartoType.cs
│   │   │   │   └── PartnerType.cs
│   │   │   ├── DataLoaders/
│   │   │   │   └── GyartoDataLoader.cs
│   │   │   └── Filters/
│   │   │       └── ErrorFilter.cs
│   │   ├── Middleware/
│   │   │   └── RequestLoggingMiddleware.cs
│   │   ├── Program.cs                [Application entry point]
│   │   ├── appsettings.json
│   │   ├── appsettings.Development.json
│   │   └── appsettings.Production.json
│   │
│   ├── GraphQLApp.Core/              [Domain Layer - Pure C#]
│   │   ├── Entities/
│   │   │   ├── User.cs
│   │   │   ├── Cikk.cs
│   │   │   ├── Gyarto.cs
│   │   │   └── Partner.cs
│   │   ├── Interfaces/
│   │   │   ├── Repositories/
│   │   │   │   ├── IUserRepository.cs
│   │   │   │   ├── ICikkRepository.cs
│   │   │   │   ├── IGyartoRepository.cs
│   │   │   │   ├── IPartnerRepository.cs
│   │   │   │   └── IStoredProcedureExecutor.cs
│   │   │   └── Services/
│   │   │       ├── IAuthService.cs
│   │   │       └── IJwtTokenService.cs
│   │   └── DTOs/
│   │       ├── LoginRequest.cs
│   │       ├── LoginResponse.cs
│   │       ├── CreateCikkInput.cs
│   │       ├── UpdateCikkInput.cs
│   │       └── ... (other DTOs)
│   │
│   ├── GraphQLApp.Infrastructure/    [Data Access Layer]
│   │   ├── Data/
│   │   │   └── DapperContext.cs      [Connection factory]
│   │   └── Repositories/
│   │       ├── UserRepository.cs
│   │       ├── CikkRepository.cs
│   │       ├── GyartoRepository.cs
│   │       ├── PartnerRepository.cs
│   │       └── StoredProcedureExecutor.cs
│   │
│   ├── GraphQLApp.Services/          [Business Logic Layer]
│   │   ├── AuthService.cs
│   │   └── JwtTokenService.cs
│   │
│   └── GraphQLApp.Web/               [Vue.js Frontend]
│       ├── public/
│       ├── src/
│       │   ├── components/
│       │   ├── views/
│       │   ├── services/
│       │   │   └── graphqlClient.js
│       │   ├── stores/
│       │   │   └── authStore.js
│       │   ├── router/
│       │   │   └── index.js
│       │   ├── App.vue
│       │   └── main.js
│       ├── package.json
│       └── vite.config.js
│
├── tests/
│   ├── GraphQLApp.API.Tests/
│   │   ├── QueryTests/
│   │   ├── MutationTests/
│   │   └── IntegrationTests/
│   ├── GraphQLApp.Infrastructure.Tests/
│   │   └── RepositoryTests/
│   └── GraphQLApp.Services.Tests/
│       └── AuthServiceTests/
│
├── scripts/
│   ├── sql/
│   │   ├── 001-init-database.sql
│   │   ├── 002-seed-data.sql
│   │   └── 003-stored-procedures.sql
│   ├── deploy-dev.ps1
│   └── deploy-prod.ps1
│
├── docs/
│   └── phase_0/
│       ├── 01_requirements_validation.md
│       ├── 02_ux_ui_design_wireframes.md
│       └── 03_architecture_design.md (this document)
│
└── .gitignore
```

### 3.2 Dependency Flow

```
GraphQLApp.API
    ↓ depends on
GraphQLApp.Services ← GraphQLApp.Core (interfaces)
    ↓ depends on
GraphQLApp.Infrastructure ← GraphQLApp.Core
    ↓ depends on
GraphQLApp.Core (no dependencies)

Rule: Core has NO external dependencies (pure domain logic)
Rule: Infrastructure implements Core interfaces
Rule: API references all projects for DI registration
```

## 4. Design Patterns

### 4.1 Repository Pattern

**Purpose:** Abstract data access logic from business logic

**Implementation:**
```csharp
// Core/Interfaces/ICikkRepository.cs
public interface ICikkRepository
{
    Task<List<Cikk>> GetAllAsync();
    Task<Cikk?> GetByIdAsync(int id);
    Task<Cikk?> GetByCikkKodAsync(string cikkKod);
    Task<Cikk> CreateAsync(Cikk cikk);
    Task UpdateAsync(Cikk cikk);
    Task<bool> DeleteAsync(int id);
}

// Infrastructure/Repositories/CikkRepository.cs
public class CikkRepository : ICikkRepository
{
    private readonly DapperContext _context;

    public CikkRepository(DapperContext context)
    {
        _context = context;
    }

    public async Task<List<Cikk>> GetAllAsync()
    {
        using var connection = _context.CreateConnection();
        var sql = "SELECT * FROM cikkek";
        var result = await connection.QueryAsync<Cikk>(sql);
        return result.ToList();
    }

    // ... other methods
}
```

**Benefits:**
- ✅ Testability (can mock repositories)
- ✅ Separation of concerns
- ✅ Easy to swap data access technology
- ✅ Centralized data access logic

### 4.2 Dependency Injection

**Purpose:** Loose coupling and testability

**Registration (Program.cs):**
```csharp
// Data Access
builder.Services.AddSingleton<DapperContext>(sp =>
{
    var connString = builder.Configuration.GetConnectionString("DefaultConnection");
    return new DapperContext(connString);
});

// Repositories
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddScoped<ICikkRepository, CikkRepository>();
builder.Services.AddScoped<IGyartoRepository, GyartoRepository>();
builder.Services.AddScoped<IPartnerRepository, PartnerRepository>();

// Services
builder.Services.AddScoped<IAuthService, AuthService>();
builder.Services.AddScoped<IJwtTokenService, JwtTokenService>();
```

**Usage in Resolvers:**
```csharp
[ExtendObjectType("Query")]
public class CikkQueries
{
    public async Task<List<CikkType>> GetCikkek(
        [Service] ICikkRepository cikkRepository)
    {
        return await cikkRepository.GetAllAsync();
    }
}
```

### 4.3 DataLoader Pattern (N+1 Prevention)

**Problem:** Nested GraphQL queries cause N+1 database calls

**Solution:** Hot Chocolate DataLoader

```csharp
// GraphQL/DataLoaders/GyartoDataLoader.cs
public class GyartoDataLoader : BatchDataLoader<int, Gyarto>
{
    private readonly IGyartoRepository _repository;

    public GyartoDataLoader(
        IGyartoRepository repository,
        IBatchScheduler scheduler)
        : base(scheduler)
    {
        _repository = repository;
    }

    protected override async Task<IReadOnlyDictionary<int, Gyarto>> LoadBatchAsync(
        IReadOnlyList<int> keys,
        CancellationToken cancellationToken)
    {
        var gyartok = await _repository.GetByIdsAsync(keys.ToList());
        return gyartok.ToDictionary(g => g.Id);
    }
}

// Usage in CikkType.cs
public async Task<GyartoType?> GetGyartoAsync(
    [DataLoader] GyartoDataLoader gyartoLoader)
{
    if (GyartoId == null) return null;
    return await gyartoLoader.LoadAsync(GyartoId.Value);
}
```

**Benefit:** Single batched query instead of N separate queries

### 4.4 Options Pattern (Configuration)

**Purpose:** Strongly-typed configuration

```csharp
// Core/Configuration/JwtSettings.cs
public class JwtSettings
{
    public string SecretKey { get; set; } = string.Empty;
    public string Issuer { get; set; } = string.Empty;
    public string Audience { get; set; } = string.Empty;
    public int ExpirationMinutes { get; set; } = 60;
}

// Registration
builder.Services.Configure<JwtSettings>(
    builder.Configuration.GetSection("JwtSettings"));

// Usage
public class JwtTokenService
{
    private readonly JwtSettings _settings;

    public JwtTokenService(IOptions<JwtSettings> options)
    {
        _settings = options.Value;
    }
}
```

## 5. Security Architecture

### 5.1 Authentication Flow

```
┌─────────┐                                    ┌─────────────┐
│ Client  │                                    │   API       │
└─────────┘                                    └─────────────┘
     │                                                │
     │  1. POST /api/auth/login                      │
     │     { username, password }                    │
     │──────────────────────────────────────────────>│
     │                                                │
     │                            2. Verify credentials
     │                               (BCrypt.Verify)  │
     │                                                │
     │                            3. Generate JWT     │
     │                               (JwtTokenService)│
     │                                                │
     │  4. 200 OK                                     │
     │<─────────────────────────────────────────────│
     │     { token, username, expiresAt }            │
     │                                                │
     │  5. GraphQL Query                              │
     │     Authorization: Bearer <JWT>               │
     │──────────────────────────────────────────────>│
     │                                                │
     │                            6. Validate JWT     │
     │                               (Middleware)     │
     │                                                │
     │                            7. Execute Query    │
     │                               (if authorized)  │
     │                                                │
     │  8. 200 OK { data }                           │
     │<──────────────────────────────────────────────│
     │                                                │
```

### 5.2 Authorization Layers

**Layer 1: Endpoint-level (Middleware)**
```csharp
app.UseAuthentication();  // Validates JWT
app.UseAuthorization();   // Checks policies
```

**Layer 2: GraphQL Resolver-level**
```csharp
[Authorize]  // Requires valid JWT
public async Task<List<CikkType>> GetCikkek([Service] ICikkRepository repo)
{
    // Implementation
}

[Authorize(Roles = new[] { "Admin" })]  // Requires Admin role
public async Task<bool> DeleteCikk(int id, [Service] ICikkRepository repo)
{
    // Implementation
}
```

**Layer 3: Data-level (Future Enhancement)**
```csharp
// Row-level security based on user context
// Example: Users can only see their assigned partners
```

### 5.3 Security Controls

| Control | Implementation | Purpose |
|---------|---------------|---------|
| **SQL Injection** | Parameterized queries (Dapper default) | Prevent malicious SQL |
| **XSS** | Input encoding, Content-Security-Policy header | Prevent script injection |
| **CSRF** | SameSite cookies, CORS policy | Prevent cross-site requests |
| **Brute Force** | Rate limiting (60 req/min) | Prevent password guessing |
| **Token Theft** | HTTPS-only, short expiration (60 min) | Minimize token exposure |
| **Sensitive Data** | Never log passwords/tokens, encrypt connection strings | Data protection |

## 6. Performance Optimization

### 6.1 Database Optimization

**Indexing Strategy:**
```sql
-- Unique indexes (enforced by database)
CREATE UNIQUE INDEX IX_users_Username ON users(Username);
CREATE UNIQUE INDEX IX_cikkek_CikkKod ON cikkek(CikkKod);

-- Foreign key indexes (improve join performance)
CREATE INDEX IX_cikkek_GyartoId ON cikkek(GyartoId);

-- Filtered indexes (for common queries)
CREATE INDEX IX_users_Active ON users(IsActive) WHERE IsActive = 1;

-- Composite indexes (for frequent filter combinations)
CREATE INDEX IX_cikkek_GyartoAr ON cikkek(GyartoId, EgysegAr);
```

**Query Optimization:**
- Use `SELECT specific_columns` instead of `SELECT *`
- Implement paging for large result sets (OFFSET/FETCH)
- Use stored procedures for complex queries
- Monitor slow queries with SQL Server Profiler

**Connection Pooling:**
```
Default SQL Server connection pooling:
- Min Pool Size: 0
- Max Pool Size: 100
- Connection Lifetime: 0 (no timeout)

For high-traffic scenarios, tune:
- Min Pool Size: 10 (maintain warm connections)
- Max Pool Size: 200 (support more concurrent requests)
```

### 6.2 GraphQL Optimization

**DataLoader for N+1 Prevention:**
- Batch load related entities in single query
- Automatically deduplicate requests within single GraphQL query

**Query Complexity Limits:**
```csharp
builder.Services
    .AddGraphQLServer()
    .AddMaxExecutionDepthRule(10)  // Prevent deeply nested queries
    .ModifyRequestOptions(opt =>
    {
        opt.ExecutionTimeout = TimeSpan.FromSeconds(30);
    });
```

**Pagination:**
```csharp
[UsePaging(MaxPageSize = 100, DefaultPageSize = 20)]
public IQueryable<CikkType> GetCikkek([Service] ICikkRepository repo)
{
    return repo.GetAllAsQueryable();
}
```

### 6.3 Caching Strategy (Future)

**Phase 1 (MVP):** No caching (keep it simple)

**Phase 2 (Future Enhancement):**
- **Response Caching:** Cache GET /graphql schema introspection
- **Distributed Caching (Redis):** Cache frequently accessed static data
- **HTTP Cache Headers:** ETag, Cache-Control for static assets

## 7. Scalability Considerations

### 7.1 Horizontal Scalability

**Current Architecture Support:**
- ✅ Stateless API (JWT tokens, no session state)
- ✅ Database connection pooling (multiple app instances can share DB)
- ✅ No in-memory caching (no state sync issues)

**Load Balancing (Future):**
```
                    [Load Balancer - IIS ARR]
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
   [API Instance 1]    [API Instance 2]    [API Instance 3]
        │                   │                   │
        └───────────────────┴───────────────────┘
                            │
                   [SQL Server]
```

### 7.2 Vertical Scalability

**Database Server:**
- Increase CPU/RAM for SQL Server
- Add SSD storage for improved I/O
- Optimize tempdb configuration

**API Server:**
- Increase IIS worker processes
- Add more RAM for .NET runtime
- Enable Server GC (garbage collection)

### 7.3 Database Scalability

**Read Replicas (Future):**
- Primary: Write operations
- Replicas: Read-only queries
- Route GraphQL queries to replicas, mutations to primary

**Partitioning (Future):**
- Horizontal partitioning (sharding) by tenant or date range if data grows significantly

## 8. Deployment Architecture

### 8.1 Environment Topology

```
┌──────────────────────────────────────────────────────────┐
│                 DEVELOPMENT ENVIRONMENT                   │
│  ┌────────────────────┐    ┌─────────────────────────┐  │
│  │  Developer Machine │───>│  SQL Server Dev DB      │  │
│  │  (localhost:5001)  │    │  (10.10.10.69:DevDB)    │  │
│  └────────────────────┘    └─────────────────────────┘  │
└──────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│                PRODUCTION ENVIRONMENT                     │
│  ┌────────────────────────────────────────────────────┐  │
│  │          IIS 10 (Windows Server 2019)              │  │
│  │  ┌──────────────────────────────────────────────┐  │  │
│  │  │  Application Pool: GraphQLAppPool            │  │  │
│  │  │  Site: GraphQLApp (Port 443 HTTPS)           │  │  │
│  │  │  Path: C:\inetpub\graphqlapp\                │  │  │
│  │  └──────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────┘  │
│                         │                                 │
│                         │ SQL Auth                        │
│                         ▼                                 │
│  ┌────────────────────────────────────────────────────┐  │
│  │      SQL Server 2019 (10.10.10.69)                 │  │
│  │  • ProdDatabase (production data)                  │  │
│  │  • Automated backups (daily full, hourly diff)     │  │
│  │  • Transaction log backups (every 15 min)          │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
```

### 8.2 Deployment Process

**Manual Deployment (Phase 0-9):**
```powershell
# 1. Build and publish
cd src/GraphQLApp.API
dotnet publish -c Release -o C:\publish\graphqlapp

# 2. Stop IIS site
Stop-WebSite -Name "GraphQLApp"

# 3. Copy files
Copy-Item -Path C:\publish\graphqlapp\* -Destination C:\inetpub\graphqlapp\ -Recurse -Force

# 4. Update appsettings.Production.json (if needed)
# Edit C:\inetpub\graphqlapp\appsettings.Production.json

# 5. Start IIS site
Start-WebSite -Name "GraphQLApp"
```

**Automated CI/CD (Future - Phase 9):**
- GitHub Actions workflow triggers on push to `main` branch
- Build, test, and package application
- Deploy to staging environment automatically
- Manual approval for production deployment

## 9. Monitoring and Observability

### 9.1 Logging Strategy

**Log Levels:**
- **Debug:** Development-only detailed logs
- **Information:** General informational messages (query execution, user actions)
- **Warning:** Non-critical issues (slow queries, deprecated feature usage)
- **Error:** Handled errors (validation failures, not found errors)
- **Critical:** Unhandled exceptions, system failures

**Log Sinks:**
```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.Console()
    .WriteTo.File(
        path: "logs/graphqlapp-.txt",
        rollingInterval: RollingInterval.Day,
        retainedFileCountLimit: 30)
    .WriteTo.Seq("http://localhost:5341")  // Optional: Seq for log aggregation
    .CreateLogger();
```

**Structured Logging Example:**
```csharp
_logger.LogInformation(
    "GraphQL query executed: {QueryName} by {Username} in {Duration}ms",
    queryName, username, duration);
```

### 9.2 Metrics to Monitor

**Application Metrics:**
- Request rate (requests/second)
- Response time (p50, p95, p99)
- Error rate (percentage)
- Active connections

**Database Metrics:**
- Query execution time
- Deadlocks
- Connection pool utilization
- Database size growth

**System Metrics:**
- CPU utilization
- Memory usage
- Disk I/O
- Network throughput

### 9.3 Health Checks

```csharp
// Program.cs
builder.Services.AddHealthChecks()
    .AddSqlServer(
        connectionString: builder.Configuration.GetConnectionString("DefaultConnection"),
        name: "sql-server");

app.MapHealthChecks("/health");
```

**Health Check Response:**
```json
{
  "status": "Healthy",
  "totalDuration": "00:00:00.0234567",
  "entries": {
    "sql-server": {
      "status": "Healthy",
      "duration": "00:00:00.0123456"
    }
  }
}
```

## 10. Disaster Recovery

### 10.1 Backup Strategy

**Database Backups:**
- **Full Backup:** Daily at 2:00 AM (retained 30 days)
- **Differential Backup:** Every 6 hours (retained 7 days)
- **Transaction Log Backup:** Every 15 minutes (retained 48 hours)

**Application Backups:**
- Configuration files backed up before each deployment
- Git repository serves as source code backup

### 10.2 Recovery Procedures

**Database Recovery:**
```sql
-- Point-in-time recovery
RESTORE DATABASE ProdDatabase
FROM DISK = 'C:\Backups\ProdDatabase_Full.bak'
WITH NORECOVERY;

RESTORE LOG ProdDatabase
FROM DISK = 'C:\Backups\ProdDatabase_Log_2025110410.trn'
WITH RECOVERY, STOPAT = '2025-11-04 10:15:00';
```

**Application Recovery:**
- Redeploy from last known good build
- Rollback IIS site to previous version (if files retained)

**RTO/RPO Targets:**
- **RTO (Recovery Time Objective):** 1 hour
- **RPO (Recovery Point Objective):** 15 minutes (transaction log frequency)

## 11. Technology Alternatives Considered

### 11.1 GraphQL Alternatives

| Alternative | Pros | Cons | Decision |
|-------------|------|------|----------|
| **REST API** | Simpler, widely understood | Multiple endpoints, over-fetching | ❌ Rejected - requirement is GraphQL |
| **gRPC** | Excellent performance, binary protocol | Limited browser support, complex tooling | ❌ Rejected - not suitable for web clients |

### 11.2 ORM Alternatives

| Alternative | Pros | Cons | Decision |
|-------------|------|------|----------|
| **Entity Framework Core** | Migrations, LINQ, change tracking | Heavier, less SQL control | ⚠️ Alternative option if team prefers |
| **Dapper** | Lightweight, fast, SQL control | Manual schema management, no migrations | ✅ **Selected** for performance |

### 11.3 Frontend Alternatives

| Alternative | Pros | Cons | Decision |
|-------------|------|------|----------|
| **Blazor WebAssembly** | C# full-stack, strong typing | Larger initial load, .NET runtime in browser | ⚠️ Alternative option |
| **Vue.js 3** | Lightweight, easy learning curve | JavaScript ecosystem | ✅ **Selected** for wider developer pool |
| **React** | Largest ecosystem, mature | Steeper learning curve, more boilerplate | ❌ Rejected - Vue simpler for this use case |

## 12. Migration and Evolution

### 12.1 Future Architecture Evolution

**Phase 1 (Current MVP):** Monolithic API
- Single deployment unit
- Direct database access
- Simple to develop and deploy

**Phase 2 (Future - If Needed):** Service-Oriented
- Separate authentication service
- Separate data access services
- Still monolithic deployment

**Phase 3 (Future - If Needed):** Microservices
- Product service
- Partner service
- User service
- API Gateway (GraphQL Federation)

### 12.2 Technology Upgrade Path

**.NET Upgrades:**
- .NET 8 (Current) → .NET 9 (2024 release, non-LTS)
- .NET 8 → .NET 10 (2025 release, LTS) ← Recommended next upgrade

**Database Upgrades:**
- SQL Server 2019 → SQL Server 2022 (when organizational schedule allows)

**Vue.js Upgrades:**
- Vue 3.4 → Vue 3.x (minor versions) - non-breaking
- Vue 3 → Vue 4 (future) - evaluate breaking changes

## 13. Architecture Decision Records (ADRs)

### ADR-001: Use Hot Chocolate for GraphQL

**Status:** Accepted

**Context:** Need to implement GraphQL API in .NET ecosystem.

**Decision:** Use Hot Chocolate 13.9+ as GraphQL server library.

**Rationale:**
- Best-in-class .NET GraphQL implementation
- Active development and community
- Built-in Banana Cake Pop UI
- Excellent documentation

**Consequences:**
- ✅ Reduced development time (vs building custom GraphQL server)
- ✅ Built-in optimizations (DataLoader, query batching)
- ⚠️ Dependency on third-party library (mitigated by maturity and community)

### ADR-002: Use Dapper Instead of Entity Framework Core

**Status:** Accepted (with EF Core as alternative)

**Context:** Need ORM for data access layer.

**Decision:** Use Dapper as primary ORM, with option to switch to EF Core if needed.

**Rationale:**
- Performance requirements favor lightweight micro-ORM
- Need to execute stored procedures easily
- SQL control for complex queries

**Consequences:**
- ✅ Better performance for read-heavy workloads
- ✅ Direct SQL control
- ⚠️ No automatic migrations (must write SQL scripts manually)
- ⚠️ More boilerplate code for CRUD operations

**Alternatives:** EF Core remains viable if team prefers code-first migrations and LINQ.

### ADR-003: Monolithic Architecture for MVP

**Status:** Accepted

**Context:** Starting new project, need to deliver MVP quickly.

**Decision:** Build as single monolithic application initially.

**Rationale:**
- Simpler deployment and debugging
- Faster initial development
- Avoid premature optimization
- Can refactor to microservices later if needed

**Consequences:**
- ✅ Faster time to market
- ✅ Simpler operational model
- ⚠️ Scaling requires scaling entire application
- ⚠️ Future refactoring effort if microservices needed

### ADR-004: JWT for Authentication (Stateless)

**Status:** Accepted

**Context:** Need authentication mechanism for API.

**Decision:** Use JWT bearer tokens, no server-side session state.

**Rationale:**
- Stateless (supports horizontal scaling)
- Industry standard
- Works across different clients
- No Redis/session store required for MVP

**Consequences:**
- ✅ Scalable architecture
- ✅ No session management complexity
- ⚠️ Cannot revoke tokens before expiration (mitigated by short expiration time)
- ⚠️ Token refresh requires additional implementation (deferred to post-MVP)

## 14. Non-Functional Requirements

### 14.1 Performance

| Metric | Target | Measurement |
|--------|--------|-------------|
| API Response Time (Simple Query) | < 200ms (p95) | Application Insights |
| API Response Time (Complex Query) | < 1000ms (p95) | Application Insights |
| GraphQL Schema Introspection | < 100ms | Manual testing |
| Concurrent Users | 100+ simultaneous | Load testing (k6, JMeter) |
| Database Query Time | < 50ms (avg) | SQL Server Profiler |

### 14.2 Reliability

| Metric | Target | Measurement |
|--------|--------|-------------|
| Uptime | > 99.5% (downtime < 3.6 hrs/month) | Uptime monitoring |
| Error Rate | < 0.1% of requests | Application logs |
| Data Loss | Zero (through backups) | Backup verification |

### 14.3 Maintainability

| Metric | Target | Measurement |
|--------|--------|-------------|
| Code Coverage | > 70% | dotnet test with coverage |
| Technical Debt | Low (SonarQube A rating) | SonarQube analysis |
| Documentation | All public APIs documented | Code review |

## 15. Summary and Next Steps

### 15.1 Architecture Summary

This architecture design provides:
- ✅ **Scalable foundation:** Stateless API, connection pooling, horizontal scaling ready
- ✅ **Secure implementation:** JWT authentication, parameterized queries, HTTPS
- ✅ **Maintainable codebase:** Layered architecture, dependency injection, clear separation of concerns
- ✅ **Performance optimized:** DataLoader pattern, database indexing, query optimization
- ✅ **Future-proof:** Can evolve from monolith to microservices if needed

### 15.2 Technology Stack Confirmed

**Backend:**
- .NET 8, Hot Chocolate 13.9+, Dapper 2.1+, Serilog 3.1+

**Frontend:**
- Vue.js 3.4+, Vite 5.0+, @urql/vue 4.0+, Tailwind CSS 3.4+

**Infrastructure:**
- IIS 10+, SQL Server 2019+, Windows Server 2019+

### 15.3 Next Steps

1. ✅ Architecture documented and approved
2. ⏭️ **Database Schema Design** (Task 4) - Create detailed ER diagrams and SQL scripts
3. ⏭️ **Dev Environment Specification** (Task 5) - Define setup procedures
4. ⏭️ **Git Repository Setup** (Task 6) - Initialize project structure

---

**Architecture Version:** 1.0
**Status:** ✅ APPROVED FOR IMPLEMENTATION
**Next Review:** After Phase 1 completion (infrastructure setup)
