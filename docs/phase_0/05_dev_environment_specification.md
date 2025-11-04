# Phase 0 - Task 5: Development Environment Specification

**Agent Role:** DevOps Infrastructure Engineer
**Date:** 2025-11-04
**Status:** ✅ COMPLETED

## 1. Executive Summary

This document specifies the complete development environment setup for the GraphQL API Application project, including required software, tools, SDK versions, IDE configuration, and step-by-step setup procedures.

## 2. System Requirements

### 2.1 Hardware Requirements

**Minimum Requirements:**
- CPU: 4 cores (Intel Core i5 or AMD Ryzen 5 equivalent)
- RAM: 8GB
- Storage: 50GB free space (SSD recommended)
- Network: Stable internet connection for package downloads

**Recommended Requirements:**
- CPU: 8 cores (Intel Core i7/i9 or AMD Ryzen 7/9)
- RAM: 16GB or more
- Storage: 100GB free space (NVMe SSD)
- Network: Gigabit Ethernet or fast Wi-Fi

### 2.2 Operating System

**Supported Platforms:**
- ✅ Windows 10/11 (Primary - for IIS compatibility testing)
- ✅ macOS 12+ (Monterey or later)
- ✅ Linux (Ubuntu 22.04 LTS, Fedora 38+)

**Note:** Windows is recommended for production-like IIS testing.

## 3. Required Software and Tools

### 3.1 Backend Development

| Software | Version | Purpose | Download Link |
|----------|---------|---------|---------------|
| **.NET SDK** | 8.0.x (LTS) | Backend framework | https://dotnet.microsoft.com/download/dotnet/8.0 |
| **SQL Server** | 2019+ or SQL Express | Database server (or access to 10.10.10.69) | https://www.microsoft.com/en-us/sql-server/sql-server-downloads |
| **SQL Server Management Studio** | 19.x (latest) | Database management | https://aka.ms/ssmsfullsetup |
| **Azure Data Studio** | Latest | Alternative DB tool (cross-platform) | https://aka.ms/azuredatastudio |

### 3.2 Frontend Development

| Software | Version | Purpose | Download Link |
|----------|---------|---------|---------------|
| **Node.js** | 20.x LTS | JavaScript runtime for Vue.js | https://nodejs.org/en/download/ |
| **npm** | 10.x (bundled with Node.js) | Package manager | Included with Node.js |
| **pnpm** | 8.x (optional) | Faster alternative to npm | https://pnpm.io/installation |

### 3.3 Development Tools

| Software | Version | Purpose | Download Link |
|----------|---------|---------|---------------|
| **Visual Studio Code** | Latest | Primary code editor | https://code.visualstudio.com/ |
| **Visual Studio 2022** | Community/Pro (optional) | Full IDE alternative | https://visualstudio.microsoft.com/ |
| **Git** | 2.40+ | Version control | https://git-scm.com/downloads |
| **Postman** | Latest | API testing | https://www.postman.com/downloads/ |
| **Insomnia** | Latest | Alternative to Postman | https://insomnia.rest/download |

### 3.4 Optional Tools (Recommended)

| Software | Purpose |
|----------|---------|
| **Docker Desktop** | Containerization for future enhancements |
| **Redis (Windows/Docker)** | Caching for future phases |
| **Windows Terminal** | Better terminal experience on Windows |
| **PowerShell 7+** | Modern PowerShell for scripts |
| **DBeaver Community** | Universal database tool (alternative to SSMS) |

## 4. Visual Studio Code Setup

### 4.1 Required Extensions

Install these extensions for optimal development experience:

```bash
# Install extensions via command line (or use Extension Marketplace in VS Code)
code --install-extension ms-dotnettools.csharp
code --install-extension ms-dotnettools.csdevkit
code --install-extension jchannon.csharpextensions
code --install-extension formulahendry.dotnet-test-explorer
code --install-extension ms-vscode.vscode-typescript-next
code --install-extension Vue.volar
code --install-extension bradlc.vscode-tailwindcss
code --install-extension esbenp.prettier-vscode
code --install-extension dbaeumer.vscode-eslint
code --install-extension eamodio.gitlens
code --install-extension mhutchie.git-graph
code --install-extension ms-azuretools.vscode-docker
code --install-extension humao.rest-client
```

