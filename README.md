# claude-code

Global Claude Code config: skills, agents, commands, rules, CLAUDE.md.
Mirrored from `~/.claude/` for use in Claude Code cloud environments.

## Bootstrap on a fresh box / cloud env

```bash
git clone https://github.com/AkrasiaNexus/claude-code.git ~/.claude
```

If `~/.claude` already exists, merge instead:
```bash
git clone https://github.com/AkrasiaNexus/claude-code.git /tmp/cc
cp -rn /tmp/cc/{skills,agents,commands,rules,CLAUDE.md} ~/.claude/
```
