---
name: "skill-sync"
description: "Sync and update all installed skill submodules to their latest remote commits. Use this skill before every Git commit, or whenever the user asks to update, sync, refresh, or pull installed skills, or when a skill is installed."
metadata:
  author: "Leeor Nahum"
  version: "2.2.0"
---

# Skill Sync

Bring every skill submodule under `.agents/skills` to its latest remote commit in a single pass, then report what moved. Skills are installed as git submodules; this skill keeps their pinned commits current without manual GUI work.

## The Command

Run exactly this from the repo root:

```bash
git submodule update --remote -- .agents/skills
```

This fetches each skill submodule's tracked branch and re-pins it to the latest remote commit. The `git fetch` underneath is incremental, so submodules that are already current transfer almost nothing. One command covers all of them; do not loop per submodule.

When a Git commit is requested, run this command before final staging so any updated skill pointers can be included in the intended commit.

## Gotchas

- Use `--remote`. Plain `git submodule update` only re-checks-out the commit already pinned in the parent and pulls nothing new. Without `--remote` this skill does nothing useful.
- Keep the `-- .agents/skills` pathspec. It scopes the update to skill submodules and leaves any other submodules in the repo untouched.
- Do not pass `--init`. Some skills are local-only directories, not submodules. Initializing would try to clone things that should stay local.
- If a single submodule fails to fetch, report which one and why, then continue with the rest. Do not abort the whole run for one failure.

## Directory Name Check

After pulling, verify that each skill submodule's local directory name matches the `name` field in its `SKILL.md` frontmatter. Read the frontmatter `name` from `.agents/skills/<dir>/SKILL.md` and compare it to `<dir>`. They must be identical.

Fix any mismatch automatically: run `git mv .agents/skills/<old> .agents/skills/<name>` from the repo root. `git mv` moves the directory and updates `.gitmodules` in one operation. Include the renamed path in the sync report.

## Report

Report what this run did:

- Which skills advanced to a new commit.
- That the rest were already current.
- Any skill that failed to fetch, with the reason.
- Any directory name that does not match the skill's frontmatter `name`.

The update command's output already names what changed, so this needs no extra work.

Do not stage, commit, or otherwise modify the parent repo's git index. Pulling a submodule leaves its new pointer as an unstaged change in the parent; leave it exactly there for the calling workflow to review and stage. Do not inspect, report on, or worry about unrelated changes or other staged files. This skill only pulls and reports.