**Extension Descriptions:**
- **C#** (ms-dotnettools.csharp): C# language support, IntelliSense, debugging
- **C# Dev Kit** (ms-dotnettools.csdevkit): Enhanced C# project management
- **C# Extensions** (jchannon.csharpextensions): Code snippets, class/interface scaffolding
- **.NET Core Test Explorer**: Run and debug unit tests in VS Code
- **Vue - Official** (Vue.volar): Vue 3 language support, syntax highlighting
- **Tailwind CSS IntelliSense**: Auto-completion for Tailwind classes
- **Prettier**: Code formatter for JavaScript/Vue files
- **ESLint**: JavaScript linter
- **GitLens**: Enhanced Git capabilities (blame, history, etc.)
- **Git Graph**: Visual Git history viewer
- **Docker**: Container management (for future use)
- **REST Client**: Test HTTP requests without leaving VS Code

### 4.2 VS Code Settings (Workspace)

Create `.vscode/settings.json` in project root:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "[csharp]": {
    "editor.defaultFormatter": "ms-dotnettools.csharp",
    "editor.formatOnSave": true
  },
  "files.exclude": {
    "**/bin": true,
    "**/obj": true,
    "**/node_modules": true
  },
  "dotnet.defaultSolution": "GraphQLApp.sln",
  "omnisharp.enableRoslynAnalyzers": true,
  "omnisharp.enableEditorConfigSupport": true,
  "csharp.suppressDotnetRestoreNotification": true,
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "files.eol": "\n",
  "typescript.tsdk": "node_modules/typescript/lib",
  "vue.server.hybridMode": true,
  "tailwindCSS.experimental.classRegex": [
    ["class:\\s*?[\"'`]([^\"'`]*).*?[\"'`]", "([a-zA-Z0-9\\-:]+)"]
  ]
}
```

### 4.3 VS Code Launch Configuration (Debugging)

Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": ".NET Core Launch (GraphQL API)",
      "type": "coreclr",
      "request": "launch",
      "preLaunchTask": "build",
      "program": "${workspaceFolder}/src/GraphQLApp.API/bin/Debug/net8.0/GraphQLApp.API.dll",
      "args": [],
      "cwd": "${workspaceFolder}/src/GraphQLApp.API",
      "stopAtEntry": false,
      "serverReadyAction": {
        "action": "openExternally",
        "pattern": "\\bNow listening on:\\s+(https?://\\S+)",
        "uriFormat": "%s/graphql"
      },
      "env": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "sourceFileMap": {
        "/Views": "${workspaceFolder}/Views"
      }
    },
    {
      "name": ".NET Core Attach",
      "type": "coreclr",
      "request": "attach"
    }
  ]
}
```

### 4.4 VS Code Tasks

Create `.vscode/tasks.json`:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "build",
      "command": "dotnet",
      "type": "process",
      "args": [
        "build",
        "${workspaceFolder}/GraphQLApp.sln",
        "/property:GenerateFullPaths=true",
        "/consoleloggerparameters:NoSummary"
      ],
      "problemMatcher": "$msCompile"
    },
    {
      "label": "publish",
      "command": "dotnet",
      "type": "process",
      "args": [
        "publish",
        "${workspaceFolder}/GraphQLApp.sln",
        "--configuration",
        "Release",
        "--output",
        "${workspaceFolder}/publish"
      ],
      "problemMatcher": "$msCompile"
    },
    {
      "label": "watch",
      "command": "dotnet",
      "type": "process",
      "args": [
        "watch",
        "run",
        "--project",
        "${workspaceFolder}/src/GraphQLApp.API"
      ],
      "problemMatcher": "$msCompile"
    },
    {
      "label": "test",
      "command": "dotnet",
      "type": "process",
      "args": [
        "test",
        "${workspaceFolder}/tests/GraphQLApp.API.Tests/GraphQLApp.API.Tests.csproj"
      ],
      "problemMatcher": "$msCompile",
      "group": {
        "kind": "test",
        "isDefault": true
      }
    }
  ]
}
```

## 5. Development Environment Setup Guide

### 5.1 Initial Setup (Windows)

**Step 1: Install .NET 8 SDK**
```powershell
# Download and run installer from:
# https://dotnet.microsoft.com/download/dotnet/8.0

