# Debug 404 Errors - Backend URL Issue

## Problem
URLs are being constructed as: `https://suna-new.vercel.app/suna-new-backend-production.up.railway.app/v1/...`

This means `NEXT_PUBLIC_BACKEND_URL` is missing `https://` prefix and is being treated as a relative path.

## Solution: Verify Vercel Environment Variable

### Step 1: Check Vercel Environment Variable

1. Go to **Vercel Dashboard** → Your Project (`suna-new`)
2. Go to **Settings** → **Environment Variables**
3. Find `NEXT_PUBLIC_BACKEND_URL`
4. **Check the value** - it MUST be exactly:
   ```
   https://suna-new-backend-production.up.railway.app/v1
   ```

### Step 2: Common Mistakes

❌ **WRONG** (missing https://):
```
suna-new-backend-production.up.railway.app/v1
```

❌ **WRONG** (extra trailing slash):
```
https://suna-new-backend-production.up.railway.app/v1/
```

✅ **CORRECT**:
```
https://suna-new-backend-production.up.railway.app/v1
```

### Step 3: After Fixing

1. **Save** the environment variable
2. **Redeploy** the Vercel app:
   - Go to **Deployments** tab
   - Click **three dots** on latest deployment
   - Click **Redeploy**

⚠️ **IMPORTANT**: Environment variables are baked into the build at build time. You MUST redeploy after changing them!

### Step 4: Verify

After redeploy, check browser console. URLs should be:
```
https://suna-new-backend-production.up.railway.app/v1/health
https://suna-new-backend-production.up.railway.app/v1/billing/account-state
```

NOT:
```
https://suna-new.vercel.app/suna-new-backend-production.up.railway.app/v1/...
```

---

## Alternative: Check if Variable is Actually Set

If the variable looks correct but still not working:

1. In Vercel Dashboard → Environment Variables
2. Make sure it's set for **Production** environment (not just Preview/Development)
3. Check for typos or hidden characters
4. Delete and recreate the variable if needed

---

## Why This Happens

Next.js `NEXT_PUBLIC_*` variables are embedded at **build time**, not runtime. If the variable is wrong when the app is built, it stays wrong until you rebuild.
