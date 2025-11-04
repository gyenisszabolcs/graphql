# Phase 0 - Task 4: Database Schema Design

**Agent Role:** Technical Architect
**Date:** 2025-11-04
**Status:** ✅ COMPLETED

## 1. Entity Relationship Diagram (ERD)

```
┌──────────────────────────────────────────────────────────────────┐
│                          users                                    │
├──────────────────────────────────────────────────────────────────┤
│ PK │ Id              INT IDENTITY(1,1)                           │
│    │ Username        NVARCHAR(100)     UNIQUE NOT NULL           │
│    │ PasswordHash    NVARCHAR(255)     NOT NULL                  │
│    │ Email           NVARCHAR(255)     NULL                      │
│    │ FullName        NVARCHAR(200)     NULL                      │
│    │ IsActive        BIT               NOT NULL DEFAULT 1        │
│    │ CreatedAt       DATETIME2         NOT NULL DEFAULT GETDATE()│
│    │ UpdatedAt       DATETIME2         NULL                      │
└──────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                         gyartok                                   │
├──────────────────────────────────────────────────────────────────┤
│ PK │ Id              INT IDENTITY(1,1)                           │
│    │ GyartoNev       NVARCHAR(200)     NOT NULL                  │
│    │ Orszag          NVARCHAR(100)     NULL                      │
│    │ ContactEmail    NVARCHAR(255)     NULL                      │
│    │ ContactPhone    NVARCHAR(50)      NULL                      │
│    │ CreatedAt       DATETIME2         NOT NULL DEFAULT GETDATE()│
│    │ UpdatedAt       DATETIME2         NULL                      │
└──────────────────────────────────────────────────────────────────┘
                              │
                              │ 1
                              │
                              ▼ N
┌──────────────────────────────────────────────────────────────────┐
│                          cikkek                                   │
├──────────────────────────────────────────────────────────────────┤
│ PK │ Id              INT IDENTITY(1,1)                           │
│    │ CikkKod         NVARCHAR(50)      UNIQUE NOT NULL           │
│    │ Megnevezes      NVARCHAR(500)     NOT NULL                  │
│    │ Leiras          NVARCHAR(MAX)     NULL                      │
│    │ EgysegAr        DECIMAL(18,2)     NOT NULL                  │
│    │ MennyisegiEgyseg NVARCHAR(20)     NULL                      │
│ FK │ GyartoId        INT               NULL → gyartok(Id)        │
│    │ CreatedAt       DATETIME2         NOT NULL DEFAULT GETDATE()│
│    │ UpdatedAt       DATETIME2         NULL                      │
└──────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                        partnerek                                  │
├──────────────────────────────────────────────────────────────────┤
│ PK │ Id              INT IDENTITY(1,1)                           │
│    │ PartnerNev      NVARCHAR(200)     NOT NULL                  │
│    │ AdoSzam         NVARCHAR(50)      NULL                      │
│    │ Cim             NVARCHAR(500)     NULL                      │
│    │ ContactPerson   NVARCHAR(200)     NULL                      │
│    │ Email           NVARCHAR(255)     NULL                      │
│    │ Phone           NVARCHAR(50)      NULL                      │
│    │ PartnerTipus    NVARCHAR(50)      NULL                      │
│    │ CreatedAt       DATETIME2         NOT NULL DEFAULT GETDATE()│
│    │ UpdatedAt       DATETIME2         NULL                      │
└──────────────────────────────────────────────────────────────────┘
```

## 2. Table Definitions (SQL Scripts)

### 2.1 users Table

```sql
CREATE TABLE users (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Username NVARCHAR(100) NOT NULL,
    PasswordHash NVARCHAR(255) NOT NULL,
    Email NVARCHAR(255) NULL,
    FullName NVARCHAR(200) NULL,
    IsActive BIT NOT NULL DEFAULT 1,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL,

    CONSTRAINT UQ_users_Username UNIQUE (Username)
);

-- Index for authentication queries
CREATE INDEX IX_users_Username ON users(Username);
CREATE INDEX IX_users_IsActive ON users(IsActive) WHERE IsActive = 1;
```

