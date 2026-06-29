<div align="center">
  <img src="Icon.png" alt="Spectral Launcher" width="96" height="96">

  # Spectral Launcher — Website

  Landing page & community hub for **Spectral**, a desktop Minecraft launcher built with Qt 6 and C++17.

  <em>Vanilla HTML / CSS / JS · no build step · zero dependencies to install</em>
</div>

---

## Overview

This repository contains the **marketing & community website** for the Spectral
Launcher (the launcher app itself lives elsewhere). It's a single-page site with
animated visuals, a dark/light theme, an accounts system with cloud sync, and a
community ideas board — all running entirely in the browser.

The whole site is three files: [`index.html`](index.html),
[`style.css`](style.css) and [`script.js`](script.js). No framework, no
bundler, no `npm install`.

## Features

- **Single-page experience** — Home, Accounts, Updates, Development, Features and Support tabs with smooth client-side transitions.
- **Animated background** — interactive particle canvas, ambient light orbs, 3D tilt cards, magnetic buttons and ripple effects.
- **Light & dark themes** — instant toggle, remembered across visits.
- **⚡ Lightweight mode** — automatic device-performance detection. Weak devices (or anyone who opts in) get a stripped-down version with the heavy canvas/blur/animation work disabled, plus an on-page notice and a toggle. See [Lightweight mode](#lightweight-mode-performance).
- **Two-tier accounts** — site accounts (with roles: `DEV`, `ADMIN`, `MODERATOR`, `PRO`, `VETERAN`, `USER`) and per-user Minecraft profiles (Microsoft / Ely.by / offline).
- **Cloud sync** — accounts and profiles are mirrored to [Supabase](https://supabase.com) so they persist across devices, with optional email / Google sign-in.
- **Community ideas board** — submit feature ideas, upvote, with moderation (approve / reject) for staff roles.
- **Dev updates feed** — news/changelog entries posted by `DEV` accounts.
- **Polish** — FAQ accordion, animated counters, terminal & code-editor demos, themed scrollbars, responsive layout and a mobile menu.
- **Accessibility-aware** — respects `prefers-reduced-motion`, uses ARIA labels and semantic landmarks.

## Tech stack

| Area        | Choice                                              |
|-------------|-----------------------------------------------------|
| Markup      | Plain HTML5                                         |
| Styling     | Hand-written CSS (custom properties, glassmorphism) |
| Logic       | Vanilla JavaScript (ES2020+), no framework          |
| Backend     | [Supabase](https://supabase.com) JS SDK (via CDN)   |
| Icons/Fonts | Font Awesome 6, Google Fonts (Outfit)               |

## Project structure

```
.
├── index.html        # All markup (sections, modals, nav, footer)
├── style.css         # All styles (~4.2k lines, incl. lightweight-mode overrides)
├── script.js         # All logic (particles, accounts, cloud sync, forum, lite mode)
├── Icon.png          # Logo / favicon source
├── SECURITY.md       # Threat model & responsible disclosure
└── .claude/
    └── launch.json   # Local preview server config (dev convenience only)
```

## Getting started

Because it's fully static, you can open `index.html` directly — but a local HTTP
server is recommended so `localStorage`, fonts and the Supabase SDK behave
consistently.

```bash
# Python (any 3.x)
python -m http.server 8765

# …or Node
npx serve -l 8765
```

Then open <http://localhost:8765>.

> **Deploying:** drop the files on any static host — GitHub Pages, Netlify,
> Vercel, Cloudflare Pages, etc. No build step required.

## Lightweight mode (performance)

The site detects weak devices **before it renders** and switches to a lighter
version so it stays smooth and doesn't stutter.

- **Detection** — an inline script in `<head>` scores the device on CPU cores
  (`navigator.hardwareConcurrency`), RAM (`navigator.deviceMemory`), network
  (`effectiveType` / `saveData`) and the `prefers-reduced-motion` setting.
- **Runtime watchdog** — if a device passes the static check but the particle
  canvas still runs below ~30 FPS in the first couple of seconds, the site
  downgrades on the fly.
- **What it disables** — the particle canvas, `backdrop-filter`/`blur`,
  ambient lights, continuous keyframe animations, 3D tilt, magnetic buttons,
  ripple and parallax. Glass panels fall back to a solid fill.
- **User control** — a banner explains that the light version is active with a
  **"Полная версия"** (full version) button, plus a floating toggle to switch
  modes anytime. The choice is stored in `localStorage` (`spectral_lite`).

## Accounts & roles

The site ships with seeded demo accounts so the UI has data to show. Roles map
to capabilities (e.g. `DEV` can post updates, `ADMIN`/`MODERATOR` can moderate
the ideas board).

> ⚠️ The seeded accounts use **placeholder demo passwords**. If you deploy this
> publicly, change or remove them and review [SECURITY.md](SECURITY.md) first —
> this is a client-side app and roles are **not** a real authorization boundary.

## Cloud sync (Supabase)

Account and Minecraft-profile data is mirrored to a Supabase project. The
Supabase **anon key** in `script.js` is public by design — it is safe to ship
**only if** Row Level Security (RLS) policies are configured on the tables.
Without RLS, the anon key allows anyone to read/write your tables. See
[SECURITY.md](SECURITY.md).

To point the site at your own Supabase project, edit `SUPABASE_URL` and
`SUPABASE_ANON_KEY` near the top of the data layer in [`script.js`](script.js).

## Security

This is a **client-side** site, so it cannot enforce real authentication on its
own. The honest threat model, what *is* hardened (stored-XSS escaping, input
sanitization, password hashing, role whitelisting) and how to report a
vulnerability are documented in **[SECURITY.md](SECURITY.md)**.

## Contributing

Issues and pull requests are welcome. When contributing:

- Keep it dependency-free — vanilla HTML/CSS/JS, no build tooling.
- Match the existing code style and section comments in `script.js` / `style.css`.
- Escape any user-controlled string with `esc()` before inserting it via `innerHTML`.
- Test both **full** and **lightweight** modes, and both **dark** and **light** themes.

## Disclaimer

Spectral Launcher is **not affiliated with Mojang or Microsoft**. Minecraft is a
trademark of Mojang AB. Built with Qt 6 / C++17.
