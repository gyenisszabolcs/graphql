# Phase 0 - Task 4: Database Schema Design

**Agent Role:** Technical Architect
**Date:** 2025-11-04
**Status:** ✅ COMPLETED (UPDATED WITH EXISTING DATABASE)

**⚠️ FONTOS VÁLTOZÁS:** A projekt egy **már létező adatbázist** használ. Ez a dokumentum a meglévő struktúrát dokumentálja.

## 1. Existing Database Information

- **Database Name:** `dev_graphql`
- **SQL Server:** `10.10.10.69`
- **Authentication:** SQL Server Authentication
- **Status:** ✅ Already exists, tables are populated

## 2. Entity Relationship Diagram (ERD)

```
┌──────────────────────────────────────────────────────────────────┐
│                          USERS                                    │
├──────────────────────────────────────────────────────────────────┤
│ PK │ USERCODE        NVARCHAR(?)                                 │
│    │ USERNAME        NVARCHAR(?)                                 │
└──────────────────────────────────────────────────────────────────┘
                         │
                         │ 1:USERCODE
                         │
         ┌───────────────┼───────────────────┐
         │               │                   │
         ▼ N:CRUS        ▼ N:CRUS            ▼ N:CRUS
┌─────────────────┐ ┌──────────────┐ ┌─────────────────┐
│     GYARTO      │ │     CIKK     │ │    PARTNER      │
├─────────────────┤ ├──────────────┤ ├─────────────────┤
│ PK │ GYARTO    │ │ PK│ CIKKID   │ │ PK│ PARTNERID  │
│    │ MEGJ      │ │   │ CIKKSZAM │ │   │ PARTNERNEV │
│    │ LEIRAS    │ │   │ CIKKNEV  │ │   │ FIZOSZT    │
│    │GYARTOADAT1│ │FK │ GYARTO───┼─┘   │ ORSZAG     │
│    │GYARTOADAT2│ │   │GYCIKKSZAM│     │ IRSZ       │
│    │ CRUS      │ │FK │ELOALLITOP│────>│ VAROS      │
│    │ CRDTI     │ │   │ CRUS     │     │ UTCA       │
└─────────────────┘ │   │ CRDTI    │     │ CRUS       │
                    └──────────────┘     │ CRDTI      │
                                         └─────────────┘

Relationships:
- GYARTO (1:GYARTO) ──────< (N:GYARTO) CIKK
- PARTNER (1:PARTNERID) ──────< (N:ELOALLITOPID) CIKK
- USERS (1:USERCODE) ──────< (N:CRUS) CIKK
- USERS (1:USERCODE) ──────< (N:CRUS) GYARTO
- USERS (1:USERCODE) ──────< (N:CRUS) PARTNER
```

## 3. Table Definitions (Existing Tables)

### 3.1 USERS Table

```sql
-- Meglévő tábla struktúra
CREATE TABLE USERS (
    USERCODE        NVARCHAR(?)     NOT NULL PRIMARY KEY,
    USERNAME        NVARCHAR(?)     NULL
);
```

**Megjegyzés:** A USERS tábla minimális. Az autentikációhoz szükséges további mezők (PasswordHash, Email, stb.) NEM részei az aktuális táblának. Későbbi fázisban:
- Új AUTH tábla létrehozása VAGY
- USERS tábla bővítése (ha az engedélyezett)

**Mezők:**
- `USERCODE` (PK): Felhasználó egyedi kódja
- `USERNAME`: Felhasználó megjelenített neve

### 3.2 GYARTO Table

```sql
-- Meglévő tábla struktúra
CREATE TABLE GYARTO (
    GYARTO          NVARCHAR(?)     NOT NULL PRIMARY KEY,
    MEGJ            NVARCHAR(?)     NULL,
    LEIRAS          NVARCHAR(?)     NULL,
    GYARTOADAT1     NVARCHAR(?)     NULL,
    GYARTOADAT2     NVARCHAR(?)     NULL,
    CRUS            NVARCHAR(?)     NULL,    -- FK -> USERS.USERCODE
    CRDTI           DATETIME        NULL
);
```

