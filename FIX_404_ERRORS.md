# Fix 404 Errors - Backend URL Configuration

## The Problem

Your URLs are being constructed incorrectly:
- **Wrong**: `https://suna-new.vercel.app/suna-new-backend-production.up.railway.app/v1/...`
- **Should be**: `https://suna-new-backend-production.up.railway.app/v1/...`

This means `NEXT_PUBLIC_BACKEND_URL` in Vercel is **missing the `https://` prefix**.

## Fix: Update Vercel Environment Variable

### Step 1: Go to Vercel Dashboard

1. Open your Vercel project: `suna-new`
2. Go to **Settings** → **Environment Variables**
3. Find `NEXT_PUBLIC_BACKEND_URL`

### Step 2: Check/Update the Value

The value **MUST** be exactly:
```
https://suna-new-backend-production.up.railway.app/v1
```

**Common mistakes:**
- ❌ Missing `https://` → `suna-new-backend-production.up.railway.app/v1`
- ❌ Extra trailing slash → `https://suna-new-backend-production.up.railway.app/v1/`
- ❌ Wrong domain → Check it's `suna-new-backend-production.up.railway.app`

### Step 3: Verify Environment

Make sure the variable is set for **Production** environment (not just Preview).

### Step 4: Redeploy (CRITICAL)

After fixing the value:

1. **Save** the environment variable
2. Go to **Deployments** tab
3. Click the **three dots** (⋯) on the latest deployment
4. Click **Redeploy**

⚠️ **IMPORTANT**: Next.js embeds `NEXT_PUBLIC_*` variables at build time. You MUST redeploy after changing them!

### Step 5: Verify It Works

After redeploy, check the browser console. URLs should be:
- ✅ `https://suna-new-backend-production.up.railway.app/v1/health`
- ✅ `https://suna-new-backend-production.up.railway.app/v1/billing/account-state`

NOT:
- ❌ `https://suna-new.vercel.app/suna-new-backend-production.up.railway.app/v1/...`

---

## Quick Checklist

- [ ] `NEXT_PUBLIC_BACKEND_URL` = `https://suna-new-backend-production.up.railway.app/v1`
- [ ] Set for **Production** environment
- [ ] No trailing slash
- [ ] Redeployed Vercel app
- [ ] Checked browser console - URLs are correct

---

## Why This Happens

The `next.config.ts` file checks `process.env.NEXT_PUBLIC_BACKEND_URL` first, but if it's not set or is malformed, the browser treats it as a relative URL and appends it to the current domain (`https://suna-new.vercel.app`).
