# Phase 0 - Task 6: Git Repository and Project Structure

**Agent Role:** DevOps Infrastructure Engineer
**Date:** 2025-11-04
**Status:** ✅ COMPLETED

## 1. Git Repository Initialization

### 1.1 Initialize Repository

```bash
# Navigate to project root
cd d:\Development\graphql

# Initialize Git repository
git init

# Expected output: Initialized empty Git repository in d:/Development/graphql/.git/
```

### 1.2 Configure Git (if not already done globally)

```bash
# Set user identity
git config user.name "Your Name"
git config user.email "your.email@example.com"

# Set default branch name
git config init.defaultBranch main

# Configure line endings (Windows)
git config core.autocrlf true

# Configure line endings (macOS/Linux)
git config core.autocrlf input
```

## 2. Branch Strategy

### 2.1 Git Flow Model

```
main (production-ready code)
  ├── develop (integration branch)
  │   ├── feature/phase-1-infrastructure
  │   ├── feature/phase-2-database
  │   ├── feature/phase-3-backend-api
  │   ├── feature/phase-4-authentication
  │   ├── feature/phase-5-graphql-api
  │   ├── feature/phase-6-frontend
  │   └── ... (other features)
  ├── bugfix/... (for non-critical bugs)
  └── hotfix/... (for production critical fixes)
```

### 2.2 Branch Naming Convention

```
Feature branches:    feature/<phase-number>-<description>
                     feature/user-authentication
                     feature/query-builder-ui

Bugfix branches:     bugfix/<issue-number>-<description>
                     bugfix/123-login-error

Hotfix branches:     hotfix/<issue-number>-<description>
                     hotfix/456-security-vulnerability

Release branches:    release/v1.0.0
                     release/v1.1.0
```

### 2.3 Create Initial Branches

```bash
# Create and switch to develop branch
git checkout -b develop

# Push to remote (when remote is configured)
git push -u origin develop

# Set develop as default branch for merges
git config branch.develop.mergeOptions --no-ff
```

## 3. .gitignore Configuration

### 3.1 Complete .gitignore File

Create `.gitignore` in project root:

```gitignore
# ===== .NET / C# =====
## Build outputs
[Bb]in/
[Oo]bj/
[Ll]og/
[Ll]ogs/
publish/

## Visual Studio user-specific files
*.rsuser
*.suo
*.user
*.userosscache
*.sln.docstates
*.userprefs

## Mono auto generated files
mono_crash.*

## Build results
[Dd]ebug/
[Dd]ebugPublic/
[Rr]elease/
[Rr]eleases/
x64/
x86/
[Aa][Rr][Mm]/
[Aa][Rr][Mm]64/
bld/
[Bb]ld/
[Oo]bj/
[Ll]og/
[Ll]ogs/

## Test Results
[Tt]est[Rr]esult*/
[Bb]uild[Ll]og.*

## ===== Local Configuration (CRITICAL - DO NOT COMMIT) =====
appsettings.Local.json
appsettings.*.Local.json
appsettings.Production.json
*.Local.json

## ===== Node.js / Frontend =====
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*
.pnpm-debug.log*

## Build outputs
dist/
dist-ssr/
*.local

## ===== Environment Variables =====
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

## ===== IDE / Editor Specific =====
.vscode/
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
*.code-workspace

.idea/
*.swp
*.swo
*~

## Visual Studio
.vs/
*.user
*.suo

## ===== Database Files =====
*.mdf
*.ldf
*.ndf
*.bak

## ===== Logs =====
logs/
*.log

## ===== OS Generated =====
.DS_Store
Thumbs.db
Desktop.ini

## ===== Package Files =====
*.nupkg
*.snupkg
*.tgz

## ===== Coverage Reports =====
coverage/
*.coverage
*.coveragexml
TestResults/

## ===== Backup Files =====
*.bak
*.tmp
*.temp
*~

## ===== Compiled Files =====
*.dll
*.exe
*.out
*.pdb
```

### 3.2 Create .gitignore

```bash
# Create .gitignore file
# (File content shown above)

# Verify .gitignore is working
git status
# Should NOT show appsettings.Local.json, bin/, obj/, etc.
```