**Field Descriptions:**
- `Id`: Auto-incrementing primary key
- `Username`: Unique login identifier (case-insensitive comparison via COLLATION)
- `PasswordHash`: BCrypt hashed password (never store plaintext)
- `Email`: Optional email for future password reset functionality
- `FullName`: Display name for UI
- `IsActive`: Soft delete flag (0 = disabled user)
- `CreatedAt`: Audit timestamp (creation)
- `UpdatedAt`: Audit timestamp (last modification)

### 2.2 gyartok Table

```sql
CREATE TABLE gyartok (
    Id INT PRIMARY KEY IDENTITY(1,1),
    GyartoNev NVARCHAR(200) NOT NULL,
    Orszag NVARCHAR(100) NULL,
    ContactEmail NVARCHAR(255) NULL,
    ContactPhone NVARCHAR(50) NULL,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL
);

-- Index for name searches
CREATE INDEX IX_gyartok_GyartoNev ON gyartok(GyartoNev);
```

**Field Descriptions:**
- `Id`: Primary key (referenced by cikkek.GyartoId)
- `GyartoNev`: Manufacturer name (e.g., "Bosch", "Samsung")
- `Orszag`: Country of origin (e.g., "Németország", "Dél-Korea")
- `ContactEmail`: Contact email for manufacturer
- `ContactPhone`: Contact phone number
- `CreatedAt/UpdatedAt`: Audit timestamps

### 2.3 cikkek Table

```sql
CREATE TABLE cikkek (
    Id INT PRIMARY KEY IDENTITY(1,1),
    CikkKod NVARCHAR(50) NOT NULL,
    Megnevezes NVARCHAR(500) NOT NULL,
    Leiras NVARCHAR(MAX) NULL,
    EgysegAr DECIMAL(18,2) NOT NULL,
    MennyisegiEgyseg NVARCHAR(20) NULL,
    GyartoId INT NULL,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL,

    CONSTRAINT UQ_cikkek_CikkKod UNIQUE (CikkKod),
    CONSTRAINT FK_cikkek_GyartoId FOREIGN KEY (GyartoId)
        REFERENCES gyartok(Id) ON DELETE SET NULL,
    CONSTRAINT CK_cikkek_EgysegAr CHECK (EgysegAr >= 0)
);

-- Indexes for common queries
CREATE INDEX IX_cikkek_CikkKod ON cikkek(CikkKod);
CREATE INDEX IX_cikkek_GyartoId ON cikkek(GyartoId);
CREATE INDEX IX_cikkek_Megnevezes ON cikkek(Megnevezes);
CREATE INDEX IX_cikkek_EgysegAr ON cikkek(EgysegAr);

-- Composite index for common filter combinations
CREATE INDEX IX_cikkek_GyartoId_EgysegAr ON cikkek(GyartoId, EgysegAr);
```

**Field Descriptions:**
- `Id`: Primary key
- `CikkKod`: Unique product code (e.g., "BOSCH-001")
- `Megnevezes`: Product name/title (max 500 characters)
- `Leiras`: Detailed product description (unlimited text)
- `EgysegAr`: Unit price in HUF (Hungarian Forint), 2 decimal places
- `MennyisegiEgyseg`: Unit of measurement (e.g., "db" (pieces), "kg", "liter")
- `GyartoId`: Foreign key to gyartok table (NULL allowed if manufacturer unknown)
- `CreatedAt/UpdatedAt`: Audit timestamps

**Business Rules:**
- `EgysegAr` must be >= 0 (CHECK constraint)
- `CikkKod` must be unique across all products
- `GyartoId` references gyartok(Id), ON DELETE SET NULL (if manufacturer deleted, products remain but manufacturer link removed)

### 2.4 partnerek Table

