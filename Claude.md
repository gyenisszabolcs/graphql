# GraphQL Projekt - Claude Kontextus

## Projekt Áttekintés

Ez egy teljes körű GraphQL alapú webalkalmazás fejlesztési projekt, amely modern technológiákat használ a backend és frontend megvalósításához.

## Technológiai Stack

### Backend
- **GraphQL API**: Rugalmas adatlekérdezési réteg
- **Node.js**: Futtatókörnyezet
- **Apollo Server** vagy **Express-GraphQL**: GraphQL szerver implementáció
- **Adatbázis**: PostgreSQL/MySQL/MongoDB (még nem meghatározott)

### Frontend
- **Vue.js 3**: Modern, reaktív frontend framework
- **Apollo Client**: GraphQL kliens a Vue.js számára
- **Composition API**: Modern Vue.js fejlesztési minta

## Projekt Felépítés

### Dokumentáció
- `GraphQL_Fejlesztesi_Specifikacio.md`: Részletes fejlesztési specifikáció és követelmények
- `graphql_prompt.md`: Projekt prompt és kezdeti leírás
- `Claude.md`: Ez a fájl - főbb kontextusok és áttekintés

### Claude Agents
A `.claude/agents/` mappában specializált agent konfigurációk találhatók:
- **backend-api-developer.md**: Backend és API fejlesztés
- **vue-frontend-developer.md**: Vue.js frontend fejlesztés
- **technical-architect.md**: Technikai architektúra tervezés
- **devops-infrastructure-engineer.md**: DevOps és infrastruktúra
- **security-auditor.md**: Biztonsági audit
- **qa-testing-specialist.md**: Tesztelés és minőségbiztosítás
- **ux-designer.md**: UX/UI tervezés
- **product-requirements-architect.md**: Követelmények specifikálása

## Fejlesztési Fázisok

### 1. Tervezési Fázis
- Követelmények definiálása
- Architektúra megtervezése
- Adatmodell kialakítása
- GraphQL schema tervezése

### 2. Backend Fejlesztés
- GraphQL API implementáció
- Adatbázis integráció
- Resolver-ek fejlesztése
- Autentikáció és autorizáció
- API tesztelés

### 3. Frontend Fejlesztés
- Vue.js alkalmazás felépítése
- Apollo Client integráció
- Komponensek fejlesztése
- State management
- UI/UX implementáció

### 4. Tesztelés és Deploy
- Unit tesztek
- Integrációs tesztek
- E2E tesztek
- DevOps pipeline
- Deployment

## Aktuális Állapot

A projekt jelenleg a **2. Fázis (Adatbázis Integráció) előtt** állapotban van:
- ✅ Git repository létrehozva és GitHub-ra feltöltve
- ✅ Alapvető dokumentáció elkészült
- ✅ Claude agents konfigurálva
- ✅ **0. Fázis BEFEJEZVE** (2025-11-04):
  - ✅ Követelmények validálva
  - ✅ UX/UI tervezés és wireframe-ek kész
  - ✅ Technikai architektúra megtervezve
  - ✅ Adatbázis séma dokumentálva (meglévő `dev_graphql` adatbázis)
  - ✅ Fejlesztői környezet specifikálva
  - ✅ Git repository struktúra kész
- ✅ **1. Fázis BEFEJEZVE** (2025-11-05):
  - ✅ SQL Server kapcsolat dokumentálva
  - ✅ .NET 8 fejlesztői környezet konfigurálva
  - ✅ Solution struktúra létrehozva (4 projekt: API, Core, Infrastructure, Services)
  - ✅ NuGet csomagok telepítve (Hot Chocolate, Dapper, Serilog, JWT, BCrypt)
  - ✅ Konfiguráció kezelés beállítva (appsettings.json, .Local.json template)
  - ✅ Serilog logging infrastruktúra működik
  - ✅ Build sikeres (0 error, 0 warning)
