# Assumptions: CCNY Fall 2021 calendar scraper

| # | Assumption | Basis | How to check |
|---|---|---|---|
| 1 | This is a guided course exercise | README wording, "assignment" in cell 5 | Ask the author |
| 2 | The snapshot is the real Fall 2021 page | Heading text and weekday agreement for every entry | Author states how it was saved |
| 3 | The live URL now shows a later fall term | The path has no year | Author opens it in a browser |
| 4 | The 403 is a bot check, not a removed page | Response title "Just a moment..." | Same browser check |
| 5 | Labels without a year belong to 2021 | Notebook cell 5 date policy, weekday asserts pass | Already tested by cell 11 |
| 6 | It runs on the author's laptop only | No deploy files | Ask the author |

## Defaults taken for the Phase 0 open questions (Phase 0 approved 19 Nov 2026)
Each line below was a default. All were accepted as written, with the Phase 1 approval: accepted 2026-11-22.

| # | Open question | Default answer used in Phase 1 | Status |
|---|---|---|---|
| 7 | How and when was `data/fall_2021_calendar.html` saved? | Unknown. Record it as unknown in a provenance file, together with its SHA 256 `d12ed79993f04b6d94fe086ee08ada09a5df6a97aa21347a4a84255b58aefce2` (computed 19 Nov 2026). The file entered git in commit c2a0e44 | accepted 2026-11-22 |
| 8 | What does the live URL show in a browser today? | A later fall term, not 2021 (assumption 3). Not checked, and no scripted request will be made to find out | accepted 2026-11-22 |
| 9 | Does the assignment require a live `requests` call? | No. The saved snapshot is the stated input. Any live fetch becomes an opt in flag, off by default (decision 0002) | accepted 2026-11-22 |
| 10 | Do CUNY terms or `robots.txt` allow automated access? | Treat as no permission. Web search on 19 Nov 2026 found no CCNY or CUNY text that grants automated access, and `robots.txt` was not read (see docs/requirements.md section 4) | accepted 2026-11-22 |
| 11 | Option A only, or A then B? | A now. B later, only after one more hand saved term exists | accepted 2026-11-22 |
| 12 | Hours per week, deadline, monthly budget (section 1 of PROJECT_BRIEF.md) | Deadline 30 Nov 2026 from the success outcome. Budget 0 dollars a month. Hours unknown | accepted 2026-11-22 |

## Phase 1 targets taken as defaults
The user approved the Phase 1 documents with every default as written.

| # | Default | Source | Status |
|---|---|---|---|
| 13 | F1 p95 2 s or less, offline | docs/requirements.md section 2 | accepted 2026-11-22 |
| 14 | F2 notebook run all 10 s or less, warm kernel | Same | accepted 2026-11-22 |
| 15 | Input 1 MB or less, at most 10 terms under option B | Same | accepted 2026-11-22 |
| 16 | Fresh clone to the F1 result in 15 minutes or less | Same | accepted 2026-11-22 |
| 17 | Decision 0002 option B: live request behind a flag, off by default | docs/decisions/0002 | accepted 2026-11-22 |

## Phase 2 assumptions (23 Nov 2026), user to confirm
| # | Assumption | Basis | How to check |
|---|---|---|---|
| 18 | The repository on GitHub is public | Stated by the user for this phase; not checked from here | Repository settings page |
| 19 | The grader accepts a notebook that imports from `calendar_scraper/` | Not known | Ask the course or the grader |
| 20 | A CI run takes 1 to 2 minutes | Estimate, not measured | First CI run in Phase 3 |
