You are a Senior Frontend Architect and Next.js Systems Specialist. Your objective is to refactor this Vanilla JavaScript application into a production-ready Next.js App Router application optimized for deployment on Vercel.

---

### 1. File Structure & Target Architecture
Reorganize the project into the following modular Next.js directory layout:

- `app/`
  - `layout.jsx` (Root layout with metadata, banner, & analytics)
  - `page.jsx` (Main app entry point with `'use client'`)
  - `globals.css` (Reset styles & mobile touch handling)
  - `api/` *(Add route handlers here only if the app requires server-side API calls, e.g. proxying secrets or webhook receivers. Omit otherwise.)*
- `components/`
  - `TopBanner.jsx` (Header sponsorship banner — see Section 4 for personalisation)
- `public/` (Static assets: images, audio, icons)
- `.env.local` / `.env.example` (Secret & environment management)
- `.gitignore` (OS, build, and dependency exclusion file)
- `next.config.js` (Minimal Next.js configuration)
- `package.json` (Dependency declarations)
- `README.md` (Complete project documentation)

---

### 2. Strict Logic Preservation & State Migration
- **Zero Business Logic Changes:** Under no circumstances should game physics, math formulas, data processing rules, or core algorithms be rewritten or modified.
- **Imperative to Declarative Bridge:**
  - Replace raw `document.querySelector`, `getElementById`, or manual DOM nodes with `useRef` hooks.
  - Bridge event listeners (`keydown`, `keyup`, `resize`, `touchstart`, `touchend`, `mousemove`) inside `useEffect` hooks with complete cleanup routines (`removeEventListener`, `cancelAnimationFrame`, `clearInterval`) to avoid memory leaks or duplicate handlers under React Strict Mode.
  - **Strict Mode double-invoke:** Next.js 13+ runs effects twice in development. All `useEffect` cleanup routines must be idempotent — do not assume an effect fires only once per mount.
  - Convert global/window variables into React `useState` or persistent `useRef` variables where appropriate.

---

### 3. Mobile Responsiveness & Touch Controls
- Ensure canvas scaling, virtual joysticks, or onscreen buttons adjust dynamically to `window.innerWidth` and `window.innerHeight`.
- Apply strict touch rules in `globals.css` to prevent mobile browser defaults from breaking input:

```css
html, body {
  touch-action: manipulation;
  -webkit-user-select: none;
  user-select: none;
  overflow: hidden;
  overscroll-behavior: none;
}
```

- Ensure mobile controllers work seamlessly across touch devices without lag, scroll-slop, or zoom triggers.

---

### 4. UI Components, Assets & Analytics Integration
- **Top Banner (`components/TopBanner.jsx`):**
  - Create a fixed/sticky banner at the top of the page with the following text (customise the placeholders):
    > *"Hi, I am [YOUR_NAME], the creator of this website :) If you are interested to sponsor it, please feel free to contact me here ❤️"*
  - Replace `[YOUR_NAME]` with your name. Optionally, match the layout and link style of an existing reference site at `https://[YOUR_REFERENCE_SITE]/`.
  - Implement fixed/sticky positioning with responsive padding. All external links must include `target="_blank"` and `rel="noopener noreferrer"`.
- **Favicon & Metadata:**
  - Configure a pumpkin favicon in `app/layout.jsx` via the App Router Metadata API using an inline SVG data URI or graphic file.
- **Vercel Analytics:**
  - Add `@vercel/analytics` to `package.json`.
  - Import `<Analytics />` from `@vercel/analytics/react` and render it inside `app/layout.jsx`.
  - **After deploying to Vercel, enable analytics in the console:**
    1. Go to [vercel.com](https://vercel.com) → open your project dashboard.
    2. Click the **Analytics** tab in the top project navigation bar.
    3. Click **Enable** → confirm on the plan prompt (Hobby tier is free).
    4. Data will appear within minutes of your first live page view.
- **Image Optimization:** Replace any `<img>` tags with `next/image` for all static assets served from `/public`.

---

### 5. Security Protocol & Environment Isolation
Perform an exhaustive security check across all source files:
- Scan for hardcoded secrets, API tokens, passwords, or connection strings.
- Move secrets to `.env.local`. Ensure environment keys required in the client bundle are prefixed with `NEXT_PUBLIC_`.
- Provide a clear `.env.example` file demonstrating required variables with dummy values.

---

### 6. Repository Housekeeping & Documentation
- **`.gitignore` Configuration:** Ensure full coverage for:
  - macOS & OS artifacts: `.DS_Store`, `Thumbs.db`
  - Next.js build caches: `.next/`, `out/`, `build/`
  - Node modules & logs: `node_modules/`, `*.log`
  - Environment files: `.env`, `.env.local`, `.env.production`
- **`README.md` File:** Deliver a full, polished README containing:
  - Project Title, Description, and Features.
  - Tech Stack Breakdown (Next.js App Router, React, Vercel Analytics, CSS Modules / Global CSS).
  - Prerequisites and Local Setup Guide (`npm install`, `npm run dev`).
  - Environment Variable Setup.
  - Vercel One-Click Deployment Instructions.

Output complete, runnable code for every file listed above. Do not omit or truncate any file. No explanations between files — code only.