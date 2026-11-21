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
Each line below is a default, user to confirm.

| # | Open question | Default answer used in Phase 1 | Status |
|---|---|---|---|
| 7 | How and when was `data/fall_2021_calendar.html` saved? | Unknown. Record it as unknown in a provenance file, together with its SHA 256 `d12ed79993f04b6d94fe086ee08ada09a5df6a97aa21347a4a84255b58aefce2` (computed 19 Nov 2026). The file entered git in commit c2a0e44 | default, user to confirm |
| 8 | What does the live URL show in a browser today? | A later fall term, not 2021 (assumption 3). Not checked, and no scripted request will be made to find out | default, user to confirm |
| 9 | Does the assignment require a live `requests` call? | No. The saved snapshot is the stated input. Any live fetch becomes an opt in flag, off by default (decision 0002) | default, user to confirm |
| 10 | Do CUNY terms or `robots.txt` allow automated access? | Treat as no permission. Web search on 19 Nov 2026 found no CCNY or CUNY text that grants automated access, and `robots.txt` was not read (see docs/requirements.md section 4) | default, user to confirm |
| 11 | Option A only, or A then B? | A now. B later, only after one more hand saved term exists | default, user to confirm |
| 12 | Hours per week, deadline, monthly budget (section 1 of PROJECT_BRIEF.md) | Deadline 30 Nov 2026 from the success outcome. Budget 0 dollars a month. Hours unknown | default, user to confirm |
