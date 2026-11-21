# 0001: Phase 0 base, worktree and tier

Date: 16 Nov 2026. Status: proposed.

## Context
The checkout at `~/Desktop/ccny-fall-2021-calendar-scraper` is on `main`, level with `origin/main`, with one uncommitted edit to the notebook. There are no other branches.

## Decision
* Work in a worktree at `~/projects/revamp/ccny-calendar-scraper` on branch `revamp/phase-0`, based on `origin/main` at 8100a2c.
* The uncommitted edit stays where it is and is not part of this work.
* Tier A.
* Exactly one request was made to the live page. No further scripted requests until the terms question is answered.
* Phase 0 writes documents only. No commits.

## Options considered
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| Worktree from origin/main | None | None found | Yes | Chosen |
| Base on the working copy with its edit | None | Mixes in unfinished work | Yes | The author asks for it |
| Edit in place | None | Touches uncommitted work | No | Never |

## Consequences
The heading edit in the original checkout will need a merge by hand if the notebook is restructured later. It is a one line change.