- 🚀 **2. Fázis (Adatbázis Integráció)**: KÖVETKEZIK
- ⏳ Backend API fejlesztés: 3-5. fázisban
- ⏳ Frontend fejlesztés: 6. fázisban

## Következő Lépések (2. Fázis - Adatbázis Integráció)

### Fontos adatbázis információk
- **Adatbázis neve**: `dev_graphql` (már létezik!)
- **SQL Server**: `10.10.10.69`
- **Meglévő táblák**: CIKK, GYARTO, PARTNER, USERS
- **Új tábla Phase 2-ben**: AUTH (autentikációhoz)

### 2. Fázis feladatai:
1. **Adatbázis séma reverse engineering**
   - Pontos mező típusok, hosszak lekérdezése
   - SQL: `SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME IN (...)`

2. **AUTH tábla létrehozása**
   - SQL script: `scripts/sql/001_create_auth_table.sql`
   - Seed admin user: `scripts/sql/002_seed_auth_data.sql`
   - Mezők: AuthId, UserCode, Email, PasswordHash, IsActive, LastLogin

3. **Entity modellek létrehozása**
   - `Core/Entities/Cikk.cs` (CIKK tábla)
   - `Core/Entities/Gyarto.cs` (GYARTO tábla)
   - `Core/Entities/Partner.cs` (PARTNER tábla)
   - `Core/Entities/User.cs` (USERS tábla)
   - `Core/Entities/Auth.cs` (AUTH tábla - új)

4. **Teljesítmény indexek elemzése**
   - Meglévő indexek lekérdezése
   - Hiányzó indexek azonosítása (CIKK.GYARTO, AUTH.Email, stb.)

5. **Tárolt eljárások fejlesztése**
   - GetCikkekByGyarto
   - GetStatisztika
   - GetCikkWithDetails

6. **Dapper repository-k implementálása**
   - `Infrastructure/Repositories/CikkRepository.cs`
   - `Infrastructure/Repositories/GyartoRepository.cs`
   - `Infrastructure/Repositories/PartnerRepository.cs`
   - `Infrastructure/Repositories/UserRepository.cs`
   - `Infrastructure/Repositories/AuthRepository.cs`

7. **Adatbázis kapcsolat tesztelése**
   - Unit tesztek minden repository-hoz
   - Integration tesztek CRUD műveletekhez

## Hasznos Parancsok

```bash
# Git műveletek
git status
git add .
git commit -m "commit message"
git push

# .NET Backend
dotnet new sln -n GraphQLApp
dotnet new webapi -n GraphQLApp.API
dotnet new classlib -n GraphQLApp.Core
dotnet new classlib -n GraphQLApp.Infrastructure
dotnet sln add **/*.csproj
dotnet restore
dotnet build
dotnet run --project src/GraphQLApp.API
dotnet test

# NuGet csomagok
dotnet add package HotChocolate.AspNetCore
dotnet add package Dapper
dotnet add package Serilog.AspNetCore
dotnet add package Microsoft.Data.SqlClient

# Vue.js Frontend (6. fázis)
npm install
npm run dev
npm run build

# SQL Server lekérdezések (reverse engineering)
sqlcmd -S 10.10.10.69 -d dev_graphql -Q "SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME IN ('CIKK', 'GYARTO', 'PARTNER', 'USERS')"
```

## Megjegyzések

- A projekt Windows környezetben fejlesztve (d:\Development\graphql)
- GitHub repository: https://github.com/gyenisszabolcs/graphql.git
- A fejlesztés során követjük a best practice-eket és modern design pattern-öket
- **⚠️ FONTOS**: A projekt egy meglévő adatbázist használ!
  - Adatbázis: `dev_graphql` (már létezik)
  - SQL Server: `10.10.10.69`
  - Táblák: CIKK, GYARTO, PARTNER, USERS
  - Credentials: `appsettings.Local.json` (git-ignored)
