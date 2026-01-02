# Fix Vercel + Railway Setup Issues

## Issues Found

1. ✅ **Backend URL missing `https://`** in Vercel environment variable
2. ✅ **CORS not allowing Vercel domain** - Fixed in code (needs deploy)

---

## Fix 1: Update Vercel Environment Variable (REQUIRED)

In **Vercel Dashboard** → Your Project → **Settings** → **Environment Variables**:

**Current (WRONG):**
```
NEXT_PUBLIC_BACKEND_URL=suna-new-backend-production.up.railway.app/v1
```

**Should be:**
```
NEXT_PUBLIC_BACKEND_URL=https://suna-new-backend-production.up.railway.app/v1
```

⚠️ **CRITICAL**: Must include `https://` prefix and `/v1` suffix!

After updating:
1. Save the environment variable
2. Go to **Deployments** tab
3. Click the **three dots** on the latest deployment
4. Click **Redeploy** (to pick up the new env var)

---

## Fix 2: Add CORS Configuration in Railway (REQUIRED)

In **Railway Backend service** → **Variables** tab:

Add this environment variable:
```
FRONTEND_URL_ENV=https://suna-new.vercel.app
```

This tells the backend to allow CORS requests from your Vercel domain.

After adding:
- Railway will automatically redeploy the backend
- Wait for deployment to complete

---

## Fix 3: Code Change (Already Done)

✅ I've already updated `backend/api.py` to use `FRONTEND_URL_ENV` for CORS.

You need to:
1. Commit and push the code change (backend/api.py)
2. Railway will auto-deploy

---

## Complete Checklist

### Vercel:
- [ ] Update `NEXT_PUBLIC_BACKEND_URL` to `https://suna-new-backend-production.up.railway.app/v1`
- [ ] Redeploy Vercel app

### Railway Backend:
- [ ] Add `FRONTEND_URL_ENV=https://suna-new.vercel.app` to environment variables
- [ ] Commit and push the backend/api.py CORS fix (if not already pushed)
- [ ] Wait for Railway to redeploy

### Verify:
- [ ] Check browser console - URLs should be correct (with `https://`)
- [ ] No more 405 errors on `/setup/initialize`
- [ ] No more CORS errors
- [ ] Can sign in successfully

---

## Expected Result

After fixes:
- Frontend will call: `https://suna-new-backend-production.up.railway.app/v1/setup/initialize`
- Backend will allow CORS from: `https://suna-new.vercel.app`
- Everything should work! 🎉