## 4. .gitattributes Configuration

### 4.1 Create .gitattributes

Create `.gitattributes` in project root:

```gitattributes
# Auto detect text files and normalize line endings to LF
* text=auto

# Explicitly declare text files
*.cs text diff=csharp
*.csproj text diff=csharp
*.sln text eol=crlf
*.md text
*.txt text
*.json text
*.xml text
*.config text
*.sql text

# JavaScript/TypeScript
*.js text
*.ts text
*.vue text
*.jsx text
*.tsx text

# Shell scripts
*.sh text eol=lf
*.bash text eol=lf

# Windows batch files
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf

# Binary files
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.pdf binary
*.dll binary
*.exe binary
*.zip binary
*.7z binary
```

## 5. Initial Commit

### 5.1 Stage Phase 0 Deliverables

```bash
# Add all Phase 0 documentation
git add docs/phase_0/

# Add project structure files
git add .gitignore
git add .gitattributes
git add .editorconfig
git add README.md

# Check staged files
git status
```

### 5.2 Create Initial Commit

```bash
# Create initial commit
git commit -m "chore: initialize project with Phase 0 deliverables

Phase 0 Completed:
- Requirements validation and finalization
- UX/UI design and wireframes
- System architecture design
- Database schema design
- Development environment specification
- Git repository and branch strategy

Deliverables:
- docs/phase_0/01_requirements_validation.md
- docs/phase_0/02_ux_ui_design_wireframes.md
- docs/phase_0/03_architecture_design.md
- docs/phase_0/04_database_schema_design.md
- docs/phase_0/05_dev_environment_specification.md
- docs/phase_0/06_git_repository_setup.md

Generated with Claude Code (Phase 0 Coordinator)"
```

## 6. Project Directory Structure

### 6.1 Current Directory Layout (After Phase 0)

```
d:\Development\graphql\
├── .git/                           # Git repository metadata
├── .gitignore                      # Git ignore rules
├── .gitattributes                  # Git attributes for line endings
├── .editorconfig                   # Editor configuration
├── README.md                       # Project overview (to be created in Phase 1)
├── GraphQL_Fejlesztesi_Specifikacio.md  # Original specification
├── graphql_fazisok.md              # Phase breakdown document
│
├── docs/                           # Documentation
│   └── phase_0/                    # Phase 0 deliverables
│       ├── 01_requirements_validation.md
│       ├── 02_ux_ui_design_wireframes.md
│       ├── 03_architecture_design.md
│       ├── 04_database_schema_design.md
│       ├── 05_dev_environment_specification.md
│       ├── 06_git_repository_setup.md
│       └── phase_0_summary.md
│
├── scripts/                        # Utility scripts
│   ├── sql/                        # Database scripts (to be created in Phase 2)
│   │   ├── 001-init-database.sql
│   │   ├── 002-seed-data.sql
│   │   └── 003-stored-procedures.sql
│   ├── deploy-dev.ps1              # Deployment scripts (to be created in Phase 9)
│   └── deploy-prod.ps1
│
├── src/                            # Source code (to be created in Phase 1)
│   ├── GraphQLApp.API/             # Main API project
│   ├── GraphQLApp.Core/            # Domain layer
│   ├── GraphQLApp.Infrastructure/  # Data access layer
│   ├── GraphQLApp.Services/        # Business logic layer
│   └── GraphQLApp.Web/             # Frontend (Vue.js)
│
└── tests/                          # Test projects (to be created in Phase 7)
    ├── GraphQLApp.API.Tests/
    ├── GraphQLApp.Infrastructure.Tests/
    └── GraphQLApp.Services.Tests/
```

### 6.2 Future Directory Structure (After Phase 1+)

