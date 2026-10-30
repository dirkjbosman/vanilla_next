Convert this Vanilla JS app into a Next.js (App Router) project ready for Vercel deployment.

### Structural Requirements
1. Logic & Controllers: Translate DOM manipulation, game loops, and event listeners into React hooks (`useRef`, `useEffect`, `useState`). Do NOT alter core application algorithms or business logic. Ensure mobile touch controls (`touchstart`, `touchend`) maintain state identically without input delay or viewport zooming (`touch-action: manipulation`).
2. Top Banner Component (`components/TopBanner.jsx`):
   - Text: "Hi, I am [YOUR_NAME], the creator of this website :) If you are interested to sponsor it, please feel free to contact me [YOUR_CONTACT_URL] ❤️"
   - `[YOUR_CONTACT_URL]` must be a hyperlink — the link text is the URL itself (e.g. an X/Twitter handle, email address, or contact page URL) with `target="_blank"` and `rel="noopener noreferrer"`.
   - Match layout, links, and styling of https://[YOUR_REFERENCE_SITE]/ (all links with `target="_blank"` and `rel="noopener noreferrer"`).
   - Replace `[YOUR_NAME]` with your name, `[YOUR_CONTACT_URL]` with your contact link, and `[YOUR_REFERENCE_SITE]` with a live site URL to match the banner style from.
3. Root Layout (`app/layout.jsx`):
   - Export metadata with pumpkin favicon: `export const metadata = { title: 'App', icons: { icon: "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🎃</text></svg>" } }`.
   - Render `<TopBanner />` at top of `<body>`.
   - Render `@vercel/analytics` using `<Analytics />` from `@vercel/analytics/react`.
   - **Vercel Analytics — enable in console after deploy:**
     1. Go to [vercel.com](https://vercel.com) → open your project dashboard.
     2. Click the **Analytics** tab in the top project navigation bar.
     3. Click **Enable** → confirm on the plan prompt (Hobby tier is free).
     4. Data will appear within minutes of your first page view.
4. Page Entry (`app/page.jsx`): Client component (`'use client'`) hosting the converted Vanilla JS app/canvas wrapper. Replace any `<img>` tags with `next/image` for static assets served from `/public`.

### Configuration & Security
- `.gitignore`: Include `.DS_Store`, `.next/`, `node_modules/`, `.env*`, `out/`, `build/`, `*.log`.
- `package.json`: Include `next`, `react`, `react-dom`, and `@vercel/analytics`.
- `next.config.js`: Generate minimal boilerplate: `/** @type {import('next').NextConfig} */ const nextConfig = {}; export default nextConfig;`.
- Security Audit: Extract all API keys, authorization tokens, or passwords to `.env.local`. Client-side exposed keys must use `NEXT_PUBLIC_` prefixes. Provide a committed `.env.example` with dummy values for every variable.
- `app/globals.css`: Include the following touch rules to prevent scroll/zoom interference on mobile:
  ```css
  html, body { touch-action: manipulation; -webkit-user-select: none; user-select: none; overflow: hidden; overscroll-behavior: none; }
  ```

### Documentation
- `README.md`: Short project title, summary of core mechanics, tech stack list, and local commands (`npm run dev`, `npm run build`).

### Output Directive
Output complete runnable file contents only. No explanations, no summaries, no commentary.
