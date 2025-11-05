# Phase 1 - Logging Infrastructure

**Workstream 6 Documentation**
**Version:** 1.0
**Date:** 2025-11-05
**Status:** READY TO EXECUTE (Blocked by WS4 + WS5)
**Agent:** backend-api-developer

---

## Executive Summary

This document details the Serilog-based structured logging infrastructure for the GraphQL API application. The system provides console and file logging with structured data capture for debugging and monitoring.

**BLOCKERS:**
- Workstream 4 (Serilog NuGet packages) must complete first
- Workstream 5 (appsettings.json configuration) must complete first

---

## Logging Architecture

### Serilog Overview
Serilog is a structured logging library that captures log events as data rather than text. This enables:
- Powerful querying and filtering
- Log aggregation and analysis
- Integration with monitoring tools
- Better debugging and troubleshooting

### Sinks Configured
1. **Console Sink** - Development visibility
2. **File Sink** - Persistent storage with rolling files

---

## 1. Program.cs Configuration

**File:** `d:\Development\graphql\src\GraphQLApp.API\Program.cs`

### Complete Implementation

```csharp
using Serilog;
using Serilog.Events;

var builder = WebApplication.CreateBuilder(args);

// ============================================
// SERILOG CONFIGURATION
// ============================================
builder.Host.UseSerilog((context, services, configuration) => configuration
    .ReadFrom.Configuration(context.Configuration)
    .ReadFrom.Services(services)
    .Enrich.FromLogContext()
    .Enrich.WithMachineName()
    .Enrich.WithThreadId()
    .WriteTo.Console(
        outputTemplate: "[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj} {Properties:j}{NewLine}{Exception}"
    )
    .WriteTo.File(
        path: "logs/log-.txt",
        rollingInterval: RollingInterval.Day,
        retainedFileCountLimit: 7,
        fileSizeLimitBytes: 104857600, // 100 MB
        rollOnFileSizeLimit: true,
        outputTemplate: "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level:u3}] {Message:lj} {Properties:j}{NewLine}{Exception}"
    )
);

// Add services to the container.
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// ============================================
// SERILOG REQUEST LOGGING
// ============================================
app.UseSerilogRequestLogging(options =>
{
    options.MessageTemplate = "HTTP {RequestMethod} {RequestPath} responded {StatusCode} in {Elapsed:0.0000} ms";
    options.GetLevel = (httpContext, elapsed, ex) => ex != null
        ? LogEventLevel.Error
        : elapsed > 5000 ? LogEventLevel.Warning : LogEventLevel.Information;
    options.EnrichDiagnosticContext = (diagnosticContext, httpContext) =>
    {
        diagnosticContext.Set("RequestHost", httpContext.Request.Host.Value);
        diagnosticContext.Set("RequestScheme", httpContext.Request.Scheme);
        diagnosticContext.Set("UserAgent", httpContext.Request.Headers["User-Agent"].ToString());
    };
});

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

// ============================================
// STARTUP LOGGING
// ============================================
try
{
    Log.Information("Starting GraphQL API application");
    Log.Information("Environment: {Environment}", app.Environment.EnvironmentName);
    Log.Information("Application Version: {Version}", typeof(Program).Assembly.GetName().Version);
    app.Run();
}
catch (Exception ex)
{
    Log.Fatal(ex, "Application terminated unexpectedly");
}
finally
{
    Log.CloseAndFlush();
}
```

---

## 2. Configuration Breakdown

### UseSerilog() Host Builder Configuration

```csharp
builder.Host.UseSerilog((context, services, configuration) => configuration
    // Read settings from appsettings.json
    .ReadFrom.Configuration(context.Configuration)

    // Enable dependency injection for sinks
    .ReadFrom.Services(services)

    // Add contextual information
    .Enrich.FromLogContext()
    .Enrich.WithMachineName()
    .Enrich.WithThreadId()

    // Console sink for development
    .WriteTo.Console(...)

    // File sink for persistent storage
    .WriteTo.File(...)
);
```

---

### Console Sink Configuration