```
d:\Development\graphql\
├── .git/
├── .gitignore
├── .gitattributes
├── .editorconfig
├── .vscode/                        # VS Code workspace settings
│   ├── settings.json
│   ├── launch.json
│   └── tasks.json
├── README.md
├── CHANGELOG.md                    # Version history
├── GraphQLApp.sln                  # Visual Studio solution
│
├── docs/
│   ├── phase_0/                    # ✅ Completed
│   ├── phase_1/                    # Infrastructure docs
│   ├── phase_2/                    # Database docs
│   ├── ...
│   ├── api/                        # API documentation
│   │   ├── queries.md
│   │   ├── mutations.md
│   │   └── examples.md
│   └── diagrams/                   # Architecture diagrams
│       ├── architecture.png
│       └── er-diagram.png
│
├── scripts/
│   ├── sql/
│   │   ├── 001-init-database.sql
│   │   ├── 002-seed-data.sql
│   │   ├── 003-stored-procedures.sql
│   │   └── migrations/
│   ├── deploy-dev.ps1
│   ├── deploy-prod.ps1
│   └── backup-database.ps1
│
├── src/
│   ├── GraphQLApp.API/
│   │   ├── Controllers/
│   │   ├── GraphQL/
│   │   │   ├── Queries/
│   │   │   ├── Mutations/
│   │   │   ├── Types/
│   │   │   ├── DataLoaders/
│   │   │   └── Filters/
│   │   ├── Middleware/
│   │   ├── Program.cs
│   │   ├── appsettings.json
│   │   ├── appsettings.Development.json
│   │   └── GraphQLApp.API.csproj
│   │
│   ├── GraphQLApp.Core/
│   │   ├── Entities/
│   │   ├── Interfaces/
│   │   │   ├── Repositories/
│   │   │   └── Services/
│   │   ├── DTOs/
│   │   └── GraphQLApp.Core.csproj
│   │
│   ├── GraphQLApp.Infrastructure/
│   │   ├── Data/
│   │   ├── Repositories/
│   │   └── GraphQLApp.Infrastructure.csproj
│   │
│   ├── GraphQLApp.Services/
│   │   ├── AuthService.cs
│   │   ├── JwtTokenService.cs
│   │   └── GraphQLApp.Services.csproj
│   │
│   └── GraphQLApp.Web/              # Vue.js frontend
│       ├── public/
│       ├── src/
│       │   ├── components/
│       │   ├── views/
│       │   ├── services/
│       │   ├── stores/
│       │   ├── router/
│       │   ├── assets/
│       │   ├── App.vue
│       │   └── main.js
│       ├── package.json
│       ├── vite.config.js
│       └── tailwind.config.js
│
└── tests/
    ├── GraphQLApp.API.Tests/
    │   ├── QueryTests/
    │   ├── MutationTests/
    │   └── IntegrationTests/
    ├── GraphQLApp.Infrastructure.Tests/
    │   └── RepositoryTests/
    └── GraphQLApp.Services.Tests/
        └── AuthServiceTests/
```

## 7. Commit Message Convention

### 7.1 Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### 7.2 Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, missing semicolons, etc.)
- `refactor`: Code refactoring (no functional changes)
- `perf`: Performance improvements
- `test`: Adding or updating tests
- `build`: Changes to build process or dependencies
- `ci`: CI/CD changes
- `chore`: Other changes (maintenance, tooling, etc.)

### 7.3 Examples

```bash
# Feature commit
git commit -m "feat(auth): implement JWT token generation

- Add JwtTokenService with token creation logic
- Configure JWT Bearer authentication middleware
- Add unit tests for token validation"

# Bug fix commit
git commit -m "fix(graphql): resolve N+1 query issue in CikkType

- Implement DataLoader for Gyarto lookups
- Add batch loading to prevent multiple database calls
- Update integration tests"

# Documentation commit
git commit -m "docs(readme): add Quick Start guide

- Add installation instructions
- Include configuration examples
- Document common troubleshooting steps"

# Chore commit
git commit -m "chore: update NuGet packages to latest versions

- Hot Chocolate 13.9.0 → 13.9.5
- Serilog 3.1.0 → 3.1.1
- Dapper 2.1.28 → 2.1.35"
```

## 8. Git Workflow

### 8.1 Feature Development Workflow

