# GraphQL Projekt - Claude Kontextus

## Projekt Áttekintés

Ez egy teljes körű GraphQL alapú webalkalmazás fejlesztési projekt, amely modern technológiákat használ a backend és frontend megvalósításához.

## Technológiai Stack

### Backend
- **GraphQL API**: Rugalmas adatlekérdezési réteg
- **Node.js**: Futtatókörnyezet
- **Apollo Server** vagy **Express-GraphQL**: GraphQL szerver implementáció
- **Adatbázis**: PostgreSQL/MySQL/MongoDB (még nem meghatározott)

### Frontend
- **Vue.js 3**: Modern, reaktív frontend framework
- **Apollo Client**: GraphQL kliens a Vue.js számára
- **Composition API**: Modern Vue.js fejlesztési minta

## Projekt Felépítés

### Dokumentáció
- `GraphQL_Fejlesztesi_Specifikacio.md`: Részletes fejlesztési specifikáció és követelmények
- `graphql_prompt.md`: Projekt prompt és kezdeti leírás
- `Claude.md`: Ez a fájl - főbb kontextusok és áttekintés

### Claude Agents
A `.claude/agents/` mappában specializált agent konfigurációk találhatók:
- **backend-api-developer.md**: Backend és API fejlesztés
- **vue-frontend-developer.md**: Vue.js frontend fejlesztés
- **technical-architect.md**: Technikai architektúra tervezés
- **devops-infrastructure-engineer.md**: DevOps és infrastruktúra
- **security-auditor.md**: Biztonsági audit
- **qa-testing-specialist.md**: Tesztelés és minőségbiztosítás
- **ux-designer.md**: UX/UI tervezés
- **product-requirements-architect.md**: Követelmények specifikálása

## Fejlesztési Fázisok

### 1. Tervezési Fázis
- Követelmények definiálása
- Architektúra megtervezése
- Adatmodell kialakítása
- GraphQL schema tervezése

### 2. Backend Fejlesztés
- GraphQL API implementáció
- Adatbázis integráció
- Resolver-ek fejlesztése
- Autentikáció és autorizáció
- API tesztelés

### 3. Frontend Fejlesztés
- Vue.js alkalmazás felépítése
- Apollo Client integráció
- Komponensek fejlesztése
- State management
- UI/UX implementáció

### 4. Tesztelés és Deploy
- Unit tesztek
- Integrációs tesztek
- E2E tesztek
- DevOps pipeline
- Deployment

## Aktuális Állapot

A projekt jelenleg az **inicializálási** fázisban van:
- ✅ Git repository létrehozva
- ✅ Alapvető dokumentáció elkészült
- ✅ Claude agents konfigurálva
- ⏳ Backend fejlesztés: Még nem kezdődött el
- ⏳ Frontend fejlesztés: Még nem kezdődött el

## Következő Lépések

1. Adatbázis technológia kiválasztása
2. GraphQL schema definiálása
3. Backend projekt inicializálása (Node.js, npm/yarn)
4. Frontend projekt inicializálása (Vue.js)
5. Fejlesztői környezet beállítása

## Hasznos Parancsok

```bash
# Git műveletek
git status
git add .
git commit -m "commit message"
git push

# Backend (később)
npm install
npm run dev
npm test

# Frontend (később)
npm install
npm run serve
npm run build
```

## Megjegyzések

- A projekt Windows környezetben fejlesztve (d:\Development\graphql)
- GitHub repository: https://github.com/gyenisszabolcs/graphql.git
- A fejlesztés során követjük a best practice-eket és modern design pattern-öket
