# skill-sync

Agent skill for pulling installed skill submodules up to their latest remote commit.

Brings every skill submodule under `.agents/skills` current in one pass and reports what changed, without touching the parent repo's git index, instead of bumping each submodule by hand.

## Install

```bash
git submodule add https://github.com/LeeorNahum/skill-sync-skill.git .agents/skills/skill-sync
```

## Files

- `SKILL.md` - skill guidance loaded by the agent
