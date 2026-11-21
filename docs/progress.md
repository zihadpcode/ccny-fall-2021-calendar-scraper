# Progress: CCNY Fall 2021 calendar scraper

| Phase | State | Date | Note |
|---|---|---|---|
| 0 Frame | Approved | 19 Nov 2026 | Brief approved by the user. Open questions answered with the brief's defaults, logged in docs/assumptions.md rows 7 to 12 |
| 1 Requirements and risk | In progress, awaiting approval | 20 Nov 2026 | Documents only. No code changed |
| 2 to 8 | Not started | | |

## Phase 0 record
* Worktree at `~/projects/revamp/ccny-calendar-scraper` from `origin/main` at 8100a2c, branch `revamp/phase-0`.
* Parse rerun against the snapshot: 36 entries, 58 rows, all notebook asserts pass.
* One live request on 16 Nov 2026: HTTP 403 bot challenge. None since.

## Phase 1 steps
| Step | File | Status |
|---|---|---|
| 0. Mark Phase 0 approved, log defaults | docs/progress.md, docs/assumptions.md | Done |
| 1a. User flows | docs/requirements.md | Done, 6 flows |
| 1b. Quality numbers | docs/requirements.md | Done; most are targets for you to confirm |
| 1c. Data classification | docs/requirements.md | Done |
| 1d. Licenses and terms | docs/requirements.md | Done, except `robots.txt` (not fetched) |
| 2. Threat model, short | docs/threat-model.md | Done |
| 3. Risk register | docs/risks.md | Done, 13 risks |
| 4. Acceptance tests | docs/acceptance-tests.md | Done, 16 tests |
| 5. Phase report | this file | Done |
| 6. Decision | docs/decisions/0002-no-live-request-by-default.md | Proposed |

Exit gate: requirements, risk register and acceptance tests approved. State: awaiting approval.

## Phase 1 report

### What was done
Requirements, threat model, risk register, acceptance tests and decision 0002 were written. Terms and licenses were checked on 19 Nov 2026.

### How it was checked
* Web search and reading of three policy pages (CCNY privacy policy, CCNY copy of the CUNY acceptable use policy, CUNY Law terms) and four PyPI pages. Links are in docs/requirements.md section 4.
* No request went to the live calendar page, and `robots.txt` was not fetched.
* Against the snapshot only, with the system Python 3.12.1 (pandas 3.0.3, Beautiful Soup 4.14.3), from scripts in the session scratch folder: notebook code cells pass all checks (36 entries, 58 rows); the four unknown labels still raise; 30 runs gave one CSV text; parse p95 0.041 s; whole process 0.30 to 0.33 s over 5 runs.
* `shasum -a 256` of the snapshot: `d12ed79993f04b6d94fe086ee08ada09a5df6a97aa21347a4a84255b58aefce2`.
* `git status` after the runs: only `PROJECT_BRIEF.md` and `docs/` are new. No cache files were written to the worktree.

### What remains uncertain
* What `robots.txt` says, and whether any CCNY terms page exists that search did not find.
* Whether the full page may sit in a public repository; the remote's visibility was not checked.
* How and when the snapshot was saved.
* The time, recovery and volume numbers are targets until you confirm them.

### Five questions for you
1. If the live request stays behind a flag that is off by default, does the assignment still count it as "download with requests", or should it go (decision 0002, option A)?
2. The snapshot is the only copy of the input. Where else, besides this repository and GitHub, would you keep it, and why does its checksum matter?
3. Abuse case 2: if the live page answered 200 with the Fall 2026 calendar, what would the notebook do today, and what should it do?
4. Why is a 2 second budget for F1 enough here, and which quality numbers did we mark as not applicable?
5. Is the GitHub repository public, and if so, are you comfortable keeping the full CCNY page in it, or only the calendar table?

## Next step
Approve or correct the Phase 1 documents and decision 0002. Then Phase 2 (Design).
