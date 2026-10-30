Convert this Next.js (App Router) application into a self-contained Vanilla JS project ready for deployment on a VPS (e.g. Hetzner, DigitalOcean, Linode) using Nginx or Caddy as the static file server.

### Structural Requirements
1. Output Files: Produce a single `index.html`, one `style.css`, and one `main.js`. Inline small assets where practical; place larger assets in a `public/` folder.
2. Logic Migration: Extract all React component logic and state into plain JS. Replace `useState`/`useRef`/`useEffect` with direct DOM manipulation, closures, and `addEventListener`. Preserve all business logic, algorithms, and math exactly — do not simplify or alter them.
3. No Build Step: The output must run directly in the browser without Node.js, npm, or a bundler. No import maps, no ES module resolution that requires a server.
4. Touch Controls: Preserve all `touchstart`/`touchend` handlers. Add the following to `style.css`:
   ```css
   html, body { touch-action: manipulation; -webkit-user-select: none; user-select: none; overflow: hidden; overscroll-behavior: none; }
   ```
5. Remove Framework Artifacts: Strip `@vercel/analytics`, `next/image`, `next/font`, App Router metadata exports, React JSX, and all `node_modules` references.

### Configuration & Security
- `.gitignore`: Include `.DS_Store`, `*.log`, `*.pem`, `.env*`.
- Security Audit: Remove any `NEXT_PUBLIC_` environment variable references. If the app consumed API keys client-side, keep them in a clearly commented `config.js` (never committed) and provide a `config.example.js` with dummy values. Document in the README that `config.js` must be created locally before running.
- No secrets in committed files.

### Server Config Snippet
Include a minimal `nginx.conf` snippet in the README (or as `nginx.conf.example`) for serving the project as a static site:
```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/your-project;
    index index.html;
    location / { try_files $uri $uri/ /index.html; }
}
```

### Documentation
- `README.md`: Project title, summary, how to run locally (`open index.html` or `npx serve .`), and VPS deployment steps (upload files, point Nginx root, reload).

### Output Directive
Output complete runnable file contents only. No explanations, no summaries, no commentary.
