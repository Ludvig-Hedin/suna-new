# Google Login Setup

## Quick Answer: Configure in Supabase Dashboard (not just env vars)

For **Google Login** (the "Sign in with Google" button), you need to configure it in the **Supabase Dashboard**, not just environment variables.

---

## Step-by-Step Setup

### 1. Get Google OAuth Credentials

1. Go to: https://console.cloud.google.com/apis/credentials
2. Select your project (or create a new one)
3. Click **"Create Credentials"** → **"OAuth client ID"**
4. Choose **"Web application"**
5. Add **Authorized redirect URIs**:
   ```
   https://iubthomvexuonmwmvzva.supabase.co/auth/v1/callback
   ```
6. Click **"Create"**
7. Copy the **Client ID** and **Client Secret**

### 2. Configure in Supabase Dashboard

1. Go to: https://supabase.com/dashboard/project/iubthomvexuonmwmvzva/auth/providers
2. Find **"Google"** in the provider list
3. Click to enable it
4. Paste your:
   - **Client ID (for OAuth)**
   - **Client Secret (for OAuth)**
5. **Save**

That's it! The Google login button will now work.

---

## Note: Two Different Google OAuth Uses

| Feature | Configuration Location |
|---------|----------------------|
| **Google Login** (user auth) | Supabase Dashboard → Auth → Providers |
| **Google Docs/Slides API** | `backend/.env` file (`GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`) |

For just Google login, you only need step 2 above (Supabase Dashboard).

The Google API integration is separate and only needed if you want to use Google Docs/Slides features in the app.

