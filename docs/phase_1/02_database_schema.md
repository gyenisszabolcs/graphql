# Database Schema Documentation - dev_graphql

**Document Version:** 1.0
**Date Created:** 2025-11-05
**Author:** DevOps Infrastructure Engineer
**Database:** dev_graphql
**Server:** 10.10.10.69

---

## Executive Summary

This document provides comprehensive schema documentation for the existing `dev_graphql` database. The database contains four main tables (CIKK, GYARTO, PARTNER, USERS) that manage articles, manufacturers, business partners, and user information.

**Important Notes:**
- ⚠️ This is an **existing database** - no tables need to be created
- ⚠️ Schema details marked with `(?)` require verification via actual database connection
- ⚠️ An additional AUTH table will be created in Phase 2 for authentication

---

## Table of Contents

1. [Schema Overview](#1-schema-overview)
2. [Table: CIKK (Articles/Products)](#2-table-cikk-articlesproducts)
3. [Table: GYARTO (Manufacturers)](#3-table-gyarto-manufacturers)
4. [Table: PARTNER (Business Partners)](#4-table-partner-business-partners)
5. [Table: USERS (User Accounts)](#5-table-users-user-accounts)
6. [Table Relationships](#6-table-relationships)
7. [Schema Extraction Queries](#7-schema-extraction-queries)
8. [Indexes and Constraints](#8-indexes-and-constraints)
9. [Future Schema Changes](#9-future-schema-changes)

---

## 1. Schema Overview

### 1.1 Database Information

| Property | Value |
|----------|-------|
| Database Name | dev_graphql |
| Server Address | 10.10.10.69 |
| Total Tables | 4 (CIKK, GYARTO, PARTNER, USERS) |
| Database Purpose | Product catalog, manufacturer, and partner management |

### 1.2 Table Summary

| Table Name | Purpose | Primary Key | Approximate Rows | Status |
|------------|---------|-------------|------------------|--------|
| CIKK | Products/Articles | CIKKID (INT) | [TO BE VERIFIED] | Existing |
| GYARTO | Manufacturers | GYARTO (NVARCHAR) | [TO BE VERIFIED] | Existing |
| PARTNER | Business Partners | PARTNERID (INT) | [TO BE VERIFIED] | Existing |
| USERS | User Accounts | USERCODE (NVARCHAR) | [TO BE VERIFIED] | Existing |

### 1.3 Entity Relationship Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                           USERS                                  │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ PK: USERCODE (NVARCHAR)                                 │   │
│  │     USERNAME (NVARCHAR) - Display name                  │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                            │
         ┌──────────────────┼───────────────────┐
         │                  │                   │
         │ (CRUS)           │ (CRUS)            │ (CRUS)
         ▼                  ▼                   ▼
┌─────────────┐    ┌─────────────┐    ┌──────────────┐
│   GYARTO    │    │   PARTNER   │    │    CIKK      │
│─────────────│    │─────────────│    │──────────────│
│PK: GYARTO   │◄───│PK:PARTNERID │    │PK: CIKKID    │
│   (NVARCHAR)│    │   (INT)     │    │    (INT)     │
│             │    │             │    │              │
│  MEGJ       │    │  PARTNERNEV │    │  CIKKSZAM    │
│  LEIRAS     │    │  FIZOSZT    │    │  CIKKNEV     │
│  GYARTOADAT1│    │  ORSZAG     │    │FK:GYARTO     │───┐
│  GYARTOADAT2│    │  IRSZ       │    │  GYCIKKSZAM  │   │
│  CRUS       │    │  VAROS      │    │FK:ELOALLITOPID   │
│  CRDTI      │    │  UTCA       │    │  CRUS        │   │
└─────────────┘    │  CRUS       │    │  CRDTI       │   │
                   │  CRDTI      │    └──────────────┘   │
                   └─────────────┘            │           │
                         ▲                    │           │
                         │                    │           │
                         └────────────────────┴───────────┘
                              (ELOALLITOPID)      (GYARTO)

Relationships:
─────────────
1. CIKK.GYARTO → GYARTO.GYARTO (N:1)
   - Many products can have one manufacturer

2. CIKK.ELOALLITOPID → PARTNER.PARTNERID (N:1)
   - Many products can have one producing partner

3. CIKK.CRUS → USERS.USERCODE (N:1)
   - Audit: Created by user

4. GYARTO.CRUS → USERS.USERCODE (N:1)
   - Audit: Created by user

5. PARTNER.CRUS → USERS.USERCODE (N:1)
   - Audit: Created by user
```

---

## 2. Table: CIKK (Articles/Products)

### 2.1 Purpose
Stores product/article information including product codes, names, manufacturer details, and audit information.

### 2.2 Schema Definition

```sql
-- CIKK Table Structure (EXISTING)
CREATE TABLE CIKK (
    CIKKID          INT             NOT NULL PRIMARY KEY,
    CIKKSZAM        NVARCHAR(?)     NULL,
    CIKKNEV         NVARCHAR(?)     NULL,
    GYARTO          NVARCHAR(?)     NULL,     -- FK to GYARTO.GYARTO
    GYCIKKSZAM      NVARCHAR(?)     NULL,
    ELOALLITOPID    INT             NULL,     -- FK to PARTNER.PARTNERID
    CRUS            NVARCHAR(?)     NULL,     -- FK to USERS.USERCODE
    CRDTI           DATETIME        NULL
);
```

### 2.3 Column Details

| Column Name | Data Type | Max Length | Nullable | Description |
|-------------|-----------|------------|----------|-------------|
| **CIKKID** | INT | 4 bytes | NOT NULL | Primary key, unique article identifier |
| CIKKSZAM | NVARCHAR | [TO VERIFY] | NULL | Article/product code (SKU) |
| CIKKNEV | NVARCHAR | [TO VERIFY] | NULL | Article/product name |
| GYARTO | NVARCHAR | [TO VERIFY] | NULL | Manufacturer identifier (references GYARTO.GYARTO) |
| GYCIKKSZAM | NVARCHAR | [TO VERIFY] | NULL | Manufacturer's product code |
| ELOALLITOPID | INT | 4 bytes | NULL | Producer/supplier ID (references PARTNER.PARTNERID) |
| CRUS | NVARCHAR | [TO VERIFY] | NULL | Creating user code (references USERS.USERCODE) |
| CRDTI | DATETIME | 8 bytes | NULL | Creation date and time |

### 2.4 Business Rules

1. **Primary Key:** CIKKID must be unique and not null
2. **Foreign Keys:**
   - GYARTO → GYARTO.GYARTO (manufacturer reference)
   - ELOALLITOPID → PARTNER.PARTNERID (producer reference)
   - CRUS → USERS.USERCODE (audit trail)
3. **Audit Fields:** CRUS and CRDTI track creation information
4. **Optional Fields:** Most fields are nullable to allow gradual data entry

### 2.5 Sample Data Query

```sql
-- Retrieve first 10 articles with related data
SELECT TOP 10
    c.CIKKID,
    c.CIKKSZAM,
    c.CIKKNEV,
    g.LEIRAS AS GyartoNev,
    p.PARTNERNEV AS EloallitoNev,
    u.USERNAME AS LetrehozoFelhasznalo,
    c.CRDTI
FROM CIKK c
LEFT JOIN GYARTO g ON c.GYARTO = g.GYARTO
LEFT JOIN PARTNER p ON c.ELOALLITOPID = p.PARTNERID
LEFT JOIN USERS u ON c.CRUS = u.USERCODE
ORDER BY c.CIKKID;
```

### 2.6 GraphQL Mapping

This table will be exposed via GraphQL as:
- **Type:** `Cikk` (Article)
- **Queries:** `cikk(id: Int!)`, `cikkek(filter: CikkFilterInput)`
- **Mutations:** `createCikk`, `updateCikk`, `deleteCikk`
- **Relations:** `gyarto`, `eloallito`, `letrehozoFelhasznalo`

---

## 3. Table: GYARTO (Manufacturers)

### 3.1 Purpose
Stores manufacturer/brand information with descriptions and additional attributes.

### 3.2 Schema Definition

```sql
-- GYARTO Table Structure (EXISTING)
CREATE TABLE GYARTO (
    GYARTO          NVARCHAR(?)     NOT NULL PRIMARY KEY,
    MEGJ            NVARCHAR(?)     NULL,
    LEIRAS          NVARCHAR(?)     NULL,
    GYARTOADAT1     NVARCHAR(?)     NULL,
    GYARTOADAT2     NVARCHAR(?)     NULL,
    CRUS            NVARCHAR(?)     NULL,     -- FK to USERS.USERCODE
    CRDTI           DATETIME        NULL
);
```

### 3.3 Column Details

| Column Name | Data Type | Max Length | Nullable | Description |
|-------------|-----------|------------|----------|-------------|
| **GYARTO** | NVARCHAR | [TO VERIFY] | NOT NULL | Primary key, manufacturer code/identifier |
| MEGJ | NVARCHAR | [TO VERIFY] | NULL | Notes/remarks about manufacturer |
| LEIRAS | NVARCHAR | [TO VERIFY] | NULL | Description/name of manufacturer |
| GYARTOADAT1 | NVARCHAR | [TO VERIFY] | NULL | Custom manufacturer attribute 1 |
| GYARTOADAT2 | NVARCHAR | [TO VERIFY] | NULL | Custom manufacturer attribute 2 |
| CRUS | NVARCHAR | [TO VERIFY] | NULL | Creating user code (references USERS.USERCODE) |
| CRDTI | DATETIME | 8 bytes | NULL | Creation date and time |

### 3.4 Business Rules

1. **Primary Key:** GYARTO must be unique and not null (string-based key)
2. **Foreign Keys:** CRUS → USERS.USERCODE (audit trail)
3. **Display Name:** LEIRAS is typically used as the display name
4. **Custom Fields:** GYARTOADAT1 and GYARTOADAT2 provide flexibility for additional attributes

### 3.5 Sample Data Query

```sql
-- Retrieve all manufacturers with article counts
SELECT
    g.GYARTO,
    g.LEIRAS,
    g.MEGJ,
    COUNT(c.CIKKID) AS CikkekSzama,
    u.USERNAME AS LetrehozoFelhasznalo,
    g.CRDTI
FROM GYARTO g
LEFT JOIN CIKK c ON g.GYARTO = c.GYARTO
LEFT JOIN USERS u ON g.CRUS = u.USERCODE
GROUP BY g.GYARTO, g.LEIRAS, g.MEGJ, u.USERNAME, g.CRDTI
ORDER BY CikkekSzama DESC;
```

### 3.6 GraphQL Mapping

This table will be exposed via GraphQL as:
- **Type:** `Gyarto` (Manufacturer)
- **Queries:** `gyarto(gyarto: String!)`, `gyartok(filter: GyartoFilterInput)`
- **Mutations:** `createGyarto`, `updateGyarto`, `deleteGyarto`
- **Relations:** `cikkek` (list of products)

---

## 4. Table: PARTNER (Business Partners)

### 4.1 Purpose
Stores business partner information including suppliers, producers, and distributors with address details.

### 4.2 Schema Definition

```sql
-- PARTNER Table Structure (EXISTING)
CREATE TABLE PARTNER (
    PARTNERID       INT             NOT NULL PRIMARY KEY,
    PARTNERNEV      NVARCHAR(?)     NULL,
    FIZOSZT         NVARCHAR(?)     NULL,
    ORSZAG          NVARCHAR(?)     NULL,
    IRSZ            NVARCHAR(?)     NULL,
    VAROS           NVARCHAR(?)     NULL,
    UTCA            NVARCHAR(?)     NULL,
    CRUS            NVARCHAR(?)     NULL,     -- FK to USERS.USERCODE
    CRDTI           DATETIME        NULL
);
```

### 4.3 Column Details

| Column Name | Data Type | Max Length | Nullable | Description |
|-------------|-----------|------------|----------|-------------|
| **PARTNERID** | INT | 4 bytes | NOT NULL | Primary key, unique partner identifier |
| PARTNERNEV | NVARCHAR | [TO VERIFY] | NULL | Partner name/company name |
| FIZOSZT | NVARCHAR | [TO VERIFY] | NULL | Payment terms/class |
| ORSZAG | NVARCHAR | [TO VERIFY] | NULL | Country |
| IRSZ | NVARCHAR | [TO VERIFY] | NULL | Postal code |
| VAROS | NVARCHAR | [TO VERIFY] | NULL | City |
| UTCA | NVARCHAR | [TO VERIFY] | NULL | Street address |
| CRUS | NVARCHAR | [TO VERIFY] | NULL | Creating user code (references USERS.USERCODE) |
| CRDTI | DATETIME | 8 bytes | NULL | Creation date and time |

### 4.4 Business Rules

1. **Primary Key:** PARTNERID must be unique and not null
2. **Foreign Keys:** CRUS → USERS.USERCODE (audit trail)
3. **Address Fields:** ORSZAG, IRSZ, VAROS, UTCA form complete address
4. **Payment Terms:** FIZOSZT may reference payment terms/conditions

### 4.5 Sample Data Query

```sql
-- Retrieve all partners with product counts
SELECT
    p.PARTNERID,
    p.PARTNERNEV,
    p.ORSZAG,
    p.VAROS,
    p.FIZOSZT,
    COUNT(c.CIKKID) AS EloallitottCikkekSzama,
    u.USERNAME AS LetrehozoFelhasznalo,
    p.CRDTI
FROM PARTNER p
LEFT JOIN CIKK c ON p.PARTNERID = c.ELOALLITOPID
LEFT JOIN USERS u ON p.CRUS = u.USERCODE
GROUP BY p.PARTNERID, p.PARTNERNEV, p.ORSZAG, p.VAROS, p.FIZOSZT, u.USERNAME, p.CRDTI
ORDER BY EloallitottCikkekSzama DESC;
```

### 4.6 GraphQL Mapping

This table will be exposed via GraphQL as:
- **Type:** `Partner` (Business Partner)
- **Queries:** `partner(id: Int!)`, `partnerek(filter: PartnerFilterInput)`
- **Mutations:** `createPartner`, `updatePartner`, `deletePartner`
- **Relations:** `eloallitottCikkek` (list of produced products)

---

## 5. Table: USERS (User Accounts)

### 5.1 Purpose
Stores basic user account information. **Note:** This table is NOT suitable for authentication (only has 2 columns).

### 5.2 Schema Definition

```sql
-- USERS Table Structure (EXISTING)
CREATE TABLE USERS (
    USERCODE        NVARCHAR(?)     NOT NULL PRIMARY KEY,
    USERNAME        NVARCHAR(?)     NULL
);
```

### 5.3 Column Details

| Column Name | Data Type | Max Length | Nullable | Description |
|-------------|-----------|------------|----------|-------------|
| **USERCODE** | NVARCHAR | [TO VERIFY] | NOT NULL | Primary key, unique user identifier/code |
| USERNAME | NVARCHAR | [TO VERIFY] | NULL | Display name of user |

### 5.4 Business Rules

1. **Primary Key:** USERCODE must be unique and not null
2. **No Password Field:** This table does NOT contain authentication credentials
3. **Referenced By:** CIKK.CRUS, GYARTO.CRUS, PARTNER.CRUS (audit trails)
4. **Future Enhancement:** AUTH table will be created in Phase 2 for authentication

### 5.5 Sample Data Query

```sql
-- Retrieve users with their activity counts
SELECT
    u.USERCODE,
    u.USERNAME,
    COUNT(DISTINCT c.CIKKID) AS LetrehozottCikkek,
    COUNT(DISTINCT g.GYARTO) AS LetrehozottGyartok,
    COUNT(DISTINCT p.PARTNERID) AS LetrehozottPartnerek
FROM USERS u
LEFT JOIN CIKK c ON u.USERCODE = c.CRUS
LEFT JOIN GYARTO g ON u.USERCODE = g.CRUS
LEFT JOIN PARTNER p ON u.USERCODE = p.CRUS
GROUP BY u.USERCODE, u.USERNAME
ORDER BY u.USERCODE;
```

### 5.6 GraphQL Mapping

This table will be exposed via GraphQL as:
- **Type:** `User` (User Account)
- **Queries:** `user(userCode: String!)`, `users`
- **Mutations:** `createUser`, `updateUser`, `deleteUser`
- **Relations:** `letrehozottCikkek`, `letrehozottGyartok`, `letrehozottPartnerek`

### 5.7 Authentication Note

⚠️ **Important:** An AUTH table will be created in Phase 2 to handle authentication:

```sql
-- Future AUTH table (Phase 2)
CREATE TABLE AUTH (
    AuthId          INT             NOT NULL PRIMARY KEY IDENTITY(1,1),
    UserCode        NVARCHAR(50)    NOT NULL,     -- FK to USERS.USERCODE
    Email           NVARCHAR(255)   NOT NULL UNIQUE,
    PasswordHash    NVARCHAR(255)   NOT NULL,
    IsActive        BIT             NOT NULL DEFAULT 1,
    LastLogin       DATETIME        NULL,
    CreatedAt       DATETIME        NOT NULL DEFAULT GETDATE(),
    UpdatedAt       DATETIME        NULL,
    CONSTRAINT FK_AUTH_USERS FOREIGN KEY (UserCode) REFERENCES USERS(USERCODE)
);
```

---

## 6. Table Relationships

### 6.1 Foreign Key Relationships

| Child Table | Child Column | Parent Table | Parent Column | Relationship Type | Cardinality |
|-------------|--------------|--------------|---------------|-------------------|-------------|
| CIKK | GYARTO | GYARTO | GYARTO | Many-to-One | N:1 |
| CIKK | ELOALLITOPID | PARTNER | PARTNERID | Many-to-One | N:1 |
| CIKK | CRUS | USERS | USERCODE | Many-to-One | N:1 |
| GYARTO | CRUS | USERS | USERCODE | Many-to-One | N:1 |
| PARTNER | CRUS | USERS | USERCODE | Many-to-One | N:1 |

### 6.2 Relationship Descriptions

**1. CIKK → GYARTO**
- Each product (CIKK) belongs to one manufacturer (GYARTO)
- One manufacturer can have many products
- Nullable: Products can exist without assigned manufacturer

**2. CIKK → PARTNER (ELOALLITOPID)**
- Each product (CIKK) has one producer/supplier (PARTNER)
- One partner can produce many products
- Nullable: Products can exist without assigned producer

**3. CIKK → USERS (CRUS)**
- Each product was created by one user
- One user can create many products
- Audit trail relationship

**4. GYARTO → USERS (CRUS)**
- Each manufacturer record was created by one user
- One user can create many manufacturers
- Audit trail relationship

**5. PARTNER → USERS (CRUS)**
- Each partner record was created by one user
- One user can create many partners
- Audit trail relationship

### 6.3 Referential Integrity

⚠️ **Verification Required:** Check if actual foreign key constraints exist in database.

```sql
-- Query to check existing FK constraints
SELECT
    fk.name AS ForeignKeyName,
    OBJECT_NAME(fk.parent_object_id) AS ChildTable,
    COL_NAME(fkc.parent_object_id, fkc.parent_column_id) AS ChildColumn,
    OBJECT_NAME(fk.referenced_object_id) AS ParentTable,
    COL_NAME(fkc.referenced_object_id, fkc.referenced_column_id) AS ParentColumn
FROM sys.foreign_keys fk
INNER JOIN sys.foreign_key_columns fkc ON fk.object_id = fkc.constraint_object_id
WHERE OBJECT_NAME(fk.parent_object_id) IN ('CIKK', 'GYARTO', 'PARTNER')
ORDER BY ChildTable, ForeignKeyName;
```

---

## 7. Schema Extraction Queries

### 7.1 Complete Schema Information

```sql
-- Extract complete schema details for all tables
SELECT
    t.TABLE_NAME,
    c.COLUMN_NAME,
    c.ORDINAL_POSITION,
    c.DATA_TYPE,
    c.CHARACTER_MAXIMUM_LENGTH,
    c.NUMERIC_PRECISION,
    c.NUMERIC_SCALE,
    c.IS_NULLABLE,
    CASE
        WHEN pk.COLUMN_NAME IS NOT NULL THEN 'YES'
        ELSE 'NO'
    END AS IS_PRIMARY_KEY
FROM INFORMATION_SCHEMA.TABLES t
INNER JOIN INFORMATION_SCHEMA.COLUMNS c ON t.TABLE_NAME = c.TABLE_NAME
LEFT JOIN (
    SELECT ku.TABLE_NAME, ku.COLUMN_NAME
    FROM INFORMATION_SCHEMA.TABLE_CONSTRAINTS tc
    INNER JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE ku
        ON tc.CONSTRAINT_TYPE = 'PRIMARY KEY'
        AND tc.CONSTRAINT_NAME = ku.CONSTRAINT_NAME
) pk ON c.TABLE_NAME = pk.TABLE_NAME AND c.COLUMN_NAME = pk.COLUMN_NAME
WHERE t.TABLE_TYPE = 'BASE TABLE'
  AND t.TABLE_NAME IN ('CIKK', 'GYARTO', 'PARTNER', 'USERS')
ORDER BY t.TABLE_NAME, c.ORDINAL_POSITION;
```

### 7.2 Table Statistics

```sql
-- Get row counts and space usage
SELECT
    t.name AS TableName,
    SUM(p.rows) AS ApproxRowCount,
    SUM(au.total_pages) * 8 AS TotalSpaceKB,
    SUM(au.used_pages) * 8 AS UsedSpaceKB,
    (SUM(au.total_pages) - SUM(au.used_pages)) * 8 AS UnusedSpaceKB
FROM sys.tables t
INNER JOIN sys.indexes i ON t.object_id = i.object_id
INNER JOIN sys.partitions p ON i.object_id = p.object_id AND i.index_id = p.index_id
INNER JOIN sys.allocation_units au ON p.partition_id = au.container_id
WHERE t.name IN ('CIKK', 'GYARTO', 'PARTNER', 'USERS')
  AND i.index_id IN (0, 1) -- Heap or clustered index
GROUP BY t.name
ORDER BY ApproxRowCount DESC;
```

### 7.3 Column Data Types and Sizes

```sql
-- Verify exact column lengths (especially for NVARCHAR fields)
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    DATA_TYPE,
    CASE
        WHEN CHARACTER_MAXIMUM_LENGTH = -1 THEN 'MAX'
        WHEN CHARACTER_MAXIMUM_LENGTH IS NOT NULL THEN CAST(CHARACTER_MAXIMUM_LENGTH AS VARCHAR(10))
        ELSE 'N/A'
    END AS MaxLength,
    IS_NULLABLE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME IN ('CIKK', 'GYARTO', 'PARTNER', 'USERS')
ORDER BY TABLE_NAME, ORDINAL_POSITION;
```

---

## 8. Indexes and Constraints

### 8.1 Existing Indexes Query

```sql
-- List all indexes on relevant tables
SELECT
    OBJECT_NAME(i.object_id) AS TableName,
    i.name AS IndexName,
    i.type_desc AS IndexType,
    i.is_unique,
    i.is_primary_key,
    COL_NAME(ic.object_id, ic.column_id) AS ColumnName,
    ic.key_ordinal AS ColumnOrder
FROM sys.indexes i
INNER JOIN sys.index_columns ic ON i.object_id = ic.object_id AND i.index_id = ic.index_id
WHERE OBJECT_NAME(i.object_id) IN ('CIKK', 'GYARTO', 'PARTNER', 'USERS')
ORDER BY TableName, IndexName, ColumnOrder;
```

### 8.2 Check Constraints Query

```sql
-- List all check constraints
SELECT
    OBJECT_NAME(cc.parent_object_id) AS TableName,
    cc.name AS ConstraintName,
    cc.definition AS ConstraintDefinition
FROM sys.check_constraints cc
WHERE OBJECT_NAME(cc.parent_object_id) IN ('CIKK', 'GYARTO', 'PARTNER', 'USERS')
ORDER BY TableName;
```

### 8.3 Recommended Indexes (Future Optimization)

Based on expected GraphQL query patterns:

```sql
-- Recommended indexes for performance (CREATE IF NOT EXISTS)

-- CIKK table indexes
CREATE NONCLUSTERED INDEX IX_CIKK_GYARTO ON CIKK(GYARTO);
CREATE NONCLUSTERED INDEX IX_CIKK_ELOALLITOPID ON CIKK(ELOALLITOPID);
CREATE NONCLUSTERED INDEX IX_CIKK_CRUS ON CIKK(CRUS);
CREATE NONCLUSTERED INDEX IX_CIKK_CIKKSZAM ON CIKK(CIKKSZAM);

-- GYARTO table indexes
CREATE NONCLUSTERED INDEX IX_GYARTO_CRUS ON GYARTO(CRUS);

-- PARTNER table indexes
CREATE NONCLUSTERED INDEX IX_PARTNER_CRUS ON PARTNER(CRUS);
CREATE NONCLUSTERED INDEX IX_PARTNER_ORSZAG ON PARTNER(ORSZAG);
```

---

## 9. Future Schema Changes

### 9.1 Phase 2: AUTH Table Creation

A new AUTH table will be added in Phase 2 to support JWT authentication:

```sql
-- To be created in Phase 2
CREATE TABLE AUTH (
    AuthId          INT             NOT NULL PRIMARY KEY IDENTITY(1,1),
    UserCode        NVARCHAR(50)    NOT NULL,
    Email           NVARCHAR(255)   NOT NULL UNIQUE,
    PasswordHash    NVARCHAR(255)   NOT NULL,
    IsActive        BIT             NOT NULL DEFAULT 1,
    LastLogin       DATETIME        NULL,
    CreatedAt       DATETIME        NOT NULL DEFAULT GETDATE(),
    UpdatedAt       DATETIME        NULL,
    CONSTRAINT FK_AUTH_USERS FOREIGN KEY (UserCode) REFERENCES USERS(USERCODE)
);

CREATE UNIQUE INDEX IX_AUTH_EMAIL ON AUTH(Email);
CREATE INDEX IX_AUTH_USERCODE ON AUTH(UserCode);
```

### 9.2 Potential Future Enhancements

**Audit Improvements:**
- Add UPUS (Update User), UPDTI (Update DateTime) to all tables
- Add soft delete flag (IsDeleted BIT)

**Performance:**
- Add composite indexes based on actual query patterns
- Consider partitioning for CIKK table if row count exceeds 1M

**Data Quality:**
- Add check constraints for data validation
- Add default values for mandatory fields

---

## 10. Schema Verification Checklist

Use this checklist after connecting to the actual database:

- [ ] Verify all 4 tables exist (CIKK, GYARTO, PARTNER, USERS)
- [ ] Document actual NVARCHAR field lengths (replace `?` in this document)
- [ ] Confirm primary keys on all tables
- [ ] Check if foreign key constraints exist
- [ ] List all existing indexes
- [ ] Verify row counts for each table
- [ ] Confirm DATETIME vs DATETIME2 data types
- [ ] Check for any additional columns not documented
- [ ] Verify nullable/not null constraints
- [ ] Document any stored procedures or views

---

## 11. Data Dictionary Export

After verification, update this section with actual data:

### 11.1 CIKK Table Data Dictionary

| Column | Type | Length | Null | PK | FK | Default | Description |
|--------|------|--------|------|----|----|---------|-------------|
| CIKKID | INT | 4 | NO | YES | NO | - | Unique article ID |
| CIKKSZAM | NVARCHAR | ? | YES | NO | NO | - | Article code/SKU |
| CIKKNEV | NVARCHAR | ? | YES | NO | NO | - | Article name |
| GYARTO | NVARCHAR | ? | YES | NO | YES | - | Manufacturer code |
| GYCIKKSZAM | NVARCHAR | ? | YES | NO | NO | - | Manufacturer's product code |
| ELOALLITOPID | INT | 4 | YES | NO | YES | - | Producer partner ID |
| CRUS | NVARCHAR | ? | YES | NO | YES | - | Creating user |
| CRDTI | DATETIME | 8 | YES | NO | NO | - | Creation timestamp |

### 11.2 GYARTO Table Data Dictionary

| Column | Type | Length | Null | PK | FK | Default | Description |
|--------|------|--------|------|----|----|---------|-------------|
| GYARTO | NVARCHAR | ? | NO | YES | NO | - | Manufacturer code |
| MEGJ | NVARCHAR | ? | YES | NO | NO | - | Notes/remarks |
| LEIRAS | NVARCHAR | ? | YES | NO | NO | - | Description/name |
| GYARTOADAT1 | NVARCHAR | ? | YES | NO | NO | - | Custom attribute 1 |
| GYARTOADAT2 | NVARCHAR | ? | YES | NO | NO | - | Custom attribute 2 |
| CRUS | NVARCHAR | ? | YES | NO | YES | - | Creating user |
| CRDTI | DATETIME | 8 | YES | NO | NO | - | Creation timestamp |

### 11.3 PARTNER Table Data Dictionary

| Column | Type | Length | Null | PK | FK | Default | Description |
|--------|------|--------|------|----|----|---------|-------------|
| PARTNERID | INT | 4 | NO | YES | NO | - | Unique partner ID |
| PARTNERNEV | NVARCHAR | ? | YES | NO | NO | - | Partner name |
| FIZOSZT | NVARCHAR | ? | YES | NO | NO | - | Payment terms |
| ORSZAG | NVARCHAR | ? | YES | NO | NO | - | Country |
| IRSZ | NVARCHAR | ? | YES | NO | NO | - | Postal code |
| VAROS | NVARCHAR | ? | YES | NO | NO | - | City |
| UTCA | NVARCHAR | ? | YES | NO | NO | - | Street address |
| CRUS | NVARCHAR | ? | YES | NO | YES | - | Creating user |
| CRDTI | DATETIME | 8 | YES | NO | NO | - | Creation timestamp |

### 11.4 USERS Table Data Dictionary

| Column | Type | Length | Null | PK | FK | Default | Description |
|--------|------|--------|------|----|----|---------|-------------|
| USERCODE | NVARCHAR | ? | NO | YES | NO | - | User code/identifier |
| USERNAME | NVARCHAR | ? | YES | NO | NO | - | Display name |

---

## 12. Related Documentation

- `01_sql_server_connection_test.md` - Connection testing procedures
- `../GraphQL_Fejlesztesi_Specifikacio.md` - Complete project specification
- Entity classes will be created in `src/GraphQLApp.Core/Entities/`
- Repository interfaces will be defined in `src/GraphQLApp.Core/Interfaces/`

---

## Document Change Log

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-05 | DevOps Infrastructure Engineer | Initial comprehensive schema documentation |

---

**Next Steps:**
1. Connect to database using instructions in `01_sql_server_connection_test.md`
2. Execute schema extraction queries from Section 7
3. Update `?` placeholders with actual column lengths
4. Complete verification checklist in Section 10
5. Proceed to Phase 1, Workstream 3 (Solution Structure)
