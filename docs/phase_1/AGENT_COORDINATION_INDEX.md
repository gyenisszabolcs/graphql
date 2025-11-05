# Phase 1 - Agent Coordination Index
**Quick Reference for Agent Task Assignments**

**Version:** 1.0
**Date:** 2025-11-05
**Status:** Ready to Execute

---

## Quick Start for Agents

Each specialized agent should:
1. Read this coordination index
2. Locate your assigned workstream below
3. Read the detailed task instructions in the referenced documents
4. Execute your workstream tasks
5. Update completion status in `phase_1_summary.md`
6. Commit your deliverables with proper commit messages

---

## Agent Assignments Summary

| Agent Role | Workstream | Tasks | Duration | Priority |
|------------|------------|-------|----------|----------|
| **devops-infrastructure-engineer** | WS1: Database & Security | 1.1, 1.2, 5.3 | 3-4 hours | P0 Critical |
| **backend-api-developer** | WS2: Dev Environment | 2.1, 2.2, 2.3 | 2-3 hours | P0 Critical |
| **technical-architect** | WS3: Solution Structure | 3.1-3.6 | 3-4 hours | P1 High |
| **backend-api-developer** | WS4: NuGet Packages | 4.1-4.5 | 1-2 hours | P1 High |
| **backend-api-developer** | WS5: Configuration | 5.1, 5.2, 5.4 | 2-3 hours | P1 High |
| **backend-api-developer** | WS6: Logging | 6.1, 6.2, 6.3 | 2-3 hours | P1 High |

---

## Workstream 1: SQL Server Connectivity and Schema Documentation

**AGENT:** `devops-infrastructure-engineer`

**READ FIRST:**
- `d:\Development\graphql\graphql_fazisok.md` - Lines 76-105 (Tasks 1.1, 1.2, 1.5)
- `d:\Development\graphql\docs\phase_1\phase_1_prd.md` - Stories 1.1, 1.2, 5.3
- `d:\Development\graphql\docs\phase_1\phase_1_summary.md` - Workstream 1 section

**YOUR TASKS:**
1. **Task 1.1** - SQL Server Connection Test (30 min, P0)
   - Connect to: Server=10.10.10.69, Database=dev_graphql
   - Verify access to tables: CIKK, GYARTO, PARTNER, USERS
   - Document connection test results

2. **Task 1.2** - Database Schema Documentation (2-3 hours, P1)
   - Extract complete schema from INFORMATION_SCHEMA
   - Document all columns (name, type, length, nullable, PK, FK)
   - List all existing indexes
   - Create ER diagram

3. **Task 5.3** - Update .gitignore (15 min, P0)
   - Add patterns for secrets (appsettings.Local.json)
   - Add patterns for build artifacts (bin/, obj/)
   - Add patterns for logs (logs/)

**YOUR DELIVERABLES:**
- `docs/phase_1/database_connectivity.md`
- `docs/phase_1/database_schema.md`
- `docs/phase_1/er_diagram.png` or `.md`
- Updated `.gitignore`

**START HERE:** Test SQL connection immediately (critical path)

**WHEN DONE:** Update Workstream 1 status in `phase_1_summary.md` → Commit with message "[Phase 1] WS1 Complete - Database documented"

---

## Workstream 2: Development Environment Setup

**AGENT:** `backend-api-developer`

**READ FIRST:**
- `d:\Development\graphql\graphql_fazisok.md` - Lines 82-84 (Task 1.2)
- `d:\Development\graphql\docs\phase_1\phase_1_prd.md` - Stories 2.1, 2.2, 2.3
- `d:\Development\graphql\docs\phase_1\phase_1_summary.md` - Workstream 2 section

**YOUR TASKS:**
1. **Task 2.1** - Install .NET 8 SDK (30 min, P0)
   - Download from: https://dotnet.microsoft.com/download/dotnet/8.0
   - Install SDK (not just runtime)
   - Verify: `dotnet --version` shows 8.0.x

