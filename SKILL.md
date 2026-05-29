---
name: skill-sync
description: Pull every skill submodule under the agent skills directory up to its latest remote commit in one pass. Use when the user wants to update, pull, sync, or refresh installed skills, bring skill submodules current, or catch up after upstream skill changes.
disable-model-invocation: true
metadata:
  author: Leeor Nahum
  version: "1.1.0"
---

# Skill Sync

Bring every skill submodule under `.agents/skills` to its latest remote commit in a single pass, then report what moved. Skills are installed as git submodules; this skill keeps their pinned commits current without manual GUI work.

## The Command

Run exactly this from the repo root:

```bash
git submodule update --remote -- .agents/skills
```

This fetches each skill submodule's tracked branch and re-pins it to the latest remote commit. The `git fetch` underneath is incremental, so submodules that are already current transfer almost nothing. One command covers all of them; do not loop per submodule.

## Gotchas

- Use `--remote`. Plain `git submodule update` only re-checks-out the commit already pinned in the parent and pulls nothing new. Without `--remote` this skill does nothing useful.
- Keep the `-- .agents/skills` pathspec. It scopes the update to skill submodules and leaves any other submodules in the repo untouched.
- Do not pass `--init`. Some skills are local-only directories, not submodules. Initializing would try to clone things that should stay local.
- If a single submodule fails to fetch, report which one and why, then continue with the rest. Do not abort the whole run for one failure.

## Report

Report what this run did:

- Which skills advanced to a new commit.
- That the rest were already current.
- Any skill that failed to fetch, with the reason.

The update command's output already names what changed, so this needs no extra work.

Do not stage, commit, or otherwise modify the parent repo's git index. Pulling a submodule leaves its new pointer as an unstaged change in the parent; leave it exactly there. Do not inspect, report on, or worry about unrelated changes, other staged files, or the parent's commit state. This skill only pulls and reports.