```csharp
.WriteTo.Console(
    outputTemplate: "[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj} {Properties:j}{NewLine}{Exception}"
)
```

**Output Example:**
```
[14:23:45 INF] Starting GraphQL API application
[14:23:45 INF] Environment: Development
[14:23:46 INF] HTTP GET /graphql responded 200 in 45.2341 ms
[14:23:50 ERR] Database connection failed: Login failed for user 'sa'
```

**Template Tokens:**
- `{Timestamp:HH:mm:ss}` - Time only (no date for console brevity)
- `{Level:u3}` - Log level (INF, WRN, ERR, etc.)
- `{Message:lj}` - Log message (with string formatting)
- `{Properties:j}` - Structured properties as JSON
- `{Exception}` - Exception details if present

---

### File Sink Configuration

```csharp
.WriteTo.File(
    path: "logs/log-.txt",
    rollingInterval: RollingInterval.Day,
    retainedFileCountLimit: 7,
    fileSizeLimitBytes: 104857600, // 100 MB
    rollOnFileSizeLimit: true,
    outputTemplate: "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level:u3}] {Message:lj} {Properties:j}{NewLine}{Exception}"
)
```

**Parameters:**
- `path: "logs/log-.txt"` - File path pattern
  - Creates: `logs/log-20251105.txt`
  - Creates: `logs/log-20251106.txt` (next day)

- `rollingInterval: RollingInterval.Day` - New file every day at midnight

- `retainedFileCountLimit: 7` - Keep last 7 days, delete older

- `fileSizeLimitBytes: 104857600` - 100 MB per file

- `rollOnFileSizeLimit: true` - If file exceeds 100MB, create:
  - `log-20251105.txt`
  - `log-20251105_001.txt`
  - `log-20251105_002.txt`

**Output Example:**
```
2025-11-05 14:23:45.123 +01:00 [INF] Starting GraphQL API application
2025-11-05 14:23:45.456 +01:00 [INF] Environment: Development
2025-11-05 14:23:46.789 +01:00 [INF] HTTP GET /graphql responded 200 in 45.2341 ms {"RequestHost":"localhost:5000","RequestScheme":"http","UserAgent":"Mozilla/5.0..."}
```

---

### Request Logging Middleware

```csharp
app.UseSerilogRequestLogging(options =>
{
    // Custom message template
    options.MessageTemplate = "HTTP {RequestMethod} {RequestPath} responded {StatusCode} in {Elapsed:0.0000} ms";

    // Dynamic log level based on response
    options.GetLevel = (httpContext, elapsed, ex) =>
        ex != null ? LogEventLevel.Error          // Errors → ERR
        : elapsed > 5000 ? LogEventLevel.Warning  // Slow requests → WRN
        : LogEventLevel.Information;              // Normal → INF

    // Add custom properties to logs
    options.EnrichDiagnosticContext = (diagnosticContext, httpContext) =>
    {
        diagnosticContext.Set("RequestHost", httpContext.Request.Host.Value);
        diagnosticContext.Set("RequestScheme", httpContext.Request.Scheme);
        diagnosticContext.Set("UserAgent", httpContext.Request.Headers["User-Agent"].ToString());
    };
});
```

**Features:**
- Automatically logs all HTTP requests
- Includes request method, path, status code, duration
- Dynamic log level (errors and slow requests get higher priority)
- Custom properties (host, scheme, user agent)

---

## 3. Structured Logging Examples

### Basic Logging

```csharp
using Microsoft.Extensions.Logging;

public class UserService
{
    private readonly ILogger<UserService> _logger;

    public UserService(ILogger<UserService> logger)
    {
        _logger = logger;
    }

    public async Task<User> GetUserById(int userId)
    {
        _logger.LogInformation("Fetching user with ID: {UserId}", userId);

        try
        {
            var user = await _repository.GetByIdAsync(userId);

            if (user == null)
            {
                _logger.LogWarning("User not found: {UserId}", userId);
                return null;
            }

            _logger.LogInformation("User fetched successfully: {UserId}, {Username}", user.Id, user.Username);
            return user;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Failed to fetch user: {UserId}", userId);
            throw;
        }
    }
}
```

