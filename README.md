# vinhnguyen1211-skills

Claude Code skills by vinhnguyen1211. These skills enforce a deliberate, preview-first workflow — Claude shows you what it's about to do and waits for explicit approval before taking any irreversible action.

## Installation

Add the marketplace in Claude Code:

```
/plugin marketplace add vinhnguyen1211/skills
```

Then open `/plugin`, go to the **Discover** tab, and install the skills you want.

## Available skills

### [exploratory-work](skills/exploratory-work/SKILL.md)

**Goal:** Understand the scope and surface unknowns before writing a single line of code.

Triggered when you say things like "how hard would it be to…", "where would I even start with…", "do a spike", or "don't write code yet." Claude reads the codebase, maps what exists, identifies risks, and writes a structured report — without touching any source files.

**Expected output:** A written report with six sections: goal restated, what exists today, a few approaches with tradeoffs, unknowns and assumptions, a recommendation, and the suggested next step. For small investigations this appears inline; for larger ones it's saved to `exploration-<topic>.md`.

---

### [review-before-commit](skills/review-before-commit/SKILL.md)

**Goal:** Never commit without seeing exactly what's going in and why.

Triggered when you say things like "commit this", "let's commit", "make a commit", or "ready to commit." Claude runs `git status` and `git diff` to gather the current state, then proposes which files to stage and a real commit message — without running `git add` or `git commit`.

**Expected output:** A preview listing files to be staged (grouped by type), files intentionally left out, a draft commit message with subject and bullet-point body, and any flags (secrets, debug prints, unrelated changes mixed in). Claude waits for you to reply `commit approved` before running anything. Amend flows and multi-commit sessions are also handled — each step requires its own approval.

---

### [merge-branch](skills/merge-branch/SKILL.md)

**Goal:** Never merge blindly — see exactly what's coming in before the history changes.

Triggered when you say things like "merge main", "merge into current branch", "bring in changes from X", or "sync with main." Claude identifies the source branch, then gathers facts with read-only git commands — without running `git merge`.

**Expected output:** A merge preview showing the current and source branches, incoming commits (listed or summarized if more than 10), files that would change, anything worth flagging (potential conflicts, wrong-direction merges, dirty working tree), and the proposed merge approach (standard merge commit, `--ff-only`, or `--squash` with reasoning). Claude waits for you to reply `merge approved` before running the merge.

---

### [ketch](skills/ketch/SKILL.md)

**Goal:** Fast, stateless web search, scraping, code search, and library doc lookups via the `ketch` CLI.

Triggered when you say "use ketch", ask to search the web, scrape a URL, look up OSS code examples, or fetch library docs. Claude runs the appropriate `ketch` subcommand (`search`, `scrape`, `crawl`, `code`, or `docs`) and returns structured results.

**Expected output:** Results inline in chat — search snippets, scraped markdown, code matches, or library documentation — depending on the subcommand used.