# Verify installation
dotnet --version
# Expected output: 8.0.x
```

**Step 2: Install Node.js 20 LTS**
```powershell
# Download and run installer from:
# https://nodejs.org/en/download/

# Verify installation
node --version
# Expected output: v20.x.x

npm --version
# Expected output: 10.x.x
```

**Step 3: Install SQL Server (if not using 10.10.10.69)**
```powershell
# Option A: SQL Server Express (Free)
# Download from: https://www.microsoft.com/en-us/sql-server/sql-server-downloads

# Option B: Use remote server (10.10.10.69)
# Test connectivity:
sqlcmd -S 10.10.10.69 -U dev_user -P your_password -Q "SELECT @@VERSION"
```

**Step 4: Install SQL Server Management Studio**
```powershell
# Download and install from:
# https://aka.ms/ssmsfullsetup
```

**Step 5: Install Git**
```powershell
# Download and install from:
# https://git-scm.com/download/win

# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

**Step 6: Install Visual Studio Code**
```powershell
# Download and install from:
# https://code.visualstudio.com/

# Install extensions (see section 4.1)
```

### 5.2 Initial Setup (macOS)

**Step 1: Install Homebrew (if not already installed)**
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**Step 2: Install .NET 8 SDK**
```bash
brew install --cask dotnet-sdk

# Verify
dotnet --version
```

**Step 3: Install Node.js 20 LTS**
```bash
brew install node@20

# Verify
node --version
npm --version
```

**Step 4: Install Azure Data Studio (SQL Server client)**
```bash
brew install --cask azure-data-studio
```

**Step 5: Install Git and VS Code**
```bash
brew install git
brew install --cask visual-studio-code

# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 5.3 Initial Setup (Linux - Ubuntu 22.04)

```bash
# Install .NET 8 SDK
wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
chmod +x dotnet-install.sh
./dotnet-install.sh --channel 8.0

# Add to PATH (add to ~/.bashrc)
export DOTNET_ROOT=$HOME/.dotnet
export PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools

# Install Node.js 20 LTS
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install Git and VS Code
sudo apt-get update
sudo apt-get install -y git
sudo snap install code --classic

# Install Azure Data Studio
wget https://go.microsoft.com/fwlink/?linkid=2204802 -O azuredatastudio-linux.deb
sudo dpkg -i azuredatastudio-linux.deb
```

## 6. Project Clone and Setup

### 6.1 Clone Repository

```bash
# Clone project repository
git clone https://github.com/your-org/graphql-app.git
cd graphql-app

# Or initialize new repository
git init
```

### 6.2 Backend Setup

```bash
# Navigate to solution directory
cd d:\Development\graphql

# Restore NuGet packages
dotnet restore GraphQLApp.sln

# Build solution
dotnet build GraphQLApp.sln

# Expected output: Build succeeded. 0 Warning(s). 0 Error(s).
```

### 6.3 Database Setup

```bash
# Connect to SQL Server and run initialization script
sqlcmd -S 10.10.10.69 -U dev_user -P your_password -i scripts/sql/001-init-database.sql

# Or use SQL Server Management Studio to execute script manually
```

### 6.4 Configuration Setup

```bash
# Create local configuration file (gitignored)
cd src/GraphQLApp.API
cp appsettings.json appsettings.Local.json

# Edit appsettings.Local.json with your local settings
# - Update connection string
# - Set JWT secret key (min 32 characters)
```

**Example appsettings.Local.json:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=DevDatabase;User ID=dev_user;Password=YOUR_PASSWORD;TrustServerCertificate=True;"
  },
  "JwtSettings": {
    "SecretKey": "your-local-development-secret-key-min-32-chars-long-abcdefghijklmnop"
  }
}
```

### 6.5 Frontend Setup

```bash
# Navigate to frontend project
cd src/GraphQLApp.Web

# Install dependencies
npm install

# Or with pnpm (faster)
pnpm install

# Expected output: Dependencies installed successfully
```

### 6.6 Run Development Servers

**Terminal 1 - Backend API:**
```bash
cd src/GraphQLApp.API
dotnet run

# Expected output:
# info: Microsoft.Hosting.Lifetime[14]
#       Now listening on: https://localhost:5001
#       Application started. Press Ctrl+C to shut down.
```

