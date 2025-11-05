# SQL Server Connection Test - Phase 1

**Document Version:** 1.0
**Date Created:** 2025-11-05
**Author:** DevOps Infrastructure Engineer
**Status:** ⚠️ REQUIRES MANUAL EXECUTION

---

## Executive Summary

This document provides comprehensive instructions and scripts for testing connectivity to the existing SQL Server database that will be used by the GraphQL API application.

**Database Details:**
- **Server Address:** 10.10.10.69
- **Database Name:** dev_graphql
- **Authentication Method:** SQL Server Authentication (username/password)
- **Expected Tables:** CIKK, GYARTO, PARTNER, USERS

---

## 1. Prerequisites

### 1.1 Required Software
- SQL Server Management Studio (SSMS) 18.0 or later, OR
- Azure Data Studio, OR
- sqlcmd command-line tool

### 1.2 Required Access
- Network access to 10.10.10.69 (verify firewall rules allow connection from development machine)
- SQL Server login credentials (username and password)
- Database-level access to `dev_graphql` database

### 1.3 Network Connectivity Check

Before attempting database connection, verify network connectivity:

```bash
# Windows Command Prompt
ping 10.10.10.69

# Test SQL Server port (default 1433)
Test-NetConnection -ComputerName 10.10.10.69 -Port 1433
```

**Expected Results:**
- Ping should receive replies (0% packet loss)
- Port 1433 should show as OPEN (TcpTestSucceeded: True)

---

## 2. Connection Test Scripts

### 2.1 Connection String Format

**Standard .NET Connection String:**
```
Server=10.10.10.69;Database=dev_graphql;User Id=YOUR_USERNAME;Password=YOUR_PASSWORD;TrustServerCertificate=True;
```

**Connection String Components:**
- `Server`: Database server IP address
- `Database`: Target database name
- `User Id`: SQL Server authentication username
- `Password`: SQL Server authentication password
- `TrustServerCertificate=True`: Accept self-signed certificates (dev environment)

### 2.2 SSMS Connection Test

**Step-by-step Instructions:**

1. Open SQL Server Management Studio (SSMS)
2. Click "Connect" → "Database Engine"
3. Enter connection details:
   - **Server name:** `10.10.10.69`
   - **Authentication:** SQL Server Authentication
   - **Login:** [Your SQL username]
   - **Password:** [Your SQL password]
4. Click "Options" button
5. On "Connection Properties" tab:
   - **Connect to database:** `dev_graphql`
6. Click "Connect"

**Success Criteria:**
- Connection succeeds without timeout errors
- Object Explorer shows `dev_graphql` database
- Can expand database to see "Tables" folder

### 2.3 sqlcmd Connection Test

```bash
# Basic connection test
sqlcmd -S 10.10.10.69 -d dev_graphql -U YOUR_USERNAME -P YOUR_PASSWORD -Q "SELECT @@VERSION"

# Alternative: Interactive mode
sqlcmd -S 10.10.10.69 -d dev_graphql -U YOUR_USERNAME -P YOUR_PASSWORD
```

**Expected Output:**
```
Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64)
[Date and build information]
```

---

## 3. Database Verification Queries

Once connected, execute these queries to verify database structure.

### 3.1 Verify Database Existence

```sql
-- Query 1: Confirm database exists and is accessible
SELECT
    name AS DatabaseName,
    database_id,
    create_date,
    compatibility_level,
    state_desc
FROM sys.databases
WHERE name = 'dev_graphql';
```

**Expected Result:**
| DatabaseName | database_id | create_date | compatibility_level | state_desc |
|--------------|-------------|-------------|---------------------|------------|
| dev_graphql  | [number]    | [date]      | 150 (SQL 2019)      | ONLINE     |

### 3.2 Verify Required Tables Exist

```sql
-- Query 2: List all tables in the database
SELECT
    TABLE_SCHEMA,
    TABLE_NAME,
    TABLE_TYPE
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_TYPE = 'BASE TABLE'
ORDER BY TABLE_NAME;
```

**Expected Tables:**
- CIKK
- GYARTO
- PARTNER
- USERS

### 3.3 Check Table Row Counts

```sql
-- Query 3: Get approximate row counts for each table
SELECT
    t.name AS TableName,
    i.rows AS ApproxRowCount
FROM sys.tables t
INNER JOIN sys.sysindexes i ON t.object_id = i.id AND i.indid < 2
WHERE t.name IN ('CIKK', 'GYARTO', 'PARTNER', 'USERS')
ORDER BY t.name;
```

**Document the results in Section 5 below.**