```sql
CREATE TABLE partnerek (
    Id INT PRIMARY KEY IDENTITY(1,1),
    PartnerNev NVARCHAR(200) NOT NULL,
    AdoSzam NVARCHAR(50) NULL,
    Cim NVARCHAR(500) NULL,
    ContactPerson NVARCHAR(200) NULL,
    Email NVARCHAR(255) NULL,
    Phone NVARCHAR(50) NULL,
    PartnerTipus NVARCHAR(50) NULL,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL,

    CONSTRAINT CK_partnerek_PartnerTipus CHECK (
        PartnerTipus IS NULL OR
        PartnerTipus IN ('Beszállító', 'Vevő', 'Mindkettő')
    )
);

-- Indexes
CREATE INDEX IX_partnerek_PartnerNev ON partnerek(PartnerNev);
CREATE INDEX IX_partnerek_PartnerTipus ON partnerek(PartnerTipus);
CREATE INDEX IX_partnerek_Email ON partnerek(Email);
```

**Field Descriptions:**
- `Id`: Primary key
- `PartnerNev`: Partner/customer/supplier name
- `AdoSzam`: Tax ID number (Hungarian format: 12345678-1-23)
- `Cim`: Full address (street, city, postal code)
- `ContactPerson`: Name of contact person
- `Email`: Contact email
- `Phone`: Contact phone number
- `PartnerTipus`: Partner type - "Beszállító" (Supplier), "Vevő" (Customer), "Mindkettő" (Both)
- `CreatedAt/UpdatedAt`: Audit timestamps

**Business Rules:**
- `PartnerTipus` must be one of the predefined values (CHECK constraint)

## 3. Stored Procedures

### 3.1 GetCikkekByGyarto

**Purpose:** Retrieve all products by a specific manufacturer (with manufacturer details)

```sql
CREATE PROCEDURE GetCikkekByGyarto
    @GyartoId INT
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        c.Id,
        c.CikkKod,
        c.Megnevezes,
        c.Leiras,
        c.EgysegAr,
        c.MennyisegiEgyseg,
        c.GyartoId,
        c.CreatedAt,
        c.UpdatedAt,
        g.GyartoNev,
        g.Orszag,
        g.ContactEmail,
        g.ContactPhone
    FROM cikkek c
    INNER JOIN gyartok g ON c.GyartoId = g.Id
    WHERE c.GyartoId = @GyartoId
    ORDER BY c.Megnevezes;
END;
GO
```

**Usage Example:**
```sql
EXEC GetCikkekByGyarto @GyartoId = 1;
```

### 3.2 GetStatisztika

**Purpose:** Get summary statistics across all tables

```sql
CREATE PROCEDURE GetStatisztika
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        (SELECT COUNT(*) FROM cikkek) AS CikkekSzama,
        (SELECT COUNT(*) FROM gyartok) AS GyartokSzama,
        (SELECT COUNT(*) FROM partnerek) AS PartnerekSzama,
        (SELECT COUNT(*) FROM users WHERE IsActive = 1) AS AktivUserekSzama,
        (SELECT AVG(EgysegAr) FROM cikkek) AS AtlagosAr,
        (SELECT MIN(EgysegAr) FROM cikkek) AS MinAr,
        (SELECT MAX(EgysegAr) FROM cikkek) AS MaxAr;
END;
GO
```

**Usage Example:**
```sql
EXEC GetStatisztika;
```

**Sample Result:**
```
CikkekSzama | GyartokSzama | PartnerekSzama | AktivUserekSzama | AtlagosAr | MinAr | MaxAr
------------|--------------|----------------|------------------|-----------|-------|-------
    142     |      28      |       56       |        12        | 15750.50  | 500.00| 89900.00
```

## 4. Indexes Strategy

### 4.1 Index Types

**Primary Key Indexes (Clustered):**
- Automatically created on all `Id` columns
- Physically orders data on disk by Id
- Optimal for range queries and joins

**Unique Indexes:**
- `users.Username` - Enforces uniqueness, speeds up login queries
- `cikkek.CikkKod` - Enforces uniqueness, speeds up product code lookups

**Foreign Key Indexes:**
- `cikkek.GyartoId` - Speeds up joins between cikkek and gyartok

**Filter Indexes:**
- `cikkek.Megnevezes` - Speeds up name searches
- `cikkek.EgysegAr` - Speeds up price range queries
- `partnerek.PartnerTipus` - Speeds up filtering by partner type