**Terminal 2 - Frontend (after backend running):**
```bash
cd src/GraphQLApp.Web
npm run dev

# Expected output:
# VITE v5.x.x  ready in xxx ms
# ➜  Local:   http://localhost:5173/
# ➜  Network: use --host to expose
```

### 6.7 Verify Installation

**Test Backend API:**
1. Open browser to `https://localhost:5001/graphql`
2. Banana Cake Pop UI should load
3. Click "Schema Reference" to verify schema loaded

**Test Frontend:**
1. Open browser to `http://localhost:5173`
2. Login page should appear
3. Try logging in with test credentials (after seed data loaded)

## 7. Environment Variables

### 7.1 Backend Environment Variables

**Development (.env or appsettings.Local.json):**
```bash
ASPNETCORE_ENVIRONMENT=Development
ASPNETCORE_URLS=https://localhost:5001;http://localhost:5000
ConnectionStrings__DefaultConnection=Data Source=10.10.10.69;Initial Catalog=DevDatabase;...
JwtSettings__SecretKey=your-secret-key-here
```

**Production (IIS Environment Variables):**
```
ASPNETCORE_ENVIRONMENT=Production
ConnectionStrings__DefaultConnection=[from appsettings.Production.json]
JwtSettings__SecretKey=[from appsettings.Production.json]
```

### 7.2 Frontend Environment Variables

**Create `.env.local` in `src/GraphQLApp.Web/`:**
```bash
VITE_API_URL=https://localhost:5001
VITE_GRAPHQL_ENDPOINT=https://localhost:5001/graphql
```

**Production `.env.production`:**
```bash
VITE_API_URL=https://your-production-domain.com
VITE_GRAPHQL_ENDPOINT=https://your-production-domain.com/graphql
```

## 8. Common Development Commands

### 8.1 Backend Commands

```bash
# Restore packages
dotnet restore

# Build solution
dotnet build

# Run API (with hot reload)
dotnet watch run --project src/GraphQLApp.API

# Run tests
dotnet test

# Run tests with coverage
dotnet test /p:CollectCoverage=true /p:CoverageReportsFormat=html

# Publish for production
dotnet publish -c Release -o ./publish

# Create new migration (if using EF Core)
dotnet ef migrations add InitialCreate --project src/GraphQLApp.Infrastructure

# Apply migrations
dotnet ef database update --project src/GraphQLApp.Infrastructure

# Format code
dotnet format
```

### 8.2 Frontend Commands

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Lint code
npm run lint

# Format code (Prettier)
npm run format

# Type check (if using TypeScript)
npm run type-check
```

### 8.3 Database Commands

```bash
# Connect to SQL Server
sqlcmd -S 10.10.10.69 -U dev_user -P password

# Run SQL script
sqlcmd -S 10.10.10.69 -U dev_user -P password -i scripts/sql/001-init-database.sql

# Backup database
sqlcmd -Q "BACKUP DATABASE DevDatabase TO DISK='C:\Backups\dev_backup.bak'"

# Restore database
sqlcmd -Q "RESTORE DATABASE DevDatabase FROM DISK='C:\Backups\dev_backup.bak' WITH REPLACE"
```

## 9. Troubleshooting

### 9.1 Common Issues

**Issue 1: Port Already in Use**
```bash
# Error: Address already in use: https://localhost:5001

# Solution: Kill process using port
# Windows:
netstat -ano | findstr :5001
taskkill /PID <process_id> /F

# macOS/Linux:
lsof -i :5001
kill -9 <process_id>
```

**Issue 2: SQL Server Connection Failed**
```bash
# Error: Cannot connect to 10.10.10.69

# Solution 1: Check SQL Server is running
# Solution 2: Verify firewall allows port 1433
# Solution 3: Test with sqlcmd:
sqlcmd -S 10.10.10.69 -U dev_user -P password -Q "SELECT @@VERSION"

# Solution 4: Check connection string format:
# Data Source=10.10.10.69;Initial Catalog=DevDatabase;User ID=dev_user;Password=pass;TrustServerCertificate=True;
```

**Issue 3: NuGet Package Restore Failed**
```bash
# Error: Unable to find package...

# Solution: Clear NuGet cache
dotnet nuget locals all --clear

