# vanilla_next

A collection of Claude Code prompts for converting Vanilla JS apps into production-ready Next.js (App Router) projects, optimized for Vercel deployment.

---

## Prompts

| File | Purpose | Token Cost |
|------|---------|------------|
| [`prompt_lean.md`](./prompt_lean.md) | Quick conversion — minimal output, essentials only | Low |
| [`prompt_long.md`](./prompt_long.md) | Thorough conversion — full architecture, step-by-step, all files | High |

Both prompts produce:
- Next.js App Router structure (`app/`, `components/`, `public/`)
- Mobile-safe `globals.css` with touch/scroll rules
- Personalised `TopBanner` component (set your name and reference site in the prompt)
- Pumpkin favicon via inline SVG metadata
- `@vercel/analytics` integration (+ Vercel console enablement steps)
- `.env.local` / `.env.example` secret management
- `next.config.js` boilerplate
- Updated `.gitignore` and `README.md` for the converted project

---

## Quick Start

The fastest way to use these prompts across any project is a **one-time global symlink setup**. The symlinks point back to this repo, so any edits to the prompts are instantly reflected everywhere — no re-copying needed.

```bash
mkdir -p ~/.claude/commands
ln -sf /path/to/vanilla_next/prompt_lean.md ~/.claude/commands/to-nextjs-lean.md
ln -sf /path/to/vanilla_next/prompt_long.md ~/.claude/commands/to-nextjs-full.md
```

Then from any vanilla JS project, open Claude Code and type:

```
/to-nextjs-lean    # quick conversion
/to-nextjs-full    # thorough conversion
```

---

## All Workflow Options

Seven ways to wire these prompts into your workflow, ranked by setup time.

---

### Option 1 — Project Slash Command

Store the commands inside a specific project's `.claude/commands/` folder.

```bash
mkdir -p .claude/commands
cp /path/to/prompt_lean.md .claude/commands/to-nextjs-lean.md
cp /path/to/prompt_long.md .claude/commands/to-nextjs-full.md
```

**Usage:** type `/to-nextjs-lean` or `/to-nextjs-full` inside Claude Code for that project.

---

### Option 2 — Global Slash Command (Recommended)

Same as Option 1 but stored globally — available in every Claude Code session on your machine.

```bash
mkdir -p ~/.claude/commands
cp /path/to/prompt_lean.md ~/.claude/commands/to-nextjs-lean.md
cp /path/to/prompt_long.md ~/.claude/commands/to-nextjs-full.md
```

Use symlinks instead of `cp` to stay in sync with this repo automatically:

```bash
ln -sf /path/to/vanilla_next/prompt_lean.md ~/.claude/commands/to-nextjs-lean.md
ln -sf /path/to/vanilla_next/prompt_long.md ~/.claude/commands/to-nextjs-full.md
```

---

### Option 3 — CLAUDE.md Project Instructions

Add a block to the `CLAUDE.md` of any vanilla JS project. Claude reads it automatically at session start.

```markdown
## Conversion Target
This project is pending conversion to Next.js for Vercel deployment.
When asked to convert, follow the instructions in `prompt_lean.md` (quick) or `prompt_long.md` (thorough).
```

Then just say *"Convert this."* — no slash command needed.

---

### Option 4 — Shell Alias / Script

For headless use or terminal-first workflows.

**Add to `~/.zshrc` or `~/.bashrc`:**
```bash
alias convert-lean='cat /path/to/prompt_lean.md | claude --print'
alias convert-full='cat /path/to/prompt_long.md | claude --print'
```

Or as a script:
```bash
#!/bin/bash
# convert-to-next.sh
PROMPT_FILE=${1:-prompt_lean}
cat ~/path/to/vanilla_next/${PROMPT_FILE}.md | claude --print > conversion_output.md
echo "Done. Output written to conversion_output.md"
```

```bash
./convert-to-next.sh prompt_lean
./convert-to-next.sh prompt_long
```

---

### Option 5 — Claude Code Hook (Auto-trigger)

Auto-inject the conversion prompt whenever you submit a message in a marked project.

**`.claude/settings.json` in the target vanilla project:**
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

Add a `.convert-to-next` marker file to activate. Remove it once done.

> `UserPromptSubmit` fires once per message — correct for injecting context. Do not use `PreToolUse` with a wildcard matcher; that fires on every tool call Claude makes internally.

---

### Option 6 — MCP Server Tool

Build a lightweight MCP server to expose the prompts as native Claude Code tools. Best for team use or when you want version-controlled prompts with logging.

**`mcp-vanilla-next/index.js`:**
```js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import fs from "fs";

const server = new Server({ name: "vanilla-next", version: "1.0.0" });

server.setRequestHandler("tools/list", async () => ({
  tools: [
    { name: "convert_lean", description: "Lean vanilla→Next.js conversion prompt" },
    { name: "convert_full", description: "Full vanilla→Next.js conversion prompt" },
  ],
}));

server.setRequestHandler("tools/call", async (req) => {
  const file = req.params.name === "convert_lean" ? "prompt_lean.md" : "prompt_long.md";
  return { content: [{ type: "text", text: fs.readFileSync(file, "utf8") }] };
});
```

Register in `~/.claude/settings.json` under `mcpServers`.

---

### Option 7 — GitHub Actions (Fully Automated)

Trigger a conversion automatically when you push to a dedicated branch.

**`.github/workflows/convert-to-next.yml`:**
```yaml
name: Convert to Next.js
on:
  push:
    branches: [convert-to-next]
jobs:
  convert:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Claude conversion
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          npm install -g @anthropic-ai/claude-code
          cat prompt_lean.md | claude --print > conversion_output.md
```

Push your vanilla project to the `convert-to-next` branch and the output lands as an artifact.

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

### Personalising the TopBanner
Both prompts include a `[YOUR_NAME]` and `[YOUR_REFERENCE_SITE]` placeholder in the TopBanner instructions. Replace these in the prompt files before running a conversion, or edit the generated component directly afterwards.

### `api/` Route Handlers
The `prompt_long.md` prompt lists `app/api/` as optional. Omit it unless the app requires server-side logic (proxying secrets, webhook receivers, server-sent events). When listed unnecessarily, models sometimes generate empty placeholder files.

---

## License

See [LICENSE](./LICENSE).
