# vanilla_next

A collection of Claude Code prompts for converting Vanilla JS apps into production-ready Next.js (App Router) projects, optimized for Vercel deployment.

---

## What This Repo Does

Drop one of the prompts below into a Claude Code session inside any Vanilla JS project and get a fully converted Next.js app — complete with mobile touch controls, Vercel Analytics, a top banner, environment variable handling, and a clean repo structure.

---

## Prompts

| File | Purpose | Token Cost |
|------|---------|------------|
| [`prompt_lean.md`](./prompt_lean.md) | Quick conversion — minimal output, essentials only | Low |
| [`prompt_long.md`](./prompt_long.md) | Thorough conversion — full architecture, step-by-step, all files | High |

Both prompts produce:
- Next.js App Router structure (`app/`, `components/`, `public/`)
- Mobile-safe `globals.css` with touch/scroll rules
- `TopBanner` component matching [YOUR_REFERENCE_SITE](https://YOUR_REFERENCE_SITE/)
- Pumpkin favicon via inline SVG metadata
- `@vercel/analytics` integration (+ Vercel console enablement steps)
- `.env.local` / `.env.example` secret management
- `next.config.js` boilerplate
- Updated `.gitignore` and `README.md` for the converted project

---

## Workflow

[`prompt_flow.md`](./prompt_flow.md) documents every way to wire these prompts into your developer workflow — from a 1-minute global slash command setup to a fully automated GitHub Actions pipeline.

**Quickest path:** Copy both prompts into `~/.claude/commands/` and use `/prompt_lean` or `/prompt_long` as slash commands inside any Claude Code session.

```bash
mkdir -p ~/.claude/commands
cp prompt_lean.md ~/.claude/commands/
cp prompt_long.md ~/.claude/commands/
```

---

## Original Requirements

These prompts were built to satisfy the following checklist for any converted project:

- Converted to Next.js (App Router)
- Mobile responsive — touch controllers preserved as-is
- Core logic untouched
- Pumpkin favicon
- Vercel Analytics SDK included and enabled
- Top banner matching YOUR_REFERENCE_SITE
- `.gitignore` covers Next.js, Node, macOS artifacts, and env files
- Polished `README.md` generated for the output project

---

## Notes & Extensions

## License

See [LICENSE](./LICENSE).

---

### `api/` Route Handlers
The `prompt_long.md` prompt lists `app/api/` as an optional directory in the file structure. In practice, **omit it unless the app requires server-side logic** (e.g. proxying a third-party API to hide credentials, server-sent events, or webhook receivers). When models see it listed, they sometimes generate empty placeholder files that add noise. Only add `app/api/` to the target architecture if the source vanilla app already makes server-side calls or if secrets must be kept off the client bundle entirely.
