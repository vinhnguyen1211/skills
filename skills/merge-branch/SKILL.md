---
name: merge-branch
description: Use this skill whenever the user signals they want to merge a branch into the current one — phrases like "merge main", "merge into current branch", "bring in changes from X", "merge branch X", "sync with main". The job is to identify the source branch, show what would change (including a dry-run conflict check), and wait for approval before running any merge command.
license: MIT
---

# Merge Branch

The user wants to merge another branch into the current one. Don't run `git merge` yet. Ask which branch, show what's coming in (including a dry-run conflict
check), flag anything risky, then wait.

## Step 1 — Ask which branch to merge

Before doing anything else, ask:

> Which branch would you like to merge into the current branch?
> **`main`** — or type a different branch name.

`main` is the default suggestion. If the user already named a branch in their message (e.g. "merge feature/auth"), skip this step and use that branch.

Once you have the source branch, proceed.

## Step 2 — Gather the facts, but don't merge yet

No `git merge`. No `git rebase`. Reading is fine — `git status`, `git log`, `git diff`, `git branch`, `git merge-tree` are how you gather what to show.

If you feel the urge to run `git merge` "just to see what happens," that's the signal to stop and show the preview instead.

Useful read-only commands:
- `git status` — confirm the working tree is clean before proposing anything
- `git fetch origin {branch}` — make sure your view of the source branch is current
- `git log HEAD..{branch} --oneline` — commits that would come in
- `git diff HEAD...{branch} --stat` — files that would change
- `git merge-base HEAD {branch}` — find the common ancestor

### Dry-run conflict detection

Before showing the preview, simulate the merge to surface conflicts without touching the working tree or any refs:

```bash
# Modern Git (2.38+) — uses the real merge strategy, most accurate
git merge-tree --write-tree HEAD {branch}

# Older Git — trivial merge mode, good for ~95% of cases
git merge-tree $(git merge-base HEAD {branch}) HEAD {branch}
```

Then inspect the output:

```bash
# All files touched on both branches
git merge-tree ... | grep 'changed in both'

# Actual conflict markers — the surrounding hunk shows what disagrees
git merge-tree ... | grep -n '<<<<<<< \|>>>>>>> \|======='
```

For each conflict hunk:
- Identify the file path (the block header above the hunk).
- Read enough surrounding context to understand both sides.
- Form a suggested resolution and a one-line rationale (e.g. "take theirs — it's a real bug fix; ours was whitespace cleanup").

Why this matters:
- **Reversible** — no working-tree changes, no `MERGE_HEAD`, no `git merge --abort` needed.
- **Shows the resolution shape** — both sides visible before committing to merge.
- **Safe to re-run** — cheap to repeat after a new fetch.

Caveats:
- Without `--write-tree`, the trivial mode can disagree slightly with a real merge on complex renames or recursive strategies. Mention this if the merge looks
unusual.
- A clean dry-run is a strong signal, not a guarantee. Real merges still run hooks, .gitattributes drivers, etc.

## Step 3 — What to deliver

A short message covering:

1. **Current branch and target** — confirm which branch is receiving the merge and which is being merged in. Avoid surprises.
2. **Commits coming in** — list the commits from the source branch not yet in the current branch (one line each). If there are more than 10, summarize ("14
commits, earliest from [date], latest: [subject]").
3. **Files that would change** — grouped if it helps (e.g., "source changes" vs "tests" vs "config"). Call out anything large or unusual.
4. **Conflict preview** — from the dry-run:
    - If clean: say so explicitly ("dry-run merges cleanly, no conflicts").
    - If conflicts exist: list every conflicted file. For each, show the conflicting hunk (or a tight summary) and your suggested resolution with rationale. The
  user should be able to approve the merge *and* the resolution plan in one go.
    - List files modified on both sides that auto-merged cleanly as a separate "worth a post-merge sanity read" note.
5. **Anything else worth flagging** — merge direction mistakes (wrong way round), uncommitted local changes that could get in the way, source branch that's
behind and might overwrite newer work, untracked files cluttering `git status`.
6. **Proposed merge approach** — standard `git merge` (creates a merge commit) is the default. If the history looks like it calls for `--ff-only` or
`--squash`, say so and why. Don't just run the alternative — propose it.

## Then wait

After showing the proposal, end every approval request with this exact line so the user always sees the required phrase:

> Reply `merge approved` to proceed.

Don't run anything until the user replies with the exact phrase `merge approved`.

Nothing else counts as approval. Not "yes," not "go ahead," not "do it," not "lgtm," not a thumbs up. If the user replies with anything other than `merge
approved`, treat it as feedback — apply any requested changes, re-show the updated state, and ask again (with the same `merge approved` prompt).

## Approval is scoped to what you showed

Approval covers the exact source branch, target branch, merge approach, **and any conflict resolutions you proposed** — nothing else. Things that invalidate
approval:

- New commits appearing on the source branch between your preview and now.
- Uncommitted local changes appearing in `git status` since you last showed it.
- Switching to a different merge approach (e.g., squash instead of merge commit).
- A real merge surfacing conflicts the dry-run did not predict — stop and re-show.
- Any other repo state change since you last showed the preview.

When in doubt, re-show. A second review costs the user one line of "yes"; a bad merge costs them a revert.

## Running the merge

Once `merge approved` is received:

1. Run `git status` first. If the working tree is dirty, stop — tell the user to stash or commit their changes before merging, and do not proceed.
2. Run `git merge {branch}` (or the agreed-upon variant).
3. If the merge exits cleanly, report success: what was merged, how many commits, whether a merge commit was created.
4. If there are conflicts:
    - If they match the dry-run prediction and you proposed resolutions that were approved, apply them, stage the files, and run `git merge --continue` (use
`GIT_EDITOR=true git merge --continue` to accept the default message non-interactively).
    - If conflicts appeared that the dry-run did not predict, stop. Do not resolve automatically. List the unexpected conflicts and ask the user how to proceed.

## Output

Inline in chat. No file needed.