**Composite Indexes:**
- `cikkek.(GyartoId, EgysegAr)` - Optimizes queries filtering by both manufacturer and price

### 4.2 Index Maintenance

**Update Statistics:** Weekly
```sql
UPDATE STATISTICS cikkek WITH FULLSCAN;
UPDATE STATISTICS gyartok WITH FULLSCAN;
UPDATE STATISTICS partnerek WITH FULLSCAN;
UPDATE STATISTICS users WITH FULLSCAN;
```

**Rebuild Fragmented Indexes:** Monthly
```sql
ALTER INDEX ALL ON cikkek REBUILD;
ALTER INDEX ALL ON gyartok REBUILD;
ALTER INDEX ALL ON partnerek REBUILD;
ALTER INDEX ALL ON users REBUILD;
```

## 5. Data Types and Constraints

### 5.1 Data Type Rationale

| Type | Usage | Reason |
|------|-------|--------|
| `INT IDENTITY(1,1)` | Primary keys | Auto-incrementing, 4-byte integer |
| `NVARCHAR(n)` | Text fields | Unicode support (Hungarian characters: á, é, í, ó, ő, ú, ű) |
| `NVARCHAR(MAX)` | Long text (descriptions) | Unlimited length for detailed content |
| `DECIMAL(18,2)` | Prices | Fixed precision, no floating-point errors |
| `DATETIME2` | Timestamps | More precise than DATETIME, better performance |
| `BIT` | Boolean flags | Single-bit storage (IsActive) |

### 5.2 Constraint Summary

| Constraint | Purpose | Tables |
|------------|---------|--------|
| `PRIMARY KEY` | Uniqueness and clustering | All tables (Id column) |
| `FOREIGN KEY` | Referential integrity | cikkek.GyartoId → gyartok.Id |
| `UNIQUE` | Uniqueness (non-PK) | users.Username, cikkek.CikkKod |
| `CHECK` | Value validation | cikkek.EgysegAr >= 0, partnerek.PartnerTipus IN (...) |
| `NOT NULL` | Required fields | Essential business fields |
| `DEFAULT` | Auto-populated values | CreatedAt = GETDATE(), IsActive = 1 |

## 6. Seed Data (Initial Sample Data)

### 6.1 Users

```sql
-- Password: admin123 (BCrypt hashed with work factor 11)
INSERT INTO users (Username, PasswordHash, Email, FullName, IsActive)
VALUES
    ('admin', '$2a$11$abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVW', 'admin@graphqlapp.com', 'Administrator', 1),
    ('test_user', '$2a$11$xyzabcdefghijklmnopqrstuvABCDEFGHIJKLMNOPQRSTUVW', 'test@graphqlapp.com', 'Test User', 1),
    ('developer', '$2a$11$devpasswordhashexampleabcdefghijklmnopqrstuvwx', 'dev@graphqlapp.com', 'Developer Account', 1);
```

**Note:** Replace hash values with actual BCrypt hashes during implementation.

### 6.2 Gyartok (Manufacturers)

```sql
INSERT INTO gyartok (GyartoNev, Orszag, ContactEmail, ContactPhone)
VALUES
    ('Bosch', 'Németország', 'info@bosch.de', '+49-711-400-40000'),
    ('Samsung', 'Dél-Korea', 'contact@samsung.com', '+82-2-2255-0114'),
    ('LG Electronics', 'Dél-Korea', 'info@lge.com', '+82-2-3777-1114'),
    ('Philips', 'Hollandia', 'support@philips.nl', '+31-20-597-7777'),
    ('Siemens', 'Németország', 'contact@siemens.com', '+49-89-636-00');
```

### 6.3 Cikkek (Products)

