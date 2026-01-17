# Environment Variables Setup Guide

## 📚 Table of Contents
1. [Understanding Environment Variables](#understanding-environment-variables)
2. [How React Uses Environment Variables](#how-react-uses-environment-variables)
3. [Setting Up for Local Development](#setting-up-for-local-development)
4. [Updating Your Code](#updating-your-code)
5. [Configuring Vercel for Production](#configuring-vercel-for-production)
6. [Common Mistakes to Avoid](#common-mistakes-to-avoid)
7. [Testing Your Setup](#testing-your-setup)

---

## Understanding Environment Variables

### What are Environment Variables?
Environment variables are configuration values that change based on where your application runs (local development, staging, production). They allow you to:
- **Keep secrets safe** (API keys, database URLs)
- **Switch between environments** without code changes
- **Configure different backends** (localhost vs production URL)

### Why Use Them?
**Without environment variables:**
```javascript
// ❌ BAD: Hardcoded localhost will break in production
const API_BASE_URL = 'http://localhost:4000';
```

**With environment variables:**
```javascript
// ✅ GOOD: Works in development AND production
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:4000';
```

---

## How React Uses Environment Variables

### React Environment Variable Rules

In Create React App (which you're using), environment variables follow these rules:

1. **Must start with `REACT_APP_`** - Only variables prefixed with `REACT_APP_` are exposed to your React code
2. **Defined in `.env` files** - Variables are loaded from `.env` files in your project root
3. **Embedded at build time** - Values are injected into your code when you run `npm run build`
4. **Not available in `node_modules`** - Only your source code can access them

### Example Environment Variable Names:
- ✅ `REACT_APP_API_URL` - Works!
- ✅ `REACT_APP_SECRET_KEY` - Works!
- ❌ `API_URL` - Won't work (missing REACT_APP_ prefix)
- ❌ `DATABASE_URL` - Won't work (missing REACT_APP_ prefix)

---

## Setting Up for Local Development

### Step 1: Create `.env` File

In your `frontend` directory, create a file named `.env`:

```env
REACT_APP_API_URL=http://localhost:4000
```

### Step 2: Create `.env.example` File (Best Practice)

This file serves as a template for other developers. It should be committed to git:

```env
REACT_APP_API_URL=http://localhost:4000
```

### Step 3: Update `.gitignore`

Make sure `.env` is in your `.gitignore` to prevent committing secrets:

```
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
```

**Note:** `.env.example` should NOT be in `.gitignore` - it's safe to commit.

### Step 4: Restart Your Development Server

⚠️ **Important:** After creating or modifying `.env` files, you MUST restart your React development server:

```bash
# Stop the server (Ctrl+C), then:
npm start
```

Environment variables are only loaded when the server starts!

---

## Updating Your Code

### Method 1: Using with Axios (Recommended)

Update your `App.js` to use the environment variable:

```javascript
// Replace the hardcoded URL
// ❌ OLD: const API_BASE_URL = 'http://localhost:4000';

// ✅ NEW: Use environment variable with fallback
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:4000';
```

**Benefits:**
- Falls back to `localhost:4000` if the variable isn't set (good for development)
- Uses production URL when deployed (set in Vercel)

### Method 2: Create an API Configuration File (Advanced)

Create `src/config.js`:

```javascript
// src/config.js
const config = {
  apiBaseUrl: process.env.REACT_APP_API_URL || 'http://localhost:4000',
  apiTimeout: 10000, // 10 seconds
};

export default config;
```

Then use it in `App.js`:

```javascript
import config from './config';
const API_BASE_URL = config.apiBaseUrl;
```

### Using with Fetch API

If you use `fetch` instead of axios:

```javascript
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:4000';

// Example fetch call
fetch(`${API_BASE_URL}/api/waste/recent`)
  .then(response => response.json())
  .then(data => console.log(data));
```

---

## Configuring Vercel for Production

### Step 1: Get Your Backend URL

Your backend is deployed on Render. Your backend URL is:
```
https://binlytics-platform-1.onrender.com
```

✅ **Verified:** Your backend is running at [https://binlytics-platform-1.onrender.com](https://binlytics-platform-1.onrender.com) (returns: `{"message":"Binlytics API is running 🚀"}`)

### Step 2: Add Environment Variable in Vercel

1. **Go to Vercel Dashboard**
   - Visit [vercel.com](https://vercel.com)
   - Sign in and select your project

2. **Navigate to Project Settings**
   - Click on your project
   - Go to **Settings** → **Environment Variables**

3. **Add the Variable**
   - **Name:** `REACT_APP_API_URL`
   - **Value:** `https://binlytics-platform-1.onrender.com` ⬅️ **Use your actual Render URL**
   - **Environment:** Select `Production`, `Preview`, and `Development` (or just Production if you prefer)

4. **Save and Redeploy**
   - Click **Save**
   - Go to **Deployments** tab
   - Click **⋯** (three dots) on the latest deployment
   - Click **Redeploy**

⚠️ **Important:** After adding/changing environment variables in Vercel, you must redeploy for changes to take effect!

### Step 3: Verify Your Environment Variable

After redeploying, you can verify it's working:

1. Open your deployed site
2. Open browser DevTools (F12)
3. Go to **Console** tab
4. Type: `console.log(process.env.REACT_APP_API_URL)`
5. You should see your Render backend URL

---

## Common Mistakes to Avoid

### ❌ Mistake 1: Using `localhost` in Production

**Problem:**
```javascript
const API_BASE_URL = 'http://localhost:4000'; // Will fail in production!
```

**Why it fails:**
- In production, your frontend runs on Vercel's servers
- There's no server running on `localhost:4000` on Vercel
- Users' browsers will try to connect to THEIR `localhost`, which won't work

**Solution:**
```javascript
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:4000';
```

---

### ❌ Mistake 2: Not Including `REACT_APP_` Prefix

**Problem:**
```env
# .env file
API_URL=http://localhost:4000  # ❌ Missing REACT_APP_ prefix
```

**Why it fails:**
- React only exposes variables starting with `REACT_APP_`
- `process.env.API_URL` will be `undefined`

**Solution:**
```env
# .env file
REACT_APP_API_URL=http://localhost:4000  # ✅ Correct
```

---

### ❌ Mistake 3: Not Restarting Development Server

**Problem:**
- You create/modify `.env` file
- You keep the dev server running
- Variables don't update

**Why it fails:**
- Environment variables are loaded only when React starts
- Changes require a restart

**Solution:**
1. Stop the server (Ctrl+C)
2. Run `npm start` again

---

### ❌ Mistake 4: Committing `.env` to Git

**Problem:**
```bash
git add .env
git commit -m "Add env file"  # ❌ Don't do this!
```

**Why it's bad:**
- `.env` may contain secrets
- Other developers will overwrite your `.env` with theirs
- Secrets could be exposed in your repository

**Solution:**
- Always add `.env` to `.gitignore`
- Commit `.env.example` instead (with example values, no secrets)

---

### ❌ Mistake 5: Forgetting to Redeploy After Adding Variables

**Problem:**
- You add environment variable in Vercel
- You wait, but it doesn't work

**Why it fails:**
- Environment variables are injected at **build time**
- Existing deployments don't have the new variable

**Solution:**
- Always redeploy after adding/changing environment variables in Vercel

---

### ❌ Mistake 6: Not Handling Missing Variables

**Problem:**
```javascript
const API_BASE_URL = process.env.REACT_APP_API_URL; // Could be undefined!
```

**Why it's risky:**
- If the variable isn't set, `API_BASE_URL` will be `undefined`
- API calls will fail with malformed URLs like `undefined/api/waste`

**Solution:**
```javascript
// ✅ Always provide a fallback
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:4000';
```

---

### ✅ Your Specific Configuration

**Your Render Backend URL:**
```
https://binlytics-platform-1.onrender.com
```

**For Vercel Production:**
- Variable Name: `REACT_APP_API_URL`
- Variable Value: `https://binlytics-platform-1.onrender.com`

**For Local Development (.env file):**
```env
REACT_APP_API_URL=http://localhost:4000
```

---

## Testing Your Setup

### Test 1: Local Development

1. Create `.env` with `REACT_APP_API_URL=http://localhost:4000`
2. Restart dev server: `npm start`
3. Open browser DevTools → Console
4. Type: `console.log(process.env.REACT_APP_API_URL)`
5. Should print: `http://localhost:4000`

### Test 2: Production Build (Local)

1. Set variable: `REACT_APP_API_URL=https://your-backend.onrender.com`
2. Build: `npm run build`
3. Serve build: `npx serve -s build`
4. Check if API calls work

### Test 3: Production (Vercel)

1. Add `REACT_APP_API_URL` in Vercel dashboard
2. Redeploy your site
3. Visit your deployed site
4. Open DevTools → Network tab
5. Perform an action that triggers an API call
6. Check the request URL - should point to your Render backend, not localhost

---

## Quick Reference Checklist

### For Local Development:
- [ ] Create `.env` file in `frontend/` directory
- [ ] Add `REACT_APP_API_URL=http://localhost:4000`
- [ ] Create `.env.example` with the same variable
- [ ] Add `.env` to `.gitignore`
- [ ] Restart development server (`npm start`)

### For Production (Vercel):
- [ ] Deploy backend on Render (get your backend URL)
- [ ] Go to Vercel dashboard → Project Settings → Environment Variables
- [ ] Add `REACT_APP_API_URL` with your Render backend URL
- [ ] Select environment(s): Production, Preview, Development
- [ ] Save and redeploy your frontend

### Code Updates:
- [ ] Replace hardcoded `localhost` URLs with `process.env.REACT_APP_API_URL`
- [ ] Add fallback: `process.env.REACT_APP_API_URL || 'http://localhost:4000'`
- [ ] Test locally with `.env` file
- [ ] Test in production after redeploy

---

## Summary

**Key Takeaways:**
1. ✅ Use `REACT_APP_` prefix for all React environment variables
2. ✅ Store local values in `.env` (not committed to git)
3. ✅ Store production values in Vercel dashboard
4. ✅ Always restart dev server after changing `.env`
5. ✅ Always redeploy after changing Vercel environment variables
6. ✅ Provide fallback values: `process.env.REACT_APP_API_URL || 'http://localhost:4000'`
7. ✅ Never commit `.env` to git, but do commit `.env.example`

**Your workflow:**
- **Development:** `.env` file → `http://localhost:4000`
- **Production:** Vercel environment variable → `https://your-backend.onrender.com`

---

## Need Help?

If you encounter issues:

1. **Check variable name:** Must start with `REACT_APP_`
2. **Restart dev server:** After changing `.env`
3. **Redeploy:** After changing Vercel variables
4. **Check browser console:** For API errors
5. **Check Network tab:** To see what URL is being called
6. **Verify CORS:** Make sure your backend allows requests from your frontend domain

---

**Good luck with your deployment! 🚀**
