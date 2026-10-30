# vanilla_next

A collection of Claude Code prompts for converting web apps between Vanilla JS and Next.js — in both directions.

---

## Why Two Directions?

### Vanilla JS → Next.js (`v2n`)

Vanilla JS projects are quick to start but hit a ceiling fast: no server-side rendering, no built-in image optimisation, no analytics, manual asset pipelines, and deployment friction. Moving to Next.js + Vercel removes that ceiling and gives you production-grade infrastructure for free on the Hobby tier. Use `v2n` when you want to graduate a side-project from "it works on my machine" to a publicly shareable, Vercel-hosted app.

### Next.js → Vanilla JS (`n2v`)

Not every project needs a framework. If you want full control over your infrastructure, zero Node.js runtime in production, and the simplicity of serving static files from your own VPS (Hetzner, DigitalOcean, Linode…), stripping Next.js out is the right call. A flat `index.html` + `main.js` served by Nginx starts in milliseconds, costs a few euros a month, and has no cold-start, no vendor lock-in, and no surprise billing. Use `n2v` when ownership and simplicity matter more than managed infrastructure.

---

## Prompts

### `v2n/` — Vanilla JS → Next.js (Vercel)

| File | Purpose | Token Cost |
|------|---------|------------|
| [`v2n/prompt_lean.md`](./v2n/prompt_lean.md) | Quick conversion — minimal output, essentials only | Low |
| [`v2n/prompt_full.md`](./v2n/prompt_full.md) | Thorough conversion — full architecture, step-by-step, all files | High |

Both `v2n` prompts produce:
- Next.js App Router structure (`app/`, `components/`, `public/`)
- Mobile-safe `globals.css` with touch/scroll rules
- Personalised `TopBanner` component (set your name and contact URL in the prompt)
- Pumpkin favicon via inline SVG metadata
- `@vercel/analytics` integration (+ Vercel console enablement steps)
- `.env.local` / `.env.example` secret management
- `next.config.js` boilerplate
- Updated `.gitignore` and `README.md` for the converted project

### `n2v/` — Next.js → Vanilla JS (VPS / self-hosted)

| File | Purpose | Token Cost |
|------|---------|------------|
| [`n2v/prompt_lean.md`](./n2v/prompt_lean.md) | Quick extraction — single HTML/CSS/JS output, essentials only | Low |
| [`n2v/prompt_full.md`](./n2v/prompt_full.md) | Thorough conversion — modular JS, Nginx config, full security audit | High |