```sql
INSERT INTO cikkek (CikkKod, Megnevezes, Leiras, EgysegAr, MennyisegiEgyseg, GyartoId)
VALUES
    ('BOSCH-001', 'Bosch Professional Fúrógép GSB 13 RE', 'Nagyteljesítményű fúrógép professzionális használatra', 25000.00, 'db', 1),
    ('BOSCH-002', 'Bosch Csavarbehajtó GSR 12V-15', 'Akkumulátoros csavarbehajtó 12V-os akkuval', 18900.00, 'db', 1),
    ('SAM-TV-01', 'Samsung 55" QLED 4K Smart TV', 'Prémium 4K QLED televízió intelligens funkciókkal', 289900.00, 'db', 2),
    ('SAM-PHONE-01', 'Samsung Galaxy S23 128GB', 'Csúcskategóriás okostelefon 128GB tárhellyel', 259900.00, 'db', 2),
    ('LG-WASH-01', 'LG Mosógép F4WV309S3E 9kg', 'Elöltöltős mosógép 9kg kapacitással', 149900.00, 'db', 3),
    ('PHI-IRON-01', 'Philips Azur Vasaló GC4567/80', 'Gőzölős vasaló optimális teljesítménnyel', 12500.00, 'db', 4),
    ('SIE-OVEN-01', 'Siemens Beépíthető Sütő HB578ABS0', 'Beépíthető elektromos sütő pirolitikus tisztítással', 179900.00, 'db', 5);
```

### 6.4 Partnerek (Partners)

```sql
INSERT INTO partnerek (PartnerNev, AdoSzam, Cim, ContactPerson, Email, Phone, PartnerTipus)
VALUES
    ('TechMegoldások Kft.', '12345678-2-41', '1051 Budapest, Nádor utca 12.', 'Nagy Péter', 'peter.nagy@techmegoldasok.hu', '+36-1-234-5678', 'Vevő'),
    ('ElektroBeszállító Zrt.', '23456789-2-42', '1138 Budapest, Váci út 76.', 'Kovács Anna', 'anna.kovacs@elektrobeszallito.hu', '+36-1-345-6789', 'Beszállító'),
    ('Műszaki Centrum', '34567890-2-43', '6000 Kecskemét, Kossuth tér 5.', 'Szabó János', 'janos.szabo@muszakicentrum.hu', '+36-76-123-456', 'Mindkettő'),
    ('IT Solutions Group', '45678901-2-44', '4032 Debrecen, Piac utca 23.', 'Tóth Eszter', 'eszter.toth@itsolutions.hu', '+36-52-987-654', 'Vevő'),
    ('Global Parts Ltd.', '56789012-2-45', '9024 Győr, Baross utca 45.', 'Molnár Gábor', 'gabor.molnar@globalparts.hu', '+36-96-555-123', 'Beszállító');
```

## 7. Database Initialization Script

Complete initialization script combining all components:

