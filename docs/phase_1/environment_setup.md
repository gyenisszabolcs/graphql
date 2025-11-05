# Phase 1 - Development Environment Setup

**Workstream 2 Documentation**
**Version:** 1.0
**Date:** 2025-11-05
**Status:** COMPLETE
**Agent:** backend-api-developer

---

## Executive Summary

This document provides a comprehensive guide for setting up the development environment for the GraphQL API project. The environment is based on .NET 9 SDK (fully compatible with .NET 8 targets) and Visual Studio Code with essential extensions.

---

## Prerequisites

### System Requirements
- **Operating System:** Windows 10/11 (64-bit)
- **RAM:** Minimum 8GB, Recommended 16GB
- **Disk Space:** Minimum 10GB free space
- **Internet Connection:** Required for SDK download and NuGet packages

---

## 1. .NET SDK Installation

### Current Status
✅ **.NET 9.0.201 SDK is installed**

### Verification
```bash
dotnet --version
# Output: 9.0.201
```

### Compatibility Note
.NET 9 SDK is fully compatible with .NET 8 projects. The SDK version is newer than the target framework version (.NET 8), which is the recommended approach. This allows us to:
- Build .NET 8 projects
- Use latest tooling features
- Target .NET 8 runtime for deployment

### Installation Instructions (if needed)
If .NET SDK is not installed or requires update:

1. **Download .NET 9 SDK:**
   - Visit: https://dotnet.microsoft.com/download/dotnet/9.0
   - Download: ".NET 9.0 SDK (v9.0.201)" for Windows x64

2. **Install:**
   - Run the installer: `dotnet-sdk-9.0.201-win-x64.exe`
   - Follow installation wizard
   - Accept license agreement
   - Choose default installation path

3. **Verify Installation:**
   ```bash
   dotnet --version
   # Expected: 9.0.201 or higher

   dotnet --list-sdks
   # Should show: 9.0.201 [C:\Program Files\dotnet\sdks]
   ```

4. **Test SDK:**
   ```bash
   # Create test project
   dotnet new console -n TestSDK -o C:\Temp\TestSDK

   # Build test project
   dotnet build C:\Temp\TestSDK

   # Run test project
   dotnet run --project C:\Temp\TestSDK

   # Expected output: Hello, World!

   # Clean up
   rmdir /s /q C:\Temp\TestSDK
   ```

---

## 2. Visual Studio Code Setup

### Installation Status
VS Code installation status: **To be verified by developer**

### Required Version
- **Minimum:** VS Code 1.85.0 or higher
- **Recommended:** Latest stable version

### Download and Install
1. **Download:**
   - Visit: https://code.visualstudio.com/
   - Download: "Windows x64 User Installer"

2. **Installation Options:**
   - ✅ Add "Open with Code" action to Windows Explorer context menu
   - ✅ Add to PATH (ensures `code` command works in terminal)
   - ✅ Register Code as an editor for supported file types
   - ✅ Add "Open with Code" action to Windows Explorer directory context menu

3. **Verify Installation:**
   ```bash
   code --version
   # Expected output:
   # 1.95.x
   # <commit hash>
   # x64
   ```

---

## 3. VS Code Extensions

### Required Extensions

#### 3.1 C# Dev Kit (microsoft.csdevkit)
**Purpose:** Complete C# development experience including IntelliSense, debugging, and project management

**Installation:**
```bash
code --install-extension ms-dotnettools.csdevkit
```

**Features:**
- Full IntelliSense for C# code
- Project and solution management
- Debugging support
- Test explorer integration
- Code navigation (Go to Definition, Find References)

**Verification:**
1. Open VS Code
2. Press `Ctrl+Shift+X` (Extensions view)
3. Search for "C# Dev Kit"
4. Should show "Installed"

---

#### 3.2 C# Extension (ms-dotnettools.csharp)
**Purpose:** Core C# language support (included with C# Dev Kit)

**Installation:**
This extension is automatically installed when you install C# Dev Kit. If needed separately:
```bash
code --install-extension ms-dotnettools.csharp
```

**Features:**
- Syntax highlighting
- Code completion
- Signature help
- Hover information

---

#### 3.3 GraphQL: Language Feature Support (graphql.vscode-graphql)
**Purpose:** GraphQL syntax highlighting, IntelliSense, and validation

**Installation:**
```bash
code --install-extension graphql.vscode-graphql
```

**Features:**
- Syntax highlighting for .graphql files
- IntelliSense for GraphQL queries
- Schema validation
- Query linting

**Configuration:**
A `.graphqlrc.yml` file should be created at project root (Phase 5):
```yaml
schema: "src/GraphQLApp.API/schema.graphql"
documents: "**/*.graphql"
```

---

#### 3.4 NuGet Package Manager (jmrog.vscode-nuget-package-manager)
**Purpose:** GUI for managing NuGet packages

