# 🚀 Vercel Production Setup - Quick Guide

## Your Backend URL

Your Render backend is running at:
**`https://binlytics-platform-1.onrender.com`**

✅ Verified: [Check your backend](https://binlytics-platform-1.onrender.com)

---

## Step-by-Step: Configure Vercel

### Step 1: Open Vercel Dashboard

1. Go to [vercel.com](https://vercel.com) and sign in
2. Select your **Binlytics** project (or create a new project if not deployed yet)

### Step 2: Add Environment Variable

1. Click on your project
2. Go to **Settings** (top navigation)
3. Click **Environment Variables** (left sidebar)

### Step 3: Create the Variable

Click **Add New** and fill in:

| Field | Value |
|-------|-------|
| **Key** | `REACT_APP_API_URL` |
| **Value** | `https://binlytics-platform-1.onrender.com` |
| **Environment** | Select: ☑️ Production ☑️ Preview (optional) ☑️ Development (optional) |

**Important:** 
- ✅ **Key must be exactly:** `REACT_APP_API_URL` (case-sensitive)
- ✅ **Value must be exactly:** `https://binlytics-platform-1.onrender.com` (no trailing slash)
- ✅ Select at least **Production** environment

### Step 4: Save

Click **Save** button

### Step 5: Redeploy (Critical!)

⚠️ **You MUST redeploy after adding/changing environment variables**

1. Go to **Deployments** tab
2. Find your latest deployment
3. Click **⋯** (three dots menu) on the right
4. Click **Redeploy**
5. Wait for deployment to complete (usually 1-2 minutes)

---

## Verify It's Working

After redeploying:

1. **Visit your deployed site** on Vercel
2. **Open Browser DevTools** (Press F12)
3. **Go to Console tab**
4. **Type this command:**
   ```javascript
   console.log(process.env.REACT_APP_API_URL)
   ```
5. **You should see:**
   ```
   https://binlytics-platform-1.onrender.com
   ```

### Test API Connection

1. **Open DevTools** → **Network tab**
2. **Perform an action** that calls the API (e.g., submit a reading, view recent readings)
3. **Check the network request:**
   - Should go to: `https://binlytics-platform-1.onrender.com/api/...`
   - ❌ **Should NOT** go to: `http://localhost:4000/api/...`

---

## Troubleshooting

### Problem: Still seeing `localhost` in network requests

**Solutions:**
- ✅ Make sure you redeployed after adding the variable
- ✅ Check the variable name is exactly `REACT_APP_API_URL`
- ✅ Check the variable value is exactly `https://binlytics-platform-1.onrender.com`
- ✅ Verify the environment is set to "Production"

### Problem: `process.env.REACT_APP_API_URL` is undefined

**Solutions:**
- ✅ Variable name must start with `REACT_APP_`
- ✅ Make sure you redeployed
- ✅ Check if variable exists in Vercel dashboard

### Problem: CORS errors in browser console

**This means your backend needs to allow requests from your Vercel domain**

**Solution:** Update your `backend/server.js` CORS configuration:

```javascript
// In backend/server.js, update CORS:
const cors = require('cors');

app.use(cors({
  origin: [
    'http://localhost:3000', // Local development
    'https://your-vercel-domain.vercel.app' // Your Vercel domain
  ],
  credentials: true
}));
```

Or allow all origins (for development):
```javascript
app.use(cors()); // Allows all origins
```

---

## Quick Reference

| Environment | Variable Value |
|-------------|----------------|
| **Local Development** | `http://localhost:4000` (from `.env` file) |
| **Production (Vercel)** | `https://binlytics-platform-1.onrender.com` (from Vercel dashboard) |

**Your Code (Already Updated):**
```javascript
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:4000';
```

---

## Next Steps

After configuring Vercel:

1. ✅ Add environment variable in Vercel
2. ✅ Redeploy your site
3. ✅ Test the deployed site
4. ✅ Verify API calls go to Render backend (not localhost)

**You're all set! 🎉**

---

For more detailed information, see [ENVIRONMENT_SETUP.md](./ENVIRONMENT_SETUP.md)
