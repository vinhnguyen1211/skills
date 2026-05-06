---
name: exploratory-work
description: Use this skill whenever the user asks you to investigate, scope, scout, survey, or "look into" something before implementing. Trigger on phrases like "how hard would it be to...", "where would I even start with...", "I want to add X but I'm not sure what it touches", "do a spike", "do some recon", "don't write code yet", or any request where the scope is unclear. The job is to map the territory and surface unknowns — NOT to start implementing.
license: MIT
---

# Exploratory Work

When scope is unclear, the worst move is to start implementing. Map the territory first.

## Don't write code yet

No edits to source files. No new modules. No "while I'm in here" refactors. Reading code is fine. Pseudocode or small inline snippets to illustrate an approach is fine.

If you feel the urge to start building, that's the signal to write down what you'd build instead. It goes in the report.

## What to deliver

A short written report covering:

1. **Goal restated** — one or two sentences of what the user actually wants. Catches misalignment early.
2. **What exists today** — the files, services, or prior art that are relevant. Be concrete.
3. **A few approaches with tradeoffs** — short name, what each gives up, risk and blast radius. Include a "minimal patch" or "do nothing" option when relevant.
4. **Unknowns and assumptions** — flag unknowns, assumptions, and questions you'd need answered before committing to a direction.
5. **Recommendation** — pick one, say why, and state what would change your mind.
6. **Suggested next step** — usually a question for the user, or the smallest spike that would resolve the biggest unknown.

## How to ask

Ask the questions one at a time.

If a question can be answered by exploring the codebase, explore the codebase instead.

## Stop and ask at real forks

If a question would meaningfully change the recommendation, ask the user. Examples: "should this live in service A or B?", "follow the older pattern (8 places) or the newer one (2)?". Don't ask about minor preferences.

## Output

Default to a markdown report — inline for small investigations, a file (`exploration-<topic>.md`) for larger ones so the user can reference it later.