Both `n2v` prompts produce:
- Dependency-free `index.html` + `style.css` + `main.js`
- Complete React/Next.js removal (no JSX, no build step)
- `config.example.js` pattern for client-side runtime config (replaces `NEXT_PUBLIC_` env vars)
- `nginx.conf.example` with static-file serving, asset caching, and security headers
- TLS setup note (Let's Encrypt / Certbot)
- Updated `.gitignore` and `README.md` for the converted project

---

## Quick Start

The fastest way to use these prompts across any project is a **one-time global symlink setup**. The symlinks point back to this repo, so any edits to the prompts are instantly reflected everywhere — no re-copying needed.

```bash
mkdir -p ~/.claude/commands

# v2n (Vanilla → Next.js)
ln -sf ~/Github_Dirk/vanilla_next/v2n/prompt_lean.md ~/.claude/commands/to-nextjs-lean.md
ln -sf ~/Github_Dirk/vanilla_next/v2n/prompt_full.md ~/.claude/commands/to-nextjs-full.md

# n2v (Next.js → Vanilla)
ln -sf ~/Github_Dirk/vanilla_next/n2v/prompt_lean.md ~/.claude/commands/to-vanilla-lean.md
ln -sf ~/Github_Dirk/vanilla_next/n2v/prompt_full.md ~/.claude/commands/to-vanilla-full.md
```

Then from any project, open Claude Code and type:

```
# Vanilla → Next.js
/to-nextjs-lean    # quick conversion
/to-nextjs-full    # thorough conversion

# Next.js → Vanilla
/to-vanilla-lean   # quick extraction
/to-vanilla-full   # thorough conversion
```

---

## All Workflow Options

Seven ways to wire these prompts into your workflow, ranked by setup time.

---

### Option 1 — Project Slash Command

Store the commands inside a specific project's `.claude/commands/` folder.

```bash
mkdir -p .claude/commands

# v2n
cp ~/Github_Dirk/vanilla_next/v2n/prompt_lean.md .claude/commands/to-nextjs-lean.md
cp ~/Github_Dirk/vanilla_next/v2n/prompt_full.md .claude/commands/to-nextjs-full.md

# n2v
cp ~/Github_Dirk/vanilla_next/n2v/prompt_lean.md .claude/commands/to-vanilla-lean.md
cp ~/Github_Dirk/vanilla_next/n2v/prompt_full.md .claude/commands/to-vanilla-full.md
```

**Usage:** type `/to-nextjs-lean`, `/to-nextjs-full`, `/to-vanilla-lean`, or `/to-vanilla-full` inside Claude Code for that project.

---

### Option 2 — Global Slash Command (Recommended)

Same as Option 1 but stored globally — available in every Claude Code session on your machine.

```bash
mkdir -p ~/.claude/commands

# v2n
cp ~/Github_Dirk/vanilla_next/v2n/prompt_lean.md ~/.claude/commands/to-nextjs-lean.md
cp ~/Github_Dirk/vanilla_next/v2n/prompt_full.md ~/.claude/commands/to-nextjs-full.md

# n2v
cp ~/Github_Dirk/vanilla_next/n2v/prompt_lean.md ~/.claude/commands/to-vanilla-lean.md
cp ~/Github_Dirk/vanilla_next/n2v/prompt_full.md ~/.claude/commands/to-vanilla-full.md
```

Use symlinks instead of `cp` to stay in sync with this repo automatically (see Quick Start above).

---

### Option 3 — CLAUDE.md Project Instructions

Add a block to the `CLAUDE.md` of any project. Claude reads it automatically at session start.

**For a vanilla project pending Next.js conversion:**
```markdown
## Conversion Target
This project is pending conversion to Next.js for Vercel deployment.
When asked to convert, follow the instructions in `v2n/prompt_lean.md` (quick) or `v2n/prompt_full.md` (thorough).
```

**For a Next.js project pending VPS extraction:**
```markdown
## Conversion Target
This project is pending extraction to Vanilla JS for self-hosted VPS deployment.
When asked to convert, follow the instructions in `n2v/prompt_lean.md` (quick) or `n2v/prompt_full.md` (thorough).
```

Then just say *"Convert this."* — no slash command needed.

---

### Option 4 — Shell Alias / Script

For headless use or terminal-first workflows.

**Add to `~/.zshrc` or `~/.bashrc`:**
```bash
alias convert-to-next-lean='cat ~/Github_Dirk/vanilla_next/v2n/prompt_lean.md | claude --print'
alias convert-to-next-full='cat ~/Github_Dirk/vanilla_next/v2n/prompt_full.md | claude --print'
alias convert-to-vanilla-lean='cat ~/Github_Dirk/vanilla_next/n2v/prompt_lean.md | claude --print'
alias convert-to-vanilla-full='cat ~/Github_Dirk/vanilla_next/n2v/prompt_full.md | claude --print'
```

Or as a script:
```bash
#!/bin/bash
# convert.sh — usage: ./convert.sh v2n lean | v2n full | n2v lean | n2v full
DIRECTION=${1:-v2n}
VARIANT=${2:-lean}
cat ~~/Github_Dirk/vanilla_next/${DIRECTION}/prompt_${VARIANT}.md | claude --print > conversion_output.md
echo "Done. Output written to conversion_output.md"
```

---

### Option 5 — Claude Code Hook (Auto-trigger)

Auto-inject the conversion prompt whenever you submit a message in a marked project.

**`.claude/settings.json` in the target project:**
```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "[ -f .convert-to-next ] && cat ~/.claude/commands/to-nextjs-lean.md"
          }
        ]
      }
    ]
  }
}
```

Add a `.convert-to-next` or `.convert-to-vanilla` marker file to activate. Remove it once done.

> `UserPromptSubmit` fires once per message — correct for injecting context. Do not use `PreToolUse` with a wildcard matcher; that fires on every tool call Claude makes internally.

---

### Option 6 — MCP Server Tool

Build a lightweight MCP server to expose all four prompts as native Claude Code tools. Best for team use or when you want version-controlled prompts with logging.

**`mcp-vanilla-next/index.js`:**
```js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import fs from "fs";

const server = new Server({ name: "vanilla-next", version: "2.0.0" });

const PROMPTS = {
  v2n_lean: "v2n/prompt_lean.md",
  v2n_full: "v2n/prompt_full.md",
  n2v_lean: "n2v/prompt_lean.md",
  n2v_full: "n2v/prompt_full.md",
};

server.setRequestHandler("tools/list", async () => ({
  tools: Object.keys(PROMPTS).map((name) => ({
    name,
    description: `Load the ${name.replace("_", " ")} conversion prompt`,
  })),
}));

server.setRequestHandler("tools/call", async (req) => {
  const file = PROMPTS[req.params.name];
  if (!file) throw new Error(`Unknown tool: ${req.params.name}`);
  return { content: [{ type: "text", text: fs.readFileSync(file, "utf8") }] };
});
```

Register in `~/.claude/settings.json` under `mcpServers`.

---

### Option 7 — GitHub Actions (Fully Automated)

Trigger a conversion automatically when you push to a dedicated branch.

**`.github/workflows/convert.yml`:**
```yaml
name: Convert Project
on:
  push:
    branches:
      - convert-to-next
      - convert-to-vanilla
jobs:
  convert:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Detect direction and run Claude
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          npm install -g @anthropic-ai/claude-code
          BRANCH="${GITHUB_REF##*/}"
          if [ "$BRANCH" = "convert-to-next" ]; then
            PROMPT="v2n/prompt_lean.md"
          else
            PROMPT="n2v/prompt_lean.md"
          fi
          cat "$PROMPT" | claude --print > conversion_output.md
      - uses: actions/upload-artifact@v4
        with:
          name: conversion-output
          path: conversion_output.md
```

Push to `convert-to-next` or `convert-to-vanilla` and the output lands as a downloadable artifact.

---

### Summary

| Option | Setup Time | Best For |
|--------|-----------|----------|
| 1 — Project slash command | 1 min | Single project, quick start |
| 2 — Global slash command + symlink | 1 min | All projects, daily driver |
| 3 — CLAUDE.md instructions | 2 min | Projects with existing CLAUDE.md |
| 4 — Shell alias/script | 5 min | Terminal-first workflow |
| 5 — Hook auto-trigger | 10 min | Zero-friction, marker-file workflow |
| 6 — MCP server | 1–2 hrs | Team use, versioned prompts, logging |
| 7 — GitHub Actions | 30 min | Fully automated CI/CD pipeline |

---

## Notes

### Personalising the TopBanner (v2n)
Both `v2n` prompts include `[YOUR_NAME]` and `[YOUR_CONTACT_URL]` placeholders in the TopBanner instructions. Replace these in the prompt files before running a conversion, or edit the generated component directly afterwards.

### `api/` Route Handlers (v2n)
The `v2n/prompt_full.md` prompt lists `app/api/` as optional. Omit it unless the app requires server-side logic (proxying secrets, webhook receivers, server-sent events). When listed unnecessarily, models sometimes generate empty placeholder files.

### Server-side Secrets (n2v)
If the original Next.js app used API route handlers that consumed private secrets, those secrets cannot live in a static Vanilla JS build. The `n2v/prompt_full.md` prompt generates a stub `server.js` and documents this clearly. A lightweight sidecar (Express, Cloudflare Worker, or serverless function) is required for those endpoints.

### Choosing lean vs full
- **lean** — fast, low token cost, best when the codebase is small or the conversion is straightforward.
- **full** — thorough, higher token cost, best for larger apps, team handoffs, or when you want a polished README and complete security audit.

---

## License

See [LICENSE](./LICENSE).