```bash
# 1. Start from develop branch
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/phase-1-infrastructure

# 3. Make changes and commit frequently
git add src/GraphQLApp.API/Program.cs
git commit -m "feat(api): configure GraphQL server with Hot Chocolate"

git add src/GraphQLApp.Infrastructure/
git commit -m "feat(infrastructure): implement repository pattern with Dapper"

# 4. Push feature branch to remote (for collaboration)
git push -u origin feature/phase-1-infrastructure

# 5. When feature is complete, merge back to develop
git checkout develop
git pull origin develop
git merge --no-ff feature/phase-1-infrastructure
git push origin develop

# 6. Delete feature branch (optional)
git branch -d feature/phase-1-infrastructure
git push origin --delete feature/phase-1-infrastructure
```

### 8.2 Hotfix Workflow (Production Issues)

```bash
# 1. Start from main branch
git checkout main
git pull origin main

# 2. Create hotfix branch
git checkout -b hotfix/critical-security-fix

# 3. Make fix and commit
git add src/GraphQLApp.Services/JwtTokenService.cs
git commit -m "fix(security): patch JWT token validation vulnerability"

# 4. Merge to main
git checkout main
git merge --no-ff hotfix/critical-security-fix
git tag -a v1.0.1 -m "Hotfix: Security patch"
git push origin main --tags

# 5. Merge to develop (so fix is in future releases)
git checkout develop
git merge --no-ff hotfix/critical-security-fix
git push origin develop

# 6. Delete hotfix branch
git branch -d hotfix/critical-security-fix
```

## 9. Remote Repository Setup

### 9.1 GitHub Repository (Recommended)

```bash
# Create repository on GitHub (via web interface)
# Repository name: graphql-app
# Description: GraphQL API Application with Vue.js Admin Interface

# Add remote
git remote add origin https://github.com/your-organization/graphql-app.git

# Push initial commit
git push -u origin main

# Push develop branch
git checkout develop
git push -u origin develop

# Set develop as default branch on GitHub (optional)
# Go to Settings → Branches → Default branch → change to develop
```

### 9.2 Azure DevOps (Alternative)

```bash
# Create repository in Azure DevOps (via web interface)

# Add remote
git remote add origin https://dev.azure.com/your-org/your-project/_git/graphql-app

# Push branches
git push -u origin main
git push -u origin develop
```

### 9.3 GitLab (Alternative)

```bash
# Create repository on GitLab

# Add remote
git remote add origin https://gitlab.com/your-org/graphql-app.git

# Push branches
git push -u origin main
git push -u origin develop
```

## 10. Branch Protection Rules

### 10.1 Recommended Branch Protection (main branch)

**On GitHub/GitLab/Azure DevOps, configure:**

- ✅ Require pull request reviews before merging (minimum 1 approval)
- ✅ Require status checks to pass before merging (CI/CD build + tests)
- ✅ Require branches to be up to date before merging
- ✅ Include administrators in restrictions
- ✅ Restrict who can push to matching branches
- ❌ Allow force pushes (never force push to main)
- ❌ Allow deletions (never delete main branch)

### 10.2 Recommended Branch Protection (develop branch)

- ✅ Require pull request reviews before merging (optional, depending on team size)
- ✅ Require status checks to pass before merging
- ✅ Require branches to be up to date before merging
- ❌ Allow force pushes

## 11. .editorconfig

Create `.editorconfig` for consistent code formatting:

```ini
# EditorConfig is awesome: https://EditorConfig.org

root = true

# All files
[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

# Code files (.NET)
[*.{cs,csx,vb,vbx}]
indent_style = space
indent_size = 4

# Code files (JavaScript/TypeScript/Vue)
[*.{js,ts,vue,json,yaml,yml}]
indent_style = space
indent_size = 2

# XML/HTML files
[*.{xml,html,cshtml,razor}]
indent_style = space
indent_size = 2

# Markdown files
[*.md]
trim_trailing_whitespace = false

# Solution files
[*.sln]
indent_style = tab

# Project files
[*.{csproj,vbproj,vcxproj,vcxproj.filters,proj,projitems,shproj}]
indent_style = space
indent_size = 2

# .NET coding conventions
[*.{cs,vb}]
dotnet_sort_system_directives_first = true
dotnet_separate_import_directive_groups = false

# C# coding conventions
[*.cs]
csharp_new_line_before_open_brace = all
csharp_new_line_before_else = true
csharp_new_line_before_catch = true
csharp_new_line_before_finally = true
csharp_prefer_braces = true:warning
csharp_indent_case_contents = true
csharp_indent_switch_labels = true
```

