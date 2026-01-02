# Deployment Guide: Railway + Vercel

## Architecture

- **Railway**: Backend API + Worker + Redis
- **Vercel**: Frontend (Next.js)

---

## 1. Railway Setup (Backend)

### Create Railway Project

1. Go to [railway.app](https://railway.app)
2. Create new project
3. Add 3 services:
   - **Backend** (from GitHub repo)
   - **Redis** (from Railway's Redis template)
   - **Worker** (from GitHub repo - same as backend but different command)

### Railway Service: Backend

**Repository**: Your GitHub repo  
**Root Directory**: `new-agent/suna/backend`  
**Start Command**: Leave default (uses Dockerfile)

**Environment Variables** (Required):

```bash
# Environment Mode
ENV_MODE=production

# Supabase (from your .env file)
SUPABASE_URL=https://iubthomvexuonmwmvzva.supabase.co
SUPABASE_ANON_KEY=<your-anon-key>
SUPABASE_SERVICE_ROLE_KEY=<your-service-role-key>
SUPABASE_JWT_SECRET=<your-jwt-secret>

# Redis (from Railway Redis service)
REDIS_HOST=${{Redis.REDIS_HOST}}
REDIS_PORT=${{Redis.REDIS_PORT}}
REDIS_PASSWORD=${{Redis.REDIS_PASSWORD}}
REDIS_USERNAME=${{Redis.REDIS_USERNAME}}
REDIS_SSL=True

# Frontend URL (will be your Vercel URL)
FRONTEND_URL_ENV=https://your-app.vercel.app

# LLM API Keys (add the ones you're using)
OPENAI_API_KEY=<your-key>
ANTHROPIC_API_KEY=<your-key>
# ... other LLM keys you configured

# Optional but recommended
KORTIX_ADMIN_API_KEY=<generate-new-key>
ENCRYPTION_KEY=<generate-new-key>
```

**Generate Keys:**
```bash
# Generate KORTIX_ADMIN_API_KEY (32 byte hex)
openssl rand -hex 32

# Generate ENCRYPTION_KEY (32 byte hex)
openssl rand -hex 32
```

**Port**: Railway will auto-assign, but the Dockerfile exposes port 8000

### Railway Service: Redis

1. Click "New" → "Database" → "Add Redis"
2. Railway will automatically create Redis instance
3. Use the variables it provides for backend REDIS_* vars

### Railway Service: Worker

**Same as Backend BUT:**
- **Start Command**: `uv run dramatiq --skip-logging --processes 4 --threads 4 run_agent_background`
- **Environment Variables**: Same as Backend service (copy all)

**Note**: Railway might not support separate services from same repo easily. Alternative:
- Use the same backend service
- Add `WORKER_MODE=true` env var
- Modify start command based on env var (or use separate Railway projects)

---

## 2. Vercel Setup (Frontend)

### Create Vercel Project

1. Go to [vercel.com](https://vercel.com)
2. Import your GitHub repository
3. **Root Directory**: `new-agent/suna/frontend`
4. **Framework Preset**: Next.js

### Vercel Environment Variables

Add these in Vercel Dashboard → Your Project → Settings → Environment Variables:

```bash
# Supabase (from your .env file)
NEXT_PUBLIC_SUPABASE_URL=https://iubthomvexuonmwmvzva.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=<your-anon-key>

# Backend URL (your Railway backend URL)
NEXT_PUBLIC_BACKEND_URL=https://your-backend.railway.app/v1

# Frontend URL (your Vercel URL - set after first deploy)
NEXT_PUBLIC_URL=https://your-app.vercel.app

# Environment Mode
NEXT_PUBLIC_ENV_MODE=production

# Admin API Key (same as Railway)
KORTIX_ADMIN_API_KEY=<same-as-railway>
```

**Important**: Set `NEXT_PUBLIC_URL` after your first deployment, then redeploy.

### Vercel Build Settings

**Build Command**: `npm run build` (default)  
**Output Directory**: `.next` (default)  
**Install Command**: `npm install` (default)

---

## 3. Post-Deployment Configuration

### Update Railway Backend CORS

After getting your Vercel URL, update Railway backend:
- Add `FRONTEND_URL_ENV=https://your-app.vercel.app`
- Redeploy backend service

The backend code should automatically allow your Vercel domain in production mode.

### Update Supabase Auth Redirect URLs

1. Go to Supabase Dashboard → Authentication → URL Configuration
2. Add your Vercel URL: `https://your-app.vercel.app/**`
3. Add callback URL: `https://your-app.vercel.app/auth/callback`

---

## 4. Required Environment Variables Summary

### Backend (Railway) - Required:
- `ENV_MODE=production`
- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`
- `SUPABASE_JWT_SECRET`
- `REDIS_HOST` (from Railway Redis)
- `REDIS_PORT` (from Railway Redis)
- `REDIS_PASSWORD` (from Railway Redis)
- `REDIS_USERNAME` (from Railway Redis)
- `REDIS_SSL=True`
- `FRONTEND_URL_ENV` (your Vercel URL)
- `KORTIX_ADMIN_API_KEY`
- `ENCRYPTION_KEY`

### Backend (Railway) - Optional (LLM Providers):
- `OPENAI_API_KEY`
- `ANTHROPIC_API_KEY`
- `GROQ_API_KEY`
- `OPENROUTER_API_KEY`
- `XAI_API_KEY`
- `GEMINI_API_KEY`
- etc. (only the ones you configured)

### Frontend (Vercel) - Required:
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `NEXT_PUBLIC_BACKEND_URL` (Railway backend URL)
- `NEXT_PUBLIC_URL` (Vercel URL)
- `NEXT_PUBLIC_ENV_MODE=production`
- `KORTIX_ADMIN_API_KEY`

---

## 5. Quick Reference

### Railway Backend URL Format
`https://<service-name>.up.railway.app`

### Vercel Frontend URL Format
`https://<project-name>.vercel.app` (production)
`https://<project-name>-<hash>.vercel.app` (preview)

### Testing

1. Deploy backend to Railway
2. Get Railway backend URL
3. Deploy frontend to Vercel (with backend URL)
4. Update Railway `FRONTEND_URL_ENV` with Vercel URL
5. Update Supabase auth redirect URLs
6. Test!

---

## Notes

- **Redis**: Railway's Redis addon provides connection details automatically via `${{Redis.*}}` variables
- **Worker**: Railway might require separate project or service configuration
- **CORS**: Backend automatically allows Vercel domains in production mode
- **Database**: All migrations should already be applied (you did this earlier)