**Mezők:**
- `GYARTO` (PK): Gyártó egyedi azonosítója (string)
- `MEGJ`: Megjegyzés
- `LEIRAS`: Részletes leírás
- `GYARTOADAT1`: Gyártó specifikus adat 1
- `GYARTOADAT2`: Gyártó specifikus adat 2
- `CRUS`: Létrehozó felhasználó kódja (FK -> USERS.USERCODE)
- `CRDTI`: Létrehozás dátuma/ideje

### 3.3 CIKK Table

```sql
-- Meglévő tábla struktúra
CREATE TABLE CIKK (
    CIKKID          INT             NOT NULL PRIMARY KEY,
    CIKKSZAM        NVARCHAR(?)     NULL,
    CIKKNEV         NVARCHAR(?)     NULL,
    GYARTO          NVARCHAR(?)     NULL,    -- FK -> GYARTO.GYARTO
    GYCIKKSZAM      NVARCHAR(?)     NULL,
    ELOALLITOPID    INT             NULL,    -- FK -> PARTNER.PARTNERID
    CRUS            NVARCHAR(?)     NULL,    -- FK -> USERS.USERCODE
    CRDTI           DATETIME        NULL
);
```

**Mezők:**
- `CIKKID` (PK): Cikk egyedi azonosítója (INT)
- `CIKKSZAM`: Cikk saját belső kódja/száma
- `CIKKNEV`: Cikk megnevezése
- `GYARTO`: Gyártó azonosító (FK -> GYARTO.GYARTO) - string típus!
- `GYCIKKSZAM`: Gyártói cikkszám
- `ELOALLITOPID`: Előállító partner azonosítója (FK -> PARTNER.PARTNERID)
- `CRUS`: Létrehozó felhasználó kódja (FK -> USERS.USERCODE)
- `CRDTI`: Létrehozás dátuma/ideje

**⚠️ FONTOS:** A GYARTO mező STRING típusú, nem INT! A kapcsolat a GYARTO tábla GYARTO mezőjére mutat.

### 3.4 PARTNER Table

```sql
-- Meglévő tábla struktúra
CREATE TABLE PARTNER (
    PARTNERID       INT             NOT NULL PRIMARY KEY,
    PARTNERNEV      NVARCHAR(?)     NULL,
    FIZOSZT         NVARCHAR(?)     NULL,
    ORSZAG          NVARCHAR(?)     NULL,
    IRSZ            NVARCHAR(?)     NULL,
    VAROS           NVARCHAR(?)     NULL,
    UTCA            NVARCHAR(?)     NULL,
    CRUS            NVARCHAR(?)     NULL,    -- FK -> USERS.USERCODE
    CRDTI           DATETIME        NULL
);
```

**Mezők:**
- `PARTNERID` (PK): Partner egyedi azonosítója (INT)
- `PARTNERNEV`: Partner neve
- `FIZOSZT`: Fizetési osztály/mód
- `ORSZAG`: Ország
- `IRSZ`: Irányítószám
- `VAROS`: Város
- `UTCA`: Utca, házszám
- `CRUS`: Létrehozó felhasználó kódja (FK -> USERS.USERCODE)
- `CRDTI`: Létrehozás dátuma/ideje

## 4. Foreign Key Constraints

**Megjegyzés:** A következő kapcsolatok logikailag léteznek, de lehet, hogy NEM érvényesülnek adatbázis szinten (CONSTRAINT hiánya):

```sql
-- Logikai kapcsolatok (lehet hogy nincs CONSTRAINT):
-- CIKK.GYARTO -> GYARTO.GYARTO (string-string kapcsolat)
-- CIKK.ELOALLITOPID -> PARTNER.PARTNERID
-- CIKK.CRUS -> USERS.USERCODE
-- GYARTO.CRUS -> USERS.USERCODE
-- PARTNER.CRUS -> USERS.USERCODE
```

**Teendő Phase 2-ben:**
- Ellenőrizni a meglévő FOREIGN KEY constraint-eket
- Hiányzó constraint-ek hozzáadása (ha szükséges és engedélyezett)

