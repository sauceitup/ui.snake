# 🐍 Spotify Snake — a bending ribbon of your liked tracks

An interactive ribbon of Spotify tracks: a list of cards that gently snakes, fans out under a pinch gesture, and scrolls with one finger. Plain JS + Canvas, no frameworks, no backend — just a static HTML file on GitHub Pages.

**[→ View demo](#)** *(add your link here after publishing)*

![preview](#) *(you can add a screenshot or GIF here)*

## What's inside

- `index.html` — the public page with the animation. Open to everyone, no login required.
- `export.html` — a utility page **for you only**: log in via Spotify once, download your `tracks.json`.
- `tracks.example.json` — a sample of the data format (in case you want to plug in tracks manually, without Spotify).

## How it works

1. `export.html` uses the Spotify Web API (Authorization Code + PKCE flow — no client secret needed, safe for a static site) to fetch all your liked tracks.
2. You download the resulting `tracks.json` and commit it to the repo.
3. `index.html` simply loads that file with a plain `fetch()` — no login for visitors, your tracks are visible to anyone with the link.
4. Tapping a card opens the track in Spotify (the app, if installed, otherwise the browser).

No tokens or secrets ever live in the public files — `tracks.json` only contains track titles, artists, and public cover/track URLs (the same data that's already public through Spotify itself).

## Quick start (for yourself)

### 1. Create a Spotify app

1. Go to [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard) and log in.
2. **Create app** → enter any name and description.
3. **Redirect URIs** — add **two** entries (use your future GitHub Pages address):
   - `https://YOUR-USERNAME.github.io/YOUR-REPO-NAME/`
   - `https://YOUR-USERNAME.github.io/YOUR-REPO-NAME/export.html`
4. Check **Web API** → Save.
5. **Settings** → copy your **Client ID** (this is not a secret, it's safe to use openly in client-side code).

### 2. Publish on GitHub Pages

1. Fork this repo (or create a new one and drop these files in).
2. Repo **Settings** → **Pages** → Source: `Deploy from a branch`, Branch: `main`, `/ (root)` → Save.
3. Wait a minute — you'll get a link like `https://username.github.io/repo-name/`.

### 3. Plug in your Client ID

Open `export.html`, find this line:

```js
const SPOTIFY_CLIENT_ID = 'PASTE_YOUR_CLIENT_ID_HERE';
```

Replace it with your Client ID from step 1. Commit.

### 4. Add yourself as an allowed user (Development Mode)

While your app is in Spotify Dashboard's Development Mode, only manually approved accounts can authorize through `export.html`:

1. In the Dashboard → your app → **Settings** → **User Management**.
2. **Add new user** → enter the email/name tied to your Spotify account.

### 5. Export your tracks

1. Open `https://username.github.io/repo-name/export.html`.
2. **Log in and export** → authorize → wait for it to load.
3. A `tracks.json` file will download.
4. Commit that file to the repo (next to `index.html`, keep the filename as is).

### 6. Done

Open `https://username.github.io/repo-name/` — your tracks are live, visible to anyone with the link.

## Saving it as an app on iPhone

1. Open the site link in **Safari** (important — not Chrome).
2. Tap "Share" → **Add to Home Screen**.
3. Done — it opens full-screen, with no address bar, like a real app.

## Customizing it

All the main parameters live near the top of the `<script>` section in `index.html`, with inline comments:

| Parameter | Where | What it does |
|---|---|---|
| `TOP_W` / `TOP_H` | card geometry | size of the largest (top) card |
| `BOTTOM_W` / `BOTTOM_H` | card geometry | size of the smallest (near the horizon) card |
| `STEP_BASE` / `STEP_GROWTH` | list grid | spacing between cards, and how it grows going down |
| `ORGANIC_AMPL_FRAC` | path/bend | the "snake" amplitude at rest (no pinch) |
| `BEND_EASE` | pinch | how smoothly the bend ramps up as fingers spread |
| `POS_EASE` | pinch | how smoothly cards glide to their new positions |
| `MAX_FLING_SPEED` | scroll | maximum inertia speed after a swipe |
| `TAP_MAX_TRAVEL` / `TAP_MAX_DURATION` | taps | thresholds distinguishing a tap from a swipe |

Card colors are in the `palette` array near the top of the script — swap in your own palette if you like.

## Technical details

- **Auth**: Authorization Code with PKCE — the standard OAuth 2.0 flow for public clients without a secret. [Spotify docs](https://developer.spotify.com/documentation/web-api/tutorials/code-pkce-flow).
- **Token storage**: `sessionStorage`, only on the `export.html` page, only during your export session. The public `index.html` holds no tokens at all.
- **Data source**: the `GET /v1/me/tracks` endpoint (Liked Songs), requires the `user-library-read` scope.
- **Rendering**: a single `<canvas>`, no third-party libraries, ~700 lines of JS.

## Known limitations

- The track list is "frozen" — it only updates when you re-run `export.html` and commit a new `tracks.json`. This is intentional: without a backend server, there's no safe way to auto-refresh the list without repeating the login.
- While the Spotify app is in Development Mode, only up to 25 users can authorize through `export.html` (relevant if you plan to let others use the export tool too, not just yourself) — beyond that you'd need Extended Quota Mode (requires a request to Spotify).

## License

MIT — fork it, tweak it, sell it as a template, just keep a credit if you don't mind 🙂