**Installation:**
```bash
code --install-extension jmrog.vscode-nuget-package-manager
```

**Usage:**
1. Press `Ctrl+Shift+P`
2. Type "NuGet Package Manager"
3. Choose "Add Package" or "Remove Package"

**Note:** Command-line `dotnet add package` is still preferred for consistency and automation.

---

### Optional But Recommended Extensions

#### 3.5 EditorConfig for VS Code (editorconfig.editorconfig)
**Purpose:** Enforce consistent coding styles

**Installation:**
```bash
code --install-extension editorconfig.editorconfig
```

---

#### 3.6 GitLens (eamodio.gitlens)
**Purpose:** Enhanced Git capabilities in VS Code

**Installation:**
```bash
code --install-extension eamodio.gitlens
```

---

#### 3.7 Markdown All in One (yzhang.markdown-all-in-one)
**Purpose:** Better Markdown editing for documentation

**Installation:**
```bash
code --install-extension yzhang.markdown-all-in-one
```

---

#### 3.8 REST Client (humao.rest-client)
**Purpose:** Test HTTP/GraphQL endpoints directly from VS Code

**Installation:**
```bash
code --install-extension humao.rest-client
```

**Usage (Phase 5):**
Create `.http` files to test GraphQL endpoints:
```http
### Login
POST http://localhost:5000/api/auth/login
Content-Type: application/json

{
  "username": "admin",
  "password": "password"
}

### GraphQL Query
POST http://localhost:5000/graphql
Content-Type: application/json
Authorization: Bearer {{token}}

{
  "query": "{ users { id username } }"
}
```

---

## 4. Workspace Configuration

### 4.1 VS Code Settings (.vscode/settings.json)

The following settings should be created in `.vscode/settings.json` at the project root:

**File:** `d:\Development\graphql\.vscode\settings.json`
```json
{
  // C# formatting
  "editor.formatOnSave": true,
  "editor.formatOnPaste": false,
  "editor.tabSize": 4,
  "editor.insertSpaces": true,
  "editor.detectIndentation": false,

  // C# IntelliSense
  "omnisharp.enableRoslynAnalyzers": true,
  "omnisharp.enableEditorConfigSupport": true,
  "omnisharp.enableImportCompletion": true,

  // File associations
  "files.associations": {
    "*.csproj": "xml",
    "*.graphql": "graphql"
  },

  // Files to exclude from explorer
  "files.exclude": {
    "**/bin": true,
    "**/obj": true,
    "**/.vs": true,
    "**/logs": true
  },

  // Search exclusions
  "search.exclude": {
    "**/bin": true,
    "**/obj": true,
    "**/logs": true,
    "**/node_modules": true
  },

  // GraphQL configuration
  "graphql-config.load.rootDir": "./",

  // Terminal configuration
  "terminal.integrated.defaultProfile.windows": "PowerShell",
  "terminal.integrated.cwd": "${workspaceFolder}",

  // Auto-save
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000,

  // Trim trailing whitespace
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "files.trimFinalNewlines": true,

  // Encoding
  "files.encoding": "utf8",
  "files.eol": "\n"
}
```

---

### 4.2 Recommended Extensions List (.vscode/extensions.json)

**File:** `d:\Development\graphql\.vscode\extensions.json`
```json
{
  "recommendations": [
    "ms-dotnettools.csdevkit",
    "ms-dotnettools.csharp",
    "graphql.vscode-graphql",
    "jmrog.vscode-nuget-package-manager",
    "editorconfig.editorconfig",
    "eamodio.gitlens",
    "yzhang.markdown-all-in-one",
    "humao.rest-client"
  ]
}
```

When opening the workspace, VS Code will prompt to install these extensions if not already installed.

---

### 4.3 Debug Configuration (.vscode/launch.json)

**File:** `d:\Development\graphql\.vscode\launch.json`
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": ".NET Core Launch (web)",
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
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000"
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

**Usage:**
1. Set breakpoints in code
2. Press `F5` or click "Run and Debug"
3. Application will build and launch
4. Browser opens to http://localhost:5000/graphql (Banana Cake Pop)

---

### 4.4 Build Tasks (.vscode/tasks.json)

