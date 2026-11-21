# Revamp plan: CCNY Fall 2021 calendar scraper

Date: 16 Nov 2026. Nothing here is started. Each slice waits for a go ahead.

## Phase map
| Phase | State | Evidence or gap |
|---|---|---|
| 0 Frame | In progress | This folder. Awaiting approval |
| 1 Requirements and risk | Not met | Terms and `robots.txt` unread. Snapshot provenance unknown |
| 2 Design | Light | One decision: snapshot first or live first |
| 3 Scaffold | Not met | No pinned environment, no test runner, no continuous integration |
| 4 Build | Met for Fall 2021 | 58 rows reproduced today from the snapshot |
| 5 Harden | Not met | Parser edge cases crash |
| 6 Deploy and operate | Skipped at Tier A | To be recorded in docs/decisions when reached |
| 7 Validate | Partly | Asserts in cell 13 pass. No fresh clone run |
| 8 Maintain | Not met | |

## Proposed restructuring
```
README.md  LICENSE  PROJECT_BRIEF.md  requirements.txt (pinned)
data/raw/fall_2021_calendar.html
data/raw/SOURCES.md            (URL, saved date, method, SHA 256)
data/out/fall_2021_calendar.csv
src/calendar_scraper/parse.py  (extract_rows, expand_dates with year passed in)
src/calendar_scraper/run.py    (snapshot in, CSV and a small run record out)
tests/test_expand_dates.py  tests/test_snapshot.py
fall_2021_calendar.ipynb       (imports from src, keeps the teaching notes)
docs/
```
* Remove the empty last cell and the duplicate pandas import.
* Replace the stale "Planned commits" README section with real run steps.
* The live fetch becomes an opt in flag, off by default, and only if the open terms question is answered yes.
* A repository rename (the name carries the term) is your call and is not proposed now.

## First slices, in order
1. Provenance: `data/raw/SOURCES.md` with your answer on how the snapshot was saved, plus its SHA 256. Test: the checksum in the file matches the file.
2. Move `expand_dates` to `src/calendar_scraper/parse.py` unchanged, with tests that pin today's behavior for the three label shapes in cell 10. Test: pytest passes and the notebook still gives 58 rows. Needs your yes to add pytest.
3. Make the snapshot the default input and write `data/out/fall_2021_calendar.csv` with a run record (input checksum, row counts 36 and 58). Test: two runs give the same output checksum with the network off.
4. Harden the parser: cross month and cross year ranges, explicit year, year as a parameter, clear error messages naming the bad label. Test: the four failing labels in docs/current-state.md either parse or raise a named error.
5. Pin `requirements.txt`, add LICENSE of your choice, add a continuous integration run of the tests.
6. Optional, option B: add one more hand saved term and make the tests pass for it.
