# Pip Pad 🎬

A locked-down, **kid-safe video player** for young children — built as a single Cloudflare Worker. No app store, no accounts, no ads, no rabbit holes. Just the videos a parent allows, on a tablet a kid can't escape.

Built for one 6-year-old named Pip. Shared in case it helps yours.

---

## What Is This

Pip Pad turns any cheap tablet (a Raspberry Pi with a touchscreen, an old Android, a kiosk display) into a **single-purpose, parent-controlled video appliance**:

- **Big-button favorite categories** — one tap loads a curated, safe search (Blippi, Mickey Mouse, Disney classics, marble runs, dominoes…).
- **Full-screen "Pick a video" grid** — large thumbnails a small child can scroll and tap without frustration.
- **Kid-safe search filtering** — adult/spam blocklists, duration floors/ceilings, safe-channel scoring.
- **De-stim visual presets** — Calm / Focus / Sleep filters (grayscale, warm tone, reduced brightness) for sensory-sensitive kids.
- **Escape lockdown** — a multi-layer guard so a child can't leave the player or reach the open web.
- **Parent PIN** — settings, app switching, and exit are all gated behind a 4-digit PIN stored server-side.
- **Local-file & LAN streaming** — play MP4s from the device or a home NAS over SMB/HTTP.

---

## Features

| Feature | What it does |
|---|---|
| Favorite categories | One-tap curated searches, fully customizable in code |
| Fullscreen result grid | Touch-friendly big thumbnails, tap-to-play |
| Kid-safe filter | Blocklist + duration gate + safe-channel scoring on every search |
| Visual presets | Calm / Focus / Sleep / Normal — CSS filters over the video |
| Parent PIN | Server-side PIN gates settings, exit, and app switching |
| PipOS launcher | A faux "home screen" with the player as the only unlocked app |
| Local + NAS playback | MP4 from device storage or an SMB share served over HTTP |

---

## Architecture

```
        +-----------------------------+
        |   Tablet / Pi kiosk         |
        |   (Chromium full-screen)    |
        +--------------+--------------+
                       | HTTPS
                       v
        +-----------------------------+
        |   Cloudflare Worker         |
        |   - PipOS launcher  (/)     |
        |   - Player          (/player)|
        |   - Admin / PIN     (/admin)|
        |   - YouTube search  (/api)  |
        |   - Settings (KV-backed)    |
        +--------------+--------------+
                       |
                       v
        +-----------------------------+
        |   KV namespace (PIP_KV)     |
        |   - parent PIN              |
        |   - default visual preset   |
        +-----------------------------+
```

Everything is **one worker file** — no build step, no framework. Search hits YouTube's public InnerTube endpoint (no API key) and is filtered for kid-safety before results are returned.

---

## Self-Hosting

1. **Create a KV namespace** and bind it as `PIP_KV`.
2. **Deploy the worker** (`pip_pad_worker.js`) to Cloudflare, with the `PIP_KV` binding.
3. **Open `/admin`** and set a parent PIN (stored in KV).
4. **Point a kiosk browser** at `/` in full-screen / guided-access mode.

### Optional: play from a home NAS

The admin panel generates copy-paste instructions to mount an SMB share and serve it over HTTP on the LAN, so your child can watch downloaded files with zero internet.

### Customizing the favorite categories

The category buttons live in the player markup as `searchKeyword('<safe query>', '<label>')` calls. Edit them to match your kid's interests.

---

## License

MIT — see [LICENSE](LICENSE). Use it, fork it, lock down a tablet for a kid you love.

---

*Part of the Indica Independent stack. Built because the kids' YouTube experience is a dark pattern, and a parent should be able to draw a hard line.*
