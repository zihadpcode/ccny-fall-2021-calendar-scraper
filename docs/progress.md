# Progress: CCNY Fall 2021 calendar scraper

| Phase | State | Date | Note |
|---|---|---|---|
| 0 Frame | Approved | 19 Nov 2026 | Brief approved by the user. Open questions answered with the brief's defaults, logged in docs/assumptions.md rows 7 to 12 |
| 1 Requirements and risk | Approved | 22 Nov 2026 | Approved by the user with every default as written. Defaults marked accepted 2026-11-22 in docs/assumptions.md. Decision 0002 accepted |
| 2 Design | In progress, awaiting approval | 23 Nov 2026 | Documents only. No code changed |
| 3 to 8 | Not started | | |

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

Exit gate: requirements, risk register and acceptance tests approved. State: approved 22 Nov 2026.

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

## Phase 2 steps
| Step | File | Status |
|---|---|---|
| 0. Mark Phase 1 approved, defaults accepted | docs/progress.md, docs/assumptions.md, decision 0002 | Done |
| 1a. Decisions with options tables | docs/design.md section 1 | Done, 8 decisions |
| 1b. Data model and change plan | docs/design.md section 2 | Done |
| 1c. Interface contracts and error cases | docs/design.md section 3 | Done |
| 1d. p95 budget split | docs/design.md section 4 | Done for F1; no request path |
| 1e. Environments and configuration | docs/design.md section 5 | Done |
| 1f. Cost now and at ten times, first limit | docs/design.md section 6 | Done, 0 dollars both |
| 1g. Architecture diagram | docs/design.md section 7 | Done, Mermaid |
| 1h. Walking skeleton plan | docs/design.md section 8 | Done |
| 2. Decision notes | docs/decisions/0003 to 0009 | Done, proposed |
| 3. Risks and plan | docs/risks.md (R14 to R17 added), docs/revamp-plan.md | Done |
| 4. Phase report | this file | Done |

Exit gate: design approved, with every major decision recorded. State: awaiting approval.

## Phase 2 report

### What was decided
* D1 input: snapshot only by default; `--live` fails closed and never feeds outputs (decision 0002, accepted).
* D2 public repository: change to heading and table only; full page stays at commit c2a0e44 (0003).
* D3 runtime: keep Python 3.12, requests, Beautiful Soup, pandas; exact pins; pytest added (0004).
* D4 layout: flat `calendar_scraper/` package, notebook imports it (0005).
* D5 parser: keep hand written, explicit grammar, year passed in (0006).
* D6 output: CSV plus JSON run record, committed, no timestamps; checksum sidecar (0007).
* D7 tests: pytest, socket guard, nbclient notebook run, GitHub Actions (0008).
* D8 hosting, authentication, background work: do not apply (0009).

### How it was checked
* On 23 Nov 2026, with no network, from a script in the session scratch folder (system Python 3.12.1, pandas 3.0.3, Beautiful Soup 4.14.3): a trimmed copy of heading and table (9,354 bytes, 0 scripts) gave the same 36 entries, 58 rows and byte equal CSV text as the full page (158,779 bytes, 32 scripts). Parse p95 0.006 s trimmed, 0.048 s full, 30 runs each. Importing pandas and Beautiful Soup took 0.28 to 0.42 s over 5 runs.
* Package versions of the original environment were read from its folder listing, not run.
* GitHub Actions pricing by web search of GitHub Docs on 23 Nov 2026: standard runners free on public repositories; private repositories on GitHub Free get 12 dollars of compute at 0.006 dollars a Linux minute.
* No request went to the CCNY site; `robots.txt` was not fetched. `git status` shows only files under `docs/` changed or added. No code, data or notebook file changed.

### What remains uncertain
* Whether the repository is public was taken from your statement, not checked here.
* How the snapshot was saved, and whether it matches the page you saw (R3).
* Whether the grader accepts a notebook that imports a package (R16).
* CI run time and the notebook test's run time (R17); not measured.
* Write time for the CSV and run record; not measured, budgeted at 0.1 s.

### Five questions for you
1. Decision 0003 removes the full page from the working tree but not from history. What does that protect against, and what does it not?
2. With `--live` on and the site answering 200 with Fall 2026, what does the design do, and why are outputs still built from the snapshot?
3. Why does the run record leave out timestamps and package versions, and what would break if it kept them?
4. Which existing notebook cells become the walking skeleton, and which part of it is new code?
5. Is adding pytest, and pushing a workflow file to the public repository, acceptable to you?

## Next step
Approve or correct docs/design.md and decisions 0003 to 0009. Then Phase 3 (Scaffold), starting with slice 3.1, which needs your yes for a network install.
