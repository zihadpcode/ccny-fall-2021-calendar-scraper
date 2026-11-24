# Revamp plan: CCNY Fall 2021 calendar scraper

Date: 16 Nov 2026. Updated 23 Nov 2026 to match docs/design.md (Phase 2). Nothing here is started. Each slice waits for a go ahead.

## Phase map
| Phase | State | Evidence or gap |
|---|---|---|
| 0 Frame | Approved 19 Nov 2026 | docs/brief.md |
| 1 Requirements and risk | Approved 22 Nov 2026 | docs/requirements.md, docs/risks.md, docs/acceptance-tests.md |
| 2 Design | In progress, awaiting approval | docs/design.md, decisions 0003 to 0009 |
| 3 Scaffold | Not met | No pinned environment, no test runner, no continuous integration |
| 4 Build | Met for Fall 2021 in memory | 58 rows reproduced from the snapshot. No command, no CSV |
| 5 Harden | Not met | |
| 6 Deploy and operate | Does not apply at Tier A | Decision 0009 |
| 7 Validate | Partly | Asserts in cell 13 pass. No fresh clone run |
| 8 Maintain | Not met | |

## Target layout (decisions 0003, 0005, 0007)
```
README.md  LICENSE  PROJECT_BRIEF.md
requirements.txt  requirements_dev.txt      (exact pins)
calendar_scraper/__init__.py  io.py  parse.py  live.py  __main__.py
data/raw/fall_2021_calendar_table.html      (heading and table only)
data/raw/fall_2021_calendar_table.html.sha256
data/raw/SOURCES.md
data/out/fall_2021_calendar.csv  data/out/fall_2021_calendar.run.json
tests/conftest.py  tests/test_parse.py  tests/test_run.py  tests/test_notebook.py
.github/workflows/tests.yml
fall_2021_calendar.ipynb                    (imports the package, USE_LIVE = False)
docs/
```
This replaces the Phase 0 `src/` sketch (decision 0005). A repository rename stays your call and is not proposed.

Outside action means something that needs your yes and leaves the machine or changes shared state: a network install, a push, a remote change, a deletion of a tracked file.

## Phase 3 slices: scaffold and walking skeleton
| Slice | Work | Acceptance tests | Outside action |
|---|---|---|---|
| 3.1 | Exact pins from the working environment into `requirements.txt`; `requirements_dev.txt` adds pytest; fresh virtual environment from them | | Yes: network install of pytest and the pinned set |
| 3.2 | Walking skeleton: `calendar_scraper` package from cells 6 to 13 with `assert` turned into named errors; checksum sidecar for the current snapshot; CSV and run record writer; `python -m calendar_scraper` | AT1, AT7, AT10 | No |
| 3.3 | `tests/conftest.py` socket guard and `tests/test_run.py` (58 rows offline, two runs byte equal) | AT1, AT4, AT9, AT15 | No |
| 3.4 | `.github/workflows/tests.yml`: Python 3.12, pinned install, pytest, rebuild and compare committed outputs | AT12 | Yes: push of the branch to run it |
| 3.5 | Rollback proof: revert one commit locally and rerun the tests | | No |

## Phase 4 slices, in order
| Slice | Work | Acceptance tests | Outside action |
|---|---|---|---|
| 4.1 | Provenance first, as it tests the riskiest assumption: `data/raw/SOURCES.md` with your answer on how the snapshot was saved, three rows checked by eye in your browser; checksum mismatch stops the run | AT3, AT14 | No; needs your answer |
| 4.2 | Quality checks complete: header row check, weekday on every day, exit codes and one line errors | AT1, AT5 partly | No |
| 4.3 | Notebook imports the package, `USE_LIVE = False`, live path in `live.py` behind `--live` and failing closed; notebook test with nbclient; hand merge of the heading edit (R12) | AT2, AT8, AT9 | No. The live path is written and tested with a mock only; no request is sent |
| 4.4 | Label grammar (decision 0006): cross month, cross year, explicit year, short month names, named errors; at least 8 label cases | AT5 | No |
| 4.5 | Trim to heading and table (decision 0003); new sidecar; README with offline run steps, source and CUNY copyright note; LICENSE of your choice | AT13, AT16, AT3 again | Yes: deleting the tracked full page file, and the LICENSE choice |
| 4.6 | Optional, option B: one more hand saved term | AT6 | You save the page in a browser |

Phase 5 then covers the hardening passes; most parser work moved into 4.4 because the success outcome on 30 Nov 2026 needs the 8 label cases.