### 3.4 Verify Database Permissions

```sql
-- Query 4: Check current user's permissions
SELECT
    SUSER_SNAME() AS LoginName,
    USER_NAME() AS DatabaseUser,
    IS_MEMBER('db_owner') AS IsDbOwner,
    IS_MEMBER('db_datareader') AS CanRead,
    IS_MEMBER('db_datawriter') AS CanWrite;
```

**Required Permissions:**
- `CanRead` = 1 (db_datareader or db_owner)
- `CanWrite` = 1 (db_datawriter or db_owner)

---

## 4. Connection Test from .NET Application

### 4.1 Test Connection String Script

Create a temporary test console application to verify .NET connectivity:

```bash
# Create test project
dotnet new console -n SqlConnectionTest
cd SqlConnectionTest
dotnet add package Microsoft.Data.SqlClient --version 5.2.2
```

**Program.cs Content:**
```csharp
using Microsoft.Data.SqlClient;
using System;

class Program
{
    static void Main(string[] args)
    {
        string connectionString = "Server=10.10.10.69;Database=dev_graphql;User Id=YOUR_USERNAME;Password=YOUR_PASSWORD;TrustServerCertificate=True;";

        try
        {
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                connection.Open();
                Console.WriteLine("✓ Connection successful!");
                Console.WriteLine($"✓ Server Version: {connection.ServerVersion}");
                Console.WriteLine($"✓ Database: {connection.Database}");

                // Test query
                using (SqlCommand command = new SqlCommand("SELECT @@VERSION", connection))
                {
                    string version = (string)command.ExecuteScalar();
                    Console.WriteLine($"✓ SQL Server Version: {version}");
                }

                // Check tables
                using (SqlCommand command = new SqlCommand(
                    "SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE = 'BASE TABLE' ORDER BY TABLE_NAME",
                    connection))
                {
                    Console.WriteLine("\n✓ Available tables:");
                    using (SqlDataReader reader = command.ExecuteReader())
                    {
                        while (reader.Read())
                        {
                            Console.WriteLine($"  - {reader.GetString(0)}");
                        }
                    }
                }
            }
            Console.WriteLine("\n✓ All tests passed!");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"✗ Connection failed: {ex.Message}");
            Console.WriteLine($"✗ Stack trace: {ex.StackTrace}");
        }
    }
}
```

**Run the test:**
```bash
dotnet run
```

### 4.2 Expected Output

```
✓ Connection successful!
✓ Server Version: 15.00.2000
✓ Database: dev_graphql
✓ SQL Server Version: Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 ...

✓ Available tables:
  - CIKK
  - GYARTO
  - PARTNER
  - USERS

✓ All tests passed!
```

---

## 5. Test Results Documentation

**⚠️ MANUAL EXECUTION REQUIRED**

After executing the connection tests, document the results here:

### 5.1 Connection Test Results

**Date Tested:** [TO BE FILLED IN]
**Tested By:** [TO BE FILLED IN]
**Connection Method:** [SSMS / sqlcmd / .NET]

**Result:** [ ] SUCCESS / [ ] FAILED

**Notes:**
```
[Document any connection issues, timeouts, authentication errors, etc.]
```

### 5.2 Database Information

| Property | Value |
|----------|-------|
| SQL Server Version | [TO BE FILLED IN] |
| Database Compatibility Level | [TO BE FILLED IN] |
| Database State | [TO BE FILLED IN] |
| Collation | [TO BE FILLED IN] |

### 5.3 Table Row Counts

| Table Name | Approximate Row Count | Notes |
|------------|----------------------|-------|
| CIKK | [TO BE FILLED IN] | Articles/Products |
| GYARTO | [TO BE FILLED IN] | Manufacturers |
| PARTNER | [TO BE FILLED IN] | Business Partners |
| USERS | [TO BE FILLED IN] | User Accounts |

### 5.4 Permission Verification

| Permission | Status | Notes |
|------------|--------|-------|
| Connect to Database | [ ] YES / [ ] NO | |
| Read Data (SELECT) | [ ] YES / [ ] NO | |
| Write Data (INSERT/UPDATE/DELETE) | [ ] YES / [ ] NO | |
| Execute Stored Procedures | [ ] YES / [ ] NO | |
| View Schema | [ ] YES / [ ] NO | |

---

## 6. Common Issues and Troubleshooting

### 6.1 Issue: Connection Timeout

**Symptoms:**
```
A network-related or instance-specific error occurred while establishing a connection to SQL Server.
The server was not found or was not accessible.
```