**Console Output:**
```
[14:30:12 INF] Fetching user with ID: 42 {"UserId":42}
[14:30:12 INF] User fetched successfully: 42, johndoe {"UserId":42,"Username":"johndoe"}
```

**File Output:**
```
2025-11-05 14:30:12.345 +01:00 [INF] Fetching user with ID: 42 {"UserId":42,"SourceContext":"UserService"}
2025-11-05 14:30:12.678 +01:00 [INF] User fetched successfully: 42, johndoe {"UserId":42,"Username":"johndoe","SourceContext":"UserService"}
```

---

### Log Levels

```csharp
// VERBOSE - Very detailed, rarely enabled
_logger.LogTrace("Entering method GetUserById");

// DEBUG - Detailed information for debugging
_logger.LogDebug("Query executed: {Query}", sqlQuery);

// INFORMATION - General informational messages
_logger.LogInformation("User logged in: {Username}", username);

// WARNING - Unexpected but non-critical issues
_logger.LogWarning("API rate limit approaching: {UsagePercent}%", usagePercent);

// ERROR - Error events, but application continues
_logger.LogError(ex, "Failed to send email to {Email}", email);

// CRITICAL - Critical failures, application may crash
_logger.LogCritical(ex, "Database connection lost");
```

---

### Structured Properties

```csharp
// Simple properties
_logger.LogInformation("User {UserId} viewed product {ProductId}", userId, productId);

// Complex objects (serialized to JSON)
_logger.LogInformation("Order created: {@Order}", order);
// Output: {"Order":{"Id":123,"Total":99.99,"Items":[...]}}

// Destructuring operator (@)
// Use @ to serialize entire object, not just ToString()
_logger.LogInformation("User object: {@User}", user);

// DO NOT do this (logs "User.ToString()")
_logger.LogInformation("User object: {User}", user);
```

---

### Scopes for Contextual Logging

```csharp
public async Task ProcessOrder(int orderId)
{
    using (_logger.BeginScope("OrderId:{OrderId}", orderId))
    {
        _logger.LogInformation("Processing order");
        // All logs in this scope will include OrderId

        await ValidateOrder();
        // Logs: Processing order {"OrderId":123}

        await ChargePayment();
        // Logs: Payment charged {"OrderId":123}

        await ShipOrder();
        // Logs: Order shipped {"OrderId":123}
    }
}
```

---

### Exception Logging

```csharp
try
{
    await riskyOperation();
}
catch (SqlException ex)
{
    _logger.LogError(ex, "Database error during operation: {Operation}", operationName);
    // Logs full exception with stack trace
}
catch (Exception ex)
{
    _logger.LogCritical(ex, "Unexpected error: {ErrorMessage}", ex.Message);
    throw; // Re-throw after logging
}
```

**Output:**
```
2025-11-05 14:35:22.123 +01:00 [ERR] Database error during operation: SaveUser
System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion...
   at GraphQLApp.Infrastructure.Repositories.UserRepository.SaveAsync(User user)
   at GraphQLApp.Services.UserService.CreateUser(CreateUserInput input)
```

---

## 4. Log Management

### Rolling Interval Options
```csharp
RollingInterval.Day      // New file every day (default, recommended)
RollingInterval.Hour     // New file every hour (high-volume apps)
RollingInterval.Month    // New file every month (low-volume apps)
RollingInterval.Year     // New file every year
RollingInterval.Infinite // Never roll (single file, not recommended)
```

---

### Retention Policy
```csharp
retainedFileCountLimit: 7  // Keep last 7 days
retainedFileCountLimit: 30 // Keep last 30 days
retainedFileCountLimit: null // Keep all files (dangerous!)
```

**Recommendation:** 7 days for development, 30-90 days for production

---

### File Size Management
```csharp
fileSizeLimitBytes: 104857600, // 100 MB
rollOnFileSizeLimit: true      // Create new file when limit reached
```