```sql
-- Script: 001-init-database.sql
-- Purpose: Initialize GraphQL API Application database schema
-- Version: 1.0
-- Date: 2025-11-04

USE master;
GO

-- Create databases (if not exists)
IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'DevDatabase')
BEGIN
    CREATE DATABASE DevDatabase;
END
GO

IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'ProdDatabase')
BEGIN
    CREATE DATABASE ProdDatabase;
END
GO

IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'TestDatabase')
BEGIN
    CREATE DATABASE TestDatabase;
END
GO

-- Switch to DevDatabase for schema creation
USE DevDatabase;
GO

-- ===== DROP EXISTING OBJECTS (for clean re-initialization) =====
DROP TABLE IF EXISTS cikkek;
DROP TABLE IF EXISTS gyartok;
DROP TABLE IF EXISTS partnerek;
DROP TABLE IF EXISTS users;
DROP PROCEDURE IF EXISTS GetCikkekByGyarto;
DROP PROCEDURE IF EXISTS GetStatisztika;
GO

-- ===== CREATE TABLES =====

-- Users table
CREATE TABLE users (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Username NVARCHAR(100) NOT NULL,
    PasswordHash NVARCHAR(255) NOT NULL,
    Email NVARCHAR(255) NULL,
    FullName NVARCHAR(200) NULL,
    IsActive BIT NOT NULL DEFAULT 1,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL,
    CONSTRAINT UQ_users_Username UNIQUE (Username)
);

CREATE INDEX IX_users_Username ON users(Username);
CREATE INDEX IX_users_IsActive ON users(IsActive) WHERE IsActive = 1;
GO

-- Gyartok table
CREATE TABLE gyartok (
    Id INT PRIMARY KEY IDENTITY(1,1),
    GyartoNev NVARCHAR(200) NOT NULL,
    Orszag NVARCHAR(100) NULL,
    ContactEmail NVARCHAR(255) NULL,
    ContactPhone NVARCHAR(50) NULL,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL
);

CREATE INDEX IX_gyartok_GyartoNev ON gyartok(GyartoNev);
GO

-- Cikkek table
CREATE TABLE cikkek (
    Id INT PRIMARY KEY IDENTITY(1,1),
    CikkKod NVARCHAR(50) NOT NULL,
    Megnevezes NVARCHAR(500) NOT NULL,
    Leiras NVARCHAR(MAX) NULL,
    EgysegAr DECIMAL(18,2) NOT NULL,
    MennyisegiEgyseg NVARCHAR(20) NULL,
    GyartoId INT NULL,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL,
    CONSTRAINT UQ_cikkek_CikkKod UNIQUE (CikkKod),
    CONSTRAINT FK_cikkek_GyartoId FOREIGN KEY (GyartoId)
        REFERENCES gyartok(Id) ON DELETE SET NULL,
    CONSTRAINT CK_cikkek_EgysegAr CHECK (EgysegAr >= 0)
);

CREATE INDEX IX_cikkek_CikkKod ON cikkek(CikkKod);
CREATE INDEX IX_cikkek_GyartoId ON cikkek(GyartoId);
CREATE INDEX IX_cikkek_Megnevezes ON cikkek(Megnevezes);
CREATE INDEX IX_cikkek_EgysegAr ON cikkek(EgysegAr);
CREATE INDEX IX_cikkek_GyartoId_EgysegAr ON cikkek(GyartoId, EgysegAr);
GO

-- Partnerek table
CREATE TABLE partnerek (
    Id INT PRIMARY KEY IDENTITY(1,1),
    PartnerNev NVARCHAR(200) NOT NULL,
    AdoSzam NVARCHAR(50) NULL,
    Cim NVARCHAR(500) NULL,
    ContactPerson NVARCHAR(200) NULL,
    Email NVARCHAR(255) NULL,
    Phone NVARCHAR(50) NULL,
    PartnerTipus NVARCHAR(50) NULL,
    CreatedAt DATETIME2 NOT NULL DEFAULT GETDATE(),
    UpdatedAt DATETIME2 NULL,
    CONSTRAINT CK_partnerek_PartnerTipus CHECK (
        PartnerTipus IS NULL OR
        PartnerTipus IN ('Beszállító', 'Vevő', 'Mindkettő')
    )
);

CREATE INDEX IX_partnerek_PartnerNev ON partnerek(PartnerNev);
CREATE INDEX IX_partnerek_PartnerTipus ON partnerek(PartnerTipus);
CREATE INDEX IX_partnerek_Email ON partnerek(Email);
GO

-- ===== CREATE STORED PROCEDURES =====

CREATE PROCEDURE GetCikkekByGyarto
    @GyartoId INT
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        c.Id, c.CikkKod, c.Megnevezes, c.Leiras,
        c.EgysegAr, c.MennyisegiEgyseg, c.GyartoId,
        c.CreatedAt, c.UpdatedAt,
        g.GyartoNev, g.Orszag, g.ContactEmail, g.ContactPhone
    FROM cikkek c
    INNER JOIN gyartok g ON c.GyartoId = g.Id
    WHERE c.GyartoId = @GyartoId
    ORDER BY c.Megnevezes;
END;
GO

CREATE PROCEDURE GetStatisztika
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        (SELECT COUNT(*) FROM cikkek) AS CikkekSzama,
        (SELECT COUNT(*) FROM gyartok) AS GyartokSzama,
        (SELECT COUNT(*) FROM partnerek) AS PartnerekSzama,
        (SELECT COUNT(*) FROM users WHERE IsActive = 1) AS AktivUserekSzama,
        (SELECT AVG(EgysegAr) FROM cikkek) AS AtlagosAr,
        (SELECT MIN(EgysegAr) FROM cikkek) AS MinAr,
        (SELECT MAX(EgysegAr) FROM cikkek) AS MaxAr;
END;
GO

PRINT 'Database schema created successfully!';
GO
```