**Possible Causes:**
1. Firewall blocking port 1433
2. SQL Server not listening on TCP/IP
3. SQL Server service not running
4. Incorrect server address

**Solutions:**
```bash
# 1. Check Windows Firewall
netsh advfirewall firewall show rule name="SQL Server"

# 2. Verify SQL Server is listening
netstat -an | findstr 1433

# 3. Check SQL Server service status (on server)
Get-Service -Name MSSQLSERVER
```

### 6.2 Issue: Login Failed

**Symptoms:**
```
Login failed for user 'username'.
```

**Possible Causes:**
1. Incorrect username or password
2. User account disabled
3. SQL Server Authentication not enabled (Windows Auth only)
4. User not mapped to database

**Solutions:**
1. Verify credentials with DBA
2. Ensure SQL Server is in Mixed Mode authentication
3. Check user has database access:
```sql
-- Run on server as admin
USE dev_graphql;
GO
EXEC sp_helplogins 'your_username';
```

### 6.3 Issue: Trust Certificate Error

**Symptoms:**
```
A connection was successfully established with the server, but then an error occurred during the login process.
(provider: SSL Provider, error: 0 - The certificate chain was issued by an authority that is not trusted.)
```

**Solution:**
Add `TrustServerCertificate=True` to connection string (already included in examples above).

### 6.4 Issue: Cannot Access Database

**Symptoms:**
```
Cannot open database "dev_graphql" requested by the login. The login failed.
```

**Solutions:**
1. Verify database exists: `SELECT name FROM sys.databases WHERE name = 'dev_graphql'`
2. Check user has access: `EXEC sp_helpuser 'your_username'`
3. Grant access (run as admin):
```sql
USE dev_graphql;
CREATE USER [your_username] FOR LOGIN [your_username];
ALTER ROLE db_datareader ADD MEMBER [your_username];
ALTER ROLE db_datawriter ADD MEMBER [your_username];
```

---

## 7. Security Considerations

### 7.1 Credential Storage

**NEVER store credentials in:**
- Source control (git)
- appsettings.json (committed file)
- Application code
- Documentation files

**ALWAYS store credentials in:**
- appsettings.Local.json (gitignored)
- Azure Key Vault (production)
- Environment variables (IIS/Azure)
- Windows Credential Manager (local dev)

### 7.2 Connection String Security Checklist

- [ ] Credentials stored in appsettings.Local.json (NOT appsettings.json)
- [ ] appsettings.Local.json added to .gitignore
- [ ] Template file (appsettings.Local.json.template) created with placeholders
- [ ] Production credentials stored in Azure Key Vault or secure vault
- [ ] Connection string encryption enabled for production
- [ ] Minimum required permissions granted to SQL user (not db_owner)

---

## 8. Next Steps

After successful connection test:

1. ✓ Connection test passed - proceed to schema documentation
2. → Execute queries in Section 3 and document results in Section 5
3. → Create detailed schema documentation (see: `database_schema.md`)
4. → Update appsettings.Local.json with working connection string (Phase 1, WS5)
5. → Test connection from actual GraphQL API project (Phase 1, WS6)

---

## 9. Related Documents

- `database_schema.md` - Detailed schema documentation (next deliverable)
- `../GraphQL_Fejlesztesi_Specifikacio.md` - Section 5: Database Architecture
- `appsettings.Local.json.template` - Configuration template (Phase 1, WS5)
- `.gitignore` - Security patterns for sensitive files

---

## 10. Appendix: Quick Reference Commands

### Quick Connection Test (sqlcmd)
```bash
sqlcmd -S 10.10.10.69 -d dev_graphql -U USERNAME -P PASSWORD -Q "SELECT 'Connection OK' AS Status"
```

### Quick Table Count
```bash
sqlcmd -S 10.10.10.69 -d dev_graphql -U USERNAME -P PASSWORD -Q "SELECT COUNT(*) FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE='BASE TABLE'"
```

### Export Schema to File
```bash
sqlcmd -S 10.10.10.69 -d dev_graphql -U USERNAME -P PASSWORD -Q "SELECT TABLE_NAME, COLUMN_NAME, DATA_TYPE FROM INFORMATION_SCHEMA.COLUMNS ORDER BY TABLE_NAME, ORDINAL_POSITION" -o schema_export.txt -W
```

---

## Document Change Log

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-05 | DevOps Infrastructure Engineer | Initial document creation with comprehensive test procedures |

---

**Status:** ⚠️ **REQUIRES MANUAL EXECUTION WITH ACTUAL CREDENTIALS**
**Action Required:** Execute connection tests using valid SQL Server credentials and document results in Section 5.