2. **Task 2.2** - Install VS Code Extensions (20 min, P2)
   - C# Dev Kit (microsoft.csdevkit)
   - GraphQL: Language Feature Support (graphql.vscode-graphql)
   - C# (ms-dotnettools.csharp)
   - NuGet Package Manager (jmrog.vscode-nuget-package-manager)

3. **Task 2.3** - Workspace Configuration (30 min, P3)
   - Create `.vscode/settings.json`
   - Create `.vscode/extensions.json`
   - Create `.vscode/launch.json` template

**YOUR DELIVERABLES:**
- `docs/phase_1/environment_setup.md`
- `.vscode/settings.json`
- `.vscode/extensions.json`
- `.vscode/launch.json`

**START HERE:** Install .NET 8 SDK (enables all .NET work)

**WHEN DONE:** Update Workstream 2 status in `phase_1_summary.md` → Commit with message "[Phase 1] WS2 Complete - Dev environment ready"

---

## Workstream 3: Solution and Project Structure

**AGENT:** `technical-architect`

**READ FIRST:**
- `d:\Development\graphql\graphql_fazisok.md` - Lines 86-89 (Task 1.3)
- `d:\Development\graphql\GraphQL_Fejlesztesi_Specifikacio.md` - Lines 204-305 (Section 4 - Project Structure)
- `d:\Development\graphql\docs\phase_1\phase_1_prd.md` - Stories 3.1-3.6
- `d:\Development\graphql\docs\phase_1\phase_1_summary.md` - Workstream 3 section

**YOUR TASKS:**
1. **Task 3.1** - Create Solution (10 min, P1)
   - `cd d:\Development\graphql\src`
   - `dotnet new sln -n GraphQLApp`

2. **Task 3.2** - Create API Project (15 min, P1)
   - `dotnet new webapi -n GraphQLApp.API`
   - Create folder structure: Controllers/, GraphQL/, Middleware/

3. **Task 3.3** - Create Core Project (15 min, P1)
   - `dotnet new classlib -n GraphQLApp.Core`
   - Create folders: Entities/, Interfaces/, DTOs/

4. **Task 3.4** - Create Infrastructure Project (15 min, P1)
   - `dotnet new classlib -n GraphQLApp.Infrastructure`
   - Create folders: Data/, Repositories/

5. **Task 3.5** - Create Services Project (15 min, P2)
   - `dotnet new classlib -n GraphQLApp.Services`

6. **Task 3.6** - Configure Project References (30 min, P1)
   - API → Core, Infrastructure, Services
   - Infrastructure → Core
   - Services → Core
   - Verify: `dotnet build` succeeds

**YOUR DELIVERABLES:**
- `src/GraphQLApp.sln`
- `src/GraphQLApp.API/` (with folder structure)
- `src/GraphQLApp.Core/` (with folder structure)
- `src/GraphQLApp.Infrastructure/` (with folder structure)
- `src/GraphQLApp.Services/`
- `docs/phase_1/architecture.md`

**START HERE:** Wait for Task 2.1 (SDK) to complete, then create solution

**WHEN DONE:** Update Workstream 3 status in `phase_1_summary.md` → Commit with message "[Phase 1] WS3 Complete - Solution structure created"

---

## Workstream 4: NuGet Package Installation

**AGENT:** `backend-api-developer`

**READ FIRST:**
- `d:\Development\graphql\graphql_fazisok.md` - Lines 91-95 (Task 1.4)
- `d:\Development\graphql\GraphQL_Fejlesztesi_Specifikacio.md` - Lines 306-330 (Section 4.2 - NuGet Packages)
- `d:\Development\graphql\docs\phase_1\phase_1_prd.md` - Stories 4.1-4.5
- `d:\Development\graphql\docs\phase_1\phase_1_summary.md` - Workstream 4 section

**YOUR TASKS:**
1. **Task 4.1** - Install Hot Chocolate (10 min, P1)
   - Navigate to `src/GraphQLApp.API/`
   - `dotnet add package HotChocolate.AspNetCore --version 13.9.12`
   - `dotnet add package HotChocolate.AspNetCore.Authorization --version 13.9.12`
   - `dotnet add package HotChocolate.Data --version 13.9.12`