# Then restore again
dotnet restore
```

**Issue 4: Hot Reload Not Working**
```bash
# Issue: Code changes not reflected immediately

# Solution: Use dotnet watch
dotnet watch run --project src/GraphQLApp.API

# Or in VS Code: Press F5 (Run and Debug)
```

**Issue 5: HTTPS Certificate Errors**
```bash
# Error: The SSL connection could not be established

# Solution: Trust development certificate
dotnet dev-certs https --trust

# If still failing, regenerate certificate:
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

## 10. Code Quality Tools

### 10.1 .editorconfig

Create `.editorconfig` in project root for consistent formatting:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.{cs,csx,vb,vbx}]
indent_style = space
indent_size = 4

[*.{js,ts,vue,json,yaml,yml}]
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

### 10.2 .gitignore

Essential `.gitignore` entries:

```gitignore
## .NET
bin/
obj/
*.user
*.suo
*.userosscache
*.sln.docstates

## Visual Studio Code
.vscode/
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json

## Local configuration (IMPORTANT - DO NOT COMMIT)
appsettings.Local.json
appsettings.*.Local.json

## Node.js
node_modules/
npm-debug.log*
yarn-error.log*
.pnpm-debug.log*

## Environment variables
.env
.env.local
.env.production.local

## Logs
logs/
*.log

## Database
*.mdf
*.ldf

## Build outputs
publish/
dist/
```

## 11. Performance Optimization Tips

### 11.1 Backend

- Use `dotnet watch run` for hot reload during development
- Enable tiered compilation (default in .NET 8)
- Use Release build for performance testing
- Profile with dotnet-counters and dotnet-trace

### 11.2 Frontend

- Use Vite for fast HMR (Hot Module Replacement)
- Enable Vue Devtools browser extension
- Use production build for performance testing (`npm run build`)

### 11.3 Database

- Use SQL Server Profiler to monitor queries
- Enable query execution plans in SSMS
- Use indexes appropriately (already defined in schema)

## 12. Team Collaboration

### 12.1 Branch Strategy

```bash
# Main branch (production-ready code)
main

# Development branch (integration branch)
develop

# Feature branches
feature/user-authentication
feature/query-builder-ui
feature/database-schema

# Bugfix branches
bugfix/login-error
bugfix/query-timeout

# Hotfix branches (for production bugs)
hotfix/critical-security-fix
```

### 12.2 Commit Message Convention

```
type(scope): subject

body (optional)

footer (optional)

Types:
- feat: New feature
- fix: Bug fix
- docs: Documentation changes
- style: Code style changes (formatting)
- refactor: Code refactoring
- test: Adding or updating tests
- chore: Build process or auxiliary tool changes

Example:
feat(auth): implement JWT token generation

- Add JwtTokenService with token creation logic
- Configure JWT Bearer authentication middleware
- Add unit tests for token validation

Closes #123
```

## 13. Summary

### 13.1 Development Stack Installed

- ✅ .NET 8 SDK for backend development
- ✅ Node.js 20 LTS for frontend development
- ✅ SQL Server for database (or access to 10.10.10.69)
- ✅ Visual Studio Code with essential extensions
- ✅ Git for version control
- ✅ SQL Server Management Studio / Azure Data Studio

### 13.2 Project Ready Checklist

- [ ] .NET SDK 8.0.x installed and verified
- [ ] Node.js 20.x installed and verified
- [ ] SQL Server accessible (10.10.10.69 or local)
- [ ] Visual Studio Code installed with extensions
- [ ] Git configured with user name and email
- [ ] Project cloned and packages restored
- [ ] Database initialized with schema script
- [ ] appsettings.Local.json configured with connection string
- [ ] Backend runs successfully (https://localhost:5001/graphql)
- [ ] Frontend runs successfully (http://localhost:5173)
- [ ] Banana Cake Pop UI loads and displays schema

### 13.3 Next Steps

1. ✅ Development environment documented
2. ⏭️ Initialize Git repository and project structure (Task 6)
3. ⏭️ Begin Phase 1 implementation (Infrastructure and Architecture)

---

**Environment Specification Version:** 1.0
**Status:** ✅ READY FOR DEVELOPMENT
**Estimated Setup Time:** 1-2 hours for experienced developers