## 12. README.md Template

Create `README.md` in project root (to be expanded in Phase 1):

```markdown
# GraphQL API Application

GraphQL-based API system for dynamic MSSQL database querying with Vue.js admin interface.

## Features

- 🚀 GraphQL API with Hot Chocolate 13+
- 🔐 JWT Authentication
- 💾 MSSQL Database with Dapper ORM
- 🎨 Vue.js 3 Admin Interface
- 🍌 Banana Cake Pop API Explorer
- 📝 Comprehensive Logging with Serilog

## Tech Stack

**Backend:**
- .NET 8 (C#)
- Hot Chocolate GraphQL
- Dapper (Micro-ORM)
- SQL Server 2019+

**Frontend:**
- Vue.js 3
- Vite
- Tailwind CSS
- URQL GraphQL Client

## Quick Start

### Prerequisites

- .NET 8 SDK
- Node.js 20 LTS
- SQL Server 2019+ (or access to 10.10.10.69)
- Visual Studio Code (recommended)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/your-org/graphql-app.git
   cd graphql-app
   ```

2. Set up database:
   ```bash
   sqlcmd -S 10.10.10.69 -U dev_user -P password -i scripts/sql/001-init-database.sql
   ```

3. Configure local settings:
   ```bash
   cd src/GraphQLApp.API
   cp appsettings.json appsettings.Local.json
   # Edit appsettings.Local.json with your database credentials
   ```

4. Run backend:
   ```bash
   cd src/GraphQLApp.API
   dotnet run
   # Navigate to https://localhost:5001/graphql
   ```

5. Run frontend:
   ```bash
   cd src/GraphQLApp.Web
   npm install
   npm run dev
   # Navigate to http://localhost:5173
   ```

## Project Structure

See [Project Structure Documentation](docs/phase_0/06_git_repository_setup.md#61-current-directory-layout-after-phase-0)

## Documentation

- [Phase 0 Summary](docs/phase_0/phase_0_summary.md)
- [Architecture Design](docs/phase_0/03_architecture_design.md)
- [Database Schema](docs/phase_0/04_database_schema_design.md)
- [Development Environment Setup](docs/phase_0/05_dev_environment_specification.md)

## Contributing

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Commit your changes: `git commit -m 'feat: add some feature'`
3. Push to the branch: `git push origin feature/your-feature`
4. Open a Pull Request

See [Git Workflow](docs/phase_0/06_git_repository_setup.md#8-git-workflow) for details.

## License

[Specify License]

## Contact

[Your Contact Information]
```

## 13. Summary

### 13.1 Git Repository Setup Complete

- ✅ Repository initialized with `git init`
- ✅ Branch strategy defined (main, develop, feature/*, bugfix/*, hotfix/*)
- ✅ .gitignore configured (excludes appsettings.Local.json, bin/, obj/, node_modules/)
- ✅ .gitattributes configured (line ending normalization)
- ✅ .editorconfig created (consistent code formatting)
- ✅ Commit message convention defined
- ✅ README.md template created
- ✅ Initial commit with Phase 0 deliverables

### 13.2 Repository State

**Current branch:** `develop`
**Commits:** 1 (initial commit with Phase 0 documentation)
**Tracked files:**
- docs/phase_0/*.md (6 files)
- .gitignore
- .gitattributes
- .editorconfig
- README.md
- GraphQL_Fejlesztesi_Specifikacio.md
- graphql_fazisok.md

**Ignored files (not tracked):**
- appsettings.Local.json
- bin/, obj/, node_modules/
- All files listed in .gitignore

### 13.3 Next Steps

1. ✅ Git repository initialized and configured
2. ⏭️ Create Phase 0 summary report
3. ⏭️ Push to remote repository (GitHub/GitLab/Azure DevOps)
4. ⏭️ Begin Phase 1: Infrastructure and Architecture implementation

---

**Git Setup Version:** 1.0
**Status:** ✅ REPOSITORY READY FOR DEVELOPMENT
**Remote:** To be configured (recommend GitHub)
