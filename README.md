# Ae Haalo Gujarat: reels worker

Makes the posters and 9:16 news reels for the stories on **https://aehaalogujarat.com** and uploads them to the
website. The website then posts them to Instagram, Facebook, YouTube Shorts and X, with the story link as the
first comment.

The look comes from the @aehaalogujarat Instagram posters: the brand poster with 29 fact layouts in turn, the
intro animation, Gujarati fonts (Baloo Bhai 2, Hind Vadodara), a colour look per section (અમદાવાદ, સુરત,
રાજકોટ, ક્રાઇમ, હવામાન…) and music picked by the story's mood.

This repository is public so GitHub Actions minutes are free. The website's code stays private, and the music
tracks are downloaded from the website when the worker runs.

## Setup (once)

1. **Repository secrets** (Settings → Secrets and variables → Actions → New repository secret):
   - `SITE_URL` = `https://aehaalogujarat.com`
   - `REELS_SECRET` = the same value as `REELS_SECRET` in the website's Hostinger environment
2. **Website environment** (hPanel → aehaalogujarat.com → Node.js → Environment variables):
   - `GITHUB_REPO` = `ppratik2026/aehaalogujarat-reels-worker`
   - `GITHUB_DISPATCH_TOKEN` = a fine-grained GitHub token with **Actions: Read and write** on this repository
3. Actions tab → **Reels worker** → **Run workflow** once to check it.

After that it runs every 30 minutes by itself, and the website starts it as soon as new stories wait.

## Flow

```
website: story published → Gemini reel script → queue
worker:  GET /api/reels/queue → poster (photo or drawn scene) → intro frames (Playwright) → MP4 + music (FFmpeg)
         → POST /api/reels/upload
website: Instagram Reel · Facebook Reel · YouTube Short · X video → first comment with the story link
```

## Run it on a computer

Needs Python 3.10+, FFmpeg and Chromium (Playwright).

```bash
pip install -r requirements.txt
python -m playwright install --with-deps chromium     # or set CHROMIUM_PATH to an installed Chrome
python worker.py --sample out/                        # demo reel from a made-up story, no website needed
SITE_URL=https://aehaalogujarat.com REELS_SECRET=... python worker.py   # one round
```

The code is kept in sync with `reels/` in the website repository (AeHaaloGujaratWeb).
