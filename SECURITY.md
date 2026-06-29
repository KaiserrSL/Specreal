# Spectral Website — Security Notes

## Threat model (be honest about it)

This is a **100% client-side static site** (HTML/CSS/JS). All data lives in the
browser's `localStorage`. There is **no server**. That means:

> **Anyone with the page open can press F12, edit `localStorage`, and make
> themselves DEV/ADMIN, read stored data, or delete anything.**

No amount of JavaScript can prevent this, because the JavaScript itself runs on
the attacker's machine. **Real authentication and authorization require a
backend** (a server that holds the source of truth and the user can't edit).

So the client-side hardening below protects against *other people's* malicious
input and casual snooping — **not** against the device owner.

## What IS protected (implemented)

1. **Stored XSS — fixed.** Every piece of user-controlled text (forum posts &
   titles, display names, bios, profile usernames, DEV news entries) is escaped
   with `esc()` before being inserted via `innerHTML`, in both text and
   attribute contexts. Previously a forum post like
   `<img src=x onerror=alert(document.cookie)>` would execute for every viewer.
2. **Input sanitization.** `sanitizeField()` strips control characters and
   clamps length on stored fields. Usernames are restricted to
   `[A-Za-z0-9 _.-]`. Avatars are restricted to `data:image/…` or `http(s)://`.
3. **No plaintext passwords.** Passwords are hashed with SHA-256 (+ a fixed
   pepper) via the Web Crypto API (`hashPassword()`); legacy plaintext records
   are upgraded to a hash on next login. A weaker non-crypto fallback is used
   only if Web Crypto is unavailable.
4. **Role whitelisting.** `roleBadgeHtml()` only renders known roles, so a
   tampered role value can't inject markup.

## What is NOT (and cannot be) protected here

- Becoming admin/DEV by editing `localStorage` — client-side only.
- Reading password **hashes** from `localStorage` (they can't be reversed, but
  a local attacker doesn't need them — they can set the session directly).
- Content moderation bypass by an attacker who edits their own storage.

## To make it actually secure (next step)

Add a backend, e.g. **Node/Express + a database**, or a BaaS like
**Supabase / Firebase**, and move there:

- user accounts + **salted, hashed passwords** (bcrypt/argon2, per-user salt);
- session tokens (httpOnly cookies);
- **server-enforced** roles and forum moderation;
- server-side validation of every write.

The current client would then call the API instead of `localStorage`.