2. **Task 4.2** - Install Dapper (10 min, P1)
   - Navigate to `src/GraphQLApp.Infrastructure/`
   - `dotnet add package Dapper --version 2.1.28`
   - `dotnet add package Microsoft.Data.SqlClient --version 5.2.2`

3. **Task 4.3** - Install Serilog (10 min, P1)
   - Navigate to `src/GraphQLApp.API/`
   - `dotnet add package Serilog.AspNetCore --version 8.0.1`
   - `dotnet add package Serilog.Sinks.Console --version 5.0.1`
   - `dotnet add package Serilog.Sinks.File --version 6.0.0`

4. **Task 4.4** - Install JWT Bearer (10 min, P2)
   - Navigate to `src/GraphQLApp.API/`
   - `dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer --version 8.0.11`

5. **Task 4.5** - Install BCrypt (10 min, P2)
   - Navigate to `src/GraphQLApp.Services/`
   - `dotnet add package BCrypt.Net-Next --version 4.0.3`

**YOUR DELIVERABLES:**
- Updated `.csproj` files with package references
- `docs/phase_1/dependencies.md`

**START HERE:** Wait for Task 3.6 (references) to complete, then install packages

**WHEN DONE:** Update Workstream 4 status in `phase_1_summary.md` → Commit with message "[Phase 1] WS4 Complete - All NuGet packages installed"

---

## Workstream 5: Configuration Management

**AGENT:** `backend-api-developer`

**READ FIRST:**
- `d:\Development\graphql\graphql_fazisok.md` - Lines 97-100 (Task 1.5)
- `d:\Development\graphql\GraphQL_Fejlesztesi_Specifikacio.md` - Section 8 (Configuration Management)
- `d:\Development\graphql\docs\phase_1\phase_1_prd.md` - Stories 5.1-5.4
- `d:\Development\graphql\docs\phase_1\phase_1_summary.md` - Workstream 5 section

**YOUR TASKS:**
1. **Task 5.1** - Create appsettings.json (30 min, P1)
   - Create in `src/GraphQLApp.API/`
   - Add ConnectionStrings with placeholders
   - Add JwtSettings with placeholder secret
   - Add Serilog configuration
   - Commit this file (no secrets!)

2. **Task 5.2** - Create Local Settings (20 min, P1)
   - Copy to `appsettings.Local.json.template` (commit this)
   - Create `appsettings.Local.json` (DO NOT commit)
   - Add your actual SQL Server credentials
   - Add your actual JWT secret (min 32 chars)
   - Verify .gitignore excludes Local.json

3. **Task 5.4** - Document Environment Variables (45 min, P3)
   - Document double-underscore syntax
   - Provide IIS configuration examples
   - Provide Azure App Service examples

**YOUR DELIVERABLES:**
- `src/GraphQLApp.API/appsettings.json` (committed)
- `src/GraphQLApp.API/appsettings.Local.json.template` (committed)
- `src/GraphQLApp.API/appsettings.Local.json` (NOT committed, local only)
- `docs/phase_1/configuration.md`

**START HERE:** Wait for Task 3.2 (API project) to complete, then create configs

**WHEN DONE:** Update Workstream 5 status in `phase_1_summary.md` → Commit with message "[Phase 1] WS5 Complete - Configuration system ready"

**CRITICAL:** Verify appsettings.Local.json is NOT in git before committing anything else!

---

## Workstream 6: Logging Infrastructure

**AGENT:** `backend-api-developer`

**READ FIRST:**
- `d:\Development\graphql\graphql_fazisok.md` - Lines 102-105 (Task 1.6)
- `d:\Development\graphql\GraphQL_Fejlesztesi_Specifikacio.md` - Section 9 (Logging and Error Handling)
- `d:\Development\graphql\docs\phase_1\phase_1_prd.md` - Stories 6.1-6.3
- `d:\Development\graphql\docs\phase_1\phase_1_summary.md` - Workstream 6 section

