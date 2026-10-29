# Conversion Workflow Options

Ways to use `prompt_lean.md` / `prompt_long.md` faster inside your daily developer workflow, ranked from simplest to most powerful.

---

## Option 1 — Custom Claude Code Slash Command (Recommended)

Claude Code supports custom slash commands stored as `.md` files inside `.claude/commands/` in any project.

**Setup (one-time):**
```bash
mkdir -p .claude/commands
cp /path/to/prompt_lean.md .claude/commands/to-nextjs-lean.md
cp /path/to/prompt_long.md .claude/commands/to-nextjs-full.md
```

**Usage:**
Inside Claude Code for any vanilla JS project, type:
```
/to-nextjs-lean
/to-nextjs-full
```
Claude Code automatically loads the full prompt and runs it against the current project. No copy-paste, no token overhead for re-explaining context.

**Why this wins:** It's native to Claude Code, no extra tooling, works in every project that has the `.claude/commands/` folder, and the command shows up in autocomplete when you type `/`.

---

## Option 2 — Global Slash Command (Cross-Project)

Same as Option 1 but stored globally so every project gets the command without copying files.

**Setup (one-time):**
```bash
mkdir -p ~/.claude/commands
cp /path/to/prompt_lean.md ~/.claude/commands/to-nextjs-lean.md
cp /path/to/prompt_long.md ~/.claude/commands/to-nextjs-full.md
```

Now `/to-nextjs-lean` and `/to-nextjs-full` are available in **every** Claude Code session, globally.

---

## Option 3 — CLAUDE.md Project Instructions

Add a short instruction block to the `CLAUDE.md` of any vanilla JS project you want to convert. Claude reads `CLAUDE.md` automatically at the start of every session.

**Example block to paste into the target project's `CLAUDE.md`:**
```markdown
## Conversion Target
This project is pending conversion to Next.js for Vercel deployment.
When asked to convert, follow the instructions in `prompt_lean.md` (quick) or `prompt_long.md` (thorough).
```

Then in a session just say: *"Convert this."* and Claude already has the protocol loaded.

---

## Option 4 — Shell Alias / Script

For running headless or from a terminal without opening an interactive Claude Code session.

**Add to `~/.zshrc` or `~/.bashrc`:**
```bash
alias convert-lean='cat /path/to/prompt_lean.md | claude --print'
alias convert-full='cat /path/to/prompt_long.md | claude --print'
```

Or a script that appends source files automatically:
```bash
#!/bin/bash
# convert-to-next.sh
PROMPT_FILE=${1:-prompt_lean}   # default to lean, pass "prompt_long" for thorough
cat ~/path/to/vanilla_next/${PROMPT_FILE}.md | claude --print > conversion_output.md
echo "Done. Output written to conversion_output.md"
```

**Usage:**
```bash
./convert-to-next.sh lean
./convert-to-next.sh long
```

---

## Option 5 — Claude Code Hook (Auto-trigger on Project Open)

Claude Code hooks let you run shell commands on specific events. You can auto-inject the conversion prompt when Claude Code starts in a project that has a specific marker file.

**In `.claude/settings.json` of the vanilla project:**
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

Add a `.convert-to-next` marker file to any project you want to convert. Remove it once done.

> **Note:** `UserPromptSubmit` fires once when you send a message — correct for injecting context. The previous `PreToolUse` with a wildcard matcher would have fired on every single tool call Claude makes, which is not the intended behaviour.

---

## Option 6 — MCP Server Tool (Most Powerful, Most Setup)

Build a lightweight MCP server that exposes `convert_to_nextjs` as a tool. Claude Code can then call it natively like any other tool.

**When to use this:** If you are converting many projects frequently and want version-controlled prompts, logging of conversions, or team-shared tooling.

**Rough structure (`mcp-vanilla-next/index.js`):**
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

Register it in `~/.claude/settings.json` under `mcpServers`.

---

## Option 7 — GitHub Actions (Fully Automated)

Trigger a conversion automatically when you push a vanilla JS project to a specific branch.

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

## Quick Recommendation Summary

| Option | Setup Time | Best For |
|--------|-----------|----------|
| 1 — Project slash command | 1 min | Single project, quick start |
| 2 — Global slash command | 1 min | All projects, daily driver |
| 3 — CLAUDE.md instructions | 2 min | Projects with existing CLAUDE.md |
| 4 — Shell alias/script | 5 min | Terminal-first workflow |
| 5 — Hook auto-trigger | 10 min | Zero-friction, marker-file workflow |
| 6 — MCP server | 1–2 hrs | Team use, versioned prompts, logging |
| 7 — GitHub Actions | 30 min | Fully automated CI/CD pipeline |

**Start with Option 2** (global slash command). Zero dependencies, no configuration, works everywhere, and you can always layer in MCP or Actions later.