## 5. Indexes (To Be Analyzed in Phase 2)

A következő indexek szükségesek lehetnek a teljesítményhez:

```sql
-- Javasolt indexek (Phase 2-ben elemezni kell):
CREATE INDEX IX_CIKK_GYARTO ON CIKK(GYARTO);
CREATE INDEX IX_CIKK_ELOALLITOPID ON CIKK(ELOALLITOPID);
CREATE INDEX IX_CIKK_CRUS ON CIKK(CRUS);
CREATE INDEX IX_GYARTO_CRUS ON GYARTO(CRUS);
CREATE INDEX IX_PARTNER_CRUS ON PARTNER(CRUS);
CREATE INDEX IX_CIKK_CIKKSZAM ON CIKK(CIKKSZAM); -- Ha gyakran keresünk rá
```

**Teendő Phase 2-ben:**
- Meglévő indexek lekérdezése: `sp_helpindex 'CIKK'`
- Hiányzó indexek azonosítása
- Új indexek létrehozása (ha szükséges)

## 6. Stored Procedures (To Be Created in Phase 2)

**Javasolt tárolt eljárások:**

### 6.1 GetCikkekByGyarto

```sql
CREATE PROCEDURE GetCikkekByGyarto
    @Gyarto NVARCHAR(100)
AS
BEGIN
    SELECT
        c.CIKKID,
        c.CIKKSZAM,
        c.CIKKNEV,
        c.GYARTO,
        c.GYCIKKSZAM,
        c.ELOALLITOPID,
        c.CRUS,
        c.CRDTI,
        g.MEGJ AS GyartoMegj,
        g.LEIRAS AS GyartoLeiras
    FROM CIKK c
    INNER JOIN GYARTO g ON c.GYARTO = g.GYARTO
    WHERE c.GYARTO = @Gyarto
    ORDER BY c.CIKKNEV;
END
```

### 6.2 GetStatisztika

```sql
CREATE PROCEDURE GetStatisztika
AS
BEGIN
    SELECT
        (SELECT COUNT(*) FROM CIKK) AS CikkekSzama,
        (SELECT COUNT(*) FROM GYARTO) AS GyartokSzama,
        (SELECT COUNT(*) FROM PARTNER) AS PartnerekSzama,
        (SELECT COUNT(*) FROM USERS) AS UsersSzama,
        (SELECT COUNT(DISTINCT GYARTO) FROM CIKK) AS HasznaltGyartokSzama,
        (SELECT COUNT(DISTINCT ELOALLITOPID) FROM CIKK WHERE ELOALLITOPID IS NOT NULL) AS HasznaltPartnerekSzama;
END
```

### 6.3 GetCikkWithDetails

```sql
CREATE PROCEDURE GetCikkWithDetails
    @CikkId INT
AS
BEGIN
    SELECT
        c.CIKKID,
        c.CIKKSZAM,
        c.CIKKNEV,
        c.GYARTO,
        c.GYCIKKSZAM,
        c.ELOALLITOPID,
        c.CRUS,
        c.CRDTI,
        g.MEGJ AS GyartoMegj,
        g.LEIRAS AS GyartoLeiras,
        p.PARTNERNEV,
        p.ORSZAG AS PartnerOrszag,
        u.USERNAME AS CreatedByUser
    FROM CIKK c
    LEFT JOIN GYARTO g ON c.GYARTO = g.GYARTO
    LEFT JOIN PARTNER p ON c.ELOALLITOPID = p.PARTNERID
    LEFT JOIN USERS u ON c.CRUS = u.USERCODE
    WHERE c.CIKKID = @CikkId;
END
```

## 7. Connection String Configuration

**appsettings.json (base config, no credentials):**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=dev_graphql;TrustServerCertificate=True;"
  }
}
```

**appsettings.Local.json (git-ignored, with credentials):**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=10.10.10.69;Initial Catalog=dev_graphql;User ID=YOUR_USERNAME;Password=YOUR_PASSWORD;TrustServerCertificate=True;"
  },
  "JwtSettings": {
    "SecretKey": "your-super-secret-key-min-32-chars-long-CHANGE-THIS-VALUE"
  }
}
```

