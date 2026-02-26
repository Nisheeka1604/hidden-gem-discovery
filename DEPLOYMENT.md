# Deployment Guide

## Deploying Flask Backend to Render.com

### Step 1: Prepare Your Repository
Push your code to GitHub (make sure `.env` is in `.gitignore`):
```bash
git init
git add .
git commit -m "Add deployment configuration"
git push origin main
```

### Step 2: Create Render Account
1. Go to [Render.com](https://render.com)
2. Sign up with GitHub
3. Connect your GitHub repository

### Step 3: Create a New Web Service
1. Click "New +" → "Web Service"
2. Select your repository
3. Configure:
   - **Name**: `hidden-gem-discovery-api`
   - **Runtime**: Python
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `python spotify_auth_server.py`
   - **Environment Variables** (set these in Render dashboard):
     ```
     FLASK_ENV=production
     SPOTIFY_CLIENT_ID=your_actual_client_id
     SPOTIFY_CLIENT_SECRET=your_actual_secret
     REDIRECT_URI=https://hidden-gem-discovery-api.onrender.com/callback
     ```

### Step 4: Update Spotify OAuth Redirect URI
1. Go to [Spotify Dashboard](https://developer.spotify.com/dashboard)
2. Edit your app settings
3. Add Redirect URI: `https://hidden-gem-discovery-api.onrender.com/callback`

### Step 5: Update Frontend
After deployment, update your Vercel environment variables:

**Option A: Use JavaScript to Set Backend URL**
In the Vercel dashboard:
1. Go to your Vercel project settings
2. Add environment variable:
   ```
   NEXT_PUBLIC_API_BASE_URL=https://hidden-gem-discovery-api.onrender.com
   ```

**Option B: Modify HTML (simpler)**
Update [ai_obscure_wildcard.html](ai_obscure_wildcard.html) line ~263:
```javascript
const API_BASE_URL = (() => {
    if (window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1') {
        return 'http://127.0.0.1:5000';  // Local development
    }
    return 'https://hidden-gem-discovery-api.onrender.com';  // Production
})();
```

### Step 6: Test
1. Visit your Vercel deployment
2. Click "Login with Spotify"
3. Get recommendations!

---

## Important Notes

⚠️ **Ollama Limitation**: 
- The AI explanations feature requires Ollama to be running
- Render backends can't easily run Ollama (it's large and memory-intensive)
- **Workaround**: App will still work without it (graceful fallback)
- Alternative: Use OpenAI API instead of Ollama for deployed version

⚠️ **CORS**: Ensure Flask backend has CORS enabled for your Vercel domain
- This is already configured in `spotify_auth_server.py`
- Just add your Vercel URL to `CORS_ORIGINS` env variable if needed

⚠️ **Free Tier**: Render's free tier spins down after 15 minutes of inactivity
- Upgrade to paid for 24/7 uptime
- Cold starts will add ~30 seconds first request

---

## Local Testing (Before Deploying)
To test with both servers before deployment:

**Terminal 1 - Backend:**
```bash
.\.venv\Scripts\python.exe spotify_auth_server.py
```

**Terminal 2 - Frontend:**
```bash
python -m http.server 8000
```

Visit: `http://127.0.0.1:8000/ai_obscure_wildcard.html`