## 8. Backup and Recovery Strategy

### 8.1 Backup Schedule

| Backup Type | Frequency | Retention | Purpose |
|-------------|-----------|-----------|---------|
| **Full Backup** | Daily at 2:00 AM | 30 days | Complete database snapshot |
| **Differential Backup** | Every 6 hours | 7 days | Changes since last full backup |
| **Transaction Log** | Every 15 minutes | 48 hours | Point-in-time recovery |

### 8.2 Backup Script

```sql
-- Full Backup
BACKUP DATABASE ProdDatabase
TO DISK = 'C:\Backups\ProdDatabase_Full_20250104.bak'
WITH COMPRESSION, STATS = 10;

-- Differential Backup
BACKUP DATABASE ProdDatabase
TO DISK = 'C:\Backups\ProdDatabase_Diff_20250104_0800.bak'
WITH DIFFERENTIAL, COMPRESSION, STATS = 10;

-- Transaction Log Backup
BACKUP LOG ProdDatabase
TO DISK = 'C:\Backups\ProdDatabase_Log_20250104_0815.trn'
WITH COMPRESSION, STATS = 10;
```

### 8.3 Recovery Procedure

```sql
-- Point-in-time recovery to 10:15 AM on 2025-11-04
RESTORE DATABASE ProdDatabase
FROM DISK = 'C:\Backups\ProdDatabase_Full_20250104.bak'
WITH NORECOVERY, REPLACE;

RESTORE DATABASE ProdDatabase
FROM DISK = 'C:\Backups\ProdDatabase_Diff_20250104_0800.bak'
WITH NORECOVERY;

RESTORE LOG ProdDatabase
FROM DISK = 'C:\Backups\ProdDatabase_Log_20250104_1015.trn'
WITH RECOVERY, STOPAT = '2025-11-04 10:15:00';
```

## 9. Performance Tuning

### 9.1 Query Optimization Checklist

- ✅ All foreign keys have indexes
- ✅ Frequently filtered columns have indexes (Username, CikkKod, EgysegAr)
- ✅ Composite indexes for multi-column filters
- ✅ Avoid SELECT * (specify columns explicitly)
- ✅ Use stored procedures for complex queries
- ✅ Implement pagination (OFFSET/FETCH) for large result sets

### 9.2 Index Fragmentation Monitoring

```sql
SELECT
    OBJECT_NAME(ips.object_id) AS TableName,
    i.name AS IndexName,
    ips.avg_fragmentation_in_percent,
    ips.page_count
FROM sys.dm_db_index_physical_stats(
    DB_ID(), NULL, NULL, NULL, 'LIMITED') ips
INNER JOIN sys.indexes i
    ON ips.object_id = i.object_id
    AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 10
  AND ips.page_count > 1000
ORDER BY ips.avg_fragmentation_in_percent DESC;
```

**Action:** Rebuild indexes with >30% fragmentation, reorganize indexes with 10-30% fragmentation.

## 10. Summary

### 10.1 Schema Highlights

- ✅ 4 core tables: users, gyartok, cikkek, partnerek
- ✅ 1:N relationship: gyartok → cikkek
- ✅ 2 stored procedures: GetCikkekByGyarto, GetStatisztika
- ✅ Comprehensive indexing for performance
- ✅ Audit fields (CreatedAt, UpdatedAt) on all tables
- ✅ Data integrity via constraints (PK, FK, UNIQUE, CHECK)
- ✅ Unicode support for Hungarian characters (NVARCHAR)

### 10.2 Next Steps

1. ✅ Database schema documented
2. ⏭️ Execute initialization script on SQL Server (10.10.10.69)
3. ⏭️ Load seed data for development/testing
4. ⏭️ Create database users (dev_user, prod_user) with appropriate permissions
5. ⏭️ Configure backup jobs on SQL Server Agent

---

**Schema Version:** 1.0
**Status:** ✅ READY FOR IMPLEMENTATION
**SQL Script Location:** `scripts/sql/001-init-database.sql`
