# Exact Railway + Vercel Settings

## Your URLs
- **Vercel Frontend**: `https://suna-new.vercel.app`
- **Railway Backend**: `https://suna-new-backend-production.up.railway.app`
- **Railway Worker**: `suna-new-worker-production.up.railway.app` (no URL needed - it's internal)

---

## Railway Setup

### ✅ Question 1: Do you need 3 Railway services?

**YES - You need 3 services:**

1. **Backend** (API server) - ✅ Already created
2. **Worker** (Background jobs) - Need to create
3. **Redis** (Cache/Queue) - Need to create

---

### ✅ Question 2: Redis Environment Variables

**How to get Redis env vars:**

1. In Railway, click **"New"** → **"Database"** → **"Add Redis"**
   - This creates a Redis service
   - Railway automatically provides env vars

2. After Redis is created, Railway will show connection variables:
   - `${{Redis.REDIS_HOST}}`
   - `${{Redis.REDIS_PORT}}`
   - `${{Redis.REDIS_PASSWORD}}`
   - `${{Redis.REDIS_USERNAME}}`

3. **In your Backend service Variables tab**, add exactly these:
   ```
   REDIS_HOST=${{Redis.REDIS_HOST}}
   REDIS_PORT=${{Redis.REDIS_PORT}}
   REDIS_PASSWORD=${{Redis.REDIS_PASSWORD}}
   REDIS_USERNAME=${{Redis.REDIS_USERNAME}}
   REDIS_SSL=True
   ```

   ⚠️ **Important**: Copy the EXACT variable names with `${{...}}` - Railway will resolve these automatically.

---

### ✅ Question 3: Database → Redis or Redis Template?

**Use: "Database" → "Add Redis"** (this is Railway's managed Redis)

NOT a template - it's under "Database" in the Railway dashboard.

---

### ✅ Question 4: Worker Port

**Answer: Workers don't need a port!**

Workers run background jobs (Dramatiq), they don't expose HTTP endpoints. Only the Backend service needs port 8000.

---

## Complete Railway Configuration

### Service 1: Backend (✅ Already set up)

**Settings:**
- **Repository**: `suna-new` (GitHub)
- **Root Directory**: `backend` ✅
- **Start Command**: (Leave default - uses Dockerfile)

**Environment Variables** (add all of these):

```bash
# Environment
ENV_MODE=production

# Supabase (from your local .env file)
SUPABASE_URL=https://iubthomvexuonmwmvzva.supabase.co
SUPABASE_ANON_KEY=<copy-from-local-env>
SUPABASE_SERVICE_ROLE_KEY=<copy-from-local-env>
SUPABASE_JWT_SECRET=<copy-from-local-env>

# Redis (use Railway variables - add these AFTER creating Redis service)
REDIS_HOST=${{Redis.REDIS_HOST}}
REDIS_PORT=${{Redis.REDIS_PORT}}
REDIS_PASSWORD=${{Redis.REDIS_PASSWORD}}
REDIS_USERNAME=${{Redis.REDIS_USERNAME}}
REDIS_SSL=True

# Frontend URL
FRONTEND_URL_ENV=https://suna-new.vercel.app

# Generate these with: openssl rand -hex 32
KORTIX_ADMIN_API_KEY=<generate-new-key>
ENCRYPTION_KEY=<generate-new-key>

# Add your LLM API keys (the ones you configured locally)
OPENAI_API_KEY=<your-key-if-using>
ANTHROPIC_API_KEY=<your-key-if-using>
# ... etc
```

---

### Service 2: Redis (Create this)

1. In Railway project, click **"New"**
2. Click **"Database"**
3. Click **"Add Redis"**
4. Railway creates it automatically - no config needed
5. After creation, you'll see the connection variables to use in Backend/Worker

---

### Service 3: Worker (Create this)

**Settings:**
- **Repository**: `suna-new` (same GitHub repo)
- **Root Directory**: `backend` (same as backend service)
- **Start Command**: `uv run dramatiq --skip-logging --processes 4 --threads 4 run_agent_background`
- **Port**: (Leave blank/unused - workers don't expose ports)

**Environment Variables** (copy ALL from Backend service):

```bash
# Same as Backend - copy all variables
ENV_MODE=production

SUPABASE_URL=https://iubthomvexuonmwmvzva.supabase.co
SUPABASE_ANON_KEY=<same-as-backend>
SUPABASE_SERVICE_ROLE_KEY=<same-as-backend>
SUPABASE_JWT_SECRET=<same-as-backend>

REDIS_HOST=${{Redis.REDIS_HOST}}
REDIS_PORT=${{Redis.REDIS_PORT}}
REDIS_PASSWORD=${{Redis.REDIS_PASSWORD}}
REDIS_USERNAME=${{Redis.REDIS_USERNAME}}
REDIS_SSL=True

FRONTEND_URL_ENV=https://suna-new.vercel.app

KORTIX_ADMIN_API_KEY=<same-as-backend>
ENCRYPTION_KEY=<same-as-backend>

# All your LLM keys (same as backend)
OPENAI_API_KEY=<same-as-backend>
# ... etc
```

---

## Vercel Configuration (✅ Already set up)

**Settings:**
- **Repository**: `suna-new`
- **Root Directory**: `new-agent/suna/frontend` (or just `frontend` if repo root is `new-agent/suna`)
- **Framework**: Next.js

**Environment Variables** (add all):

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://iubthomvexuonmwmvzva.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=<copy-from-local-env>

# Backend URL
NEXT_PUBLIC_BACKEND_URL=https://suna-new-backend-production.up.railway.app/v1

# Frontend URL
NEXT_PUBLIC_URL=https://suna-new.vercel.app

# Environment
NEXT_PUBLIC_ENV_MODE=production

# Admin Key (same as Railway)
KORTIX_ADMIN_API_KEY=<same-as-railway-backend>
```

---

## Deployment Order

1. ✅ **Backend service** (already created)
2. **Create Redis service** in Railway
3. **Add Redis env vars** to Backend service (use `${{Redis.*}}` variables)
4. **Create Worker service** in Railway
5. **Copy all env vars** from Backend to Worker
6. ✅ **Vercel** (already created)
7. **Update Vercel env vars** with Railway backend URL
8. **Update Railway Backend** `FRONTEND_URL_ENV` with Vercel URL
9. **Update Supabase** auth redirect URLs:
   - Go to Supabase Dashboard → Authentication → URL Configuration
   - Add: `https://suna-new.vercel.app/**`
   - Add: `https://suna-new.vercel.app/auth/callback`

---

## Quick Checklist

- [ ] Create Redis service in Railway
- [ ] Add Redis env vars to Backend (`${{Redis.*}}`)
- [ ] Add all other env vars to Backend
- [ ] Create Worker service in Railway
- [ ] Copy all env vars from Backend to Worker
- [ ] Set Worker start command: `uv run dramatiq --skip-logging --processes 4 --threads 4 run_agent_background`
- [ ] Add Vercel env vars
- [ ] Update Supabase auth redirect URLs
- [ ] Deploy and test!

---

## Notes

- **Worker port**: Workers don't need/expose ports - they only connect to Redis
- **Redis variables**: Use `${{Redis.REDIS_HOST}}` format - Railway resolves these automatically
- **Same env vars**: Worker needs the SAME env vars as Backend (they share Redis and Supabase)
- **Vercel URL**: Make sure to add `/v1` to the backend URL: `https://suna-new-backend-production.up.railway.app/v1`