**YOUR TASKS:**
1. **Task 6.1** - Configure Serilog (45 min, P1)
   - Open `src/GraphQLApp.API/Program.cs`
   - Add UseSerilog() to host builder
   - Configure console and file sinks
   - Add UseSerilogRequestLogging() middleware
   - Test: run app, verify logs appear

2. **Task 6.2** - Document Structured Logging (1 hour, P3)
   - Create examples of LogInformation, LogWarning, LogError
   - Document message template syntax
   - Document structured property syntax (@)
   - Provide best practices

3. **Task 6.3** - Configure Log Management (30 min, P2)
   - Verify rolling interval (Daily)
   - Verify retention (7 days)
   - Verify file size limit (100 MB)
   - Test log rotation

**YOUR DELIVERABLES:**
- Updated `src/GraphQLApp.API/Program.cs`
- `docs/phase_1/logging.md`
- Working log files in `logs/` (gitignored)

**START HERE:** Wait for Task 4.3 (Serilog packages) and Task 5.1 (appsettings) to complete

**WHEN DONE:** Update Workstream 6 status in `phase_1_summary.md` → Commit with message "[Phase 1] WS6 Complete - Logging infrastructure operational"

---

## Parallel Execution Strategy

### Wave 1 (Day 1 Morning) - Can Run in Parallel
- **DevOps Agent:** WS1 Task 1.1 (SQL test) + Task 5.3 (.gitignore)
- **Backend Agent:** WS2 Tasks 2.1-2.2 (SDK + VS Code)

**Dependencies:** None - all can start immediately

---

### Wave 2 (Day 1 Afternoon) - Sequential Dependencies
- **Architect Agent:** WS3 Tasks 3.1-3.5 (create projects)
  - **Requires:** WS2 Task 2.1 (SDK) complete
- **DevOps Agent:** WS1 Task 1.2 (schema docs)
  - **Requires:** WS1 Task 1.1 (connection) complete

**Dependencies:** Wait for Wave 1

---

### Wave 3 (Day 2 Morning) - Sequential Dependencies
- **Architect Agent:** WS3 Task 3.6 (configure references)
  - **Requires:** WS3 Tasks 3.1-3.5 complete
- **Backend Agent:** WS4 All Tasks (install packages)
  - **Requires:** WS3 Task 3.6 complete

**Dependencies:** Wait for Wave 2

---

### Wave 4 (Day 2 Afternoon) - Parallel After Dependencies Met
- **Backend Agent:** WS5 Tasks 5.1-5.2 (configuration)
  - **Requires:** WS3 Task 3.2 (API project) complete
- **Backend Agent:** WS6 Task 6.1 (Serilog config)
  - **Requires:** WS4 Task 4.3 (Serilog packages) + WS5 Task 5.1 (appsettings) complete

**Dependencies:** Wait for specific tasks in Wave 3

---

### Wave 5 (Day 3) - Polish and Documentation
- **All Agents:** Remaining documentation tasks
  - WS2 Task 2.3 (workspace config)
  - WS5 Task 5.4 (env var docs)
  - WS6 Tasks 6.2-6.3 (logging docs)
- **All Agents:** Quality Assurance Checklist
- **All Agents:** Phase 1 review and sign-off

**Dependencies:** All previous waves complete

---

## Critical Path Visualization

```
SDK Install (2.1)
    ↓
Solution Create (3.1)
    ↓
API Project (3.2)
    ↓
All Projects (3.3-3.5)
    ↓
References (3.6)
    ↓
Serilog Packages (4.3)
    ↓
appsettings.json (5.1)
    ↓
Serilog Config (6.1)
    ↓
PHASE 1 COMPLETE
```

**Critical Path Duration:** 6-7 hours

---

## Completion Checklist for All Agents

Before marking your workstream complete:

### Code Checklist
- [ ] All files created in correct locations
- [ ] All code compiles without errors
- [ ] All references correct (no red squiggles)
- [ ] No hardcoded secrets in committed code

### Documentation Checklist
- [ ] All deliverable docs created in `docs/phase_1/`
- [ ] Documentation is clear and complete
- [ ] Code examples tested and working
- [ ] Troubleshooting section added if applicable