**Without rollOnFileSizeLimit:**
- File grows to 100MB, then stops logging (dangerous!)

**With rollOnFileSizeLimit:**
- Creates `log-20251105.txt` (100 MB)
- Creates `log-20251105_001.txt` (100 MB)
- Creates `log-20251105_002.txt` (continues...)

---

### Log Directory Structure
```
logs/
├── log-20251101.txt          (deleted after 7 days)
├── log-20251102.txt          (deleted after 7 days)
├── log-20251103.txt
├── log-20251104.txt
├── log-20251105.txt          (today)
├── log-20251105_001.txt      (today, rolled due to size)
└── log-20251105_002.txt      (today, rolled due to size)
```

---

## 5. Best Practices

### DO:

✅ **Use structured logging (not string concatenation)**
```csharp
// GOOD
_logger.LogInformation("User {UserId} logged in at {LoginTime}", userId, DateTime.UtcNow);

// BAD
_logger.LogInformation($"User {userId} logged in at {DateTime.UtcNow}");
```

---

✅ **Use appropriate log levels**
```csharp
// Information: Normal operations
_logger.LogInformation("Application started");

// Warning: Unexpected but handled
_logger.LogWarning("Cache miss for key {Key}", key);

// Error: Operation failed but app continues
_logger.LogError(ex, "Failed to send email");

// Critical: System failure
_logger.LogCritical(ex, "Database unreachable");
```

---

✅ **Include context in log messages**
```csharp
// GOOD
_logger.LogError(ex, "Failed to delete user {UserId} from database {Database}", userId, dbName);

// BAD
_logger.LogError(ex, "Failed to delete user");
```

---

✅ **Use scopes for request tracking**
```csharp
using (_logger.BeginScope("CorrelationId:{CorrelationId}", correlationId))
{
    // All logs in this scope include CorrelationId
}
```

---

### DON'T:

❌ **Don't log sensitive data**
```csharp
// BAD
_logger.LogInformation("User logged in with password: {Password}", password);

// GOOD
_logger.LogInformation("User {UserId} logged in successfully", userId);
```

---

❌ **Don't log in tight loops**
```csharp
// BAD
foreach (var item in items)
{
    _logger.LogDebug("Processing item {ItemId}", item.Id); // Thousands of logs!
}

// GOOD
_logger.LogInformation("Processing {ItemCount} items", items.Count);
```

---

❌ **Don't log full objects without @**
```csharp
// BAD - logs "User.ToString()"
_logger.LogInformation("User: {User}", user);

// GOOD - logs full object structure
_logger.LogInformation("User: {@User}", user);
```

---

❌ **Don't swallow exceptions without logging**
```csharp
// BAD
try
{
    await operation();
}
catch { } // Silent failure!

// GOOD
catch (Exception ex)
{
    _logger.LogError(ex, "Operation failed");
    throw; // Or handle appropriately
}
```

---

## 6. Monitoring and Alerting

### Log Analysis Queries

**Find all errors in last 24 hours:**
```bash
# Using grep
grep "\[ERR\]" logs/log-20251105.txt

# Using PowerShell
Select-String -Path "logs\log-20251105.txt" -Pattern "\[ERR\]"
```

---

**Find slow requests (>5 seconds):**
```bash
grep "responded.*in [5-9][0-9][0-9][0-9]" logs/log-20251105.txt
```

---

**Count log entries by level:**
```bash
# PowerShell
Get-Content logs\log-20251105.txt | Select-String -Pattern "\[(INF|WRN|ERR)\]" -AllMatches | Group-Object { $_.Matches.Value }
```

---

### Integration with Monitoring Tools

**Seq (Structured Log Server):**
```csharp
// Add NuGet: Serilog.Sinks.Seq
.WriteTo.Seq("http://localhost:5341")
```

**Elasticsearch + Kibana:**
```csharp
// Add NuGet: Serilog.Sinks.Elasticsearch
.WriteTo.Elasticsearch(new ElasticsearchSinkOptions(new Uri("http://localhost:9200")))
```

