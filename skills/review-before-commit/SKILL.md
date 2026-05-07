---
name: review-before-commit
description: Use this skill whenever the user signals they want to commit changes — phrases like "commit this", "let's commit", "make a commit", "prepare a commit", "ready to commit". The job is to show the user what would be staged and propose a commit message, then wait for approval. Do NOT run git add or git commit until the user explicitly approves.
license: MIT
---

# Review Before Commit

The user wants to review what's about to be committed before any git command runs. Don't stage. Don't commit. Show and wait.

## Don't run git commands yet

No `git add`. No `git commit`. No `git push`. Reading is fine — `git status`, `git diff`, `git log` are how you gather what to show.

If you feel the urge to start staging "to make the next step easier," that's the signal to stop and show the user what you'd stage instead.

## What to deliver

A short message covering:

1. **Files that would be staged** — list them, grouped if it helps (e.g., "source changes" vs "tests" vs "config"). Note anything unusual: new files, deletions, renames, large diffs.
2. **Files left out** — anything modified or untracked that you're *not* proposing to stage, with a one-line reason. Catches accidental omissions.
3. **Proposed commit message** — a real draft, not a placeholder. Format:
   - Subject line: concise summary of the overall change.
   - Body (skip for tiny commits like a one-line fix or typo): bullet list with `- ` prefix, one bullet per change included in the commit, each bullet 1–2 sentences max.
4. **Anything worth flagging** — secrets that look committed by accident, debug prints left in, unrelated changes mixed in, files that probably shouldn't be tracked.

## Then wait

After showing the proposal, end every approval request with this exact line so the user always sees the required phrase:

> Reply `commit approved` to proceed.

Don't run anything until the user replies with the exact phrase `commit approved`.

Nothing else counts as approval. Not "yes," not "go ahead," not "do it," not "lgtm," not a thumbs up. If the user replies with anything other than `commit approved`, treat it as feedback or a new proposal — apply any requested changes, re-show the updated state, and ask again (with the same `commit approved` prompt).

This applies to every approval in this skill, including amend approvals.

## Approval is scoped to what you showed

Approval covers the exact files and message you presented, nothing else. If anything changes between approval and running `git commit`, the previous approval is stale and you must show the new state and ask again. Things that invalidate approval:

- Doing additional work that creates or modifies files (even small side tasks like adding to `.gitignore`, fixing a typo, updating a comment).
- Adding or removing files from the staged set.
- Changing the commit message.
- Any new modifications appearing in `git status` since you last showed it.

When in doubt, re-show. A second review costs the user one line of "yes"; a wrong commit costs them an amend or a revert.

An approval for a side task is not an approval for the commit. After completing any work that changes the repo state, re-show the updated staged set and message and ask again.

## If there's a previous commit this session

After reviewing the staged files, compare them to the previous commit you helped with in this session. If they look related (same files, same feature, same fix), ask whether to amend or make a new commit. Show a one-line reason for why you think they're related so the user can sanity-check.

If the user picks amend:

1. Decide whether the existing commit message still fits or needs revision. Small additions usually keep the message; meaningful new behavior usually warrants a revised message.
2. Show either "keep existing message" or the proposed revised message.
3. Wait for approval before running `git commit --amend`.

If the changes look unrelated to the previous commit, don't ask — just propose a new commit normally.

## Output

Inline in chat. No file needed.
