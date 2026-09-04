---
name: sync-claude-cloud
description: "Use when the user wants to push local Claude Code config (skills, agents, commands, rules, CLAUDE.md) to their cloud config repo at github.com/AkrasiaNexus/claude-code so cloud environments pick it up. Triggers: 'sync claude cloud', 'push my skills', 'update the cloud config', 'sync claude to github', 'publish skill to cloud', '/sync-claude-cloud'. Also use after creating/editing any skill or agent the user wants live in their cloud env."
---

# /sync-claude-cloud

Publish local `~/.claude/` config to `github.com/AkrasiaNexus/claude-code` (branch `main`) so Claude Code cloud environments load it on clone.

## Prerequisites

- Git credential manager authed to `github.com/AkrasiaNexus`
- A working clone of the repo. If none exists on this machine:
  ```bash
  git clone https://github.com/AkrasiaNexus/claude-code.git ~/claude-code-repo
  export CC_REPO=~/claude-code-repo
  ```
  Otherwise reuse the existing scratch path as `CC_REPO`.

## One-shot sync

```bash
CC_REPO="${CC_REPO:-$HOME/claude-code-repo}"
cd "$CC_REPO"
cp -r /c/Users/Lucky/.claude/skills .
cp -r /c/Users/Lucky/.claude/agents .
cp -r /c/Users/Lucky/.claude/commands .
cp -r /c/Users/Lucky/.claude/rules .
cp    /c/Users/Lucky/.claude/CLAUDE.md .
git add .
git status --short | head
```

Show the user the `git status` summary. If nothing changed, stop and say so.

## Pre-push safety scan (mandatory)

Scan the staged diff for secrets before committing:
```bash
git diff --cached | grep -EnI "(sk-[A-Za-z0-9]{20,}|ghp_[A-Za-z0-9]{20,}|gsk_[A-Za-z0-9]{20,}|AIza[A-Za-z0-9_-]{35}|xox[baprs]-[A-Za-z0-9-]{10,}|Bearer [A-Za-z0-9_.-]{20,})" | head
```
If anything prints, STOP. Unstage the file, add it to `.gitignore`, re-run.

`.gitignore` in the repo already blocks `.env*`, `*token*`, `*secret*`, `*credential*`, `*.key`, `*.pem`, `settings.local.json`, `sessions/`, `cache/`, `metrics/`, `history.jsonl`. Never remove those entries.

## Commit + push

```bash
git -c user.name="Lucky" -c user.email="vishnutuition0206@gmail.com" \
    commit -m "sync: <one-line summary of what changed>

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>"
GIT_TERMINAL_PROMPT=0 git push origin main
```

Report the commit SHA + affected paths back to the user.

## After a fresh cloud env boots

Tell the user to run once inside the cloud env:
```bash
git clone https://github.com/AkrasiaNexus/claude-code.git ~/.claude
```
Or if `~/.claude` already exists:
```bash
git clone https://github.com/AkrasiaNexus/claude-code.git /tmp/cc && \
cp -rn /tmp/cc/{skills,agents,commands,rules,CLAUDE.md} ~/.claude/
```

## When NOT to use

- Personal-only tweaks (aliases, per-machine paths) - keep local
- `settings.json` mirroring - skip; has local paths + MCP tokens
- Anything under `~/.claude/{sessions,projects,cache,metrics,plugins,history*,cost-tracker.log}` - already gitignored, never publish

ponytail: one commit per sync, meaningful message, no force-push, no branch other than `main`.