**Application Insights (Azure):**
```csharp
// Add NuGet: Serilog.Sinks.ApplicationInsights
.WriteTo.ApplicationInsights(telemetryConfiguration, TelemetryConverter.Traces)
```

---

## 7. Testing Logging

### Manual Testing

```bash
# Run application
cd d:\Development\graphql
dotnet run --project src\GraphQLApp.API

# Make requests
curl http://localhost:5000/weatherforecast

# Check console output
# Expected: [HH:mm:ss INF] HTTP GET /weatherforecast responded 200 in X.XXXX ms

# Check log files
ls logs\
# Expected: log-20251105.txt

# View log file
cat logs\log-20251105.txt
# or
notepad logs\log-20251105.txt
```

---

### Unit Testing Logging

```csharp
using Microsoft.Extensions.Logging;
using Moq;
using Xunit;

public class UserServiceTests
{
    [Fact]
    public async Task GetUserById_LogsInformation()
    {
        // Arrange
        var mockLogger = new Mock<ILogger<UserService>>();
        var service = new UserService(mockLogger.Object);

        // Act
        await service.GetUserById(42);

        // Assert
        mockLogger.Verify(
            x => x.Log(
                LogLevel.Information,
                It.IsAny<EventId>(),
                It.Is<It.IsAnyType>((v, t) => v.ToString().Contains("Fetching user")),
                null,
                It.IsAny<Func<It.IsAnyType, Exception, string>>()
            ),
            Times.Once
        );
    }
}
```

---

## 8. Troubleshooting

### Issue: Logs not appearing in console
**Solution:**
1. Check log level: `"MinimumLevel": { "Default": "Information" }`
2. Check if UseSerilog() is called in Program.cs
3. Check if Serilog.AspNetCore package is installed
4. Restart application

---

### Issue: Log files not created
**Solution:**
1. Check logs/ directory exists and is writable
2. Check path in WriteTo.File: `"path": "logs/log-.txt"`
3. Check file permissions (Windows: right-click logs/ → Properties → Security)
4. Check disk space

---

### Issue: Log files too large
**Solution:**
1. Reduce retention: `retainedFileCountLimit: 3`
2. Reduce file size limit: `fileSizeLimitBytes: 52428800` (50 MB)
3. Increase log level: `"MinimumLevel": { "Default": "Warning" }`
4. Set up log rotation/archival script

---

### Issue: Structured properties not appearing
**Solution:**
1. Use @ operator: `_logger.LogInformation("User: {@User}", user)`
2. Check output template includes `{Properties:j}`
3. Verify object is serializable (not circular references)

---

## 9. Next Steps

### After Workstream 6 Completion:
✅ Logging infrastructure operational
✅ Console and file logging working
✅ Structured logging documented

### Enables:
→ Phase 2: Log database operations and errors
→ Phase 3: Log API requests and responses
→ Phase 4: Log authentication events
→ Phase 5: Log GraphQL query execution
→ Phase 9: Production monitoring and alerting

---

## 10. Acceptance Criteria

- ✅ Serilog configured in Program.cs
- ✅ UseSerilog() called in host builder
- ✅ UseSerilogRequestLogging() middleware added
- ✅ Console logging verified (run app, see logs)
- ✅ File logging verified (logs/ folder, log file created)
- ✅ Log file naming correct (log-YYYYMMDD.txt)
- ✅ Rolling interval set to Daily
- ✅ Retention set to 7 days
- ✅ Log format includes timestamp, level, message
- ✅ Structured logging examples documented

---

## Workstream 6 Status

**Status:** READY TO EXECUTE (waiting for WS4 + WS5)
**Blockers:**
- Workstream 4 (Serilog packages must be installed)
- Workstream 5 (appsettings.json must be created)
**Estimated Execution Time:** 2-3 hours once unblocked
**Prepared By:** backend-api-developer
**Date:** 2025-11-05

---

**Document Version:** 1.0
**Last Updated:** 2025-11-05
**Author:** backend-api-developer agent
