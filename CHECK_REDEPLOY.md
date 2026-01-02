# Check: Did You Redeploy After Setting Env Var?

## Critical Question

**Did you redeploy your Vercel app AFTER setting the `NEXT_PUBLIC_BACKEND_URL` environment variable?**

## Why This Matters

Next.js embeds `NEXT_PUBLIC_*` environment variables into the JavaScript bundle **at build time**, not runtime. This means:

- ✅ Setting the env var in Vercel dashboard = stored, but not used yet
- ❌ Not redeploying = old build still running with old (wrong) URL
- ✅ Redeploying = new build uses the new env var

## How to Redeploy

1. Go to Vercel Dashboard → Your Project (`suna-new`)
2. Go to **Deployments** tab
3. Click the **three dots** (⋯) on the latest deployment
4. Click **Redeploy**
5. Wait for the build to complete
6. Test again

## Alternative: Trigger New Deploy

You can also trigger a new deploy by:
- Pushing a commit to your repo (if connected to GitHub)
- Or manually clicking "Redeploy" in Vercel

---

## If You Already Redeployed

If you already redeployed and it's still not working:

1. **Check the build logs** in Vercel to see what URL was used
2. **Hard refresh** your browser (Cmd+Shift+R / Ctrl+Shift+R)
3. **Check browser console** - look for what `process.env.NEXT_PUBLIC_BACKEND_URL` actually is
4. Try opening browser DevTools → Console and type: `console.log(process.env.NEXT_PUBLIC_BACKEND_URL)` (though this might not work for Next.js env vars)

---

## Quick Test

After redeploy, the URLs in browser console should be:
- ✅ `https://suna-new-backend-production.up.railway.app/v1/health`
- ❌ NOT `https://suna-new.vercel.app/suna-new-backend-production.up.railway.app/v1/health`
