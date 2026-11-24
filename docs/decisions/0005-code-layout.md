# 0005: Code layout

Date: 23 Nov 2026. Status: proposed.

## Context
All logic lives in notebook cells. F1 needs one command and a CSV (AT1 fails), F5 needs tests on labels (AT5 fails), and `display` exists only in Jupyter (R9).

## Options
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Keep notebook only | None | AT1 and AT5 cannot pass; tests would need to run the notebook | Yes | Never, given AT1 and AT5 |
| B. One script file beside the notebook | 1 hour | Notebook and script drift apart | Yes | No notebook is needed |
| C. Flat package `calendar_scraper/` at the root (`io.py`, `parse.py`, `live.py`, `__main__.py`); notebook imports it | 2 hours | Notebook must run from the root folder | Yes | Recommended |
| D. `src/calendar_scraper` with `pyproject.toml` and an editable install | 3 hours | Build backend and install step; more to explain | Yes | The code becomes a published package |

## Decision
C. `python -m calendar_scraper` works from a fresh clone with no install step. The notebook keeps its teaching text and `display` calls and imports the functions. This replaces the `src/` layout sketched in docs/revamp-plan.md in Phase 0.

## Keep or change
Change, because AT1 and AT5 fail on the current layout.

## Consequences
The heading edit in the original checkout (decision 0001) needs a hand merge when the notebook cells change (R12).