## 8. Data Migration Strategy

**⚠️ NINCS MIGRATION SZÜKSÉGES!**

A projekt egy meglévő, működő adatbázist használ. Tábla létrehozás vagy séma módosítás NEM szükséges.

**Ami szükséges lehet:**
1. **Phase 2 - Adatbázis integráció:**
   - Séma reverse engineering (pontos mező típusok, hosszak lekérdezése)
   - Index optimalizálás
   - Tárolt eljárások létrehozása

2. **Autentikáció megoldása:**
   - **Opció A:** Új AUTH tábla létrehozása (UserCode, PasswordHash, Email, IsActive, stb.)
   - **Opció B:** USERS tábla bővítése (ha engedélyezett)
   - **Opció C:** Külső authentication provider (Azure AD, Identity Server)

## 9. Reverse Engineering Script (For Phase 2)

```sql
-- Pontos séma lekérdezése minden táblához
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    DATA_TYPE,
    CHARACTER_MAXIMUM_LENGTH,
    IS_NULLABLE,
    COLUMN_DEFAULT
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME IN ('USERS', 'GYARTO', 'CIKK', 'PARTNER')
ORDER BY TABLE_NAME, ORDINAL_POSITION;

-- Meglévő indexek lekérdezése
EXEC sp_helpindex 'CIKK';
EXEC sp_helpindex 'GYARTO';
EXEC sp_helpindex 'PARTNER';
EXEC sp_helpindex 'USERS';

-- Foreign key kapcsolatok lekérdezése
SELECT
    fk.name AS ForeignKeyName,
    tp.name AS ParentTable,
    cp.name AS ParentColumn,
    tr.name AS ReferencedTable,
    cr.name AS ReferencedColumn
FROM sys.foreign_keys AS fk
INNER JOIN sys.foreign_key_columns AS fkc ON fk.object_id = fkc.constraint_object_id
INNER JOIN sys.tables AS tp ON fkc.parent_object_id = tp.object_id
INNER JOIN sys.columns AS cp ON fkc.parent_object_id = cp.object_id AND fkc.parent_column_id = cp.column_id
INNER JOIN sys.tables AS tr ON fkc.referenced_object_id = tr.object_id
INNER JOIN sys.columns AS cr ON fkc.referenced_object_id = cr.object_id AND fkc.referenced_column_id = cr.column_id
WHERE tp.name IN ('CIKK', 'GYARTO', 'PARTNER');
```

## 10. Backup Strategy Recommendations

**⚠️ A backup stratégia ellenőrzése szükséges Phase 2-ben!**

Javasolt backup stratégia:
- **Full backup:** Hetente egyszer (vasárnap éjjel)
- **Differential backup:** Naponta (éjjel 2:00)
- **Transaction log backup:** Óránként
- **Retention:** 30 nap
- **Recovery Time Objective (RTO):** 1 óra
- **Recovery Point Objective (RPO):** 15 perc

## 11. Success Criteria

- [x] Meglévő adatbázis struktúra dokumentálva
- [x] ERD diagram elkészült a valós táblákkal
- [x] Tábla definíciók dokumentálva
- [x] Kapcsolatok azonosítva
- [x] Javasolt indexek listázva
- [x] Javasolt tárolt eljárások tervezve
- [x] Connection string sablon elkészült
- [x] Reverse engineering script elkészült
- [x] Backup stratégia javaslat dokumentálva

## 12. Next Steps (Phase 2)

1. Futtatni a reverse engineering script-et a pontos séma lekérdezéséhez
2. Entity osztályok létrehozása C#-ban a pontos mezőnevekkel és típusokkal
3. Dapper repository-k implementálása
4. Tárolt eljárások létrehozása
5. Index optimalizálás
6. Autentikáció megoldásának kiválasztása
7. Kapcsolat tesztelése

---

**Status:** ✅ **COMPLETED AND UPDATED**
**Deliverable:** Meglévő adatbázis struktúra teljes dokumentációja
**Ready for:** Phase 1 és Phase 2 implementáció
