# MyList Feature - Netflix-style Implementation

This project implements a scalable "My List" feature similar to Netflix, with high-performance paginated reads using Redis caching, version-based cache invalidation, optimistic updates, and stampede protection.

The solution is fully production-ready and includes automated tests, CI/CD pipeline, and sample data seeding.

## 🚀 Features

### Core Requirements Implemented

- ✅ Add item to MyList
- ✅ Remove item from MyList
- ✅ Paginated "List My Items"
- ✅ Cursor-based pagination
- ✅ Sorted by createdAt DESC
- ✅ Idempotent writes
- ✅ Concurrency-safe (parallel adds)
- ✅ Redis-backed high-performance pagination
- ✅ Cache invalidation via version bumps
- ✅ Stampede protection for cold cache rebuilds
- ✅ Optimistic updates to first page cache

## 🛠️ Tech Stack

- **Backend**: Node.js (TypeScript), Express
- **Database**: MongoDB (Mongoose)
- **Cache**: Upstash Redis
- **Testing**: Jest + Supertest + mongodb-memory-server
- **Deployment**: Render.com
- **CI/CD**: GitHub Actions

## 📦 Project Structure

```
src/
  config/         # Configuration files
  db/             # Database connection
  models/         # Mongoose schemas
  routes/         # API routes
  services/       # Business logic
  utils/          # Helper functions
tests/
  integration/    # Integration tests
scripts/
  seed.ts         # Database seeding script
README.md
package.json
```

## ⚙️ Project Setup

### 1. Clone Repository

```bash
git clone https://github.com/avinashbansal95/mylist-assignment.git
cd <project-directory>
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Environment Configuration

Create a `.env` file in the root directory:

```env
PORT=4000
MONGO_URI=your-mongodb-connection-string
UPSTASH_REDIS_REST_URL=your-upstash-redis-url
UPSTASH_REDIS_REST_TOKEN=your-upstash-redis-token
REDIS_CACHE_TTL_SECONDS=30
```

### 4. Start Development Server

```bash
npm run dev
```

Server runs at `http://localhost:4000`

## 🧪 Running Tests

```bash
npm test
```

**Test coverage includes:**

- Add item
- Remove item
- Paginated listing
- Version bump logic
- Optimistic page update
- Cache hit/miss
- Stampede protection
- Concurrency (parallel writes)
- Validation failures

**Tests run on:**

- In-memory MongoDB
- In-memory Redis mock
- No external systems required

## 🌱 Seeding Sample Data

Run:

```bash
npm run seed
```

**Creates:**

- 1 demo user
- Multiple movies
- Multiple TV shows
- Initial MyList items

**Save the printed `userId`** — required for making MyList API requests.

## 🔥 API Endpoints

- **Base URL (Production)**: `https://deploy-check-us4b.onrender.com/`
- **Base URL (Local)**: `http://localhost:4000/`

All MyList endpoints require:

```
x-user-id: <SEED_USER_ID>
```

## ✅ Demo Content Creation Endpoints (For Testing Only)

> **Note:** These endpoints were created ONLY for assignment testing. They intentionally contain simplified schemas and do not include all fields from the assignment's detailed schema (genres, episodes, preferences, etc.). The MyList feature only requires id, title, and content type, so minimal versions are implemented.

### ➤ 1. Create User

**POST** `/api/users`

**Body:**

```json
{
  "username": "john"
}
```

**Example curl:**

```bash
curl -X POST http://localhost:4000/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"john"}'
```

### ➤ 2. Create Movie

**POST** `/api/movies`

**Body:**

```json
{
  "title": "Inception"
}
```

**Example curl:**

```bash
curl -X POST http://localhost:4000/api/movies \
  -H "Content-Type: application/json" \
  -d '{"title":"Inception"}'
```

### ➤ 3. Create TV Show

**POST** `/api/tvshows`

**Body:**

```json
{
  "title": "Breaking Bad"
}
```

**Example curl:**

```bash
curl -X POST http://localhost:4000/api/tvshows \
  -H "Content-Type: application/json" \
  -d '{"title":"Breaking Bad"}'
```

## 📘 MyList Feature Endpoints

### 1. List My Items (Paginated)

**GET** `/my-list`

**Query Parameters:**

- `limit` — optional, default 10
- `cursor` — only for next pages

**Headers:**

```
x-user-id: <SEED_USER_ID>
```

**First Page Example:**

```bash
curl -H "x-user-id: <SEED_USER_ID>" \
  "https://deploy-check-us4b.onrender.com/my-list?limit=10"
```
### 2. Add Item to MyList

**POST** `/my-list`

**Headers:**

```
x-user-id: <userId>
Content-Type: application/json
```

**Body:**

```json
{
  "contentId": "xxxx",
  "contentType": "movie"
}
```

### 3. Remove Item from MyList

**DELETE** `/my-list/:contentId`

**Headers:**

```
x-user-id: <userId>
```

## 🧠 High-Level Design

### Cache Key Structure

```
mylist:{userId}:version
mylist:{userId}:page:{cursorKey}:v{version}
mylist:lock:{userId}:{cursorKey}
```

### Why Versioning?

Prevents page-shifting problems on add/remove by invalidating all pages O(1).

### Optimistic Updates

If page 1 is cached: prepend new item → pop last → update cache without full rebuild.

### Stampede Protection

Uses a short-lived Redis lock:

```
SET lockKey NX EX 3
```

Only one rebuild happens for cold cache.

## 🧠 Additional Design Decisions

### Pre-prepared Redis pages

Pages are stored as fully-serialized JSON → no recomputation → <1ms response time.

### mongodb-memory-server for testing

- Deterministic
- No external DB required
- Perfect for CI/CD pipelines

## 🚀 Deployment

Deployed on **Render.com**

**Live URL:** `https://deploy-check-us4b.onrender.com/`

## 🔄 CI/CD (GitHub Actions)

**Pipeline Includes:**

1. Install dependencies
2. Run full test suite
3. If tests pass → trigger Render Deploy Hook

**Located at:** `.github/workflows/deploy-to-render.yml`

## 📝 Assumptions

- Authentication is not part of assignment
  - Using mock `x-user-id` instead of auth
- Simplified Movie / TVShow / User schemas for testing
  - Full schema from assignment can be supported if needed

## ⭐ Deliverable Checklist

| Item | Status |
|------|--------|
| Models, routes, services | ✅ |
| Redis caching + versioning | ✅ |
| Pagination + cursor logic | ✅ |
| Integration tests | ✅ |
| CI/CD | ✅ |
| Seed script | ✅ |
| Demo content creation endpoints | ✅ |
| Deployment | ✅ |

## 📞 Support

If you need help running or reviewing the project, please reach out. I will be happy to assist.