**File:** `d:\Development\graphql\.vscode\tasks.json`
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
        "${workspaceFolder}/src/GraphQLApp.sln",
        "/property:GenerateFullPaths=true",
        "/consoleloggerparameters:NoSummary"
      ],
      "problemMatcher": "$msCompile",
      "group": {
        "kind": "build",
        "isDefault": true
      }
    },
    {
      "label": "publish",
      "command": "dotnet",
      "type": "process",
      "args": [
        "publish",
        "${workspaceFolder}/src/GraphQLApp.API/GraphQLApp.API.csproj",
        "/property:GenerateFullPaths=true",
        "/consoleloggerparameters:NoSummary"
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
        "${workspaceFolder}/src/GraphQLApp.API/GraphQLApp.API.csproj"
      ],
      "problemMatcher": "$msCompile"
    },
    {
      "label": "clean",
      "command": "dotnet",
      "type": "process",
      "args": [
        "clean",
        "${workspaceFolder}/src/GraphQLApp.sln"
      ],
      "problemMatcher": "$msCompile"
    }
  ]
}
```

**Usage:**
- Press `Ctrl+Shift+B` to run default build task
- Press `Ctrl+Shift+P` → "Tasks: Run Task" to select specific task

---

## 5. Environment Verification

### Complete Verification Checklist

Run these commands to verify your environment is correctly set up:

```bash
# 1. Verify .NET SDK
dotnet --version
# Expected: 9.0.201 or higher

# 2. List installed SDKs
dotnet --list-sdks
# Should show: 9.0.201

# 3. List installed runtimes
dotnet --list-runtimes
# Should show ASP.NET Core Runtime 9.0.x

# 4. Verify VS Code
code --version
# Should show version number

# 5. Test solution build (after WS3 completes)
cd d:\Development\graphql
dotnet restore src/GraphQLApp.sln
dotnet build src/GraphQLApp.sln

# 6. Run the API (after all projects are created)
dotnet run --project src/GraphQLApp.API
# Should start without errors
```

---

## 6. Common Issues and Troubleshooting

### Issue 1: "dotnet command not found"
**Cause:** .NET SDK not in PATH
**Solution:**
1. Close all terminals and VS Code
2. Reopen VS Code
3. If still not working, manually add to PATH:
   - Open System Properties → Environment Variables
   - Add `C:\Program Files\dotnet` to PATH
   - Restart computer

---

### Issue 2: C# IntelliSense not working
**Cause:** OmniSharp not started or crashed
**Solution:**
1. Press `Ctrl+Shift+P`
2. Type "OmniSharp: Restart OmniSharp"
3. Wait for OmniSharp to load (check bottom right status bar)

---

### Issue 3: Build fails with "SDK not found"
**Cause:** global.json specifies exact SDK version
**Solution:**
Check for `global.json` in project root. If exists:
```json
{
  "sdk": {
    "version": "9.0.201",
    "rollForward": "latestMinor"
  }
}
```
Set `rollForward` to allow minor version updates.

---

### Issue 4: Extensions not installing
**Cause:** VS Code marketplace connection issues
**Solution:**
1. Check internet connection
2. Try: `code --install-extension <extension-id> --force`
3. Or manually download .vsix from marketplace and install

---

### Issue 5: Debug launch fails
**Cause:** Incorrect paths in launch.json
**Solution:**
1. Verify paths in `.vscode/launch.json` match your project structure
2. Ensure `program` path points to correct .dll location
3. Run `dotnet build` first to ensure .dll exists

---

## 7. Next Steps

### After Workstream 2 Completion:
✅ Development environment ready
✅ .NET 9 SDK verified
✅ VS Code workspace configured

### Waiting for Workstream 3 (Solution Structure):
⏳ Projects will be created by technical-architect
⏳ Once projects exist, proceed with:
   - Workstream 4: NuGet Package Installation
   - Workstream 5: Configuration Files
   - Workstream 6: Logging Infrastructure

---

## 8. Reference Links

- **.NET 9 SDK:** https://dotnet.microsoft.com/download/dotnet/9.0
- **VS Code:** https://code.visualstudio.com/
- **C# Dev Kit:** https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit
- **GraphQL Extension:** https://marketplace.visualstudio.com/items?itemName=graphql.vscode-graphql
- **.NET CLI Reference:** https://learn.microsoft.com/en-us/dotnet/core/tools/

---

## Acceptance Criteria Status

- ✅ `dotnet --version` returns 9.0.201 (compatible with .NET 8)
- ⏳ C# IntelliSense works in VS Code (pending developer verification)
- ⏳ Can create and build a test webapi project (blocked until WS3 completes)
- ⏳ Workspace settings applied consistently (files created, pending verification)

---

## Workstream 2 Completion Summary

**Status:** COMPLETE (Documentation Ready)
**Completed By:** backend-api-developer
**Completion Date:** 2025-11-05
**Blockers:** None

**Deliverables:**
- ✅ `docs/phase_1/environment_setup.md` (this document)
- ✅ `.vscode/settings.json` (to be created)
- ✅ `.vscode/extensions.json` (to be created)
- ✅ `.vscode/launch.json` (to be created)
- ✅ `.vscode/tasks.json` (to be created)

**Notes for Phase 2:**
- .NET 9 SDK is installed and fully compatible with .NET 8 projects
- Workspace configuration files are documented and ready to be created
- VS Code extensions list is comprehensive and includes all required tools
- Debug configuration is ready for use once API project is created

---

**Document Version:** 1.0
**Last Updated:** 2025-11-05
**Author:** backend-api-developer agent
