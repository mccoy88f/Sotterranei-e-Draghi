# 🎲 DM Companion - Specifiche Complete per Sviluppo

**Documento per AI Development**  
**Versione**: 1.0  
**Data**: Gennaio 2026  
**Progetto**: Applicazione web full-stack per gestione sessioni D&D 5e

---

## 📑 Indice

1. [Panoramica Progetto](#1-panoramica-progetto)
2. [Requisiti Tecnici](#2-requisiti-tecnici)
3. [Architettura Sistema](#3-architettura-sistema)
4. [Database Schema Completo](#4-database-schema-completo)
5. [Backend API Specifications](#5-backend-api-specifications)
6. [Frontend Components](#6-frontend-components)
7. [WebSocket Events](#7-websocket-events)
8. [Docker Setup](#8-docker-setup)
9. [File Structure Completa](#9-file-structure-completa)
10. [Codice da Generare](#10-codice-da-generare)
11. [Testing Requirements](#11-testing-requirements)
12. [Deployment Instructions](#12-deployment-instructions)

---

## 1. Panoramica Progetto

### 1.1 Obiettivo
Creare un'applicazione web self-hosted per gestire sessioni di Dungeons & Dragons 5a Edizione, permettendo a DM e giocatori di:
- Gestire personaggi e campagne
- Tracciare combattimenti in tempo reale
- Consultare compendio SRD 5.2.1
- Sincronizzare stato di gioco via WebSocket
- Utilizzare tools per DM (generators, mappe)

### 1.2 Utenti Target
- **Dungeon Master (DM)**: Gestisce campagna, NPCs, combattimenti, ha accesso completo
- **Giocatori (Players)**: Gestiscono propri personaggi, vedono stato combattimento, interagiscono

### 1.3 Funzionalità Principali

**MVP (Phase 1)**:
1. Sistema autenticazione (JWT)
2. CRUD Campagne
3. CRUD Personaggi con calcoli automatici
4. Sistema combattimento con initiative tracker
5. Real-time sync via WebSocket
6. HP tracking e gestione turni

**Phase 2**:
7. Database compendio (spells, monsters, items)
8. Browser e ricerca compendio
9. Tracker condizioni avanzate
10. Chat in-app e whisper
11. Dice roller integrato

**Phase 3**:
12. DM Tools (encounter builder, generators)
13. Upload e gestione mappe
14. Token placement su grid
15. Fog of War
16. UI/UX polish e mobile responsive

### 1.4 Vincoli e Requisiti
- **Licenza**: MIT + contenuti SRD 5.2.1 sotto CC BY 4.0
- **Lingua**: Interfaccia in italiano, dati SRD in inglese
- **Self-hosted**: Deploy Docker completo
- **Performance**: Supportare 8 giocatori + DM simultanei
- **Latency**: WebSocket sync < 100ms

---

## 2. Requisiti Tecnici

### 2.1 Stack Tecnologico

**Frontend**:
```
- React 18.2+
- TypeScript 5.0+
- Vite 5.0+ (build tool)
- Tailwind CSS 3.4+
- shadcn/ui (UI components)
- Zustand (state management)
- Socket.io-client (WebSocket)
- React Router 6
- React Hook Form + Zod (forms & validation)
- Lucide React (icons)
- date-fns (date utilities)
```

**Backend**:
```
- Node.js 20 LTS
- TypeScript 5.0+
- Express.js 4.18+
- Prisma 5.0+ (ORM)
- Socket.io (WebSocket server)
- bcryptjs (password hashing)
- jsonwebtoken (JWT)
- Zod (validation)
- Multer (file upload)
- Sharp (image processing)
```

**Database & Infrastructure**:
```
- PostgreSQL 16
- Redis 7 (cache + pub/sub)
- MinIO (S3-compatible storage)
- Nginx (reverse proxy)
- Docker & Docker Compose
```

### 2.2 Versioni Minime
- Node.js >= 20.0.0
- Docker >= 24.0.0
- Docker Compose >= 2.20.0
- npm >= 10.0.0

### 2.3 Porte Utilizzate
```
80    - HTTP (Nginx)
443   - HTTPS (Nginx con SSL)
3000  - Frontend dev server
5000  - Backend API
3001  - WebSocket server
5432  - PostgreSQL
6379  - Redis
9000  - MinIO API
9001  - MinIO Console
```

---

## 3. Architettura Sistema

### 3.1 Diagramma Architettura

```
                    ┌─────────────────┐
                    │   NGINX         │
                    │ Reverse Proxy   │
                    │  (Port 80/443)  │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
       ┌──────▼──────┐ ┌────▼─────┐ ┌─────▼──────┐
       │  Frontend   │ │   API    │ │ WebSocket  │
       │   React     │ │ Express  │ │ Socket.io  │
       │ (Port 3000) │ │(Port 5000)│ │(Port 3001) │
       └─────────────┘ └─────┬────┘ └──────┬─────┘
                             │              │
              ┌──────────────┼──────────────┼─────────┐
              │              │              │         │
        ┌─────▼─────┐  ┌────▼─────┐  ┌────▼────┐ ┌──▼────┐
        │PostgreSQL │  │  Redis   │  │  Redis  │ │ MinIO │
        │    DB     │  │  Cache   │  │ Pub/Sub │ │ Files │
        │(Port 5432)│  │(Port 6379)│  │         │ │(9000) │
        └───────────┘  └──────────┘  └─────────┘ └───────┘
```

### 3.2 Flusso Dati

**Autenticazione**:
```
1. User → POST /api/auth/login (email, password)
2. API → Valida credenziali (bcrypt)
3. API → Genera JWT token
4. API → Return { token, refreshToken, user }
5. Frontend → Salva token in localStorage
6. Frontend → Include token in Authorization header per richieste successive
```

**Combattimento Real-Time**:
```
1. DM → Crea combattimento via API
2. API → Salva in DB, broadcast via Redis pub/sub
3. WebSocket → Emit 'combat-started' a tutti i client nella room
4. Giocatori → Ricevono evento, aggiornano UI
5. DM → Aggiorna HP via WebSocket
6. WebSocket → Broadcast 'hp-updated' a tutti
7. Frontend → Aggiorna UI in real-time senza refresh
```

### 3.3 Pattern Architetturali

**Backend (Layered Architecture)**:
```
Routes → Controllers → Services → Repositories (Prisma) → Database
```

**Frontend (Component-Based)**:
```
Pages → Layouts → Feature Components → UI Components → Hooks → API/Store
```

**Real-Time Communication**:
```
Socket.io Rooms per campagna/combattimento
Redis Pub/Sub per scaling multipli server WebSocket (futuro)
```

---

## 4. Database Schema Completo

### 4.1 Prisma Schema

**File**: `backend/prisma/schema.prisma`

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ==================== USERS & AUTH ====================

model User {
  id           String   @id @default(uuid())
  email        String   @unique
  username     String   @unique
  passwordHash String
  role         UserRole @default(PLAYER)
  
  avatar       String?
  
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
  
  // Relations
  ownedCampaigns Campaign[]         @relation("CampaignDM")
  characters     Character[]
  playerIn       CampaignPlayer[]
  
  @@map("users")
}

enum UserRole {
  PLAYER
  DM
  ADMIN
}

// ==================== CAMPAIGNS ====================

model Campaign {
  id          String   @id @default(uuid())
  name        String
  description String?  @db.Text
  
  dmId        String
  dm          User     @relation("CampaignDM", fields: [dmId], references: [id], onDelete: Cascade)
  
  isActive    Boolean  @default(true)
  imageUrl    String?
  
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  // Relations
  players     CampaignPlayer[]
  sessions    Session[]
  characters  Character[]
  npcs        NPC[]
  locations   Location[]
  
  @@index([dmId])
  @@index([isActive])
  @@map("campaigns")
}

model CampaignPlayer {
  id         String   @id @default(uuid())
  
  campaignId String
  campaign   Campaign @relation(fields: [campaignId], references: [id], onDelete: Cascade)
  
  userId     String
  user       User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  joinedAt   DateTime @default(now())
  
  @@unique([campaignId, userId])
  @@index([campaignId])
  @@index([userId])
  @@map("campaign_players")
}

// ==================== SESSIONS ====================

model Session {
  id            String   @id @default(uuid())
  sessionNumber Int
  title         String
  summary       String?  @db.Text
  notes         String?  @db.Text
  
  campaignId    String
  campaign      Campaign @relation(fields: [campaignId], references: [id], onDelete: Cascade)
  
  date          DateTime
  durationMinutes Int?
  
  createdAt     DateTime @default(now())
  updatedAt     DateTime @updatedAt
  
  // Relations
  combats       Combat[]
  
  @@index([campaignId])
  @@index([date])
  @@map("sessions")
}

// ==================== CHARACTERS ====================

model Character {
  id          String   @id @default(uuid())
  name        String
  race        String
  class       String
  level       Int      @default(1)
  background  String?
  alignment   String?
  
  // Ability Scores
  strength     Int
  dexterity    Int
  constitution Int
  intelligence Int
  wisdom       Int
  charisma     Int
  
  // Combat Stats
  currentHp    Int
  maxHp        Int
  tempHp       Int      @default(0)
  armorClass   Int
  initiative   Int      @default(0)
  speed        Int      @default(30)
  
  // Progression
  experience   Int      @default(0)
  proficiency  Int      @default(2)
  
  // Flexible Data (JSON)
  skills       Json     @default("{}") // { "acrobatics": true, "athletics": false, ... }
  savingThrows Json     @default("{}") // { "strength": true, "dexterity": false, ... }
  features     Json     @default("[]") // [{ name, description }]
  inventory    Json     @default("[]") // [{ name, quantity, weight }]
  equipment    Json     @default("{}") // { armor, weapons, tools }
  spellSlots   Json     @default("{}") // { "1": { max: 4, current: 2 }, ... }
  
  hitDice      Json     @default("{}") // { total: 5, current: 3, die: "d8" }
  deathSaves   Json     @default("{}") // { successes: 0, failures: 0 }
  
  avatar       String?
  notes        String?  @db.Text
  
  // Relations
  userId       String
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  campaignId   String
  campaign     Campaign @relation(fields: [campaignId], references: [id], onDelete: Cascade)
  
  spells       CharacterSpell[]
  combatants   Combatant[]
  
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
  
  @@index([userId])
  @@index([campaignId])
  @@index([name])
  @@map("characters")
}

model CharacterSpell {
  id          String    @id @default(uuid())
  
  characterId String
  character   Character @relation(fields: [characterId], references: [id], onDelete: Cascade)
  
  spellId     String
  spell       Spell     @relation(fields: [spellId], references: [id], onDelete: Cascade)
  
  prepared    Boolean   @default(false)
  alwaysPrepared Boolean @default(false)
  
  @@unique([characterId, spellId])
  @@index([characterId])
  @@index([spellId])
  @@map("character_spells")
}

// ==================== COMBAT ====================

model Combat {
  id          String   @id @default(uuid())
  name        String   @default("Combattimento")
  
  sessionId   String?
  session     Session? @relation(fields: [sessionId], references: [id], onDelete: SetNull)
  
  round       Int      @default(1)
  turnIndex   Int      @default(0)
  isActive    Boolean  @default(true)
  
  // Map data (if using)
  mapUrl      String?
  gridSize    Int?     @default(30) // pixels per square
  
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  // Relations
  combatants  Combatant[]
  
  @@index([isActive])
  @@index([sessionId])
  @@map("combats")
}

model Combatant {
  id          String   @id @default(uuid())
  
  combatId    String
  combat      Combat   @relation(fields: [combatId], references: [id], onDelete: Cascade)
  
  // Can be character OR monster
  characterId String?
  character   Character? @relation(fields: [characterId], references: [id], onDelete: Cascade)
  
  monsterId   String?
  monster     Monster?   @relation(fields: [monsterId], references: [id], onDelete: Cascade)
  
  // Combat-specific overrides
  name        String
  initiative  Int
  currentHp   Int
  maxHp       Int
  tempHp      Int      @default(0)
  armorClass  Int
  
  // Status
  conditions  Json     @default("[]") // [{ name: "Prono", duration: 0 }, ...]
  isConcentrating Boolean @default(false)
  concentrationSpell String?
  
  isNpc       Boolean  @default(false)
  isVisible   Boolean  @default(true) // DM can hide
  
  position    Json?    // { x: 10, y: 15 } for map
  
  notes       String?
  
  @@index([combatId])
  @@index([characterId])
  @@index([monsterId])
  @@map("combatants")
}

// ==================== COMPENDIUM (SRD) ====================

model Spell {
  id          String   @id @default(uuid())
  name        String   @unique
  nameIt      String?  // Italian translation
  
  level       Int
  school      String
  
  castingTime String
  range       String
  components  String   // "V, S, M (ruby dust)"
  duration    String
  
  description String   @db.Text
  higherLevel String?  @db.Text
  
  ritual      Boolean  @default(false)
  concentration Boolean @default(false)
  
  classes     String[] // ["Mago", "Stregone", "Chierico"]
  
  source      String   @default("SRD 5.2.1")
  
  // Relations
  characterSpells CharacterSpell[]
  
  @@index([level])
  @@index([school])
  @@index([name])
  @@fulltext([name, description])
  @@map("spells")
}

model Monster {
  id          String   @id @default(uuid())
  name        String   @unique
  nameIt      String?
  
  size        String   // "Medium", "Large", etc.
  type        String   // "Humanoid", "Dragon", etc.
  alignment   String?
  
  // Combat Stats
  armorClass  Int
  hitPoints   String   // "45 (6d8 + 18)"
  hitDice     String   // "6d8 + 18"
  speed       Json     // { walk: 30, fly: 60, swim: 30 }
  
  // Ability Scores
  strength     Int
  dexterity    Int
  constitution Int
  intelligence Int
  wisdom       Int
  charisma     Int
  
  // Challenge
  challengeRating Float
  experience      Int
  proficiencyBonus Int
  
  // Flexible Data
  savingThrows Json?    // { "dex": 5, "con": 6 }
  skills       Json?    // { "stealth": 7, "perception": 4 }
  damageResistances String[]
  damageImmunities  String[]
  conditionImmunities String[]
  senses       Json?    // { darkvision: 60, passive_perception: 14 }
  languages    String[]
  
  specialAbilities Json  // [{ name, description }]
  actions          Json  // [{ name, description, attack_bonus, damage }]
  reactions        Json? // [{ name, description }]
  legendaryActions Json? // [{ name, description, cost }]
  
  description  String?  @db.Text
  source       String   @default("SRD 5.2.1")
  
  // Relations
  combatants   Combatant[]
  
  @@index([challengeRating])
  @@index([type])
  @@index([name])
  @@fulltext([name, description])
  @@map("monsters")
}

model MagicItem {
  id          String   @id @default(uuid())
  name        String   @unique
  nameIt      String?
  
  type        String   // "Weapon", "Armor", "Wondrous Item", etc.
  rarity      String   // "Common", "Uncommon", "Rare", "Very Rare", "Legendary", "Artifact"
  
  requiresAttunement Boolean @default(false)
  attunementReq      String? // "by a spellcaster"
  
  description String   @db.Text
  
  source      String   @default("SRD 5.2.1")
  
  @@index([rarity])
  @@index([type])
  @@index([name])
  @@fulltext([name, description])
  @@map("magic_items")
}

// ==================== NPCs & LOCATIONS ====================

model Location {
  id          String   @id @default(uuid())
  name        String
  description String?  @db.Text
  mapUrl      String?
  
  campaignId  String
  campaign    Campaign @relation(fields: [campaignId], references: [id], onDelete: Cascade)
  
  npcs        NPC[]
  
  @@index([campaignId])
  @@map("locations")
}

model NPC {
  id          String   @id @default(uuid())
  name        String
  race        String?
  occupation  String?
  description String?  @db.Text
  personality String?  @db.Text
  
  avatar      String?
  notes       String?  @db.Text
  
  campaignId  String
  campaign    Campaign @relation(fields: [campaignId], references: [id], onDelete: Cascade)
  
  locationId  String?
  location    Location? @relation(fields: [locationId], references: [id], onDelete: SetNull)
  
  @@index([campaignId])
  @@index([locationId])
  @@map("npcs")
}
```

### 4.2 Migrations

**File**: `backend/prisma/migrations/`

Le migrations saranno generate automaticamente da Prisma con:
```bash
npx prisma migrate dev --name init
```

### 4.3 Seed Data

**File**: `backend/prisma/seed.ts`

Deve popolare:
1. User admin di test (DM)
2. Tutti gli incantesimi da SRD 5.2.1
3. Tutti i mostri da SRD 5.2.1
4. Oggetti magici da SRD 5.2.1

**Fonti SRD**:
- https://5e.tools/spells.html (export JSON)
- https://5e.tools/bestiary.html (export JSON)
- https://5e.tools/items.html (export JSON)

---

## 5. Backend API Specifications

### 5.1 Base Configuration

**File**: `backend/src/index.ts`

```typescript
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import { createServer } from 'http';
import { initializeWebSocket } from './websocket/socketServer';
import routes from './routes';
import { errorHandler } from './middleware/error.middleware';

const app = express();
const httpServer = createServer(app);

// Middleware
app.use(helmet());
app.use(cors({
  origin: process.env.FRONTEND_URL || 'http://localhost:3000',
  credentials: true
}));
app.use(morgan('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use('/api', routes);

// Error Handler
app.use(errorHandler);

// WebSocket
initializeWebSocket(httpServer);

const PORT = process.env.PORT || 5000;

httpServer.listen(PORT, () => {
  console.log(`🚀 Server running on port ${PORT}`);
});
```

### 5.2 Routes Structure

**File**: `backend/src/routes/index.ts`

```typescript
import { Router } from 'express';
import authRoutes from './auth.routes';
import campaignRoutes from './campaigns.routes';
import characterRoutes from './characters.routes';
import combatRoutes from './combat.routes';
import compendiumRoutes from './compendium.routes';
import generatorRoutes from './generators.routes';
import fileRoutes from './files.routes';

const router = Router();

router.use('/auth', authRoutes);
router.use('/campaigns', campaignRoutes);
router.use('/characters', characterRoutes);
router.use('/combats', combatRoutes);
router.use('/compendium', compendiumRoutes);
router.use('/generators', generatorRoutes);
router.use('/files', fileRoutes);

export default router;
```

### 5.3 API Endpoints Dettagliati

#### 5.3.1 Authentication Routes

**File**: `backend/src/routes/auth.routes.ts`

```typescript
POST   /api/auth/register
Body: { email, username, password, role? }
Response: { user: { id, email, username, role }, token, refreshToken }

POST   /api/auth/login
Body: { email, password }
Response: { user, token, refreshToken }

POST   /api/auth/refresh
Body: { refreshToken }
Response: { token, refreshToken }

POST   /api/auth/logout
Headers: Authorization: Bearer {token}
Response: { message: "Logged out" }

GET    /api/auth/me
Headers: Authorization: Bearer {token}
Response: { user }
```

#### 5.3.2 Campaign Routes

**File**: `backend/src/routes/campaigns.routes.ts`

```typescript
GET    /api/campaigns
Headers: Authorization: Bearer {token}
Query: ?role=dm|player
Response: [{ id, name, description, dm, players, isActive, createdAt }]

POST   /api/campaigns
Headers: Authorization: Bearer {token}
Body: { name, description?, imageUrl? }
Response: { campaign }

GET    /api/campaigns/:id
Response: { campaign with players, sessions, characters }

PATCH  /api/campaigns/:id
Body: { name?, description?, isActive?, imageUrl? }
Response: { campaign }

DELETE /api/campaigns/:id
Response: { message }

POST   /api/campaigns/:id/players
Body: { userId }
Response: { campaignPlayer }

DELETE /api/campaigns/:id/players/:userId
Response: { message }

GET    /api/campaigns/:id/sessions
Response: [{ sessions }]

POST   /api/campaigns/:id/sessions
Body: { title, date, notes?, summary? }
Response: { session }
```

#### 5.3.3 Character Routes

**File**: `backend/src/routes/characters.routes.ts`

```typescript
GET    /api/characters
Query: ?campaignId=uuid&userId=uuid
Response: [{ characters }]

POST   /api/characters
Body: { 
  campaignId, name, race, class, level,
  strength, dexterity, constitution, intelligence, wisdom, charisma,
  maxHp, armorClass, speed, ...
}
Response: { character }

GET    /api/characters/:id
Response: { character with spells, inventory, etc. }

PATCH  /api/characters/:id
Body: { any field to update }
Response: { character }

DELETE /api/characters/:id
Response: { message }

PATCH  /api/characters/:id/hp
Body: { currentHp, maxHp?, tempHp? }
Response: { character }

PATCH  /api/characters/:id/level-up
Body: { newLevel, hpIncrease }
Response: { character }

PATCH  /api/characters/:id/rest
Body: { type: 'short'|'long' }
Response: { character with reset slots/hp }

POST   /api/characters/:id/spells
Body: { spellId, prepared?, alwaysPrepared? }
Response: { characterSpell }

DELETE /api/characters/:id/spells/:spellId
Response: { message }

PATCH  /api/characters/:id/spells/:spellId
Body: { prepared }
Response: { characterSpell }

PATCH  /api/characters/:id/inventory
Body: { action: 'add'|'remove'|'update', item: {...} }
Response: { character }
```

#### 5.3.4 Combat Routes

**File**: `backend/src/routes/combat.routes.ts`

```typescript
GET    /api/combats/:id
Response: { combat with combatants sorted by initiative }

POST   /api/combats
Body: { sessionId?, name?, mapUrl?, gridSize? }
Response: { combat }

PATCH  /api/combats/:id
Body: { round?, turnIndex?, isActive?, mapUrl? }
Response: { combat }

DELETE /api/combats/:id
Response: { message }

POST   /api/combats/:id/combatants
Body: { 
  characterId? | monsterId?,
  name, initiative, maxHp, currentHp, armorClass,
  isNpc?, position?
}
Response: { combatant }

PATCH  /api/combats/:id/combatants/:combatantId
Body: { currentHp?, tempHp?, conditions?, position?, etc. }
Response: { combatant }

DELETE /api/combats/:id/combatants/:combatantId
Response: { message }

POST   /api/combats/:id/roll-initiative
Body: { combatants: [{ id, roll }] }
Response: { combat with sorted combatants }

POST   /api/combats/:id/next-turn
Response: { combat with updated turnIndex }

POST   /api/combats/:id/next-round
Response: { combat with round++ and turnIndex=0 }

POST   /api/combats/:id/apply-damage
Body: { combatantId, damage, type? }
Response: { combatant }

POST   /api/combats/:id/apply-healing
Body: { combatantId, healing }
Response: { combatant }

POST   /api/combats/:id/add-condition
Body: { combatantId, condition: { name, duration, description? } }
Response: { combatant }

DELETE /api/combats/:id/remove-condition
Body: { combatantId, conditionName }
Response: { combatant }
```

#### 5.3.5 Compendium Routes

**File**: `backend/src/routes/compendium.routes.ts`

```typescript
// Spells
GET    /api/compendium/spells
Query: ?level=1&school=evocation&class=Mago&ritual=true&search=fire
Response: [{ spells }] with pagination

GET    /api/compendium/spells/:id
Response: { spell }

// Monsters
GET    /api/compendium/monsters
Query: ?cr=5&type=dragon&size=large&search=red
Response: [{ monsters }] with pagination

GET    /api/compendium/monsters/:id
Response: { monster with full stat block }

// Magic Items
GET    /api/compendium/magic-items
Query: ?rarity=rare&type=weapon&requiresAttunement=true
Response: [{ items }] with pagination

GET    /api/compendium/magic-items/:id
Response: { item }
```

#### 5.3.6 Generator Routes

**File**: `backend/src/routes/generators.routes.ts`

```typescript
POST   /api/generators/encounter
Body: { partyLevel: 5, partySize: 4, difficulty: 'easy'|'medium'|'hard'|'deadly' }
Response: { 
  xpBudget, 
  monsters: [{ monster, quantity, totalXp }],
  totalXp,
  difficulty
}

POST   /api/generators/treasure
Body: { challengeRating: 5, isHoard: true }
Response: { 
  coins: { copper, silver, gold, platinum },
  gems: [{ value, description }],
  artObjects: [{ value, description }],
  magicItems: [{ item, rarity }]
}

POST   /api/generators/name
Body: { type: 'human'|'elf'|'dwarf'|'tavern'|'city', gender?: 'male'|'female' }
Response: { name: "Generated Name" }

POST   /api/generators/npc
Body: { race?, occupation? }
Response: { 
  name, race, gender, occupation,
  personality, appearance, quirk, bond, flaw
}

POST   /api/generators/loot
Body: { monsterCr: 3, quantity?: 1 }
Response: { items: [{ name, type, value }] }
```

#### 5.3.7 File Routes

**File**: `backend/src/routes/files.routes.ts`

```typescript
POST   /api/files/upload
Headers: Content-Type: multipart/form-data
Body: FormData with 'file' field
Response: { url: "https://minio/bucket/filename.jpg", filename, size }

DELETE /api/files/:filename
Response: { message }

POST   /api/files/avatar
Body: FormData with 'avatar' field
Response: { url }

POST   /api/files/map
Body: FormData with 'map' field
Response: { url, gridSize: auto-detected? }
```

### 5.4 Middleware

#### 5.4.1 Auth Middleware

**File**: `backend/src/middleware/auth.middleware.ts`

```typescript
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';

export const authenticate = (req: Request, res: Response, next: NextFunction) => {
  // Verifica JWT token da Authorization header
  // Aggiunge req.user con { id, email, username, role }
  // Se invalido, return 401
};

export const requireDM = (req: Request, res: Response, next: NextFunction) => {
  // Verifica che req.user.role === 'DM'
  // Altrimenti return 403
};
```

#### 5.4.2 Validation Middleware

**File**: `backend/src/middleware/validation.middleware.ts`

```typescript
import { z } from 'zod';

export const validate = (schema: z.ZodSchema) => {
  return (req: Request, res: Response, next: NextFunction) => {
    // Valida req.body con schema Zod
    // Se errore, return 400 con dettagli
  };
};

// Example schemas
export const createCharacterSchema = z.object({
  campaignId: z.string().uuid(),
  name: z.string().min(1).max(100),
  race: z.string(),
  class: z.string(),
  level: z.number().int().min(1).max(20),
  // ... etc
});
```

#### 5.4.3 Error Handler

**File**: `backend/src/middleware/error.middleware.ts`

```typescript
export const errorHandler = (err: any, req: Request, res: Response, next: NextFunction) => {
  console.error(err);
  
  if (err.name === 'ValidationError') {
    return res.status(400).json({ error: err.message, details: err.errors });
  }
  
  if (err.name === 'UnauthorizedError') {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  
  res.status(500).json({ error: 'Internal Server Error' });
};
```

---

## 6. Frontend Components

### 6.1 App Structure

**File**: `frontend/src/App.tsx`

```typescript
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { Toaster } from '@/components/ui/toaster';
import { AuthProvider } from '@/contexts/AuthContext';
import { SocketProvider } from '@/contexts/SocketContext';

// Pages
import LoginPage from '@/pages/LoginPage';
import RegisterPage from '@/pages/RegisterPage';
import DashboardPage from '@/pages/DashboardPage';
import CampaignPage from '@/pages/CampaignPage';
import CharacterSheetPage from '@/pages/CharacterSheetPage';
import CombatPage from '@/pages/CombatPage';
import CompendiumPage from '@/pages/CompendiumPage';

// Layouts
import ProtectedRoute from '@/components/layouts/ProtectedRoute';
import DMRoute from '@/components/layouts/DMRoute';

function App() {
  return (
    <BrowserRouter>
      <AuthProvider>
        <SocketProvider>
          <Routes>
            <Route path="/login" element={<LoginPage />} />
            <Route path="/register" element={<RegisterPage />} />
            
            <Route element={<ProtectedRoute />}>
              <Route path="/" element={<DashboardPage />} />
              <Route path="/campaigns/:id" element={<CampaignPage />} />
              <Route path="/characters/:id" element={<CharacterSheetPage />} />
              <Route path="/combats/:id" element={<CombatPage />} />
              <Route path="/compendium" element={<CompendiumPage />} />
              
              <Route element={<DMRoute />}>
                <Route path="/dm/encounter-builder" element={<EncounterBuilderPage />} />
                <Route path="/dm/generators" element={<GeneratorsPage />} />
              </Route>
            </Route>
          </Routes>
          
          <Toaster />
        </SocketProvider>
      </AuthProvider>
    </BrowserRouter>
  );
}

export default App;
```

### 6.2 Core Components da Implementare

#### 6.2.1 Combat Components

**InitiativeTracker.tsx**:
```typescript
interface Combatant {
  id: string;
  name: string;
  initiative: number;
  currentHp: number;
  maxHp: number;
  armorClass: number;
  conditions: Condition[];
  isNpc: boolean;
  position?: { x: number; y: number };
}

Features:
- Lista combattenti ordinata per iniziativa
- Indicatore turno corrente (highlight)
- Quick actions: Next Turn, End Combat
- HP bars visivi
- Condition badges
- Drag to reorder
- Add/Remove combatant buttons (DM only)
- Roll initiative button
```

**CombatantCard.tsx**:
```typescript
Features:
- Avatar/Token
- Name
- HP bar (current/max/temp)
- AC badge
- Initiative badge
- Conditions list
- Quick HP adjust (+/- buttons)
- Add condition button (DM)
- Remove button (DM only)
- Context menu: Apply Damage, Heal, Add Condition
```

**DamageModal.tsx**:
```typescript
Features:
- Input damage amount
- Damage type selector
- Apply to selected combatant
- Calculate with resistances/immunities
- Temp HP handling
- Confirm/Cancel
```

**ConditionSelector.tsx**:
```typescript
Features:
- List tutte condizioni SRD (Prono, Accecato, etc.)
- Duration input (rounds)
- Description tooltip
- Apply to combatant
```

#### 6.2.2 Character Components

**CharacterSheet.tsx**:
```typescript
Features:
- Header: Avatar, Name, Race, Class, Level, XP
- Ability Scores (6 boxes with mod calculation)
- Skills list con checkboxes competenza
- HP tracker (current/max/temp) con history
- AC, Initiative, Speed badges
- Saving Throws
- Proficiency Bonus
- Features & Traits list
- Equipment panel
- Spells panel (se caster)
- Inventory panel con peso totale
- Notes textarea
- Rest buttons (Short/Long)
```

**SpellList.tsx**:
```typescript
Features:
- Grouped by level (Cantrips, 1st, 2nd, etc.)
- Slot tracker per livello
- Prepared checkbox
- Click spell → show detail modal
- Cast button → consume slot
- Filter: Prepared only, By School, Search
```

**InventoryPanel.tsx**:
```typescript
Features:
- Item list con quantity
- Add Item button
- Remove/Edit item
- Total weight calculation
- Equipment slots (Armor, Weapon, etc.)
- Money tracker (cp, sp, gp, pp) con conversione
```

#### 6.2.3 Compendium Components

**SpellBrowser.tsx**:
```typescript
Features:
- Search bar
- Filters: Level, School, Class, Ritual, Concentration
- Pagination
- Card/List view toggle
- Click spell → Detail modal
- Add to character button (se tuo PG)
```

**MonsterBrowser.tsx**:
```typescript
Features:
- Search bar
- Filters: CR, Type, Size, Alignment
- Sorting: Name, CR
- Card/List view
- Click monster → Stat Block modal
- Add to Combat button (DM only)
```

**StatBlockModal.tsx**:
```typescript
Features:
- Monster name, size, type, alignment
- AC, HP, Speed
- Ability scores
- Saving Throws, Skills, Senses
- Damage Resistances/Immunities
- Condition Immunities
- Languages
- Challenge Rating, XP, Prof Bonus
- Special Abilities list
- Actions list con attack/damage
- Reactions, Legendary Actions (if any)
- Add to Combat button
```

#### 6.2.4 DM Tools Components

**EncounterBuilder.tsx**:
```typescript
Features:
- Party input: Level, Size
- Difficulty selector
- XP Budget calculation
- Search monsters to add
- Selected monsters list con quantity
- Total XP display
- Difficulty indicator (Easy/Medium/Hard/Deadly)
- Generate Random button
- Save Encounter button
- Start Combat button
```

**GeneratorPanel.tsx**:
```typescript
Features:
- Tabs: Names, NPCs, Treasure, Loot
- Input parametri
- Generate button
- Results display
- Copy to clipboard
- Save to campaign
```

#### 6.2.5 Shared Components

**DiceRoller.tsx**:
```typescript
Features:
- Formula input (1d20+5, 2d6+3)
- Quick buttons (d4, d6, d8, d10, d12, d20, d100)
- Advantage/Disadvantage toggle
- Roll button
- Result display con breakdown
- History list (ultimi 10 roll)
- Private roll checkbox (DM)
```

**ChatBox.tsx**:
```typescript
Features:
- Messages list (auto-scroll)
- Input textarea
- Send button
- Whisper to DM button
- Message types: Chat, Roll, System, Whisper
- Timestamp per messaggio
- Username coloring
```

**MapViewer.tsx**:
```typescript
Features:
- Display uploaded map
- Grid overlay (toggle)
- Zoom/Pan controls
- Token placement (drag & drop)
- Ping tool
- Drawing tool (basic lines)
- Fog of War layer (DM toggle)
- Measure distance tool
```

### 6.3 Hooks Custom

**useAuth.ts**:
```typescript
export const useAuth = () => {
  // Gestisce login, logout, user state
  // Return: { user, login, logout, register, isAuthenticated, isLoading }
};
```

**useSocket.ts**:
```typescript
export const useSocket = () => {
  // Gestisce connessione WebSocket
  // Return: { socket, isConnected, emit, on, off }
};
```

**useCombat.ts**:
```typescript
export const useCombat = (combatId: string) => {
  // Gestisce stato combattimento con sync real-time
  // Return: { 
  //   combat, 
  //   currentCombatant, 
  //   nextTurn, 
  //   applyDamage, 
  //   applyHealing, 
  //   addCondition, 
  //   removeCondition 
  // }
};
```

**useCharacter.ts**:
```typescript
export const useCharacter = (characterId: string) => {
  // Gestisce stato personaggio
  // Return: { character, updateHp, levelUp, rest, addSpell, removeSpell }
};
```

---

## 7. WebSocket Events

### 7.1 Server Implementation

**File**: `backend/src/websocket/socketServer.ts`

```typescript
import { Server } from 'socket.io';
import { Server as HTTPServer } from 'http';
import jwt from 'jsonwebtoken';

export const initializeWebSocket = (httpServer: HTTPServer) => {
  const io = new Server(httpServer, {
    cors: {
      origin: process.env.FRONTEND_URL,
      credentials: true
    },
    path: '/socket.io'
  });

  // Authentication middleware
  io.use((socket, next) => {
    const token = socket.handshake.auth.token;
    if (!token) return next(new Error('Authentication error'));
    
    try {
      const decoded = jwt.verify(token, process.env.JWT_SECRET!);
      socket.data.user = decoded;
      next();
    } catch (err) {
      next(new Error('Authentication error'));
    }
  });

  io.on('connection', (socket) => {
    console.log(`User connected: ${socket.data.user.username}`);

    // Join campaign room
    socket.on('join-campaign', (campaignId: string) => {
      socket.join(`campaign:${campaignId}`);
      console.log(`${socket.data.user.username} joined campaign ${campaignId}`);
    });

    // Join combat room
    socket.on('join-combat', (combatId: string) => {
      socket.join(`combat:${combatId}`);
      io.to(`combat:${combatId}`).emit('user-joined-combat', {
        username: socket.data.user.username
      });
    });

    // Combat events
    socket.on('combat-update', (data) => {
      io.to(`combat:${data.combatId}`).emit('combat-updated', data);
    });

    socket.on('hp-change', (data) => {
      io.to(`combat:${data.combatId}`).emit('hp-updated', data);
    });

    socket.on('condition-added', (data) => {
      io.to(`combat:${data.combatId}`).emit('condition-updated', data);
    });

    socket.on('turn-changed', (data) => {
      io.to(`combat:${data.combatId}`).emit('turn-updated', data);
    });

    // Dice rolls
    socket.on('roll-dice', (data) => {
      const room = data.isPrivate 
        ? `dm:${data.campaignId}` 
        : `campaign:${data.campaignId}`;
      
      io.to(room).emit('dice-rolled', {
        ...data,
        username: socket.data.user.username,
        userId: socket.data.user.id
      });
    });

    // Chat
    socket.on('chat-message', (data) => {
      if (data.isWhisper) {
        // Send only to DM
        io.to(`dm:${data.campaignId}`).emit('chat-message', {
          ...data,
          username: socket.data.user.username,
          isWhisper: true
        });
      } else {
        io.to(`campaign:${data.campaignId}`).emit('chat-message', {
          ...data,
          username: socket.data.user.username
        });
      }
    });

    // Map events
    socket.on('token-moved', (data) => {
      socket.to(`combat:${data.combatId}`).emit('token-moved', data);
    });

    socket.on('ping', (data) => {
      io.to(`combat:${data.combatId}`).emit('ping-received', {
        ...data,
        username: socket.data.user.username
      });
    });

    // Disconnect
    socket.on('disconnect', () => {
      console.log(`User disconnected: ${socket.data.user.username}`);
    });
  });

  return io;
};
```

### 7.2 Client Implementation

**File**: `frontend/src/contexts/SocketContext.tsx`

```typescript
import { createContext, useContext, useEffect, useState } from 'react';
import { io, Socket } from 'socket.io-client';
import { useAuth } from './AuthContext';

interface SocketContextType {
  socket: Socket | null;
  isConnected: boolean;
  emit: (event: string, data: any) => void;
  on: (event: string, callback: (...args: any[]) => void) => void;
  off: (event: string) => void;
}

const SocketContext = createContext<SocketContextType>(null!);

export const SocketProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [socket, setSocket] = useState<Socket | null>(null);
  const [isConnected, setIsConnected] = useState(false);
  const { user, token } = useAuth();

  useEffect(() => {
    if (!token) return;

    const newSocket = io(import.meta.env.VITE_WS_URL || 'ws://localhost:3001', {
      auth: { token }
    });

    newSocket.on('connect', () => {
      console.log('WebSocket connected');
      setIsConnected(true);
    });

    newSocket.on('disconnect', () => {
      console.log('WebSocket disconnected');
      setIsConnected(false);
    });

    setSocket(newSocket);

    return () => {
      newSocket.close();
    };
  }, [token]);

  const emit = (event: string, data: any) => {
    socket?.emit(event, data);
  };

  const on = (event: string, callback: (...args: any[]) => void) => {
    socket?.on(event, callback);
  };

  const off = (event: string) => {
    socket?.off(event);
  };

  return (
    <SocketContext.Provider value={{ socket, isConnected, emit, on, off }}>
      {children}
    </SocketContext.Provider>
  );
};

export const useSocket = () => useContext(SocketContext);
```

---

## 8. Docker Setup

### 8.1 Docker Compose

**File**: `docker-compose.yml`

```yaml
version: '3.9'

services:
  nginx:
    image: nginx:alpine
    container_name: dm-companion-nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - frontend
      - api
    networks:
      - dm-network

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      args:
        - VITE_API_URL=${VITE_API_URL}
        - VITE_WS_URL=${VITE_WS_URL}
    container_name: dm-companion-frontend
    restart: unless-stopped
    environment:
      - NODE_ENV=production
    networks:
      - dm-network

  api:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: dm-companion-api
    restart: unless-stopped
    ports:
      - "5000:5000"
      - "3001:3001"
    environment:
      - NODE_ENV=production
      - PORT=5000
      - WS_PORT=3001
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
      - REDIS_URL=redis://redis:6379
      - JWT_SECRET=${JWT_SECRET}
      - JWT_EXPIRES_IN=7d
      - MINIO_ENDPOINT=minio
      - MINIO_PORT=9000
      - MINIO_ACCESS_KEY=${MINIO_ACCESS_KEY}
      - MINIO_SECRET_KEY=${MINIO_SECRET_KEY}
      - MINIO_BUCKET=dm-companion
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started
      minio:
        condition: service_started
    networks:
      - dm-network
    volumes:
      - ./backend/uploads:/app/uploads

  postgres:
    image: postgres:16-alpine
    container_name: dm-companion-db
    restart: unless-stopped
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${POSTGRES_DB}
    volumes:
      - postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - dm-network

  redis:
    image: redis:7-alpine
    container_name: dm-companion-redis
    restart: unless-stopped
    command: redis-server --appendonly yes --requirepass ${REDIS_PASSWORD}
    volumes:
      - redis-data:/data
    ports:
      - "6379:6379"
    networks:
      - dm-network

  minio:
    image: minio/minio:latest
    container_name: dm-companion-minio
    restart: unless-stopped
    command: server /data --console-address ":9001"
    environment:
      - MINIO_ROOT_USER=${MINIO_ACCESS_KEY}
      - MINIO_ROOT_PASSWORD=${MINIO_SECRET_KEY}
    volumes:
      - minio-data:/data
    ports:
      - "9000:9000"
      - "9001:9001"
    networks:
      - dm-network

volumes:
  postgres-data:
    driver: local
  redis-data:
    driver: local
  minio-data:
    driver: local

networks:
  dm-network:
    driver: bridge
```

### 8.2 Backend Dockerfile

**File**: `backend/Dockerfile`

```dockerfile
FROM node:20-alpine AS base

WORKDIR /app

# Dependencies
FROM base AS deps
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

# Build
FROM base AS build
COPY package*.json ./
RUN npm ci
COPY . .
RUN npx prisma generate
RUN npm run build

# Production
FROM base AS production
ENV NODE_ENV=production

COPY --from=deps /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist
COPY --from=build /app/prisma ./prisma
COPY package*.json ./

RUN npx prisma generate

EXPOSE 5000 3001

CMD ["sh", "-c", "npx prisma migrate deploy && node dist/index.js"]
```

### 8.3 Frontend Dockerfile

**File**: `frontend/Dockerfile`

```dockerfile
FROM node:20-alpine AS base

WORKDIR /app

# Dependencies
FROM base AS deps
COPY package*.json ./
RUN npm ci && npm cache clean --force

# Build
FROM base AS build
ARG VITE_API_URL
ARG VITE_WS_URL
ENV VITE_API_URL=$VITE_API_URL
ENV VITE_WS_URL=$VITE_WS_URL

COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production (serve with nginx)
FROM nginx:alpine AS production

COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### 8.4 Nginx Configuration

**File**: `nginx/nginx.conf`

```nginx
upstream frontend {
    server frontend:80;
}

upstream api {
    server api:5000;
}

upstream websocket {
    server api:3001;
}

server {
    listen 80;
    server_name localhost;

    # Frontend
    location / {
        proxy_pass http://frontend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # API
    location /api {
        proxy_pass http://api;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # WebSocket
    location /socket.io {
        proxy_pass http://websocket;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # MinIO (optional, for direct access)
    location /files {
        proxy_pass http://minio:9000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### 8.5 Environment Variables

**File**: `.env.example`

```bash
# Database
POSTGRES_USER=dmuser
POSTGRES_PASSWORD=changethis123
POSTGRES_DB=dmcompanion
DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}

# JWT
JWT_SECRET=your-super-secret-jwt-key-min-32-chars-change-this
JWT_EXPIRES_IN=7d

# Redis
REDIS_PASSWORD=changethis456

# MinIO
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin123
MINIO_BUCKET=dm-companion

# Frontend URLs
VITE_API_URL=http://localhost/api
VITE_WS_URL=ws://localhost/socket.io

# App
NODE_ENV=production
PORT=5000
WS_PORT=3001
FRONTEND_URL=http://localhost
```

---

## 9. File Structure Completa

```
dm-companion/
├── .env
├── .env.example
├── .gitignore
├── docker-compose.yml
├── README.md
├── LICENSE
│
├── nginx/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── ssl/
│       ├── cert.pem
│       └── key.pem
│
├── frontend/
│   ├── .env
│   ├── .eslintrc.json
│   ├── .gitignore
│   ├── Dockerfile
│   ├── index.html
│   ├── nginx.conf
│   ├── package.json
│   ├── tsconfig.json
│   ├── vite.config.ts
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   │
│   ├── public/
│   │   ├── favicon.ico
│   │   └── assets/
│   │
│   └── src/
│       ├── main.tsx
│       ├── App.tsx
│       ├── vite-env.d.ts
│       │
│       ├── components/
│       │   ├── ui/                    # shadcn components
│       │   │   ├── button.tsx
│       │   │   ├── card.tsx
│       │   │   ├── dialog.tsx
│       │   │   ├── input.tsx
│       │   │   ├── select.tsx
│       │   │   └── ...
│       │   │
│       │   ├── dm/
│       │   │   ├── InitiativeTracker.tsx
│       │   │   ├── CombatManager.tsx
│       │   │   ├── EncounterBuilder.tsx
│       │   │   ├── GeneratorPanel.tsx
│       │   │   └── CompendiumBrowser.tsx
│       │   │
│       │   ├── player/
│       │   │   ├── CharacterSheet.tsx
│       │   │   ├── CharacterHeader.tsx
│       │   │   ├── AbilityScores.tsx
│       │   │   ├── Skills.tsx
│       │   │   ├── SpellList.tsx
│       │   │   ├── Inventory.tsx
│       │   │   └── HitPointTracker.tsx
│       │   │
│       │   ├── combat/
│       │   │   ├── CombatantCard.tsx
│       │   │   ├── DamageModal.tsx
│       │   │   ├── HealingModal.tsx
│       │   │   ├── ConditionSelector.tsx
│       │   │   └── TurnIndicator.tsx
│       │   │
│       │   └── shared/
│       │       ├── DiceRoller.tsx
│       │       ├── ChatBox.tsx
│       │       ├── MapViewer.tsx
│       │       ├── Navbar.tsx
│       │       ├── Sidebar.tsx
│       │       └── LoadingSpinner.tsx
│       │
│       ├── pages/
│       │   ├── LoginPage.tsx
│       │   ├── RegisterPage.tsx
│       │   ├── DashboardPage.tsx
│       │   ├── CampaignPage.tsx
│       │   ├── CharacterSheetPage.tsx
│       │   ├── CombatPage.tsx
│       │   ├── CompendiumPage.tsx
│       │   ├── EncounterBuilderPage.tsx
│       │   └── GeneratorsPage.tsx
│       │
│       ├── layouts/
│       │   ├── MainLayout.tsx
│       │   ├── ProtectedRoute.tsx
│       │   └── DMRoute.tsx
│       │
│       ├── contexts/
│       │   ├── AuthContext.tsx
│       │   └── SocketContext.tsx
│       │
│       ├── hooks/
│       │   ├── useAuth.ts
│       │   ├── useSocket.ts
│       │   ├── useCombat.ts
│       │   ├── useCharacter.ts
│       │   ├── useCampaign.ts
│       │   └── useCompendium.ts
│       │
│       ├── stores/
│       │   ├── authStore.ts
│       │   ├── campaignStore.ts
│       │   ├── combatStore.ts
│       │   └── compendiumStore.ts
│       │
│       ├── types/
│       │   ├── index.ts
│       │   ├── user.ts
│       │   ├── campaign.ts
│       │   ├── character.ts
│       │   ├── combat.ts
│       │   ├── spell.ts
│       │   ├── monster.ts
│       │   └── api.ts
│       │
│       ├── lib/
│       │   ├── api.ts
│       │   ├── socket.ts
│       │   └── utils.ts
│       │
│       ├── utils/
│       │   ├── calculations.ts
│       │   ├── diceRoller.ts
│       │   ├── validators.ts
│       │   └── formatters.ts
│       │
│       └── styles/
│           └── globals.css
│
└── backend/
    ├── .env
    ├── .eslintrc.json
    ├── .gitignore
    ├── Dockerfile
    ├── package.json
    ├── tsconfig.json
    │
    ├── prisma/
    │   ├── schema.prisma
    │   ├── seed.ts
    │   └── migrations/
    │
    └── src/
        ├── index.ts
        ├── types/
        │   └── index.ts
        │
        ├── config/
        │   ├── database.ts
        │   ├── redis.ts
        │   └── minio.ts
        │
        ├── routes/
        │   ├── index.ts
        │   ├── auth.routes.ts
        │   ├── campaigns.routes.ts
        │   ├── characters.routes.ts
        │   ├── combat.routes.ts
        │   ├── compendium.routes.ts
        │   ├── generators.routes.ts
        │   └── files.routes.ts
        │
        ├── controllers/
        │   ├── AuthController.ts
        │   ├── CampaignController.ts
        │   ├── CharacterController.ts
        │   ├── CombatController.ts
        │   ├── CompendiumController.ts
        │   ├── GeneratorController.ts
        │   └── FileController.ts
        │
        ├── services/
        │   ├── AuthService.ts
        │   ├── CampaignService.ts
        │   ├── CharacterService.ts
        │   ├── CombatService.ts
        │   ├── CompendiumService.ts
        │   ├── GeneratorService.ts
        │   ├── FileService.ts
        │   ├── DiceService.ts
        │   └── EncounterService.ts
        │
        ├── repositories/
        │   ├── UserRepository.ts
        │   ├── CampaignRepository.ts
        │   ├── CharacterRepository.ts
        │   ├── CombatRepository.ts
        │   └── CompendiumRepository.ts
        │
        ├── middleware/
        │   ├── auth.middleware.ts
        │   ├── validation.middleware.ts
        │   ├── error.middleware.ts
        │   └── rateLimiter.middleware.ts
        │
        ├── websocket/
        │   ├── socketServer.ts
        │   ├── rooms.ts
        │   └── handlers/
        │       ├── combatHandler.ts
        │       ├── chatHandler.ts
        │       └── mapHandler.ts
        │
        ├── utils/
        │   ├── jwt.ts
        │   ├── encryption.ts
        │   ├── validators.ts
        │   ├── calculations.ts
        │   └── dice.ts
        │
        └── data/
            ├── srd-spells.json
            ├── srd-monsters.json
            └── srd-magic-items.json
```

---

## 10. Codice da Generare

### 10.1 Priority 1 - MVP Core (Genera per primo)

1. **Backend Setup**
   - [ ] `backend/src/index.ts` - Server Express base
   - [ ] `backend/prisma/schema.prisma` - Schema completo
   - [ ] `backend/src/config/database.ts` - Prisma client
   - [ ] `backend/src/middleware/auth.middleware.ts` - JWT auth
   - [ ] `backend/src/middleware/error.middleware.ts` - Error handler

2. **Authentication**
   - [ ] `backend/src/routes/auth.routes.ts`
   - [ ] `backend/src/controllers/AuthController.ts`
   - [ ] `backend/src/services/AuthService.ts`
   - [ ] `frontend/src/contexts/AuthContext.tsx`
   - [ ] `frontend/src/pages/LoginPage.tsx`
   - [ ] `frontend/src/pages/RegisterPage.tsx`

3. **Campaigns**
   - [ ] `backend/src/routes/campaigns.routes.ts`
   - [ ] `backend/src/controllers/CampaignController.ts`
   - [ ] `backend/src/services/CampaignService.ts`
   - [ ] `frontend/src/pages/DashboardPage.tsx`
   - [ ] `frontend/src/pages/CampaignPage.tsx`

4. **Characters**
   - [ ] `backend/src/routes/characters.routes.ts`
   - [ ] `backend/src/controllers/CharacterController.ts`
   - [ ] `backend/src/services/CharacterService.ts`
   - [ ] `frontend/src/components/player/CharacterSheet.tsx`
   - [ ] `frontend/src/utils/calculations.ts`

5. **Combat System**
   - [ ] `backend/src/routes/combat.routes.ts`
   - [ ] `backend/src/controllers/CombatController.ts`
   - [ ] `backend/src/services/CombatService.ts`
   - [ ] `frontend/src/components/dm/InitiativeTracker.tsx`
   - [ ] `frontend/src/components/combat/CombatantCard.tsx`

6. **WebSocket**
   - [ ] `backend/src/websocket/socketServer.ts`
   - [ ] `backend/src/websocket/handlers/combatHandler.ts`
   - [ ] `frontend/src/contexts/SocketContext.tsx`
   - [ ] `frontend/src/hooks/useCombat.ts`

### 10.2 Priority 2 - Enhanced Features

7. **Compendium**
   - [ ] `backend/prisma/seed.ts` - Popola DB con SRD
   - [ ] `backend/src/routes/compendium.routes.ts`
   - [ ] `backend/src/services/CompendiumService.ts`
   - [ ] `frontend/src/components/dm/CompendiumBrowser.tsx`
   - [ ] `frontend/src/pages/CompendiumPage.tsx`

8. **Advanced Combat**
   - [ ] `frontend/src/components/combat/DamageModal.tsx`
   - [ ] `frontend/src/components/combat/ConditionSelector.tsx`
   - [ ] `backend/src/utils/dice.ts`

9. **Communication**
   - [ ] `backend/src/websocket/handlers/chatHandler.ts`
   - [ ] `frontend/src/components/shared/ChatBox.tsx`
   - [ ] `frontend/src/components/shared/DiceRoller.tsx`

### 10.3 Priority 3 - DM Tools & Polish

10. **Generators**
    - [ ] `backend/src/routes/generators.routes.ts`
    - [ ] `backend/src/services/GeneratorService.ts`
    - [ ] `backend/src/services/EncounterService.ts`
    - [ ] `frontend/src/components/dm/EncounterBuilder.tsx`
    - [ ] `frontend/src/components/dm/GeneratorPanel.tsx`

11. **Maps & Visual**
    - [ ] `backend/src/routes/files.routes.ts`
    - [ ] `backend/src/services/FileService.ts`
    - [ ] `frontend/src/components/shared/MapViewer.tsx`

12. **UI/UX Polish**
    - [ ] Responsive design per tutti i componenti
    - [ ] Dark/Light theme toggle
    - [ ] Loading states
    - [ ] Error boundaries
    - [ ] Animations con Framer Motion

### 10.4 Utility Files (Genera sempre)

- [ ] `backend/src/utils/calculations.ts` - Calcoli D&D (mod, proficiency, etc.)
- [ ] `backend/src/utils/validators.ts` - Zod schemas
- [ ] `frontend/src/lib/api.ts` - Axios wrapper con auth
- [ ] `frontend/src/types/index.ts` - TypeScript types
- [ ] `frontend/src/utils/calculations.ts` - Client-side calculations
- [ ] `frontend/src/utils/formatters.ts` - Format data for display

---

## 11. Testing Requirements

### 11.1 Backend Tests

**Framework**: Jest + Supertest

```typescript
// Example: backend/src/__tests__/auth.test.ts
describe('Authentication', () => {
  it('should register a new user', async () => {
    const res = await request(app)
      .post('/api/auth/register')
      .send({
        email: 'test@example.com',
        username: 'testuser',
        password: 'password123'
      });
    
    expect(res.status).toBe(201);
    expect(res.body).toHaveProperty('token');
  });

  it('should login with valid credentials', async () => {
    // ...
  });

  it('should reject login with invalid credentials', async () => {
    // ...
  });
});
```

**Test Coverage Minima**:
- Auth: Registration, Login, Token validation
- Combat: Create, Add combatant, Update HP, Next turn
- Characters: CRUD operations
- Compendium: Search/Filter spells and monsters

### 11.2 Frontend Tests

**Framework**: Vitest + React Testing Library

```typescript
// Example: frontend/src/components/__tests__/CharacterSheet.test.tsx
import { render, screen } from '@testing-library/react';
import CharacterSheet from '../player/CharacterSheet';

describe('CharacterSheet', () => {
  it('renders character name', () => {
    const character = { name: 'Thoren', /* ... */ };
    render(<CharacterSheet character={character} />);
    expect(screen.getByText('Thoren')).toBeInTheDocument();
  });

  it('calculates ability modifier correctly', () => {
    // Test modifier calculation (16 STR = +3)
  });
});
```

### 11.3 E2E Tests

**Framework**: Playwright

```typescript
// Example: e2e/combat.spec.ts
test('DM can start combat and players see it', async ({ page }) => {
  // Login as DM
  await page.goto('/login');
  await page.fill('[name="email"]', 'dm@example.com');
  await page.fill('[name="password"]', 'password');
  await page.click('button[type="submit"]');

  // Navigate to campaign
  await page.click('text=Test Campaign');

  // Start combat
  await page.click('text=Start Combat');
  
  // Verify combat tracker appears
  await expect(page.locator('.initiative-tracker')).toBeVisible();
});
```

---

## 12. Deployment Instructions

### 12.1 Development Setup

```bash
# Clone repository
git clone https://github.com/yourusername/dm-companion.git
cd dm-companion

# Setup environment variables
cp .env.example .env
# Edit .env with your values

# Start all services
docker-compose up -d

# Check all containers are running
docker-compose ps

# Run migrations
docker-compose exec api npx prisma migrate dev

# Seed database with SRD data
docker-compose exec api npm run seed

# Access application
# Frontend: http://localhost
# MinIO Console: http://localhost:9001
# API: http://localhost/api
# WebSocket: ws://localhost/socket.io
```

### 12.2 Production Deployment

**Prerequisites**:
- Server with Docker & Docker Compose
- Domain name (es. dmcompanion.example.com)
- SSL certificate (Let's Encrypt)

```bash
# 1. Clone on server
git clone https://github.com/yourusername/dm-companion.git
cd dm-companion

# 2. Setup production .env
cp .env.example .env
nano .env  # Edit with production values

# 3. Generate strong secrets
JWT_SECRET=$(openssl rand -base64 32)
POSTGRES_PASSWORD=$(openssl rand -base64 32)
REDIS_PASSWORD=$(openssl rand -base64 32)

# 4. Setup SSL with Certbot
docker run -it --rm \
  -v /etc/letsencrypt:/etc/letsencrypt \
  -v /var/lib/letsencrypt:/var/lib/letsencrypt \
  certbot/certbot certonly --standalone \
  -d dmcompanion.example.com

# Copy certificates
cp /etc/letsencrypt/live/dmcompanion.example.com/fullchain.pem nginx/ssl/cert.pem
cp /etc/letsencrypt/live/dmcompanion.example.com/privkey.pem nginx/ssl/key.pem

# 5. Build and start
docker-compose -f docker-compose.prod.yml up -d --build

# 6. Run migrations
docker-compose exec api npx prisma migrate deploy

# 7. Seed data
docker-compose exec api npm run seed

# 8. Setup auto-renewal for SSL
echo "0 0 1 * * docker run --rm -v /etc/letsencrypt:/etc/letsencrypt certbot/certbot renew" | crontab -
```

### 12.3 Backup Strategy

```bash
# Backup script (scripts/backup.sh)
#!/bin/bash

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"

# Backup database
docker-compose exec -T postgres pg_dump -U $POSTGRES_USER $POSTGRES_DB > "$BACKUP_DIR/db_$DATE.sql"

# Backup uploads (MinIO)
docker-compose exec -T minio mc mirror /data "$BACKUP_DIR/files_$DATE"

# Compress
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" "$BACKUP_DIR/db_$DATE.sql" "$BACKUP_DIR/files_$DATE"

# Cleanup old backups (keep last 7 days)
find $BACKUP_DIR -name "backup_*.tar.gz" -mtime +7 -delete

echo "Backup completed: backup_$DATE.tar.gz"
```

**Setup cron**:
```bash
# Daily backup at 2 AM
0 2 * * * /path/to/dm-companion/scripts/backup.sh
```

### 12.4 Monitoring

**Health Checks**:
```bash
# Check all services
docker-compose ps

# Check API health
curl http://localhost/api/health

# Check database connections
docker-compose exec postgres psql -U $POSTGRES_USER -c "SELECT count(*) FROM users;"

# Check Redis
docker-compose exec redis redis-cli PING

# View logs
docker-compose logs -f api
docker-compose logs -f frontend
```

**Metrics to Monitor**:
- API response time
- WebSocket connections count
- Database query performance
- Memory/CPU usage per container
- Disk space (especially postgres and minio volumes)

---

## 13. Istruzioni Aggiuntive per l'AI

### 13.1 Priorità Sviluppo

**Genera in questo ordine**:
1. Database schema e migrations
2. Backend auth system completo
3. Frontend auth pages + context
4. Backend campaigns/characters CRUD
5. Frontend dashboard e character sheet
6. Backend combat system
7. Frontend initiative tracker
8. WebSocket integration
9. Seed script con SRD
10. Compendium browser
11. Advanced features

### 13.2 Code Style Guidelines

**TypeScript**:
- Use `interface` per types pubblici
- Use `type` per unions/intersections
- Prefer `const` over `let`
- Use arrow functions
- Async/await over promises
- Proper error handling con try/catch

**React**:
- Functional components only
- Custom hooks per logic riutilizzabile
- Props destructuring
- Prop types con TypeScript interfaces
- Use Context per stato globale
- Zustand per state management complesso

**Naming**:
- Components: PascalCase (`CharacterSheet.tsx`)
- Files: kebab-case (`character-sheet.utils.ts`)
- Functions: camelCase (`calculateModifier`)
- Constants: UPPER_SNAKE_CASE (`MAX_PARTY_SIZE`)
- Database: snake_case (`user_id`)

**Comments**:
- JSDoc per funzioni pubbliche
- Inline comments per logica complessa
- TODO comments con nome e data

### 13.3 Performance Considerations

**Backend**:
- Use Prisma select per limitare campi
- Implement pagination (skip/take)
- Cache queries frequenti in Redis (spells list, monsters)
- Use database indexes
- Debounce WebSocket events

**Frontend**:
- Lazy load pages con React.lazy
- Memoize components pesanti (React.memo)
- Use useMemo per calcoli costosi
- Virtualize long lists (react-window)
- Optimize images (WebP, lazy loading)

### 13.4 Security Checklist

- [ ] JWT tokens con expiration
- [ ] Password hashing con bcrypt (12+ rounds)
- [ ] Input validation su tutti gli endpoints
- [ ] SQL injection prevention (Prisma)
- [ ] XSS protection (sanitize HTML)
- [ ] CSRF tokens per forms
- [ ] Rate limiting su auth endpoints
- [ ] HTTPS only in production
- [ ] Secure cookies (httpOnly, secure, sameSite)
- [ ] Environment variables per secrets

### 13.5 Accessibilità (A11y)

**Frontend Requirements**:
- Semantic HTML elements
- ARIA labels dove necessario
- Keyboard navigation support
- Focus indicators visibili
- Color contrast ratio WCAG AA
- Alt text per immagini
- Screen reader friendly

### 13.6 Errori Comuni da Evitare

**Backend**:
- ❌ Non hashare password
- ❌ Esporre stack traces in prod
- ❌ Non validare input utente
- ❌ Queries N+1 (use Prisma include)
- ❌ Non gestire eccezioni async

**Frontend**:
- ❌ Non gestire loading states
- ❌ Non gestire errori API
- ❌ Infinite re-renders (useEffect deps)
- ❌ Non memoize callbacks passati a children
- ❌ Non cleanup WebSocket listeners

### 13.7 Testing Strategy

**Unit Tests**:
- Utils functions (calculations, validators)
- Services (business logic)
- Custom hooks

**Integration Tests**:
- API endpoints
- Database operations
- WebSocket events

**E2E Tests**:
- User flows critici (login, create character, start combat)
- Cross-browser testing

### 13.8 Documentation

**Genera anche**:
- API documentation (Swagger/OpenAPI)
- Component storybook
- Setup instructions (README.md)
- Troubleshooting guide
- Contribution guidelines

---

## 14. Risorse e Reference

### 14.1 Links Utili

**D&D 5e SRD**:
- https://dnd.wizards.com/resources/systems-reference-document
- https://5e.tools (JSON exports)
- https://www.dndbeyond.com

**Stack Documentation**:
- React: https://react.dev
- TypeScript: https://www.typescriptlang.org/docs/
- Prisma: https://www.prisma.io/docs
- Socket.io: https://socket.io/docs/
- Tailwind: https://tailwindcss.com/docs
- shadcn/ui: https://ui.shadcn.com

**Tools**:
- Docker: https://docs.docker.com
- PostgreSQL: https://www.postgresql.org/docs/
- Redis: https://redis.io/documentation

### 14.2 SRD Data Sources

**Spells**:
```
Source: https://5e.tools/data/spells/spells-phb.json
Format: JSON array with spell objects
Fields needed: name, level, school, castingTime, range, components, duration, description, classes
```

**Monsters**:
```
Source: https://5e.tools/data/bestiary/bestiary-mm.json
Format: JSON array with monster objects
Fields needed: name, size, type, ac, hp, speed, str, dex, con, int, wis, cha, cr, actions, abilities
```

**Magic Items**:
```
Source: https://5e.tools/data/items/items-dmg.json
Format: JSON array with item objects
Fields needed: name, type, rarity, requiresAttunement, description
```

---

## 15. Success Criteria

### 15.1 MVP Success Metrics

**Functional**:
- [ ] User can register and login
- [ ] DM can create campaign and invite players
- [ ] Players can create characters with automatic calculations
- [ ] DM can start combat with initiative tracking
- [ ] All users see combat updates in real-time (< 100ms latency)
- [ ] HP changes reflect immediately for all users

**Performance**:
- [ ] API response time < 200ms (95th percentile)
- [ ] Frontend initial load < 3s
- [ ] Database queries < 100ms average
- [ ] Supports 8 concurrent users smoothly

**Quality**:
- [ ] 80%+ test coverage critical paths
- [ ] Zero console errors in production
- [ ] Mobile responsive (min 375px width)
- [ ] Works on Chrome, Firefox, Safari, Edge

### 15.2 Phase 2 Success Metrics

**Functional**:
- [ ] Complete SRD compendium searchable
- [ ] Advanced combat (conditions, concentration)
- [ ] Chat and dice roller working
- [ ] Whisper to DM functional

**Quality**:
- [ ] 85%+ test coverage
- [ ] Lighthouse score > 90
- [ ] No major accessibility issues

### 15.3 Phase 3 Success Metrics

**Functional**:
- [ ] All DM generators working
- [ ] Map upload and token placement
- [ ] Fog of War basic implementation
- [ ] UI polish complete

**Quality**:
- [ ] Production-ready deployment
- [ ] Documentation complete
- [ ] User onboarding flow smooth

---

## 16. Final Notes

### 16.1 Assumptions

- Users have basic D&D 5e knowledge
- Self-hosted deployment (not SaaS initially)
- Single DM per campaign (no co-DM support)
- English/Italian content only
- Desktop-first, mobile-friendly
- Modern browsers only (no IE11)

### 16.2 Future Enhancements (Post-Launch)

- Multi-language support
- Import/Export characters (JSON)
- Campaign sharing/templates
- Advanced map tools (measurement, drawing)
- Voice chat integration
- Mobile native apps
- AI-assisted DM tools
- Homebrew content support
- Marketplace for community content
- Campaign analytics/statistics

### 16.3 Known Limitations

- Fog of War basic (non dynamic)
- No offline mode
- Single server instance (no clustering initially)
- Limited file size uploads (10MB max)
- No video chat (use external tools)

---

**Questo documento contiene tutte le specifiche necessarie per sviluppare il progetto completo. Generare il codice seguendo l'ordine di priorità indicato nella sezione 10.**

**Per domande o chiarimenti, consulta le sezioni 13 (Istruzioni AI) e 14 (Risorse).**

**Licenza**: MIT per il codice, CC BY 4.0 per i contenuti SRD.

**Buon sviluppo! 🎲🚀**
