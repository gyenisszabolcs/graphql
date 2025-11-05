# GraphQL API Alkalmazás - Fejlesztési Specifikáció

## 📋 Tartalomjegyzék

1. [Áttekintés](#áttekintés)
2. [Rendszerarchitektúra](#rendszerarchitektúra)
3. [Technológiai Stack](#technológiai-stack)
4. [Projektstruktúra](#projektstruktúra)
5. [Adatbázis architektúra](#adatbázis-architektúra)
6. [GraphQL API specifikáció](#graphql-api-specifikáció)
7. [Autentikáció és jogosultságkezelés](#autentikáció-és-jogosultságkezelés)
8. [Konfiguráció kezelés](#konfiguráció-kezelés)
9. [Naplózás és hibakezelés](#naplózás-és-hibakezelés)
10. [Webes felület](#webes-felület)
11. [Telepítés és futtatás](#telepítés-és-futtatás)
12. [Fejlesztési útmutató](#fejlesztési-útmutató)
13. [Biztonsági megfontolások](#biztonsági-megfontolások)
14. [Jövőbeli bővítési lehetőségek](#jövőbeli-bővítési-lehetőségek)

---

## 1. Áttekintés

### 1.1 Projekt célja

Egy GraphQL alapú API rendszer kifejlesztése, amely lehetővé teszi MSSQL adatbázis tartalmának dinamikus lekérdezését és módosítását. A rendszer JWT alapú autentikációval védett, és támogatja mind a közvetlen GraphQL kliens, mind a webes felületen keresztüli használatot.

### 1.2 Főbb funkciók

- **GraphQL API** Hot Chocolate használatával
- **JWT autentikáció** biztonságos hozzáféréshez
- **CRUD műveletek** users, cikkek, gyartok, partnerek táblákra
- **Tárolt eljárások hívása** GraphQL-ből
- **Banana Cake Pop** integrált API Explorer
- **Webes admin felület** lekérdezések építéséhez és futtatásához
- **Naplózás** minden műveletről
- **Dev/Prod környezet** támogatás

### 1.3 Célközönség

- Webáruház rendszer (elsődleges kliens)
- Belső admin felhasználók
- Fejlesztők (API Explorer használatával)

---

## 2. Rendszerarchitektúra

### 2.1 Magas szintű architektúra

```
┌─────────────────────────────────────────────────────────────┐
│                     Kliens Alkalmazások                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Webáruház   │  │ Admin Web UI │  │  GraphQL     │      │
│  │              │  │  (Vue.js/    │  │  Clients     │      │
│  │              │  │   Blazor)    │  │              │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS + JWT Token
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    IIS / Windows Server                      │
│  ┌─────────────────────────────────────────────────────┐   │
│  │          GraphQL API (.NET 8 Web API)               │   │
│  │  ┌──────────────────────────────────────────────┐  │   │
│  │  │        Hot Chocolate GraphQL Middleware       │  │   │
│  │  │  • /graphql endpoint                          │  │   │
│  │  │  • Banana Cake Pop UI                         │  │   │
│  │  │  • Introspection                              │  │   │
│  │  └──────────────────────────────────────────────┘  │   │
│  │  ┌──────────────────────────────────────────────┐  │   │
│  │  │           Authentication Layer                │  │   │
│  │  │  • JWT Token Validation                       │  │   │
│  │  │  • User Authentication Service                │  │   │
│  │  └──────────────────────────────────────────────┘  │   │
│  │  ┌──────────────────────────────────────────────┐  │   │
│  │  │            Business Logic Layer               │  │   │
│  │  │  • Queries (Query Resolvers)                  │  │   │
│  │  │  • Mutations (Mutation Resolvers)             │  │   │
│  │  │  • Data Loaders                               │  │   │
│  │  └──────────────────────────────────────────────┘  │   │
│  │  ┌──────────────────────────────────────────────┐  │   │
│  │  │         Data Access Layer (Repository)        │  │   │
│  │  │  • UserRepository                             │  │   │
│  │  │  • CikkRepository                             │  │   │
│  │  │  • GyartoRepository                           │  │   │
│  │  │  • PartnerRepository                          │  │   │
│  │  │  • StoredProcedureExecutor                    │  │   │
│  │  └──────────────────────────────────────────────┘  │   │
│  │  ┌──────────────────────────────────────────────┐  │   │
│  │  │         Cross-Cutting Concerns                │  │   │
│  │  │  • Logging (Serilog)                          │  │   │
│  │  │  • Configuration Management                   │  │   │
│  │  │  • Exception Handling                         │  │   │
│  │  └──────────────────────────────────────────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ SQL Authentication
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Microsoft SQL Server (10.10.10.69)             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Database: AppDatabase                              │   │
│  │  • users table                                      │   │
│  │  • cikkek table                                     │   │
│  │  • gyartok table                                    │   │
│  │  • partnerek table                                  │   │
│  │  • Stored Procedures                                │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 Komponensek és felelősségeik

#### **2.2.1 GraphQL API Layer**
- **Felelősség**: HTTP kérések fogadása, GraphQL query/mutation feldolgozás
- **Technológia**: Hot Chocolate 13+, ASP.NET Core 8
- **Főbb feladatok**:
  - GraphQL schema definíció
  - Query és Mutation resolverek
  - Schema introspection
  - Banana Cake Pop UI kiszolgálása

#### **2.2.2 Authentication Layer**
- **Felelősség**: Felhasználói azonosítás és jogosultság ellenőrzés
- **Technológia**: JWT (JSON Web Tokens)
- **Főbb feladatok**:
  - JWT token generálás (login)
  - Token validáció minden kérésnél
  - User claims kezelése
  - Authorization policy-k

#### **2.2.3 Business Logic Layer**
- **Felelősség**: Üzleti logika, adatkezelés koordinálása
- **Komponensek**:
  - **Query Resolvers**: Lekérdezések kezelése
  - **Mutation Resolvers**: Módosítások kezelése
  - **Services**: Komplex üzleti logika
  - **Data Loaders**: N+1 query probléma megoldása

#### **2.2.4 Data Access Layer (Repository Pattern)**
- **Felelősség**: Adatbázis műveletek absztrakciója
- **Technológia**: Dapper (lightweight ORM) vagy EF Core
- **Komponensek**:
  - `IUserRepository` / `UserRepository`
  - `ICikkRepository` / `CikkRepository`
  - `IGyartoRepository` / `GyartoRepository`
  - `IPartnerRepository` / `PartnerRepository`
  - `IStoredProcedureExecutor`

#### **2.2.5 Cross-Cutting Concerns**
- **Logging**: Serilog minden művelet naplózására
- **Configuration**: appsettings.json, user secrets
- **Exception Handling**: Global error filter
- **Validation**: Input validáció

#### **2.2.6 Web Frontend**
- **Felelősség**: Felhasználói felület admin funkciókhoz
- **Technológia**: Vue.js 3 vagy Blazor WebAssembly
- **Funkciók**:
  - Bejelentkezés
  - GraphQL query builder
  - Eredmény megjelenítés
  - Tábla és mező tallózás

---

## 3. Technológiai Stack

### 3.1 Backend

| Komponens | Technológia | Verzió | Indoklás |
|-----------|-------------|--------|----------|
| Framework | .NET | 8.0 | Modern, nagy teljesítményű, hosszú távú támogatás (LTS) |
| GraphQL Library | Hot Chocolate | 13.9+ | Legjobb .NET GraphQL implementáció, teljes feature set |
| ORM | Dapper | 2.1+ | Lightweight, gyors, SQL kontroll |
| Alternatív ORM | Entity Framework Core | 8.0 | Complex queries, migrations támogatás |
| Logging | Serilog | 3.1+ | Strukturált naplózás, flexible sink-ek |
| Authentication | JWT Bearer | beépített | Stateless, skálázható |
| Configuration | ASP.NET Core Config | beépített | JSON, környezeti változók, user secrets |
| Database Client | Microsoft.Data.SqlClient | 5.1+ | MSSQL natív támogatás |

### 3.2 Frontend

| Komponens | Technológia | Verzió | Indoklás |
|-----------|-------------|--------|----------|
| Framework | Vue.js | 3.4+ | Modern, reaktív, könnyű |
| Alternatíva | Blazor WebAssembly | .NET 8 | C# full-stack, típusbiztonság |
| GraphQL Client | @urql/vue vagy Apollo | latest | GraphQL kliens integráció |
| UI Framework | Vuetify / Bootstrap | latest | Gyors UI fejlesztés |

### 3.3 Infrastruktúra

- **Web Server**: IIS 10+
- **OS**: Windows Server 2019+
- **Database**: Microsoft SQL Server 2019+
- **Deployment**: Publish to folder → IIS

---

## 4. Projektstruktúra

### 4.1 Fájlstruktúra

```
GraphQLApp/
│
├── src/
│   ├── GraphQLApp.API/                    # Fő API projekt
│   │   ├── Controllers/
│   │   │   └── AuthController.cs          # Login endpoint
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
│   │   │   │   └── UserByIdDataLoader.cs
│   │   │   └── Filters/
│   │   │       └── ErrorFilter.cs
│   │   ├── Middleware/
│   │   │   └── RequestLoggingMiddleware.cs
│   │   ├── Program.cs
│   │   ├── appsettings.json
│   │   ├── appsettings.Development.json
│   │   ├── appsettings.Production.json
│   │   └── GraphQLApp.API.csproj
│   │
│   ├── GraphQLApp.Core/                    # Domain modellek, interfészek
│   │   ├── Entities/
│   │   │   ├── User.cs
│   │   │   ├── Cikk.cs
│   │   │   ├── Gyarto.cs
│   │   │   └── Partner.cs
│   │   ├── Interfaces/
│   │   │   ├── IUserRepository.cs
│   │   │   ├── ICikkRepository.cs
│   │   │   ├── IGyartoRepository.cs
│   │   │   ├── IPartnerRepository.cs
│   │   │   └── IStoredProcedureExecutor.cs
│   │   ├── DTOs/
│   │   │   ├── LoginRequest.cs
│   │   │   ├── LoginResponse.cs
│   │   │   └── CreateCikkInput.cs
│   │   └── GraphQLApp.Core.csproj
│   │
│   ├── GraphQLApp.Infrastructure/          # Data access implementációk
│   │   ├── Repositories/
│   │   │   ├── UserRepository.cs
│   │   │   ├── CikkRepository.cs
│   │   │   ├── GyartoRepository.cs
│   │   │   ├── PartnerRepository.cs
│   │   │   └── StoredProcedureExecutor.cs
│   │   ├── Data/
│   │   │   └── DapperContext.cs
│   │   └── GraphQLApp.Infrastructure.csproj
│   │
│   ├── GraphQLApp.Services/                # Business logic services
│   │   ├── AuthService.cs
│   │   ├── JwtTokenService.cs
│   │   └── GraphQLApp.Services.csproj
│   │
│   └── GraphQLApp.Web/                     # Frontend (Vue.js vagy Blazor)
│       ├── public/
│       ├── src/
│       │   ├── components/
│       │   ├── views/
│       │   ├── services/
│       │   │   └── graphqlClient.js
│       │   ├── App.vue
│       │   └── main.js
│       ├── package.json
│       └── vite.config.js
│
├── tests/
│   ├── GraphQLApp.API.Tests/
│   └── GraphQLApp.Infrastructure.Tests/
│
├── docs/
│   └── API_Documentation.md
│
├── scripts/
│   ├── deploy-dev.ps1
│   └── deploy-prod.ps1
│
├── .gitignore
├── appsettings.Local.json               # Git-ignore-olva!
├── GraphQLApp.sln
└── README.md
```

### 4.2 NuGet csomagok

**GraphQLApp.API.csproj:**
```xml
<ItemGroup>
  <PackageReference Include="HotChocolate.AspNetCore" Version="13.9.0" />
  <PackageReference Include="HotChocolate.AspNetCore.Authorization" Version="13.9.0" />
  <PackageReference Include="HotChocolate.Data" Version="13.9.0" />
  <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="8.0.0" />
  <PackageReference Include="Serilog.AspNetCore" Version="8.0.0" />
  <PackageReference Include="Serilog.Sinks.File" Version="5.0.0" />
  <PackageReference Include="Serilog.Sinks.Console" Version="5.0.0" />
</ItemGroup>
```

**GraphQLApp.Infrastructure.csproj:**
```xml
<ItemGroup>
  <PackageReference Include="Dapper" Version="2.1.28" />
  <PackageReference Include="Microsoft.Data.SqlClient" Version="5.1.5" />
  <!-- Vagy EF Core használata esetén: -->
  <!-- <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.0" /> -->
</ItemGroup>
```

---

## 5. Adatbázis architektúra

### 5.1 Meglévő adatbázis

**⚠️ FONTOS:** A projekt egy **már létező** adatbázist használ. Nem kell új adatbázist vagy táblákat létrehozni!

- **Adatbázis neve:** `dev_graphql`
- **SQL Server cím:** `10.10.10.69`
- **Autentikáció:** SQL Server Authentication (felhasználónév/jelszó tárolt az appsettings.Local.json-ban)

### 5.2 Táblák sémája (meglévő struktúra)

#### **CIKK tábla**
```sql
-- Meglévő tábla struktúra
CIKKID          INT             -- Primary Key
CIKKSZAM        NVARCHAR(?)     -- Cikkszám
CIKKNEV         NVARCHAR(?)     -- Cikk megnevezés
GYARTO          NVARCHAR(?)     -- Gyártó (GYARTO.GYARTO-hoz kapcsolódik)
GYCIKKSZAM      NVARCHAR(?)     -- Gyártói cikkszám
ELOALLITOPID    INT             -- Előállító (PARTNER.PARTNERID-hoz kapcsolódik)
CRUS            NVARCHAR(?)     -- Létrehozó felhasználó (USERS.USERCODE)
CRDTI           DATETIME        -- Létrehozás dátuma/ideje
```

#### **GYARTO tábla**
```sql
-- Meglévő tábla struktúra
GYARTO          NVARCHAR(?)     -- Primary Key (gyártó azonosító)
MEGJ            NVARCHAR(?)     -- Megjegyzés
LEIRAS          NVARCHAR(?)     -- Leírás
GYARTOADAT1     NVARCHAR(?)     -- Gyártó adat 1
GYARTOADAT2     NVARCHAR(?)     -- Gyártó adat 2
CRUS            NVARCHAR(?)     -- Létrehozó felhasználó (USERS.USERCODE)
CRDTI           DATETIME        -- Létrehozás dátuma/ideje
```

#### **PARTNER tábla**
```sql
-- Meglévő tábla struktúra
PARTNERID       INT             -- Primary Key
PARTNERNEV      NVARCHAR(?)     -- Partner neve
FIZOSZT         NVARCHAR(?)     -- Fizetési osztály/mód
ORSZAG          NVARCHAR(?)     -- Ország
IRSZ            NVARCHAR(?)     -- Irányítószám
VAROS           NVARCHAR(?)     -- Város
UTCA            NVARCHAR(?)     -- Utca
CRUS            NVARCHAR(?)     -- Létrehozó felhasználó (USERS.USERCODE)
CRDTI           DATETIME        -- Létrehozás dátuma/ideje
```

#### **USERS tábla**
```sql
-- Meglévő tábla struktúra
USERCODE        NVARCHAR(?)     -- Primary Key (felhasználói kód)
USERNAME        NVARCHAR(?)     -- Felhasználó neve
```

**Megjegyzés:** A USERS tábla jelenleg csak 2 mezőt tartalmaz. Az autentikációhoz szükséges további mezők (pl. PasswordHash) későbbi fázisban kerülnek hozzáadásra, vagy alternatív megoldást kell alkalmazni (pl. külön Auth tábla).

### 5.3 Kapcsolati séma

```
GYARTO (1:GYARTO) ──────< (N:GYARTO) CIKK
PARTNER (1:PARTNERID) ──────< (N:ELOALLITOPID) CIKK
USERS (1:USERCODE) ──────< (N:CRUS) CIKK
USERS (1:USERCODE) ──────< (N:CRUS) GYARTO
USERS (1:USERCODE) ──────< (N:CRUS) PARTNER
```

### 5.4 Tárolt eljárások példák

**Megjegyzés:** Ezek példa tárolt eljárások, amelyek a későbbi fázisokban kerülnek implementálásra.

#### **GetCikkekByGyarto**
```sql
CREATE PROCEDURE GetCikkekByGyarto
    @Gyarto NVARCHAR(100)
AS
BEGIN
    SELECT c.*, g.MEGJ, g.LEIRAS
    FROM CIKK c
    INNER JOIN GYARTO g ON c.GYARTO = g.GYARTO
    WHERE c.GYARTO = @Gyarto
    ORDER BY c.CIKKNEV;
END
```

#### **GetStatisztika**
```sql
CREATE PROCEDURE GetStatisztika
AS
BEGIN
    SELECT
        (SELECT COUNT(*) FROM CIKK) AS CikkekSzama,
        (SELECT COUNT(*) FROM GYARTO) AS GyartokSzama,
        (SELECT COUNT(*) FROM PARTNER) AS PartnerekSzama,
        (SELECT COUNT(DISTINCT GYARTO) FROM CIKK) AS HasznaltGyartokSzama;
END
```

### 5.5 Connection String konfiguráció

**⚠️ FONTOS:** A projekt egyetlen adatbázist használ (`dev_graphql`), nincs szükség külön dev/prod környezetre az adatbázis szintjén.

**appsettings.json (alapértelmezett):**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=dev_graphql;TrustServerCertificate=True;"
  },
  "JwtSettings": {
    "SecretKey": "PLACEHOLDER_MIN_32_CHARS_REPLACE_IN_LOCAL_JSON_FILE",
    "Issuer": "GraphQLApp",
    "Audience": "GraphQLApp",
    "ExpirationMinutes": 60
  }
}
```

**appsettings.Local.json** (git-ignore-olva, valós credentials itt):
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=dev_graphql;User ID=YOUR_USERNAME;Password=YOUR_PASSWORD;TrustServerCertificate=True;"
  },
  "JwtSettings": {
    "SecretKey": "your-super-secret-key-min-32-chars-long-12345678-CHANGE-THIS"
  }
}
```

**⚠️ BIZTONSÁGI FIGYELMEZTETÉSEK:**
1. Az `appsettings.Local.json` fájlt **SOHA NE** commitoljuk Git-be!
2. Az SQL Server felhasználónevet és jelszót **csak** az `appsettings.Local.json`-ban tároljuk
3. A JWT SecretKey-t **minimum 32 karakter** hosszúnak kell lennie
4. Production környezetben használjunk még erősebb jelszavakat és titkosítást

**.gitignore:**
```
appsettings.Local.json
appsettings.*.Local.json
*.Local.json
```

---

## 6. GraphQL API specifikáció

### 6.1 GraphQL Schema

#### **6.1.1 Types (Entitások)**

**Megjegyzés:** Ezek a GraphQL típusok a meglévő adatbázis táblákat reprezentálják.

**UserType.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Types;

public class UserType
{
    [GraphQLName("userCode")]
    public string UserCode { get; set; } = string.Empty; // USERS.USERCODE (PK)

    [GraphQLName("userName")]
    public string UserName { get; set; } = string.Empty; // USERS.USERNAME
}
```

**CikkType.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Types;

public class CikkType
{
    [GraphQLName("cikkId")]
    public int CikkId { get; set; } // CIKK.CIKKID (PK)

    [GraphQLName("cikkSzam")]
    public string? CikkSzam { get; set; } // CIKK.CIKKSZAM

    [GraphQLName("cikkNev")]
    public string? CikkNev { get; set; } // CIKK.CIKKNEV

    [GraphQLName("gyarto")]
    public string? Gyarto { get; set; } // CIKK.GYARTO (FK -> GYARTO.GYARTO)

    [GraphQLName("gyCikkSzam")]
    public string? GyCikkSzam { get; set; } // CIKK.GYCIKKSZAM

    [GraphQLName("eloallitoPId")]
    public int? EloallitoPId { get; set; } // CIKK.ELOALLITOPID (FK -> PARTNER.PARTNERID)

    [GraphQLName("crus")]
    public string? Crus { get; set; } // CIKK.CRUS (FK -> USERS.USERCODE)

    [GraphQLName("crdti")]
    public DateTime? Crdti { get; set; } // CIKK.CRDTI

    // Navigation properties - resolverek
    public async Task<GyartoType?> GetGyartoAsync(
        [Service] IGyartoRepository gyartoRepository)
    {
        if (string.IsNullOrEmpty(Gyarto)) return null;
        return await gyartoRepository.GetByGyartoCodeAsync(Gyarto);
    }

    public async Task<PartnerType?> GetEloallitoAsync(
        [Service] IPartnerRepository partnerRepository)
    {
        if (EloallitoPId == null) return null;
        return await partnerRepository.GetByIdAsync(EloallitoPId.Value);
    }

    public async Task<UserType?> GetCreatedByUserAsync(
        [Service] IUserRepository userRepository)
    {
        if (string.IsNullOrEmpty(Crus)) return null;
        return await userRepository.GetByUserCodeAsync(Crus);
    }
}
```

**GyartoType.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Types;

public class GyartoType
{
    [GraphQLName("gyarto")]
    public string Gyarto { get; set; } = string.Empty; // GYARTO.GYARTO (PK)

    [GraphQLName("megj")]
    public string? Megj { get; set; } // GYARTO.MEGJ

    [GraphQLName("leiras")]
    public string? Leiras { get; set; } // GYARTO.LEIRAS

    [GraphQLName("gyartoAdat1")]
    public string? GyartoAdat1 { get; set; } // GYARTO.GYARTOADAT1

    [GraphQLName("gyartoAdat2")]
    public string? GyartoAdat2 { get; set; } // GYARTO.GYARTOADAT2

    [GraphQLName("crus")]
    public string? Crus { get; set; } // GYARTO.CRUS (FK -> USERS.USERCODE)

    [GraphQLName("crdti")]
    public DateTime? Crdti { get; set; } // GYARTO.CRDTI

    // Collection navigation
    public async Task<List<CikkType>> GetCikkekAsync(
        [Service] ICikkRepository cikkRepository)
    {
        return await cikkRepository.GetByGyartoAsync(Gyarto);
    }

    public async Task<UserType?> GetCreatedByUserAsync(
        [Service] IUserRepository userRepository)
    {
        if (string.IsNullOrEmpty(Crus)) return null;
        return await userRepository.GetByUserCodeAsync(Crus);
    }
}
```

**PartnerType.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Types;

public class PartnerType
{
    [GraphQLName("partnerId")]
    public int PartnerId { get; set; } // PARTNER.PARTNERID (PK)

    [GraphQLName("partnerNev")]
    public string? PartnerNev { get; set; } // PARTNER.PARTNERNEV

    [GraphQLName("fizOszt")]
    public string? FizOszt { get; set; } // PARTNER.FIZOSZT

    [GraphQLName("orszag")]
    public string? Orszag { get; set; } // PARTNER.ORSZAG

    [GraphQLName("irsz")]
    public string? Irsz { get; set; } // PARTNER.IRSZ

    [GraphQLName("varos")]
    public string? Varos { get; set; } // PARTNER.VAROS

    [GraphQLName("utca")]
    public string? Utca { get; set; } // PARTNER.UTCA

    [GraphQLName("crus")]
    public string? Crus { get; set; } // PARTNER.CRUS (FK -> USERS.USERCODE)

    [GraphQLName("crdti")]
    public DateTime? Crdti { get; set; } // PARTNER.CRDTI

    // Collection navigation
    public async Task<List<CikkType>> GetCikkekAsync(
        [Service] ICikkRepository cikkRepository)
    {
        return await cikkRepository.GetByEloallitoIdAsync(PartnerId);
    }

    public async Task<UserType?> GetCreatedByUserAsync(
        [Service] IUserRepository userRepository)
    {
        if (string.IsNullOrEmpty(Crus)) return null;
        return await userRepository.GetByUserCodeAsync(Crus);
    }
}
```

#### **6.1.2 Queries (Lekérdezések)**

**UserQueries.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Queries;

[ExtendObjectType("Query")]
public class UserQueries
{
    [Authorize]
    public async Task<List<UserType>> GetUsers(
        [Service] IUserRepository userRepository)
    {
        return await userRepository.GetAllAsync();
    }

    [Authorize]
    public async Task<UserType?> GetUserById(
        int id,
        [Service] IUserRepository userRepository)
    {
        return await userRepository.GetByIdAsync(id);
    }

    [Authorize]
    public async Task<UserType?> GetUserByUsername(
        string username,
        [Service] IUserRepository userRepository)
    {
        return await userRepository.GetByUsernameAsync(username);
    }
}
```

**CikkQueries.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Queries;

[ExtendObjectType("Query")]
public class CikkQueries
{
    [Authorize]
    [UsePaging]
    [UseFiltering]
    [UseSorting]
    public async Task<IEnumerable<CikkType>> GetCikkek(
        [Service] ICikkRepository cikkRepository)
    {
        return await cikkRepository.GetAllAsync();
    }

    [Authorize]
    public async Task<CikkType?> GetCikkById(
        int id,
        [Service] ICikkRepository cikkRepository)
    {
        return await cikkRepository.GetByIdAsync(id);
    }

    [Authorize]
    public async Task<CikkType?> GetCikkByCikkKod(
        string cikkKod,
        [Service] ICikkRepository cikkRepository)
    {
        return await cikkRepository.GetByCikkKodAsync(cikkKod);
    }

    [Authorize]
    public async Task<List<CikkType>> GetCikkekByGyarto(
        int gyartoId,
        [Service] IStoredProcedureExecutor spExecutor)
    {
        return await spExecutor.ExecuteAsync<CikkType>(
            "GetCikkekByGyarto",
            new { GyartoId = gyartoId });
    }
}
```

**GyartoQueries.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Queries;

[ExtendObjectType("Query")]
public class GyartoQueries
{
    [Authorize]
    [UseFiltering]
    [UseSorting]
    public async Task<List<GyartoType>> GetGyartok(
        [Service] IGyartoRepository gyartoRepository)
    {
        return await gyartoRepository.GetAllAsync();
    }

    [Authorize]
    public async Task<GyartoType?> GetGyartoById(
        int id,
        [Service] IGyartoRepository gyartoRepository)
    {
        return await gyartoRepository.GetByIdAsync(id);
    }
}
```

**PartnerQueries.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Queries;

[ExtendObjectType("Query")]
public class PartnerQueries
{
    [Authorize]
    [UseFiltering]
    [UseSorting]
    public async Task<List<PartnerType>> GetPartnerek(
        [Service] IPartnerRepository partnerRepository)
    {
        return await partnerRepository.GetAllAsync();
    }

    [Authorize]
    public async Task<PartnerType?> GetPartnerById(
        int id,
        [Service] IPartnerRepository partnerRepository)
    {
        return await partnerRepository.GetByIdAsync(id);
    }

    [Authorize]
    public async Task<List<PartnerType>> GetPartnerekByTipus(
        string partnerTipus,
        [Service] IPartnerRepository partnerRepository)
    {
        return await partnerRepository.GetByTipusAsync(partnerTipus);
    }
}
```

#### **6.1.3 Mutations (Módosítások)**

**Input típusok (DTOs):**

```csharp
namespace GraphQLApp.Core.DTOs;

public record CreateCikkInput(
    string CikkKod,
    string Megnevezes,
    string? Leiras,
    decimal EgysegAr,
    string? MennyisegiEgyseg,
    int? GyartoId
);

public record UpdateCikkInput(
    int Id,
    string? CikkKod,
    string? Megnevezes,
    string? Leiras,
    decimal? EgysegAr,
    string? MennyisegiEgyseg,
    int? GyartoId
);

public record CreateGyartoInput(
    string GyartoNev,
    string? Orszag,
    string? ContactEmail,
    string? ContactPhone
);

public record UpdateGyartoInput(
    int Id,
    string? GyartoNev,
    string? Orszag,
    string? ContactEmail,
    string? ContactPhone
);

public record CreatePartnerInput(
    string PartnerNev,
    string? AdoSzam,
    string? Cim,
    string? ContactPerson,
    string? Email,
    string? Phone,
    string? PartnerTipus
);

public record UpdatePartnerInput(
    int Id,
    string? PartnerNev,
    string? AdoSzam,
    string? Cim,
    string? ContactPerson,
    string? Email,
    string? Phone,
    string? PartnerTipus
);
```

**CikkMutations.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Mutations;

[ExtendObjectType("Mutation")]
public class CikkMutations
{
    [Authorize]
    public async Task<CikkType> CreateCikk(
        CreateCikkInput input,
        [Service] ICikkRepository cikkRepository,
        [Service] ILogger<CikkMutations> logger)
    {
        logger.LogInformation("Creating new cikk: {CikkKod}", input.CikkKod);

        var cikk = new Cikk
        {
            CikkKod = input.CikkKod,
            Megnevezes = input.Megnevezes,
            Leiras = input.Leiras,
            EgysegAr = input.EgysegAr,
            MennyisegiEgyseg = input.MennyisegiEgyseg,
            GyartoId = input.GyartoId,
            CreatedAt = DateTime.UtcNow
        };

        var created = await cikkRepository.CreateAsync(cikk);
        return created;
    }

    [Authorize]
    public async Task<CikkType> UpdateCikk(
        UpdateCikkInput input,
        [Service] ICikkRepository cikkRepository,
        [Service] ILogger<CikkMutations> logger)
    {
        logger.LogInformation("Updating cikk: {Id}", input.Id);

        var existing = await cikkRepository.GetByIdAsync(input.Id);
        if (existing == null)
            throw new GraphQLException("Cikk nem található.");

        if (input.CikkKod != null) existing.CikkKod = input.CikkKod;
        if (input.Megnevezes != null) existing.Megnevezes = input.Megnevezes;
        if (input.Leiras != null) existing.Leiras = input.Leiras;
        if (input.EgysegAr.HasValue) existing.EgysegAr = input.EgysegAr.Value;
        if (input.MennyisegiEgyseg != null) existing.MennyisegiEgyseg = input.MennyisegiEgyseg;
        if (input.GyartoId.HasValue) existing.GyartoId = input.GyartoId;

        existing.UpdatedAt = DateTime.UtcNow;

        await cikkRepository.UpdateAsync(existing);
        return existing;
    }

    [Authorize]
    public async Task<bool> DeleteCikk(
        int id,
        [Service] ICikkRepository cikkRepository,
        [Service] ILogger<CikkMutations> logger)
    {
        logger.LogInformation("Deleting cikk: {Id}", id);
        return await cikkRepository.DeleteAsync(id);
    }
}
```

**GyartoMutations.cs, PartnerMutations.cs, UserMutations.cs** hasonló felépítéssel.

#### **6.1.4 Generált GraphQL Schema (példa)**

```graphql
type Query {
  users: [UserType!]!
  userById(id: Int!): UserType
  userByUsername(username: String!): UserType

  cikkek(first: Int, after: String): CikkConnection!
  cikkById(id: Int!): CikkType
  cikkByCikkKod(cikkKod: String!): CikkType
  cikkekByGyarto(gyartoId: Int!): [CikkType!]!

  gyartok: [GyartoType!]!
  gyartoById(id: Int!): GyartoType

  partnerek: [PartnerType!]!
  partnerById(id: Int!): PartnerType
  partnerekByTipus(partnerTipus: String!): [PartnerType!]!
}

type Mutation {
  createCikk(input: CreateCikkInput!): CikkType!
  updateCikk(input: UpdateCikkInput!): CikkType!
  deleteCikk(id: Int!): Boolean!

  createGyarto(input: CreateGyartoInput!): GyartoType!
  updateGyarto(input: UpdateGyartoInput!): GyartoType!
  deleteGyarto(id: Int!): Boolean!

  createPartner(input: CreatePartnerInput!): PartnerType!
  updatePartner(input: UpdatePartnerInput!): PartnerType!
  deletePartner(id: Int!): Boolean!
}

type UserType {
  id: Int!
  username: String!
  email: String
  fullName: String
  isActive: Boolean!
  createdAt: DateTime!
  updatedAt: DateTime
}

type CikkType {
  id: Int!
  cikkKod: String!
  megnevezes: String!
  leiras: String
  egysegAr: Decimal!
  mennyisegiEgyseg: String
  gyartoId: Int
  createdAt: DateTime!
  updatedAt: DateTime
  gyarto: GyartoType
}

type GyartoType {
  id: Int!
  gyartoNev: String!
  orszag: String
  contactEmail: String
  contactPhone: String
  createdAt: DateTime!
  updatedAt: DateTime
  cikkek: [CikkType!]!
}

type PartnerType {
  id: Int!
  partnerNev: String!
  adoSzam: String
  cim: String
  contactPerson: String
  email: String
  phone: String
  partnerTipus: String
  createdAt: DateTime!
  updatedAt: DateTime
}

input CreateCikkInput {
  cikkKod: String!
  megnevezes: String!
  leiras: String
  egysegAr: Decimal!
  mennyisegiEgyseg: String
  gyartoId: Int
}

input UpdateCikkInput {
  id: Int!
  cikkKod: String
  megnevezes: String
  leiras: String
  egysegAr: Decimal
  mennyisegiEgyseg: String
  gyartoId: Int
}

# ... további input típusok
```

### 6.2 Hot Chocolate konfiguráció

**Program.cs:**
```csharp
using GraphQLApp.API.GraphQL.Queries;
using GraphQLApp.API.GraphQL.Mutations;
using GraphQLApp.API.GraphQL.Filters;
using GraphQLApp.Infrastructure.Data;
using GraphQLApp.Infrastructure.Repositories;
using GraphQLApp.Core.Interfaces;
using GraphQLApp.Services;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;
using Serilog;

var builder = WebApplication.CreateBuilder(args);

// Serilog konfiguráció
Log.Logger = new LoggerConfiguration()
    .ReadFrom.Configuration(builder.Configuration)
    .Enrich.FromLogContext()
    .WriteTo.Console()
    .WriteTo.File("logs/graphqlapp-.txt", rollingInterval: RollingInterval.Day)
    .CreateLogger();

builder.Host.UseSerilog();

// MSSQL Connection
builder.Services.AddSingleton<DapperContext>(sp =>
{
    var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
    return new DapperContext(connectionString);
});

// Repositories
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddScoped<ICikkRepository, CikkRepository>();
builder.Services.AddScoped<IGyartoRepository, GyartoRepository>();
builder.Services.AddScoped<IPartnerRepository, PartnerRepository>();
builder.Services.AddScoped<IStoredProcedureExecutor, StoredProcedureExecutor>();

// Services
builder.Services.AddScoped<IAuthService, AuthService>();
builder.Services.AddScoped<IJwtTokenService, JwtTokenService>();

// JWT Authentication
var jwtSettings = builder.Configuration.GetSection("JwtSettings");
var secretKey = jwtSettings["SecretKey"] ?? throw new InvalidOperationException("JWT SecretKey is missing");

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = jwtSettings["Issuer"],
            ValidAudience = jwtSettings["Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey))
        };
    });

builder.Services.AddAuthorization();

// GraphQL
builder.Services
    .AddGraphQLServer()
    .AddQueryType(d => d.Name("Query"))
        .AddTypeExtension<UserQueries>()
        .AddTypeExtension<CikkQueries>()
        .AddTypeExtension<GyartoQueries>()
        .AddTypeExtension<PartnerQueries>()
    .AddMutationType(d => d.Name("Mutation"))
        .AddTypeExtension<CikkMutations>()
        .AddTypeExtension<GyartoMutations>()
        .AddTypeExtension<PartnerMutations>()
        .AddTypeExtension<UserMutations>()
    .AddAuthorization()
    .AddFiltering()
    .AddSorting()
    .AddProjections()
    .AddErrorFilter<ErrorFilter>()
    .ModifyRequestOptions(opt => opt.IncludeExceptionDetails = builder.Environment.IsDevelopment());

builder.Services.AddControllers();

var app = builder.Build();

app.UseSerilogRequestLogging();

if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}

app.UseRouting();

app.UseAuthentication();
app.UseAuthorization();

app.MapGraphQL();
app.MapControllers();

app.MapGet("/", () => Results.Redirect("/graphql"));

Log.Information("GraphQL API elindult: {Environment}", app.Environment.EnvironmentName);

app.Run();
```

### 6.3 Error Filter

**ErrorFilter.cs:**
```csharp
namespace GraphQLApp.API.GraphQL.Filters;

public class ErrorFilter : IErrorFilter
{
    private readonly ILogger<ErrorFilter> _logger;

    public ErrorFilter(ILogger<ErrorFilter> logger)
    {
        _logger = logger;
    }

    public IError OnError(IError error)
    {
        _logger.LogError(error.Exception, "GraphQL Error: {Message}", error.Message);

        return error.WithMessage(error.Exception?.Message ?? error.Message);
    }
}
```

---

## 7. Autentikáció és jogosultságkezelés

### 7.1 JWT Token alapú autentikáció

#### **7.1.1 Konfiguráció**

**appsettings.json:**
```json
{
  "JwtSettings": {
    "SecretKey": "place-this-in-appsettings-local-json",
    "Issuer": "GraphQLApp.API",
    "Audience": "GraphQLApp.Clients",
    "ExpirationMinutes": 60
  }
}
```

#### **7.1.2 JWT Token Service**

**IJwtTokenService.cs:**
```csharp
namespace GraphQLApp.Core.Interfaces;

public interface IJwtTokenService
{
    string GenerateToken(int userId, string username, List<string> roles);
    ClaimsPrincipal? ValidateToken(string token);
}
```

**JwtTokenService.cs:**
```csharp
namespace GraphQLApp.Services;

using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Tokens;

public class JwtTokenService : IJwtTokenService
{
    private readonly IConfiguration _configuration;

    public JwtTokenService(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public string GenerateToken(int userId, string username, List<string> roles)
    {
        var jwtSettings = _configuration.GetSection("JwtSettings");
        var secretKey = jwtSettings["SecretKey"] ?? throw new InvalidOperationException("SecretKey missing");
        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey));
        var credentials = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

        var claims = new List<Claim>
        {
            new Claim(ClaimTypes.NameIdentifier, userId.ToString()),
            new Claim(ClaimTypes.Name, username),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
        };

        foreach (var role in roles)
        {
            claims.Add(new Claim(ClaimTypes.Role, role));
        }

        var token = new JwtSecurityToken(
            issuer: jwtSettings["Issuer"],
            audience: jwtSettings["Audience"],
            claims: claims,
            expires: DateTime.UtcNow.AddMinutes(Convert.ToDouble(jwtSettings["ExpirationMinutes"])),
            signingCredentials: credentials
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }

    public ClaimsPrincipal? ValidateToken(string token)
    {
        var jwtSettings = _configuration.GetSection("JwtSettings");
        var secretKey = jwtSettings["SecretKey"] ?? throw new InvalidOperationException("SecretKey missing");
        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey));

        var tokenHandler = new JwtSecurityTokenHandler();
        try
        {
            var principal = tokenHandler.ValidateToken(token, new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidateAudience = true,
                ValidateLifetime = true,
                ValidateIssuerSigningKey = true,
                ValidIssuer = jwtSettings["Issuer"],
                ValidAudience = jwtSettings["Audience"],
                IssuerSigningKey = key
            }, out _);

            return principal;
        }
        catch
        {
            return null;
        }
    }
}
```

#### **7.1.3 Authentication Service**

**IAuthService.cs:**
```csharp
namespace GraphQLApp.Core.Interfaces;

public interface IAuthService
{
    Task<LoginResponse?> AuthenticateAsync(string username, string password);
}
```

**AuthService.cs:**
```csharp
namespace GraphQLApp.Services;

using GraphQLApp.Core.Interfaces;
using GraphQLApp.Core.DTOs;

public class AuthService : IAuthService
{
    private readonly IUserRepository _userRepository;
    private readonly IJwtTokenService _jwtTokenService;

    public AuthService(IUserRepository userRepository, IJwtTokenService jwtTokenService)
    {
        _userRepository = userRepository;
        _jwtTokenService = jwtTokenService;
    }

    public async Task<LoginResponse?> AuthenticateAsync(string username, string password)
    {
        var user = await _userRepository.GetByUsernameAsync(username);
        if (user == null || !user.IsActive)
            return null;

        // Jelszó ellenőrzés (BCrypt vagy hasonló hash)
        if (!BCrypt.Net.BCrypt.Verify(password, user.PasswordHash))
            return null;

        // Token generálás
        var token = _jwtTokenService.GenerateToken(
            user.Id,
            user.Username,
            new List<string> { "User" }
        );

        return new LoginResponse
        {
            Token = token,
            Username = user.Username,
            ExpiresAt = DateTime.UtcNow.AddMinutes(60)
        };
    }
}
```

#### **7.1.4 Login Controller**

**AuthController.cs:**
```csharp
namespace GraphQLApp.API.Controllers;

using Microsoft.AspNetCore.Mvc;
using GraphQLApp.Core.Interfaces;
using GraphQLApp.Core.DTOs;

[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private readonly IAuthService _authService;
    private readonly ILogger<AuthController> _logger;

    public AuthController(IAuthService authService, ILogger<AuthController> logger)
    {
        _authService = authService;
        _logger = logger;
    }

    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginRequest request)
    {
        _logger.LogInformation("Login attempt for user: {Username}", request.Username);

        var response = await _authService.AuthenticateAsync(request.Username, request.Password);

        if (response == null)
        {
            _logger.LogWarning("Failed login attempt for user: {Username}", request.Username);
            return Unauthorized(new { message = "Hibás felhasználónév vagy jelszó" });
        }

        _logger.LogInformation("Successful login for user: {Username}", request.Username);
        return Ok(response);
    }
}
```

**LoginRequest.cs / LoginResponse.cs:**
```csharp
namespace GraphQLApp.Core.DTOs;

public record LoginRequest(string Username, string Password);

public record LoginResponse
{
    public string Token { get; init; } = string.Empty;
    public string Username { get; init; } = string.Empty;
    public DateTime ExpiresAt { get; init; }
}
```

### 7.2 GraphQL Authorization

Hot Chocolate beépített `[Authorize]` attribútumával:

```csharp
[Authorize] // JWT token kötelező
public async Task<List<CikkType>> GetCikkek([Service] ICikkRepository repo)
{
    return await repo.GetAllAsync();
}

[Authorize(Roles = new[] { "Admin" })] // Csak Admin szerepkör
public async Task<bool> DeleteCikk(int id, [Service] ICikkRepository repo)
{
    return await repo.DeleteAsync(id);
}
```

---

## 8. Konfiguráció kezelés

### 8.1 Környezeti konfiguráció

**appsettings.json** (alap):
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "HotChocolate": "Information"
    }
  },
  "AllowedHosts": "*",
  "JwtSettings": {
    "Issuer": "GraphQLApp.API",
    "Audience": "GraphQLApp.Clients",
    "ExpirationMinutes": 60
  }
}
```

**appsettings.Development.json:**
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "HotChocolate.Execution": "Debug"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=DevDatabase;User ID=dev_user;Password=DevPass123;TrustServerCertificate=True;"
  },
  "JwtSettings": {
    "SecretKey": "dev-secret-key-at-least-32-characters-long-1234567890"
  }
}
```

**appsettings.Production.json:**
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning",
      "HotChocolate.Execution": "Error"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=ProdDatabase;User ID=prod_user;Password=USE_SECURE_SECRET_HERE;TrustServerCertificate=True;"
  }
}
```

**appsettings.Local.json (git-ignored):**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=MyLocalDB;User ID=local_user;Password=MyRealPassword;TrustServerCertificate=True;"
  },
  "JwtSettings": {
    "SecretKey": "my-actual-super-secret-key-min-32-chars-1234567890abcdef"
  }
}
```

### 8.2 Konfiguráció betöltés

```csharp
var builder = WebApplication.CreateBuilder(args);

// Helyi konfiguráció betöltése (ha létezik)
builder.Configuration.AddJsonFile("appsettings.Local.json", optional: true, reloadOnChange: true);

// Környezeti változókból is olvashat
builder.Configuration.AddEnvironmentVariables();
```

### 8.3 .gitignore

```
bin/
obj/
.vs/
.vscode/
*.user
*.suo
appsettings.Local.json
appsettings.*.Local.json
logs/
```

---

## 9. Naplózás és hibakezelés

### 9.1 Serilog konfiguráció

**appsettings.json:**
```json
{
  "Serilog": {
    "Using": ["Serilog.Sinks.Console", "Serilog.Sinks.File"],
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "System": "Warning"
      }
    },
    "WriteTo": [
      {
        "Name": "Console",
        "Args": {
          "outputTemplate": "[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}"
        }
      },
      {
        "Name": "File",
        "Args": {
          "path": "logs/graphqlapp-.txt",
          "rollingInterval": "Day",
          "outputTemplate": "[{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} {Level:u3}] {Message:lj}{NewLine}{Exception}"
        }
      }
    ],
    "Enrich": ["FromLogContext", "WithMachineName", "WithThreadId"]
  }
}
```

### 9.2 Request Logging Middleware

**RequestLoggingMiddleware.cs:**
```csharp
namespace GraphQLApp.API.Middleware;

public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestLoggingMiddleware> _logger;

    public RequestLoggingMiddleware(RequestDelegate next, ILogger<RequestLoggingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var requestId = Guid.NewGuid().ToString();
        context.Items["RequestId"] = requestId;

        _logger.LogInformation(
            "GraphQL Request started: {RequestId} | Path: {Path} | Method: {Method}",
            requestId, context.Request.Path, context.Request.Method);

        var sw = System.Diagnostics.Stopwatch.StartNew();

        await _next(context);

        sw.Stop();

        _logger.LogInformation(
            "GraphQL Request completed: {RequestId} | Status: {StatusCode} | Duration: {Duration}ms",
            requestId, context.Response.StatusCode, sw.ElapsedMilliseconds);
    }
}

// Regisztráció Program.cs-ben:
// app.UseMiddleware<RequestLoggingMiddleware>();
```

### 9.3 Error Handling

**ErrorFilter.cs** (már korábban bemutattuk):
```csharp
public class ErrorFilter : IErrorFilter
{
    private readonly ILogger<ErrorFilter> _logger;

    public ErrorFilter(ILogger<ErrorFilter> logger)
    {
        _logger = logger;
    }

    public IError OnError(IError error)
    {
        if (error.Exception != null)
        {
            _logger.LogError(
                error.Exception,
                "GraphQL Error: {Message} | Code: {Code}",
                error.Message,
                error.Code);
        }
        else
        {
            _logger.LogWarning(
                "GraphQL Validation Error: {Message}",
                error.Message);
        }

        // Production-ben ne adjunk ki részletes exception információt
        if (error.Exception != null &&
            !Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")?.Equals("Development") == true)
        {
            return error.WithMessage("Belső szerverhiba történt.");
        }

        return error;
    }
}
```

---

## 10. Webes felület

### 10.1 Technológia választás

**Opció A: Vue.js 3 (ajánlott egyszerűség miatt)**

**Opció B: Blazor WebAssembly (ajánlott .NET ökoszisztémában)**

### 10.2 Vue.js implementáció

#### **10.2.1 Projekt felépítés**

```bash
npm create vite@latest graphql-web -- --template vue
cd graphql-web
npm install
npm install @urql/vue graphql
npm install vue-router pinia
```

#### **10.2.2 GraphQL Client beállítás**

**src/services/graphqlClient.js:**
```javascript
import { createClient, fetchExchange } from '@urql/vue';

const token = localStorage.getItem('jwt_token');

export const client = createClient({
  url: 'https://your-api-domain.com/graphql',
  fetchOptions: () => {
    const token = localStorage.getItem('jwt_token');
    return {
      headers: {
        Authorization: token ? `Bearer ${token}` : '',
      },
    };
  },
  exchanges: [fetchExchange],
});
```

#### **10.2.3 Login komponens**

**src/views/LoginView.vue:**
```vue
<template>
  <div class="login-container">
    <h2>Bejelentkezés</h2>
    <form @submit.prevent="handleLogin">
      <div class="form-group">
        <label>Felhasználónév:</label>
        <input v-model="username" type="text" required />
      </div>
      <div class="form-group">
        <label>Jelszó:</label>
        <input v-model="password" type="password" required />
      </div>
      <button type="submit">Belépés</button>
      <p v-if="error" class="error">{{ error }}</p>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();
const username = ref('');
const password = ref('');
const error = ref('');

const handleLogin = async () => {
  try {
    const response = await fetch('https://your-api-domain.com/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        username: username.value,
        password: password.value,
      }),
    });

    if (!response.ok) {
      throw new Error('Hibás felhasználónév vagy jelszó');
    }

    const data = await response.json();
    localStorage.setItem('jwt_token', data.token);
    router.push('/dashboard');
  } catch (err) {
    error.value = err.message;
  }
};
</script>

<style scoped>
.login-container {
  max-width: 400px;
  margin: 100px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
}
.form-group {
  margin-bottom: 15px;
}
.error {
  color: red;
}
</style>
```

#### **10.2.4 Query Builder komponens**

**src/views/QueryBuilderView.vue:**
```vue
<template>
  <div class="query-builder">
    <h2>GraphQL Lekérdezés Építő</h2>

    <div class="builder-section">
      <h3>Válassz táblát:</h3>
      <select v-model="selectedTable" @change="loadFields">
        <option value="">-- Válassz --</option>
        <option value="cikkek">Cikkek</option>
        <option value="gyartok">Gyártók</option>
        <option value="partnerek">Partnerek</option>
      </select>
    </div>

    <div v-if="fields.length" class="builder-section">
      <h3>Válaszd ki a mezőket:</h3>
      <div v-for="field in fields" :key="field">
        <label>
          <input type="checkbox" :value="field" v-model="selectedFields" />
          {{ field }}
        </label>
      </div>
    </div>

    <div v-if="selectedFields.length" class="builder-section">
      <h3>Generált lekérdezés:</h3>
      <pre>{{ generatedQuery }}</pre>
      <button @click="executeQuery">Lekérdezés futtatása</button>
    </div>

    <div v-if="queryResult" class="result-section">
      <h3>Eredmény:</h3>
      <pre>{{ JSON.stringify(queryResult, null, 2) }}</pre>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useQuery } from '@urql/vue';

const selectedTable = ref('');
const selectedFields = ref([]);
const fields = ref([]);
const queryResult = ref(null);

const fieldMap = {
  cikkek: ['id', 'cikkKod', 'megnevezes', 'leiras', 'egysegAr', 'mennyisegiEgyseg'],
  gyartok: ['id', 'gyartoNev', 'orszag', 'contactEmail', 'contactPhone'],
  partnerek: ['id', 'partnerNev', 'adoSzam', 'cim', 'email', 'phone'],
};

const loadFields = () => {
  fields.value = fieldMap[selectedTable.value] || [];
  selectedFields.value = [];
};

const generatedQuery = computed(() => {
  if (!selectedTable.value || !selectedFields.value.length) return '';
  const fieldsStr = selectedFields.value.join('\n    ');
  return `query {
  ${selectedTable.value} {
    ${fieldsStr}
  }
}`;
});

const executeQuery = async () => {
  const { data } = await useQuery({
    query: generatedQuery.value,
  });
  queryResult.value = data.value;
};
</script>

<style scoped>
.query-builder {
  padding: 20px;
}
.builder-section, .result-section {
  margin: 20px 0;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
}
pre {
  background: #f4f4f4;
  padding: 10px;
  border-radius: 4px;
}
</style>
```

### 10.3 Blazor WebAssembly alternatíva

**Services/GraphQLService.cs:**
```csharp
using System.Net.Http.Headers;
using System.Net.Http.Json;

public class GraphQLService
{
    private readonly HttpClient _httpClient;

    public GraphQLService(HttpClient httpClient)
    {
        _httpClient = httpClient;
        _httpClient.BaseAddress = new Uri("https://your-api-domain.com/graphql");
    }

    public void SetAuthToken(string token)
    {
        _httpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", token);
    }

    public async Task<T?> QueryAsync<T>(string query)
    {
        var request = new { query };
        var response = await _httpClient.PostAsJsonAsync("", request);
        response.EnsureSuccessStatusCode();
        var result = await response.Content.ReadFromJsonAsync<GraphQLResponse<T>>();
        return result?.Data;
    }
}

public class GraphQLResponse<T>
{
    public T? Data { get; set; }
    public List<GraphQLError>? Errors { get; set; }
}

public class GraphQLError
{
    public string? Message { get; set; }
}
```

---

## 11. Telepítés és futtatás

### 11.1 Fejlesztői környezet beállítása

#### **11.1.1 Előfeltételek**

- .NET 8 SDK telepítve
- Visual Studio Code + C# Extension
- SQL Server elérhető (10.10.10.69)
- Node.js (ha Vue.js frontend)

#### **11.1.2 Projekt klónozás és build**

```bash
# Repository klónozás
git clone https://github.com/your-org/graphql-app.git
cd graphql-app

# Helyi konfiguráció létrehozása
cp appsettings.json appsettings.Local.json
# Szerkeszd az appsettings.Local.json-t a valós adatokkal!

# Restore és build
dotnet restore
dotnet build

# Futtatás development módban
cd src/GraphQLApp.API
dotnet run --environment Development
```

### 11.2 Adatbázis inicializálás

**SQL script (scripts/init-database.sql):**
```sql
-- Adatbázis létrehozása
CREATE DATABASE AppDatabase;
GO

USE AppDatabase;
GO

-- Táblák létrehozása (lásd 5.1 fejezet)
-- ...

-- Kezdeti admin user létrehozása
INSERT INTO users (Username, PasswordHash, Email, FullName, IsActive)
VALUES ('admin', '$2a$11$hashed_password_here', 'admin@example.com', 'Admin User', 1);
GO

-- Példaadatok
INSERT INTO gyartok (GyartoNev, Orszag) VALUES ('Bosch', 'Németország');
INSERT INTO gyartok (GyartoNev, Orszag) VALUES ('Samsung', 'Dél-Korea');
GO

INSERT INTO cikkek (CikkKod, Megnevezes, EgysegAr, GyartoId)
VALUES ('BOSCH-001', 'Fúrógép', 25000, 1);
GO
```

Futtatás:
```bash
sqlcmd -S 10.10.10.69 -U sa -P YourPassword -i scripts/init-database.sql
```

### 11.3 IIS-re telepítés (Windows Server)

#### **11.3.1 Publikálás**

```bash
# Production build
dotnet publish src/GraphQLApp.API/GraphQLApp.API.csproj -c Release -o ./publish
```

#### **11.3.2 IIS konfiguráció**

1. **IIS Manager megnyitása**
2. **Új Application Pool létrehozása:**
   - Név: `GraphQLAppPool`
   - .NET CLR Version: `No Managed Code`
   - Identity: `ApplicationPoolIdentity` vagy dedikált service account

3. **Új Site létrehozása:**
   - Site name: `GraphQLApp`
   - Physical path: `C:\inetpub\graphqlapp\`
   - Binding: HTTPS, port 443
   - Application Pool: `GraphQLAppPool`

4. **Fájlok másolása:**
   ```powershell
   Copy-Item -Path .\publish\* -Destination C:\inetpub\graphqlapp\ -Recurse
   ```

5. **appsettings.Production.json beállítása:**
   - Valós connection string
   - Éles JWT secret key

6. **Környezeti változó beállítása:**
   - IIS Manager → Site → Configuration Editor
   - `system.webServer/aspNetCore` section
   - `environmentVariables` → Add: `ASPNETCORE_ENVIRONMENT = Production`

7. **SSL tanúsítvány telepítése**

8. **Restart:**
   ```powershell
   Restart-WebAppPool -Name GraphQLAppPool
   ```

#### **11.3.3 Windows Service (alternatíva IIS helyett)**

**Program.cs módosítás:**
```csharp
builder.Services.AddWindowsService();
```

**Publikálás:**
```bash
dotnet publish -c Release -r win-x64 --self-contained
```

**Service telepítés:**
```powershell
sc.exe create "GraphQLAppService" binPath="C:\Services\GraphQLApp\GraphQLApp.API.exe"
sc.exe start "GraphQLAppService"
```

### 11.4 Ellenőrzés

1. **Böngészőben:** `https://your-server/graphql`
2. **Banana Cake Pop UI-nak** meg kell jelennie
3. **Login tesztelése:** `/api/auth/login` endpoint
4. **GraphQL query tesztelése** Banana Cake Pop-ban token-nel

---

## 12. Fejlesztési útmutató

### 12.1 Új tábla hozzáadása

#### **1. lépés: Adatbázis**
```sql
CREATE TABLE termekek (
    Id INT PRIMARY KEY IDENTITY(1,1),
    TermekNev NVARCHAR(200) NOT NULL,
    -- ...
);
```

#### **2. lépés: Entity osztály**
```csharp
// GraphQLApp.Core/Entities/Termek.cs
namespace GraphQLApp.Core.Entities;

public class Termek
{
    public int Id { get; set; }
    public string TermekNev { get; set; } = string.Empty;
    // ...
}
```

#### **3. lépés: Repository interface és implementáció**
```csharp
// GraphQLApp.Core/Interfaces/ITermekRepository.cs
public interface ITermekRepository
{
    Task<List<Termek>> GetAllAsync();
    Task<Termek?> GetByIdAsync(int id);
    Task<Termek> CreateAsync(Termek termek);
    Task UpdateAsync(Termek termek);
    Task<bool> DeleteAsync(int id);
}

// GraphQLApp.Infrastructure/Repositories/TermekRepository.cs
public class TermekRepository : ITermekRepository
{
    private readonly DapperContext _context;

    public TermekRepository(DapperContext context)
    {
        _context = context;
    }

    public async Task<List<Termek>> GetAllAsync()
    {
        using var connection = _context.CreateConnection();
        var sql = "SELECT * FROM termekek";
        var result = await connection.QueryAsync<Termek>(sql);
        return result.ToList();
    }

    // ... többi metódus
}
```

#### **4. lépés: GraphQL Type**
```csharp
// GraphQLApp.API/GraphQL/Types/TermekType.cs
public class TermekType
{
    public int Id { get; set; }

    [GraphQLName("termekNev")]
    public string TermekNev { get; set; } = string.Empty;
}
```

#### **5. lépés: Query**
```csharp
// GraphQLApp.API/GraphQL/Queries/TermekQueries.cs
[ExtendObjectType("Query")]
public class TermekQueries
{
    [Authorize]
    public async Task<List<TermekType>> GetTermekek(
        [Service] ITermekRepository termekRepository)
    {
        return await termekRepository.GetAllAsync();
    }
}
```

#### **6. lépés: Mutation**
```csharp
// GraphQLApp.API/GraphQL/Mutations/TermekMutations.cs
[ExtendObjectType("Mutation")]
public class TermekMutations
{
    [Authorize]
    public async Task<TermekType> CreateTermek(
        CreateTermekInput input,
        [Service] ITermekRepository termekRepository)
    {
        // implementation
    }
}
```

#### **7. lépés: Regisztráció Program.cs-ben**
```csharp
builder.Services.AddScoped<ITermekRepository, TermekRepository>();

builder.Services
    .AddGraphQLServer()
    // ...
    .AddTypeExtension<TermekQueries>()
    .AddTypeExtension<TermekMutations>();
```

### 12.2 Új tárolt eljárás hozzáadása

#### **SQL:**
```sql
CREATE PROCEDURE GetTop10Termek
AS
BEGIN
    SELECT TOP 10 * FROM termekek ORDER BY EgysegAr DESC;
END
```

#### **Repository metódus:**
```csharp
public async Task<List<Termek>> GetTop10Async()
{
    using var connection = _context.CreateConnection();
    var result = await connection.QueryAsync<Termek>(
        "GetTop10Termek",
        commandType: CommandType.StoredProcedure);
    return result.ToList();
}
```

#### **GraphQL Query:**
```csharp
[Authorize]
public async Task<List<TermekType>> GetTop10Termek(
    [Service] ITermekRepository termekRepository)
{
    return await termekRepository.GetTop10Async();
}
```

### 12.3 Unit Testing

**GraphQLApp.API.Tests/QueryTests.cs:**
```csharp
using Xunit;
using Moq;
using GraphQLApp.API.GraphQL.Queries;
using GraphQLApp.Core.Interfaces;

public class CikkQueriesTests
{
    [Fact]
    public async Task GetCikkById_ReturnsCorrectCikk()
    {
        // Arrange
        var mockRepo = new Mock<ICikkRepository>();
        mockRepo.Setup(r => r.GetByIdAsync(1))
            .ReturnsAsync(new Cikk { Id = 1, CikkKod = "TEST-001" });

        var queries = new CikkQueries();

        // Act
        var result = await queries.GetCikkById(1, mockRepo.Object);

        // Assert
        Assert.NotNull(result);
        Assert.Equal("TEST-001", result.CikkKod);
    }
}
```

---

## 13. Biztonsági megfontolások

### 13.1 SQL Injection védelem

- **Paraméteres query-k használata** (Dapper automatikusan védett)
- **SOHA ne építsünk SQL stringet** concatenation-nal

**Rossz:**
```csharp
var sql = $"SELECT * FROM users WHERE Username = '{username}'"; // VESZÉLYES!
```

**Helyes:**
```csharp
var sql = "SELECT * FROM users WHERE Username = @Username";
var result = await connection.QueryAsync<User>(sql, new { Username = username });
```

### 13.2 JWT Token biztonság

- **SecretKey minimum 32 karakter**, erős véletlenszerű
- **Token lejárati idő**: ne legyen túl hosszú (60 perc ajánlott)
- **HTTPS only**: Soha ne küldjünk tokent HTTP-n
- **Refresh token** implementálása hosszabb session-höz

### 13.3 Jelszó tárolás

- **BCrypt vagy Argon2** hash használata
- **SOHA ne tároljuk plaintext jelszót**

```csharp
// Regisztráció
var passwordHash = BCrypt.Net.BCrypt.HashPassword(plainPassword);

// Login
var isValid = BCrypt.Net.BCrypt.Verify(plainPassword, storedHash);
```

### 13.4 Rate Limiting

**Program.cs:**
```csharp
using AspNetCoreRateLimit;

builder.Services.AddMemoryCache();
builder.Services.Configure<IpRateLimitOptions>(builder.Configuration.GetSection("IpRateLimiting"));
builder.Services.AddInMemoryRateLimiting();
builder.Services.AddSingleton<IRateLimitConfiguration, RateLimitConfiguration>();

// ...

app.UseIpRateLimiting();
```

**appsettings.json:**
```json
{
  "IpRateLimiting": {
    "EnableEndpointRateLimiting": true,
    "StackBlockedRequests": false,
    "GeneralRules": [
      {
        "Endpoint": "*",
        "Period": "1m",
        "Limit": 60
      }
    ]
  }
}
```

### 13.5 CORS konfiguráció

**Production környezetben csak megbízható origin-eket engedélyezzünk:**

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("ProductionPolicy", policy =>
    {
        policy.WithOrigins("https://your-webshop.com", "https://admin.your-domain.com")
              .AllowAnyMethod()
              .AllowAnyHeader()
              .AllowCredentials();
    });
});

// ...

app.UseCors("ProductionPolicy");
```

### 13.6 GraphQL Query Complexity

**Védelem túl bonyolult query-k ellen:**

```csharp
builder.Services
    .AddGraphQLServer()
    // ...
    .AddMaxExecutionDepthRule(10)
    .ModifyRequestOptions(opt =>
    {
        opt.ExecutionTimeout = TimeSpan.FromSeconds(30);
    });
```

---

## 14. Jövőbeli bővítési lehetőségek

### 14.1 Subscription-ök (valós idejű adatok)

```csharp
[Subscribe]
public IAsyncEnumerable<CikkType> OnCikkCreated(
    [Service] ITopicEventReceiver eventReceiver)
{
    return eventReceiver.SubscribeAsync<CikkType>("CikkCreated");
}
```

### 14.2 File Upload támogatás

```csharp
public async Task<string> UploadFile(IFile file)
{
    using var stream = file.OpenReadStream();
    var fileName = $"{Guid.NewGuid()}_{file.Name}";
    var path = Path.Combine("uploads", fileName);

    using var fileStream = File.Create(path);
    await stream.CopyToAsync(fileStream);

    return fileName;
}
```

### 14.3 Caching (Redis)

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
});

// Query-ben
[UseDbContext(typeof(AppDbContext))]
[UsePaging]
[UseProjection]
[UseFiltering]
[UseSorting]
[Cache(Duration = 300)] // 5 perc cache
public IQueryable<CikkType> GetCikkek([ScopedService] AppDbContext context)
{
    return context.Cikkek.AsQueryable();
}
```

### 14.4 Elastic Search integráció

- Full-text keresés támogatása cikkekre
- Gyors szűrés nagy adathalmazon

### 14.5 GraphQL Federation

- Több mikroszerviz összefogása egy GraphQL gateway-en keresztül

### 14.6 Audit Log

```csharp
public class AuditLogEntry
{
    public int Id { get; set; }
    public string UserId { get; set; }
    public string Action { get; set; }
    public string EntityType { get; set; }
    public int EntityId { get; set; }
    public DateTime Timestamp { get; set; }
}
```

### 14.7 Multi-tenancy

- Több webáruház támogatása ugyanazon API-n keresztül
- Tenant azonosítás JWT claim-ben

### 14.8 Export funkciók

- Excel export
- PDF generálás
- CSV export

---

## 15. Összefoglalás és Quick Start

### 15.1 5 perces gyors start

```bash
# 1. Projekt klónozás
git clone https://github.com/your-org/graphql-app.git
cd graphql-app

# 2. Konfiguráció
cp appsettings.json appsettings.Local.json
# Szerkeszd az appsettings.Local.json-t!

# 3. Adatbázis inicializálás
sqlcmd -S 10.10.10.69 -U sa -P password -i scripts/init-database.sql

# 4. Futtatás
cd src/GraphQLApp.API
dotnet run
```

**Böngészőben:** `https://localhost:5001/graphql`

### 15.2 Kulcsfontosságú fájlok

| Fájl | Funkció |
|------|---------|
| `Program.cs` | Alkalmazás belépési pont, service regisztráció |
| `appsettings.Local.json` | Helyi konfiguráció (git-ignore-olva) |
| `GraphQL/Queries/` | GraphQL lekérdezések |
| `GraphQL/Mutations/` | GraphQL módosítások |
| `Infrastructure/Repositories/` | Adatbázis hozzáférés |
| `Services/AuthService.cs` | JWT autentikáció |

### 15.3 Fontos parancsok

```bash
# Build
dotnet build

# Tesztek futtatása
dotnet test

# Publikálás
dotnet publish -c Release -o ./publish

# Migrációk (ha EF Core-t használunk)
dotnet ef migrations add InitialCreate
dotnet ef database update
```

### 15.4 Támogatás és dokumentáció

- **Hot Chocolate docs**: https://chillicream.com/docs/hotchocolate
- **Dapper**: https://github.com/DapperLib/Dapper
- **Serilog**: https://serilog.net/
- **JWT**: https://jwt.io/

---

## 16. Példa GraphQL lekérdezések

### 16.1 Összes cikk lekérdezése

```graphql
query GetAllCikkek {
  cikkek {
    id
    cikkKod
    megnevezes
    egysegAr
    gyarto {
      gyartoNev
      orszag
    }
  }
}
```

### 16.2 Egy cikk lekérdezése ID alapján

```graphql
query GetCikk($id: Int!) {
  cikkById(id: $id) {
    id
    cikkKod
    megnevezes
    leiras
    egysegAr
    gyarto {
      id
      gyartoNev
      contactEmail
    }
  }
}

# Variables:
# { "id": 1 }
```

### 16.3 Szűrt lekérdezés

```graphql
query GetFilteredCikkek {
  cikkek(
    where: { egysegAr: { gt: 10000 } }
    order: { megnevezes: ASC }
  ) {
    cikkKod
    megnevezes
    egysegAr
  }
}
```

### 16.4 Új cikk létrehozása

```graphql
mutation CreateNewCikk($input: CreateCikkInput!) {
  createCikk(input: $input) {
    id
    cikkKod
    megnevezes
    egysegAr
  }
}

# Variables:
{
  "input": {
    "cikkKod": "NEW-001",
    "megnevezes": "Új termék",
    "leiras": "Részletes leírás",
    "egysegAr": 15000,
    "mennyisegiEgyseg": "db",
    "gyartoId": 1
  }
}
```

### 16.5 Cikk módosítása

```graphql
mutation UpdateExistingCikk($input: UpdateCikkInput!) {
  updateCikk(input: $input) {
    id
    cikkKod
    megnevezes
    egysegAr
    updatedAt
  }
}

# Variables:
{
  "input": {
    "id": 1,
    "egysegAr": 18000
  }
}
```

### 16.6 Cikk törlése

```graphql
mutation DeleteCikk($id: Int!) {
  deleteCikk(id: $id)
}

# Variables:
# { "id": 5 }
```

---

## 17. Hibaelhárítás

### 17.1 Gyakori hibák

#### **"Unable to connect to SQL Server"**

**Megoldás:**
- Ellenőrizd a connection string-et
- Ellenőrizd, hogy a SQL Server elérhető-e: `ping 10.10.10.69`
- Tesztelj SQL autentikációval: `sqlcmd -S 10.10.10.69 -U username -P password`

#### **"JWT token validation failed"**

**Megoldás:**
- Ellenőrizd a `JwtSettings:SecretKey` hosszát (min 32 karakter)
- Ellenőrizd, hogy az Issuer és Audience megegyezik-e
- Token lejárt? Generálj újat

#### **"GraphQL query error: Cannot return null for non-nullable field"**

**Megoldás:**
- Ellenőrizd a GraphQL típus definíciót (nullable vs non-nullable)
- Ellenőrizd az adatbázis adatokat

#### **"Banana Cake Pop not loading"**

**Megoldás:**
- Ellenőrizd, hogy a `/graphql` endpoint elérhető-e
- Dev környezetben az introspection enabled?

### 17.2 Logging és monitoring

**Logfájlok helye:**
```
logs/graphqlapp-20250104.txt
```

**SQL query logging (Dapper):**
```csharp
// TODO: Add MiniProfiler for dev environment
```

---

## 18. Checklist - Éles környezetbe állás előtt

- [ ] Összes jelszó és secret az appsettings.Local.json-ben van
- [ ] appsettings.Local.json git-ignore-olva
- [ ] HTTPS enabled IIS-ben
- [ ] JWT SecretKey legalább 32 karakter, biztonságos
- [ ] SQL Connection használ dedikált service accountot (nem SA!)
- [ ] Rate limiting beállítva
- [ ] CORS policy megfelelően konfigurálva (csak trusted origins)
- [ ] Logging működik és a logok megfelelő helyre írnak
- [ ] Exception details kikapcsolva production-ben
- [ ] Adatbázis backup stratégia kialakítva
- [ ] Firewall szabályok beállítva (csak HTTPS port nyitott)
- [ ] Health check endpoint létezik (`/health`)
- [ ] Load testing elvégezve
- [ ] Dokumentáció frissítve

---

## 19. Kapcsolat és támogatás

**Fejlesztői csapat:**
- Email: dev-team@your-company.com
- Issue tracker: https://github.com/your-org/graphql-app/issues

**Dokumentáció verzió:** 1.0
**Utolsó frissítés:** 2025-01-04
**Készítette:** Claude AI Assistant

---

# Vége

Ez a specifikáció egy teljes körű útmutatót nyújt a GraphQL alapú API rendszer fejlesztéséhez. A dokumentum minden lényeges aspektust lefed az architektúrától a telepítésig, példakódokkal illusztrálva a megvalósítást.

**Sikeres fejlesztést kívánunk! 🚀**
