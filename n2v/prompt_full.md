You are a Senior Frontend Architect. Your objective is to convert this Next.js (App Router) application into a production-ready Vanilla JavaScript project optimized for self-hosted deployment on a VPS (e.g. Hetzner, DigitalOcean, Linode) served by Nginx or Caddy. The output must have zero Node.js runtime dependency in production.

---

### 1. File Structure & Target Architecture
Produce the following flat, browser-native layout:

```
index.html          — Single entry point; all markup lives here
style.css           — All styles (reset, layout, component, mobile)
main.js             — All application logic (modules via IIFE or native ES modules)
public/             — Static assets: images, audio, fonts, icons
config.example.js   — Template for runtime config (committed)
config.js           — Actual runtime config with real values (git-ignored)
nginx.conf.example  — Reference server block for Nginx deployment
.gitignore
README.md
```

If the app has meaningful logical sections, split `main.js` into focused files (e.g. `game.js`, `ui.js`, `api.js`) and load them via `<script type="module">` in `index.html`. Keep the dependency tree shallow — no bundler required.

---

### 2. Framework Removal & Logic Preservation
- **Strip React entirely:** Remove all JSX, component syntax, `useState`, `useRef`, `useEffect`, `useCallback`, `useMemo`, and any React import.
- **Imperative Rewrite:** Replace declarative React rendering with direct DOM manipulation (`document.createElement`, `element.innerHTML`, `element.classList`). Use closures or a minimal module pattern to scope state — do not use global variables unless unavoidable.
- **Zero Business Logic Changes:** Game physics, math formulas, scoring rules, data processing — all must be preserved byte-for-byte in behaviour. Only the framework wrapper changes.
- **Event Lifecycle:** Replace React synthetic events and `useEffect` subscriptions with `addEventListener` / `removeEventListener` pairs. Attach listeners on init, remove them on teardown (e.g. page unload or explicit destroy function).
- **Animation Loops:** Replace React render-cycle-driven updates with `requestAnimationFrame` loops. Ensure `cancelAnimationFrame` is called on cleanup to prevent duplicate loops.

---

### 3. Mobile Responsiveness & Touch Controls
- Preserve all touch event handlers (`touchstart`, `touchend`, `touchmove`) exactly. Map former React synthetic touch props to native equivalents.
- Apply the following in `style.css`:

```css
html, body {
  touch-action: manipulation;
  -webkit-user-select: none;
  user-select: none;
  overflow: hidden;
  overscroll-behavior: none;
}
```

- Canvas or viewport scaling must respond dynamically to `window.innerWidth` / `window.innerHeight` via a `resize` event listener (debounced if expensive).

---

### 4. Asset Handling
- Remove all `next/image` wrappers — replace with standard `<img>` tags. Set explicit `width` and `height` attributes to prevent layout shift.
- Remove `next/font` — load fonts via a `<link rel="preconnect">` + `<link rel="stylesheet">` in `<head>`, or self-host the font files in `public/fonts/`.
- Remove the pumpkin SVG favicon injected via Next.js metadata — add it directly in `<head>`:
  ```html
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🎃</text></svg>">
  ```
- Remove `@vercel/analytics` entirely. If analytics are still needed, replace with a self-hosted option (e.g. Umami, Plausible) and add the tracking snippet to `index.html`.

---

### 5. Configuration & Secret Management
- Identify every `NEXT_PUBLIC_` environment variable. These were baked into the client bundle at build time — they must now be surfaced via a runtime `config.js` file:

**`config.example.js`** (committed):
```js
// Copy this file to config.js and fill in real values. Never commit config.js.
const CONFIG = {
  API_BASE_URL: "https://api.example.com",
  ANALYTICS_ID: "UA-XXXXXXX",
};
```

- Load `config.js` before `main.js` in `index.html`:
  ```html
  <script src="config.js"></script>
  <script type="module" src="main.js"></script>
  ```
- Server-side secrets (formerly in `.env.local`, used only in API route handlers) cannot exist in a purely static site. If the app had server-side logic that consumed secrets, document clearly in the README that a lightweight backend (e.g. a small Express server, a Cloudflare Worker, or a serverless function) is required for those routes, and provide a stub `server.js` with the relevant endpoints.

---

### 6. Security Audit
- Scan all source files for hardcoded API keys, tokens, or passwords. Remove them and move to `config.js` (not committed).
- Ensure no `NEXT_PUBLIC_` references remain in any committed file.
- Review all external links: confirm `target="_blank"` anchors include `rel="noopener noreferrer"`.
- Add a strict `Content-Security-Policy` comment in `nginx.conf.example` as a starting point for the operator to tighten.

---

### 7. Server Configuration
Produce `nginx.conf.example` with a complete, annotated server block:

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name your-domain.com www.your-domain.com;

    root /var/www/your-project;
    index index.html;

    # Serve static files; fall back to index.html for SPA-style routing
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets aggressively
    location ~* \.(js|css|png|jpg|jpeg|gif|svg|ico|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    add_header Referrer-Policy "strict-origin-when-cross-origin";
}
```

Also note: run `sudo certbot --nginx -d your-domain.com` to obtain a free TLS certificate via Let's Encrypt.

---

### 8. Repository Housekeeping & Documentation
- **`.gitignore`:** Cover OS artifacts (`.DS_Store`, `Thumbs.db`), logs (`*.log`), certificates (`*.pem`), and the runtime config (`config.js`).
- **`README.md`:** Deliver a full, polished README containing:
  - Project title, description, and feature list.
  - Tech stack (Vanilla JS, HTML5, CSS3 — no framework).
  - Local development (`open index.html` or `npx serve .`).
  - Config setup (`cp config.example.js config.js`, then fill values).
  - VPS deployment steps: upload files via `rsync` or `scp`, configure Nginx, obtain TLS cert.
  - Notes on any routes that require a separate backend.

Output complete, runnable code for every file listed above. Do not omit or truncate any file. No explanations between files — code only.
