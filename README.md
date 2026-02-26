# Hidden Gem Discovery - Spotify Music Recommendation App

Discover obscure, lesser-known songs based on your Spotify listening profile with AI-powered explanations.

## Quick Start

### 1. Install Dependencies
```bash
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install flask flask-cors flask-limiter python-dotenv requests
```

### 2. Set Up Environment Variables
The `.env` file contains your Spotify credentials. Keep it secure and never commit to git.

### 3. Ensure Ollama is Running (for AI descriptions)
```bash
ollama pull mistral
ollama serve
```
Ollama runs on `http://127.0.0.1:11434`

### 4. Start Both Servers

In PowerShell Terminal 1:
```bash
.\.venv\Scripts\python.exe spotify_auth_server.py
```
Server runs on `http://127.0.0.1:5000`

In PowerShell Terminal 2:
```bash
python -m http.server 8000
```
Frontend server runs on `http://127.0.0.1:8000`

### 5. Open the App
```
http://127.0.0.1:8000/ai_obscure_wildcard.html
```

Click **"Login with Spotify"** → authorize → get recommendations!

## How It Works

1. **Login**: OAuth 2.0 with your Spotify account (secure, read-only)
2. **Profile Analysis**: Fetches your top 50 artists and their genres
3. **Search**: Queries Spotify for tracks by random artists in your listening profile
4. **AI Explanation**: Uses Ollama + Mistral to generate why you'd like each song
5. **React**: Mark songs as "Intriguing", "Maybe", or "Pass" to refine recommendations

## Features

- ✅ Real Spotify OAuth authentication
- ✅ Obscurity scoring (100 - song popularity)
- ✅ Match likelihood percentage
- ✅ AI-generated explanations (local Ollama, free)
- ✅ Rate limiting (50 requests/hour per IP)
- ✅ Input validation (OWASP security)
- ✅ Response sanitization (no sensitive data leaked)
- ✅ Comprehensive logging

## Architecture

- **Frontend**: `ai_obscure_wildcard.html` - Vanilla JS, no dependencies
- **Backend**: `spotify_auth_server.py` - Flask with security hardening
- **AI Model**: Ollama + Mistral 7B (runs locally, offline)
- **APIs**: Spotify Web API, Ollama local API

## Troubleshooting

**"Error: artist name contains invalid characters"**
- Make sure `spotify_auth_server.py` is the latest version
- Kill all Python processes: `Get-Process python | Stop-Process -Force`
- Restart fresh servers

**No recommendations showing**
- Check if both servers are running (port 5000 and 8000)
- Press F12 in browser to see error messages in console
- Check `spotify_auth.log` for backend errors

**Ollama not working**
- Ensure Ollama is running: `ollama serve`
- Verify with: `curl http://127.0.0.1:11434/api/generate`
- App will still work with template descriptions if Ollama is unavailable

## Security

- Spotify credentials: Never exposed in code, stored in `.env`
- Rate limiting: Per-IP limits to prevent abuse
- Input validation: All user inputs sanitized
- Response sanitization: Sensitive fields removed from API responses
- CORS: Restricted to localhost only
- Logging: Security events logged, tokens never logged

See `SECURITY_HARDENING_REPORT.md` for full details.

## Development

To modify validation rules, edit the `validate_artist_names()` and `validate_genres()` functions in `spotify_auth_server.py`.

Current validation allows:
- Artist names: up to 200 characters, any Unicode (including accents)
- Genres: up to 50 characters, letters/numbers/hyphens
- Rejects only control characters for security

## API Endpoints

### GET `/` (Public)
Health check / status page

### GET `/health` (Public)
Simple health check for monitoring

### GET `/callback` (Spotify OAuth)
Handles Spotify authorization code exchange
Returns: URL redirect with access token in hash

### POST `/api/get-track` (Authenticated)
**Request:**
```json
{
  "token": "spotify_access_token_here",
  "artist_names": "Artist One,Artist Two,Artist Three",
  "genres": ["pop", "rock", "indie"]
}
```

**Response:**
```json
{
  "track": {
    "name": "Song Title",
    "artists": [{"name": "Artist Name"}],
    "popularity": 25,
    "album": {"name": "Album Name"}
  },
  "description": "AI-generated explanation of why you'd like this song"
}
```

## Files

- `ai_obscure_wildcard.html` - Frontend (UI, OAuth flow, recommendations display)
- `spotify_auth_server.py` - Backend (OAuth, Spotify API, AI integration)
- `.env` - Environment variables (credentials, config) - **KEEP PRIVATE**
- `.env.example` - Template for `.env` (safe to commit)
- `SECURITY_HARDENING_REPORT.md` - Security implementation details
- `README.md` - This file

## License

Personal project. Spotify API usage subject to Spotify Terms of Service.
