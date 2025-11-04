"GraphQL alkalmazás specifikáció generálása (Banana Cake Pop-pal)"

Kérlek, készítsd el egy teljes fejlesztési specifikációját (rendszertervét) annak az alkalmazásnak, amely az alábbi paraméterekkel rendelkezik:

🔹 Alapadatok:

Technológia: .NET 8, C#, Hot Chocolate GraphQL

Fejlesztői környezet: Visual Studio Code

Adatbázis: Microsoft SQL Server

Adatbázis IP címe: 10.10.10.69

Kapcsolat típusa: SQL autentikáció (felhasználónév + jelszó)

Az autentikációs adatok és az IP külön konfigurációs fájlban tárolódnak, amely nincs verziókezelésben (pl. .gitignore-ban tiltott)

🔹 Adatbázis és entitások:

Táblák (első körben):

users

cikkek

gyartok

partnerek

Támogatott műveletek: lekérdezés, beszúrás, módosítás

Lesznek tárolt eljárások is, amelyeket GraphQL-ből kell tudni hívni

🔹 GraphQL API:

Könyvtár: Hot Chocolate

Autentikáció: JWT token alapú

Schema Explorer és automatikus dokumentáció:

Használja a Banana Cake Pop integrált eszközt

Legyen elérhető a /graphql endpointon

Legyen automatikus introspekció és dokumentáció

Naplózás: minden lekérdezés és hiba legyen naplózva

Dev / Prod környezet támogatás külön konfigurációs fájlokkal

🔹 Webes felület:

A webes felület célja:

A felhasználó kiválaszthatja vagy megadhatja a táblákat és mezőket

Ezekből GraphQL lekérdezést vagy módosítást tud összeállítani

A lekérdezést futtatni tudja, és az eredményt az oldalon megjeleníti

A frontend legyen egyszerű, de modern: pl. minimalista Vue.js vagy Blazor WebAssembly

A webes felület az API-n keresztül kommunikáljon

Legyen bejelentkezés JWT tokennel

Az admin felületet csak hitelesített felhasználók érhessék el

🔹 Architektúra és futtatás:

A GraphQL API Windows szolgáltatásként fog futni

Futtatási környezet: Windows Server

Publikálás: IIS alá telepítve

A projekt kódja legyen moduláris, hogy később bővíthető legyen további adatforrásokkal és táblákkal

Naplózás és konfiguráció elválasztása

A konfigurációban szerepeljen:

MSSQL elérés adatai

JWT kulcs és token lejárat

Külön connection string dev / prod környezetre

🔹 Csatlakozó rendszerek:

Webáruház fog csatlakozni az API-hoz

Lekérdezéseket és módosításokat is végez majd

Kommunikáció GraphQL protokollon keresztül

🔹 Elvárások a specifikációval kapcsolatban:

A válaszban kérem, hogy a specifikáció tartalmazza a következőket:

Rendszerarchitektúra-diagram (leírás formájában is elég)

Komponensek és felelősségeik (API, Auth, Repository, Logger, Frontend stb.)

Példák a GraphQL séma definíciókra (Query, Mutation, Type)

JWT autentikáció implementációs terv

Kapcsolatkezelés az MSSQL szerverrel (repository pattern ajánlás)

Fájlszerkezet és projektstruktúra javaslat

Dev és Prod környezet közti különbségek

Webes felület működési vázlata

Logging és hibakezelés koncepciója

Fejlesztési és telepítési útmutató

A specifikáció legyen részletes, gyakorlati fejlesztésre alkalmas, és tartalmazzon példakódokat is, ahol ez segíti a megértést.

🧱 Kimenet formátuma:

Fejezetcímekkel tagolt, jól strukturált dokumentum

Kódminták és példák C# és GraphQL nyelven

Tartalmazza a jövőbeli bővítéshez ajánlott irányokat

🏁 Cél:

Olyan dokumentumot kérek, ami alapján egy fejlesztő azonnal el tudja kezdeni az alkalmazás implementálását Visual Studio Code-ban, és tudja, mit, hogyan, hol kell megvalósítani.

🗂️ Kulcsszavak:

MSSQL, GraphQL, HotChocolate, .NET 8, JWT, Windows Service, IIS, C#, Banana Cake Pop, Logging, Vue, Config separation, Repository pattern

Szükség esetén egészítsd ki a specifikációt ajánlott open-source csomagokkal (pl. Serilog, EF Core, Dapper, HotChocolate 13+).

✅ Feladatod tehát:
Készítsd el ennek az alkalmazásnak a teljes fejlesztési specifikációját, a fenti részletek alapján.