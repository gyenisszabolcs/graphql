# GraphQL Projekt - Implementációs Fázisok

**Verzió:** 1.0
**Dátum:** 2025-11-04
**Projekt:** GraphQL API Alkalmazás - Teljes Stack Fejlesztés

---

## Tartalomjegyzék

1. [0. Fázis - Előkészítés és Tervezés](#0-fázis---előkészítés-és-tervezés)
2. [1. Fázis - Infrastruktúra és Architektúra](#1-fázis---infrastruktúra-és-architektúra)
3. [2. Fázis - Adatbázis Fejlesztés](#2-fázis---adatbázis-fejlesztés)
4. [3. Fázis - Backend API Alapok](#3-fázis---backend-api-alapok)
5. [4. Fázis - Autentikáció és Biztonság](#4-fázis---autentikáció-és-biztonság)
6. [5. Fázis - GraphQL API Fejlesztés](#5-fázis---graphql-api-fejlesztés)
7. [6. Fázis - Frontend Fejlesztés](#6-fázis---frontend-fejlesztés)
8. [7. Fázis - Tesztelés](#7-fázis---tesztelés)
9. [8. Fázis - Biztonság és Auditálás](#8-fázis---biztonság-és-auditálás)
10. [9. Fázis - Telepítés és DevOps](#9-fázis---telepítés-és-devops)
11. [10. Fázis - Dokumentáció és Átadás](#10-fázis---dokumentáció-és-átadás)

---

## 0. Fázis - Előkészítés és Tervezés

### Cél
A projekt technikai alapjainak meghatározása, követelmények tisztázása és a fejlesztői környezet előkészítése.

### Feladatok

#### 1. Követelmények véglegesítése
- **Agent:** product-requirements-architect
- **Leírás:** A specifikáció áttekintése, hiányzó követelmények azonosítása, üzleti igények validálása
- **Kimenet:** Validált követelmény lista, prioritizált funkciók

#### 2. UX/UI tervezés és wireframe készítés
- **Agent:** ux-designer
- **Leírás:** A webes admin felület wireframe-jeinek és UX flow-jának megtervezése
- **Kimenet:** Wireframe-ek, user flow diagramok, design mockup-ok

#### 3. Architektúra tervezés
- **Agent:** technical-architect
- **Leírás:** Rendszerarchitektúra véglegesítése, technológiai stack kiválasztása, komponens diagramok készítése
- **Kimenet:** Architektúra dokumentum, komponens diagram, deployment diagram

#### 4. Adatbázis séma tervezés
- **Agent:** technical-architect
- **Leírás:** Adatbázis táblák, kapcsolatok, indexek, tárolt eljárások tervezése
- **Kimenet:** ER diagram, tábla definíciók, index terv

#### 5. Fejlesztői környezet specifikáció
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Szükséges eszközök, SDK verziók, infrastruktúra követelmények meghatározása
- **Kimenet:** Környezet setup guide, eszköz lista

#### 6. Git repository és projekt struktúra létrehozása
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Git repo inicializálása, .gitignore beállítása, branch stratégia meghatározása
- **Kimenet:** Inicializált git repository, branch stratégia dokumentum

#### 7. Projekt timeline és mérföldkövek meghatározása
- **Agent:** Gyenis Szabolcs
- **Leírás:** Fejlesztési ütemterv készítése, mérföldkövek és deadline-ok meghatározása
- **Kimenet:** Projekt timeline, mérföldkő lista

---

## 1. Fázis - Infrastruktúra és Architektúra

### Cél
A fejlesztői környezet előkészítése, alapvető infrastruktúra létrehozása.

### Feladatok

#### 1. SQL Server kapcsolat tesztelése
- **Agent:** devops-infrastructure-engineer
- **Leírás:** SQL Server (10.10.10.69) elérhetőség tesztelése, `dev_graphql` adatbázis elérésének ellenőrzése, meglévő táblák (CIKK, GYARTO, PARTNER, USERS) sémájának dokumentálása
- **Kimenet:** Kapcsolat sikeres, adatbázis struktúra dokumentálva

#### 2. Visual Studio / VS Code fejlesztői környezet beállítása
- **Agent:** backend-api-developer
- **Leírás:** .NET 8 SDK telepítése, VS Code extensions telepítése (C#, Hot Chocolate), workspace beállítása
- **Kimenet:** Konfigurált fejlesztői környezet

#### 3. Solution és projekt struktúra létrehozása
- **Agent:** technical-architect
- **Leírás:** .NET Solution létrehozása a következő projektekkel: GraphQLApp.API, GraphQLApp.Core, GraphQLApp.Infrastructure, GraphQLApp.Services
- **Kimenet:** Projekt struktúra a specifikáció szerint

#### 4. NuGet csomagok hozzáadása
- **Agent:** backend-api-developer
- **Leírás:** Hot Chocolate, Dapper, Serilog, JWT Bearer és egyéb szükséges NuGet csomagok telepítése
- **Kimenet:** Projektek függőségi referenciákkal ellátva

#### 5. Konfiguráció kezelés beállítása
- **Agent:** backend-api-developer
- **Leírás:** appsettings.json létrehozása `dev_graphql` kapcsolódási string-gel (credentials nélkül), appsettings.Local.json template létrehozása (git-ignore-olva), SQL Server credentials tárolása Local.json-ban
- **Kimenet:** Konfig fájlok kész, .gitignore frissítve, credentials biztonságosan tárolva

#### 6. Logging infrastruktúra beállítása (Serilog)
- **Agent:** backend-api-developer
- **Leírás:** Serilog konfiguráció beállítása, console és file sink-ek konfigurálása, log struktura meghatározása
- **Kimenet:** Működő logging rendszer

---

## 2. Fázis - Adatbázis Integráció

### Cél
Meglévő adatbázis integráció, tárolt eljárások létrehozása, teljesítmény optimalizálás.

**⚠️ FONTOS:** Ez a fázis NEM tartalmaz tábla létrehozást, mert a `dev_graphql` adatbázis és táblák (CIKK, GYARTO, PARTNER, USERS) már léteznek!

### Feladatok

#### 1. Adatbázis séma reverse engineering
- **Agent:** backend-api-developer
- **Leírás:** Meglévő táblák (CIKK, GYARTO, PARTNER, USERS) pontos sémájának lekérdezése, mező típusok, hosszak, NULL-abiliy dokumentálása
- **Kimenet:** Részletes adatbázis séma dokumentáció minden mezővel

#### 2. AUTH tábla létrehozása
- **Agent:** backend-api-developer
- **Leírás:** Új AUTH tábla létrehozása az autentikációhoz (AuthId, UserCode, Email, PasswordHash, IsActive, LastLogin, CreatedAt, UpdatedAt). SQL script futtatása a dev_graphql adatbázison. Seed adatok létrehozása (admin user).
- **Kimenet:** AUTH tábla létrehozva, SQL scriptek: `001_create_auth_table.sql`, `002_seed_auth_data.sql`

#### 3. Entity modellek létrehozása
- **Agent:** backend-api-developer
- **Leírás:** C# entity osztályok létrehozása a CIKK, GYARTO, PARTNER, USERS, AUTH tábláknak megfelelően (pontos mező névvel és típussal)
- **Kimenet:** Entity osztályok a GraphQLApp.Core/Entities mappában (Cikk.cs, Gyarto.cs, Partner.cs, User.cs, Auth.cs)

#### 4. Teljesítmény indexek elemzése
- **Agent:** technical-architect
- **Leírás:** Meglévő indexek elemzése, hiányzó indexek azonosítása (CIKK.GYARTO, CIKK.ELOALLITOPID, AUTH.Email stb.), új indexek tervezése
- **Kimenet:** Index optimalizálási javaslat dokumentum

#### 5. Tárolt eljárások fejlesztése
- **Agent:** backend-api-developer
- **Leírás:** GetCikkekByGyarto, GetStatisztika, GetCikkWithDetails és egyéb hasznos tárolt eljárások implementálása a meglévő táblákra
- **Kimenet:** Stored procedure SQL scriptek (`003_stored_procedures.sql`)

#### 6. Adatbázis kapcsolat tesztelése
- **Agent:** backend-api-developer
- **Leírás:** Dapper konfigurálása, kapcsolat tesztelése, egyszerű CRUD műveletek tesztelése minden táblára (CIKK, GYARTO, PARTNER, USERS, AUTH)
- **Kimenet:** Működő adatbázis kapcsolat minden táblához, unit tesztek

#### 7. Adatbázis backup stratégia ellenőrzése
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Meglévő backup stratégia ellenőrzése, javasolt backup ütemezés dokumentálása, AUTH tábla backup-ba való beillesztése
- **Kimenet:** Backup stratégia ellenőrzési riport

---

## 3. Fázis - Backend API Alapok

### Cél
A .NET 8 Web API projekt alapjainak létrehozása, kapcsolódás az adatbázishoz, repository pattern implementálása.

### Feladatok

#### 1. Entity modellek létrehozása
- **Agent:** backend-api-developer
- **Leírás:** User, Cikk, Gyarto, Partner entitás osztályok létrehozása GraphQLApp.Core projektben
- **Kimenet:** Entity osztályok a `Core/Entities/` mappában

#### 2. Repository interfészek definiálása
- **Agent:** technical-architect
- **Leírás:** IUserRepository, ICikkRepository, IGyartoRepository, IPartnerRepository, IStoredProcedureExecutor interfészek létrehozása
- **Kimenet:** Repository interface-ek a `Core/Interfaces/` mappában

#### 3. Dapper Context osztály implementálása
- **Agent:** backend-api-developer
- **Leírás:** DapperContext létrehozása connection string kezeléssel
- **Kimenet:** `Infrastructure/Data/DapperContext.cs`

#### 4. Repository implementációk létrehozása
- **Agent:** backend-api-developer
- **Leírás:** UserRepository, CikkRepository, GyartoRepository, PartnerRepository osztályok implementálása Dapper-rel
- **Kimenet:** Repository osztályok CRUD műveletekkel a `Infrastructure/Repositories/` mappában

#### 5. Stored Procedure Executor implementálása
- **Agent:** backend-api-developer
- **Leírás:** IStoredProcedureExecutor implementáció, amely képes tárolt eljárások hívására
- **Kimenet:** `Infrastructure/Repositories/StoredProcedureExecutor.cs`

#### 6. Dependency Injection konfigurálása
- **Agent:** backend-api-developer
- **Leírás:** Repository-k és service-ek regisztrálása a DI containerben a Program.cs-ben
- **Kimenet:** Konfiguráció Program.cs-ben

#### 7. Exception handling middleware létrehozása
- **Agent:** backend-api-developer
- **Leírás:** Global exception handler middleware implementálása
- **Kimenet:** `Middleware/ExceptionHandlingMiddleware.cs`

---

## 4. Fázis - Autentikáció és Biztonság

### Cél
JWT alapú autentikáció implementálása, jelszó kezelés, authorization policy-k beállítása.

### Feladatok

#### 1. JWT Token Service implementálása
- **Agent:** backend-api-developer
- **Leírás:** IJwtTokenService és JwtTokenService létrehozása token generálással és validációval
- **Kimenet:** `Services/JwtTokenService.cs`

#### 2. JWT konfiguráció beállítása
- **Agent:** security-auditor
- **Leírás:** JwtSettings konfiguráció az appsettings.json-ban, titkos kulcs generálás (min 32 karakter)
- **Kimenet:** JWT konfiguráció minden környezetre

#### 3. Authentication Service implementálása
- **Agent:** backend-api-developer
- **Leírás:** IAuthService és AuthService létrehozása felhasználó hitelesítéssel és BCrypt jelszó ellenőrzéssel
- **Kimenet:** `Services/AuthService.cs`

#### 4. BCrypt jelszó hashelés bevezetése
- **Agent:** security-auditor
- **Leírás:** BCrypt.Net NuGet csomag hozzáadása, jelszó hash és verify metódusok implementálása
- **Kimenet:** Biztonságos jelszó kezelés

#### 5. Authentication middleware konfigurálása
- **Agent:** backend-api-developer
- **Leírás:** JWT Bearer authentication beállítása a Program.cs-ben, token validation paraméterek konfigurálása
- **Kimenet:** Authentication middleware konfiguráció

#### 6. AuthController létrehozása (Login endpoint)
- **Agent:** backend-api-developer
- **Leírás:** POST /api/auth/login endpoint implementálása, amely JWT tokent ad vissza sikeres bejelentkezés esetén
- **Kimenet:** `Controllers/AuthController.cs`

#### 7. LoginRequest és LoginResponse DTO-k létrehozása
- **Agent:** backend-api-developer
- **Leírás:** Login kérés és válasz modell osztályok létrehozása
- **Kimenet:** `Core/DTOs/LoginRequest.cs`, `Core/DTOs/LoginResponse.cs`

#### 8. Authorization policy-k beállítása
- **Agent:** security-auditor
- **Leírás:** Role-based authorization policy-k definiálása (User, Admin szerepkörök)
- **Kimenet:** Authorization policy konfiguráció

---

## 5. Fázis - GraphQL API Fejlesztés

### Cél
Hot Chocolate GraphQL middleware beállítása, Query és Mutation resolver-ek implementálása, Banana Cake Pop UI aktiválása.

### Feladatok

#### 1. Hot Chocolate konfiguráció beállítása
- **Agent:** backend-api-developer
- **Leírás:** GraphQL server hozzáadása a DI containerhez, query és mutation type-ok regisztrálása
- **Kimenet:** GraphQL konfiguráció Program.cs-ben

#### 2. GraphQL Type osztályok létrehozása
- **Agent:** backend-api-developer
- **Leírás:** UserType, CikkType, GyartoType, PartnerType osztályok létrehozása GraphQL Name attribútumokkal
- **Kimenet:** Type osztályok a `GraphQL/Types/` mappában

#### 3. UserQueries implementálása
- **Agent:** backend-api-developer
- **Leírás:** GetUsers, GetUserById, GetUserByUsername query resolver-ek implementálása [Authorize] attribútummal
- **Kimenet:** `GraphQL/Queries/UserQueries.cs`

#### 4. CikkQueries implementálása
- **Agent:** backend-api-developer
- **Leírás:** GetCikkek, GetCikkById, GetCikkByCikkKod, GetCikkekByGyarto query resolver-ek implementálása
- **Kimenet:** `GraphQL/Queries/CikkQueries.cs`

#### 5. GyartoQueries implementálása
- **Agent:** backend-api-developer
- **Leírás:** GetGyartok, GetGyartoById query resolver-ek implementálása
- **Kimenet:** `GraphQL/Queries/GyartoQueries.cs`

#### 6. PartnerQueries implementálása
- **Agent:** backend-api-developer
- **Leírás:** GetPartnerek, GetPartnerById, GetPartnerekByTipus query resolver-ek implementálása
- **Kimenet:** `GraphQL/Queries/PartnerQueries.cs`

#### 7. Input DTO-k létrehozása
- **Agent:** backend-api-developer
- **Leírás:** CreateCikkInput, UpdateCikkInput, CreateGyartoInput, UpdateGyartoInput, CreatePartnerInput, UpdatePartnerInput record osztályok
- **Kimenet:** Input DTO-k a `Core/DTOs/` mappában

#### 8. CikkMutations implementálása
- **Agent:** backend-api-developer
- **Leírás:** CreateCikk, UpdateCikk, DeleteCikk mutation resolver-ek implementálása
- **Kimenet:** `GraphQL/Mutations/CikkMutations.cs`

#### 9. GyartoMutations implementálása
- **Agent:** backend-api-developer
- **Leírás:** CreateGyarto, UpdateGyarto, DeleteGyarto mutation resolver-ek implementálása
- **Kimenet:** `GraphQL/Mutations/GyartoMutations.cs`

#### 10. PartnerMutations implementálása
- **Agent:** backend-api-developer
- **Leírás:** CreatePartner, UpdatePartner, DeletePartner mutation resolver-ek implementálása
- **Kimenet:** `GraphQL/Mutations/PartnerMutations.cs`

#### 11. UserMutations implementálása
- **Agent:** backend-api-developer
- **Leírás:** CreateUser, UpdateUser, DeleteUser mutation resolver-ek implementálása
- **Kimenet:** `GraphQL/Mutations/UserMutations.cs`

#### 12. GraphQL Error Filter implementálása
- **Agent:** backend-api-developer
- **Leírás:** IErrorFilter implementáció hibák naplózására és formázására
- **Kimenet:** `GraphQL/Filters/ErrorFilter.cs`

#### 13. Filtering, Sorting, Paging beállítása
- **Agent:** backend-api-developer
- **Leírás:** Hot Chocolate Filtering, Sorting, Paging middleware-ek aktiválása a query-kben
- **Kimenet:** [UseFiltering], [UseSorting], [UsePaging] attribútumok használata

#### 14. Data Loader implementálása (N+1 probléma)
- **Agent:** technical-architect
- **Leírás:** DataLoader osztály létrehozása a Gyártó adatok batch betöltéséhez
- **Kimenet:** `GraphQL/DataLoaders/GyartoDataLoader.cs`

#### 15. Banana Cake Pop UI aktiválása
- **Agent:** backend-api-developer
- **Leírás:** MapGraphQL() konfiguráció ellenőrzése, Banana Cake Pop elérhető legyen /graphql útvonalon
- **Kimenet:** Működő GraphQL Playground

#### 16. GraphQL schema introspection tesztelése
- **Agent:** backend-api-developer
- **Leírás:** Schema generálás ellenőrzése, introspection query futtatása
- **Kimenet:** Validált GraphQL schema

---

## 6. Fázis - Frontend Fejlesztés

### Cél
Vue.js 3 alapú webes admin felület fejlesztése GraphQL lekérdezés építővel és eredmény megjelenítéssel.

### Feladatok

#### 1. Vue.js projekt inicializálása
- **Agent:** vue-frontend-developer
- **Leírás:** Vite segítségével Vue 3 projekt létrehozása, függőségek telepítése (@urql/vue, graphql, vue-router, pinia)
- **Kimenet:** `GraphQLApp.Web/` projekt struktúra

#### 2. GraphQL Client beállítása
- **Agent:** vue-frontend-developer
- **Leírás:** URQL client konfiguráció JWT token kezeléssel, fetchOptions beállítása
- **Kimenet:** `src/services/graphqlClient.js`

#### 3. Vue Router konfigurálása
- **Agent:** vue-frontend-developer
- **Leírás:** Routing beállítása Login, Dashboard, QueryBuilder nézetek között
- **Kimenet:** `src/router/index.js`

#### 4. Pinia State Management beállítása
- **Agent:** vue-frontend-developer
- **Leírás:** Auth store létrehozása JWT token és user state kezelésére
- **Kimenet:** `src/stores/authStore.js`

#### 5. Login komponens fejlesztése
- **Agent:** vue-frontend-developer
- **Leírás:** Login form implementálása, API hívás /api/auth/login endpoint-ra, token mentése localStorage-ba
- **Kimenet:** `src/views/LoginView.vue`

#### 6. Dashboard komponens fejlesztése
- **Agent:** vue-frontend-developer
- **Leírás:** Fő dashboard nézet navigációval a különböző funkciókhoz
- **Kimenet:** `src/views/DashboardView.vue`

#### 7. Query Builder komponens fejlesztése
- **Agent:** vue-frontend-developer
- **Leírás:** Interaktív query builder: tábla választó, mező választó, dinamikus GraphQL query generálás
- **Kimenet:** `src/views/QueryBuilderView.vue`

#### 8. Eredmény megjelenítő komponens fejlesztése
- **Agent:** vue-frontend-developer
- **Leírás:** GraphQL query eredmények táblázatos és JSON formátumú megjelenítése
- **Kimenet:** `src/components/ResultDisplay.vue`

#### 9. Tábla és mező böngésző komponens fejlesztése
- **Agent:** vue-frontend-developer
- **Leírás:** Elérhető táblák és mezők listázása, introspection alapú séma böngésző
- **Kimenet:** `src/components/SchemaBrowser.vue`

#### 10. Authentication Guard implementálása
- **Agent:** vue-frontend-developer
- **Leírás:** Route guard a védett oldalakhoz, automatikus átirányítás login-ra token hiányában
- **Kimenet:** Navigation guard a router-ben

#### 11. Error handling UI komponensek
- **Agent:** vue-frontend-developer
- **Leírás:** Hibaüzenetek megjelenítése, toast/snackbar komponens létrehozása
- **Kimenet:** `src/components/ErrorToast.vue`

#### 12. UI stílusok és layout kialakítása
- **Agent:** ux-designer
- **Leírás:** CSS/SCSS stílusok, responsive design, színséma és tipográfia beállítása
- **Kimenet:** `src/assets/styles/` mappában stílus fájlok

#### 13. Vue build konfiguráció
- **Agent:** vue-frontend-developer
- **Leírás:** Vite konfiguráció production build-hez, environment változók kezelése
- **Kimenet:** `vite.config.js`, `.env` fájlok

---

## 7. Fázis - Tesztelés

### Cél
Átfogó tesztelés minden rétegen: unit tesztek, integrációs tesztek, API tesztek, frontend tesztek.

### Feladatok

#### 1. Unit teszt projekt létrehozása (Backend)
- **Agent:** qa-testing-specialist
- **Leírás:** xUnit test projekt létrehozása GraphQLApp.API.Tests és GraphQLApp.Infrastructure.Tests néven
- **Kimenet:** Test projekt struktúra

#### 2. Repository unit tesztek írása
- **Agent:** qa-testing-specialist
- **Leírás:** Mock adatbázis használatával repository metódusok unit tesztjei (Moq library)
- **Kimenet:** Repository test osztályok

#### 3. Service unit tesztek írása
- **Agent:** qa-testing-specialist
- **Leírás:** AuthService, JwtTokenService unit tesztjei
- **Kimenet:** Service test osztályok

#### 4. GraphQL Query resolver tesztek
- **Agent:** qa-testing-specialist
- **Leírás:** Query resolver metódusok unit tesztjei mock repository-kkal
- **Kimenet:** Query test osztályok

#### 5. GraphQL Mutation resolver tesztek
- **Agent:** qa-testing-specialist
- **Leírás:** Mutation resolver metódusok unit tesztjei, adatvalidáció tesztelése
- **Kimenet:** Mutation test osztályok

#### 6. Integrációs tesztek (API)
- **Agent:** qa-testing-specialist
- **Leírás:** WebApplicationFactory használatával teljes API endpoint tesztek valós GraphQL kérésekkel
- **Kimenet:** Integration test osztályok

#### 7. JWT autentikáció tesztek
- **Agent:** qa-testing-specialist
- **Leírás:** Token generálás, validáció, lejárat tesztelése, sikertelen login tesztek
- **Kimenet:** Auth test osztályok

#### 8. GraphQL schema validation tesztek
- **Agent:** qa-testing-specialist
- **Leírás:** Schema introspection tesztek, type ellenőrzések
- **Kimenet:** Schema validation tesztek

#### 9. Adatbázis integrációs tesztek
- **Agent:** qa-testing-specialist
- **Leírás:** Valós SQL Server adatbázissal CRUD műveletek tesztelése
- **Kimenet:** Database integration tesztek

#### 10. Frontend unit tesztek (Vue)
- **Agent:** qa-testing-specialist
- **Leírás:** Vitest használatával Vue komponensek unit tesztjei
- **Kimenet:** Frontend unit test fájlok

#### 11. Frontend E2E tesztek
- **Agent:** qa-testing-specialist
- **Leírás:** Cypress vagy Playwright használatával teljes user flow tesztelés (login → query build → eredmény megjelenítés)
- **Kimenet:** E2E test suite

#### 12. Performance tesztek
- **Agent:** qa-testing-specialist
- **Leírás:** Load testing GraphQL endpoint-okra (pl. k6 vagy Artillery használatával)
- **Kimenet:** Performance teszt scriptek, eredmény jelentések

#### 13. API dokumentáció tesztelése
- **Agent:** qa-testing-specialist
- **Leírás:** Banana Cake Pop-ban minden dokumentált query és mutation futtatása, példák validálása
- **Kimenet:** API teszt jegyzőkönyv

#### 14. Teszt lefedettség ellenőrzése
- **Agent:** qa-testing-specialist
- **Leírás:** Code coverage riport generálása, minimum 70% lefedettség biztosítása
- **Kimenet:** Coverage riport

---

## 8. Fázis - Biztonság és Auditálás

### Cél
Biztonsági audit, sebezhetőségek azonosítása és javítása, biztonsági best practice-ek implementálása.

### Feladatok

#### 1. SQL Injection védelem auditálása
- **Agent:** security-auditor
- **Leírás:** Minden adatbázis hívás ellenőrzése, paraméteres query-k validálása
- **Kimenet:** SQL injection audit riport

#### 2. JWT token biztonság auditálása
- **Agent:** security-auditor
- **Leírás:** SecretKey erősség ellenőrzése, token lejárati idők validálása, HTTPS enforcement
- **Kimenet:** JWT security audit riport

#### 3. Jelszó tárolás auditálása
- **Agent:** security-auditor
- **Leírás:** BCrypt konfiguráció ellenőrzése, jelszó erősség validáció implementálása
- **Kimenet:** Password security audit riport

#### 4. HTTPS konfiguráció ellenőrzése
- **Agent:** security-auditor
- **Leírás:** SSL/TLS beállítások ellenőrzése, HTTPS redirect implementálása
- **Kimenet:** HTTPS konfiguráció validáció

#### 5. CORS policy auditálása
- **Agent:** security-auditor
- **Leírás:** CORS beállítások ellenőrzése, csak megbízható origin-ek engedélyezése
- **Kimenet:** CORS security riport

#### 6. Rate Limiting implementálása
- **Agent:** backend-api-developer
- **Leírás:** AspNetCoreRateLimit middleware hozzáadása, endpoint-onkénti limit beállítása
- **Kimenet:** Rate limiting konfiguráció

#### 7. GraphQL Query Complexity limitálása
- **Agent:** backend-api-developer
- **Leírás:** MaxExecutionDepthRule beállítása, timeout konfiguráció, túl bonyolult query-k elleni védelem
- **Kimenet:** Query complexity védelem

#### 8. Input validáció auditálása
- **Agent:** security-auditor
- **Leírás:** Minden mutation input validációjának ellenőrzése, FluentValidation bevezetése
- **Kimenet:** Input validation audit riport

#### 9. Sensitive data exposure ellenőrzése
- **Agent:** security-auditor
- **Leírás:** Log fájlok, error response-ok ellenőrzése, hogy ne tartalmazzanak érzékeny adatokat
- **Kimenet:** Data exposure audit riport

#### 10. Dependency vulnerability scanning
- **Agent:** security-auditor
- **Leírás:** NuGet és npm csomagok biztonsági sebezhetőségeinek ellenőrzése (dotnet list package --vulnerable)
- **Kimenet:** Dependency vulnerability riport

#### 11. Penetration testing
- **Agent:** security-auditor
- **Leírás:** Automatizált biztonsági tesztek futtatása (OWASP ZAP vagy Burp Suite)
- **Kimenet:** Penetration test riport

#### 12. Biztonsági checklist validálása
- **Agent:** security-auditor
- **Leírás:** A specifikációban szereplő biztonsági checklist minden pontjának ellenőrzése
- **Kimenet:** Biztonsági checklist kitöltve

---

## 9. Fázis - Telepítés és DevOps

### Cél
Production környezet beállítása, IIS deployment, CI/CD pipeline kialakítása, monitoring beállítása.

### Feladatok

#### 1. IIS szerver előkészítése
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Windows Server IIS telepítése, ASP.NET Core Hosting Bundle telepítése
- **Kimenet:** IIS ready a .NET 8 alkalmazáshoz

#### 2. Application Pool konfigurálása
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Dedikált Application Pool létrehozása GraphQLAppPool néven, .NET CLR: No Managed Code
- **Kimenet:** Konfigurált Application Pool

#### 3. IIS Site létrehozása
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Új IIS Site létrehozása HTTPS binding-gel, SSL tanúsítvány telepítése
- **Kimenet:** IIS Site HTTPS endpoint-tal

#### 4. Production appsettings konfiguráció
- **Agent:** devops-infrastructure-engineer
- **Leírás:** appsettings.Production.json konfiguráció éles adatbázis connection string-gel, JWT secret-tel
- **Kimenet:** Production konfiguráció (biztonságosan tárolva)

#### 5. Publish script készítése
- **Agent:** devops-infrastructure-engineer
- **Leírás:** PowerShell script dotnet publish paranccsal, build és deployment automatizáláshoz
- **Kimenet:** `scripts/deploy-prod.ps1`

#### 6. Backend deployment IIS-re
- **Agent:** devops-infrastructure-engineer
- **Leírás:** .NET API publish és fájlok másolása IIS site könyvtárába
- **Kimenet:** Deployed backend IIS-en

#### 7. Frontend build és deployment
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Vue.js production build, statikus fájlok deployment IIS-re vagy CDN-re
- **Kimenet:** Deployed frontend

#### 8. Környezeti változók beállítása IIS-ben
- **Agent:** devops-infrastructure-engineer
- **Leírás:** ASPNETCORE_ENVIRONMENT=Production beállítása IIS Configuration Editor-ban
- **Kimenet:** Környezeti változók konfigurálva

#### 9. Windows Firewall szabályok beállítása
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Csak HTTPS (443) port megnyitása, egyéb portok zárása
- **Kimenet:** Firewall szabályok

#### 10. Health check endpoint implementálása
- **Agent:** backend-api-developer
- **Leírás:** /health endpoint létrehozása monitoringhoz
- **Kimenet:** Health check endpoint

#### 11. Application Insights / Monitoring beállítása
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Telemetria és monitoring konfiguráció (opcionálisan Azure Application Insights vagy más monitoring tool)
- **Kimenet:** Monitoring dashboard

#### 12. Log rotation és archiválás beállítása
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Serilog rolling file konfiguráció, régi log-ok archiválása vagy törlése
- **Kimenet:** Log management konfiguráció

#### 13. Backup automatizálás (Database + App)
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Ütemezett SQL Server backup-ok, alkalmazás konfiguráció backup
- **Kimenet:** Backup job-ok, recovery dokumentáció

#### 14. CI/CD pipeline beállítása (opcionális)
- **Agent:** devops-infrastructure-engineer
- **Leírás:** GitHub Actions vagy Azure DevOps pipeline automatikus build és deploy-hoz
- **Kimenet:** CI/CD pipeline YAML konfiguráció

#### 15. Smoke test production környezetben
- **Agent:** qa-testing-specialist
- **Leírás:** Alapvető funkciók tesztelése production-ben: login, GraphQL query, mutation
- **Kimenet:** Smoke test riport

#### 16. Performance monitoring beállítása
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Response time, memory usage, CPU monitoring beállítása
- **Kimenet:** Performance monitoring dashboard

---

## 10. Fázis - Dokumentáció és Átadás

### Cél
Teljes körű dokumentáció elkészítése, tudásátadás, felhasználói és fejlesztői útmutatók.

### Feladatok

#### 1. API dokumentáció finalizálása
- **Agent:** backend-api-developer
- **Leírás:** GraphQL schema dokumentálás, minden query és mutation példákkal, Banana Cake Pop query collection
- **Kimenet:** `docs/API_Documentation.md`

#### 2. Felhasználói kézikönyv készítése
- **Agent:** ux-designer
- **Leírás:** Webes felület használati útmutató képernyőképekkel, lépésről lépésre guide
- **Kimenet:** `docs/User_Guide.md`

#### 3. Fejlesztői útmutató finalizálása
- **Agent:** technical-architect
- **Leírás:** Új funkció hozzáadása, tábla hozzáadása, tárolt eljárás hozzáadása útmutatók
- **Kimenet:** `docs/Developer_Guide.md`

#### 4. Deployment útmutató
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Lépésről lépésre IIS deployment, environment setup, troubleshooting guide
- **Kimenet:** `docs/Deployment_Guide.md`

#### 5. Biztonsági dokumentáció
- **Agent:** security-auditor
- **Leírás:** Biztonsági best practice-ek, credential management, audit trail
- **Kimenet:** `docs/Security_Documentation.md`

#### 6. Architecture Decision Records (ADR)
- **Agent:** technical-architect
- **Leírás:** Technológiai döntések dokumentálása, architektúra választások indoklása
- **Kimenet:** `docs/adr/` mappában ADR fájlok

#### 7. GraphQL példa lekérdezések gyűjteménye
- **Agent:** backend-api-developer
- **Leírás:** Gyakori use-case-ek GraphQL query és mutation példákkal
- **Kimenet:** `docs/GraphQL_Examples.md`

#### 8. Troubleshooting útmutató
- **Agent:** devops-infrastructure-engineer
- **Leírás:** Gyakori hibák és megoldásaik, log elemzés, debug tippek
- **Kimenet:** `docs/Troubleshooting_Guide.md`

#### 9. README.md frissítése
- **Agent:** Gyenis Szabolcs
- **Leírás:** Projekt overview, quick start guide, link-ek a további dokumentációkhoz
- **Kimenet:** `README.md`

#### 10. Changelog készítése
- **Agent:** Gyenis Szabolcs
- **Leírás:** Verzió történet, release notes dokumentálása
- **Kimenet:** `CHANGELOG.md`

#### 11. Tudásátadó session tartása
- **Agent:** technical-architect
- **Leírás:** Prezentáció és demo a teljes rendszerről, Q&A session
- **Kimenet:** Prezentáció slides, session notes

#### 12. Kód review és minőségellenőrzés
- **Agent:** technical-architect
- **Leírás:** Teljes kódbázis review, code quality metrics ellenőrzése, refactoring javaslatok
- **Kimenet:** Code review riport

#### 13. Final acceptance testing
- **Agent:** Gyenis Szabolcs
- **Leírás:** Teljes rendszer átvételi tesztelése az üzleti követelmények alapján
- **Kimenet:** Acceptance test sign-off

#### 14. Projekt záró dokumentáció
- **Agent:** Gyenis Szabolcs
- **Leírás:** Project summary, lessons learned, jövőbeli fejlesztési javaslatok
- **Kimenet:** `docs/Project_Summary.md`

---

## Függelék

### Agent szerepkörök összefoglalása

- **backend-api-developer**: .NET backend fejlesztés, GraphQL API, repository-k, service-ek
- **vue-frontend-developer**: Vue.js frontend fejlesztés, komponensek, state management
- **technical-architect**: Rendszerarchitektúra, design döntések, high-level tervezés
- **devops-infrastructure-engineer**: Infrastruktúra, deployment, CI/CD, monitoring
- **security-auditor**: Biztonsági audit, sebezhetőség elemzés, security best practices
- **qa-testing-specialist**: Tesztelés minden szinten, teszt automatizálás, quality assurance
- **ux-designer**: UI/UX design, wireframe-ek, felhasználói felület tervezés
- **product-requirements-architect**: Követelmények tisztázása, user story-k, acceptance criteria
- **Gyenis Szabolcs**: Projekt menedzsment, döntéshozatal, final review, acceptance

### Becsült időigények fázisonként

| Fázis | Becsült időigény | Kritikus útvonal |
|-------|------------------|------------------|
| 0. Előkészítés és Tervezés | 1 hét | ✓ |
| 1. Infrastruktúra és Architektúra | 3-4 nap | ✓ |
| 2. Adatbázis Fejlesztés | 2-3 nap | ✓ |
| 3. Backend API Alapok | 1 hét | ✓ |
| 4. Autentikáció és Biztonság | 3-4 nap | ✓ |
| 5. GraphQL API Fejlesztés | 1.5 hét | ✓ |
| 6. Frontend Fejlesztés | 1.5 hét | ✓ |
| 7. Tesztelés | 1 hét | - |
| 8. Biztonság és Auditálás | 3-4 nap | - |
| 9. Telepítés és DevOps | 4-5 nap | ✓ |
| 10. Dokumentáció és Átadás | 3-4 nap | - |
| **Összesen** | **~8-10 hét** | |

### Mérföldkövek

1. **M1 - Architektúra elfogadva** (0. fázis vége)
2. **M2 - Backend API működik lokálisan** (3. fázis vége)
3. **M3 - GraphQL API teljes** (5. fázis vége)
4. **M4 - Frontend teljes** (6. fázis vége)
5. **M5 - Tesztek 70%+ lefedettséggel** (7. fázis vége)
6. **M6 - Production deployment** (9. fázis vége)
7. **M7 - Projekt lezárva és átadva** (10. fázis vége)

### Függőségek

- 1. fázis előfeltétele: 0. fázis befejezése
- 2. fázis előfeltétele: 1. fázis SQL Server készen áll
- 3. fázis előfeltétele: 2. fázis adatbázis ready
- 4. fázis párhuzamos a 3. fázissal (részben)
- 5. fázis előfeltétele: 3. és 4. fázis befejezése
- 6. fázis párhuzamos az 5. fázissal (backend API endpoint-ok készek után)
- 7. fázis előfeltétele: 5. és 6. fázis befejezése
- 8. fázis párhuzamos a 7. fázissal
- 9. fázis előfeltétele: 7. és 8. fázis sikeres
- 10. fázis folyamatos minden fázis során, finalizálás a végén

---

**Dokumentum vége**

**Verzió:** 1.0
**Utolsó módosítás:** 2025-11-04
**Készítette:** Claude (business-to-tech-requirements agent)