### Git Checklist
- [ ] All changes committed with proper message format
- [ ] .gitignore working (no secrets in git status)
- [ ] Commit message follows: "[Phase 1] <Task ID> - <Description>"
- [ ] Changes pushed to repository

### Handoff Checklist
- [ ] Updated completion status in `phase_1_summary.md`
- [ ] Filled in "Completed Workstreams" section
- [ ] Documented any issues encountered
- [ ] Added notes for dependent workstreams

---

## Emergency Contacts and Escalation

### If You're Blocked
1. Document the blocker in `phase_1_summary.md` (Issues section)
2. Mark task as "Blocked" in status
3. Try to work on non-dependent tasks
4. Escalate if blocked > 2 hours

### Escalation Path
- **Technical Issues:** → Technical Architect
- **Database Access:** → DevOps → DBA
- **Package/Build Issues:** → Backend Developer → Senior Dev
- **Git Issues:** → DevOps → Git Admin

### Common Issues and Quick Fixes
- **SQL Connection Fails:** Check firewall, VPN, credentials
- **dotnet not found:** Install SDK, restart terminal, check PATH
- **Build fails:** Run `dotnet clean`, delete bin/obj, restore packages
- **Package conflicts:** Use exact versions from PRD, clear NuGet cache

---

## Success Criteria for Phase 1

Phase 1 is complete when:
- [ ] All 6 workstreams marked complete
- [ ] All 22 tasks done
- [ ] All 11 documentation files created
- [ ] Solution builds: `dotnet build` succeeds
- [ ] Application runs: `dotnet run --project src/GraphQLApp.API` succeeds
- [ ] Logs appear in console and `logs/` folder
- [ ] Quality Assurance Checklist 100% complete
- [ ] Fresh clone test passed by second developer
- [ ] Phase 1 sign-off obtained

---

## Quick Reference: File Locations

### Documentation to Create
```
docs/phase_1/
├── database_connectivity.md     (WS1 - DevOps)
├── database_schema.md           (WS1 - DevOps)
├── er_diagram.png               (WS1 - DevOps)
├── environment_setup.md         (WS2 - Backend)
├── architecture.md              (WS3 - Architect)
├── dependencies.md              (WS4 - Backend)
├── configuration.md             (WS5 - Backend)
├── logging.md                   (WS6 - Backend)
├── phase_1_prd.md               ✅ (Already created)
├── phase_1_priority_matrix.md   ✅ (Already created)
└── phase_1_summary.md           ✅ (Already created)
```

### Code to Create
```
src/
├── GraphQLApp.sln                        (WS3 - Architect)
├── GraphQLApp.API/                       (WS3 - Architect)
│   ├── appsettings.json                  (WS5 - Backend) ✓ Commit
│   ├── appsettings.Local.json.template   (WS5 - Backend) ✓ Commit
│   ├── appsettings.Local.json            (WS5 - Backend) ✗ DO NOT COMMIT
│   ├── Program.cs                        (WS6 - Backend)
│   └── GraphQLApp.API.csproj             (WS4 - Backend - packages)
├── GraphQLApp.Core/                      (WS3 - Architect)
│   └── GraphQLApp.Core.csproj
├── GraphQLApp.Infrastructure/            (WS3 - Architect)
│   └── GraphQLApp.Infrastructure.csproj  (WS4 - Backend - packages)
└── GraphQLApp.Services/                  (WS3 - Architect)
    └── GraphQLApp.Services.csproj        (WS4 - Backend - packages)

.vscode/
├── settings.json                (WS2 - Backend)
├── extensions.json              (WS2 - Backend)
└── launch.json                  (WS2 - Backend)

.gitignore                       (WS1 - DevOps - update)
```

---

## Document Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2025-11-05 | Initial coordination index created | Product Requirements Architect |

---

**AGENT QUICK START:**
1. Find your agent role above
2. Read your assigned workstream section
3. Follow the "READ FIRST" links
4. Execute your tasks in order
5. Update completion status
6. Commit with proper message

**LET'S BUILD THIS! 🚀**
