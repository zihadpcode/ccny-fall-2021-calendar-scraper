# Current state audit: CCNY Fall 2021 calendar scraper

Date: 16 Nov 2026. Base: `origin/main` at 8100a2c. Cell numbers count all cells from 0.

The user's checkout has one uncommitted change (a heading edit in notebook cell 0). It is not in this worktree and I left it alone.

## What exists
* `fall_2021_calendar.ipynb`: 17 cells, 9 executed code cells, one empty trailing cell.
* `data/fall_2021_calendar.html`: 158,779 bytes, one table of 37 rows.
* `README.md`, `requirements.txt` (3 unpinned names), `.gitignore`.

## What I ran
* Command: `~/Desktop/ccny-fall-2021-calendar-scraper/.venv/bin/python <scratch>/ccny_check.py ~/projects/revamp/ccny-calendar-scraper`. The script executes the notebook's own code cells 7 to 13 against the snapshot and skips the network call in cell 6.
* Result: `All checks passed. Original calendar entries: 36. Final DataFrame shape: (58, 2). First date: 2021-08-01. Last date: 2022-01-01.`
* I then fed `expand_dates` four labels that the 2021 table does not contain. All four raise:
  * `December 24 - January 1` gives `ValueError: invalid literal for int()`.
  * `August 25 - 31, 2021` gives the same error.
  * `Feb 3` gives `ValueError: time data ... does not match format`.
  * `September 30 - 31` gives `ValueError: day is out of range for month`.
* One live request, described in docs/brief.md: HTTP 403, bot challenge.
* I did not run the notebook itself, because cell 6 would make a second live request.

## What works, bounded by the checks above
* The parse of the saved snapshot gives the documented 58 rows, and the weekday printed on the page agrees with the computed weekday for the first and last day of every entry (cell 11 asserts).
* The heading check in cell 6 fails loudly if the HTML is not the Fall 2021 page.

## Against the data pipeline addendum
| Item | State | Evidence |
|---|---|---|
| Reruns give the same result | Only by accident | Cell 6 tries the live page first. Today it is refused, so the snapshot is always used |
| Schema validation at ingestion | Partly | Heading check in cell 6, three cell check in cell 8. No header row check |
| Quality checks with counts | Present but hard coded | Cell 13 asserts 36 and 58 |
| Which input made which output | Missing | `source_used` is printed only. No output file, no checksum, no date |
| Backfill plan | Missing | Not needed for one fixed term |
| Rule for late data | Missing | The page can change after capture. No stated rule |

## Defects and gaps
1. `fall_2021_calendar.ipynb` cell 6: if the live page ever answers 200 with another term, the heading check raises and the notebook stops. The fallback only covers `requests.RequestException`.
2. Cell 6: no client name header, no `robots.txt` check, no stated terms review.
3. Cell 9 `expand_dates`: a range must stay inside one month, since the end is built with `start.replace(day=...)`. Cross month ranges crash.
4. Cell 9: the year 2021 is hard coded in two places. A range with an explicit year crashes.
5. Cell 9: any hyphen marks a range, so a label with a hyphen for another reason is misread.
6. Cell 11: only the first and last weekday of a range are checked, and with `assert`, which vanishes under `python -O`.
7. Cell 7: `tables[0]` is taken without checking the header row text.
8. Cell 8 imports pandas a second time. Cells 8, 11, 12, 14 use `display`, which exists only inside Jupyter.
9. Nothing is written to disk. The result lives only in notebook output.
10. `requirements.txt` lines 1 to 3: no versions, no lockfile.
11. `README.md` lines 24 to 30: the "Planned commits" list is stale, since the work is finished.
12. No LICENSE, no tests outside the notebook, no continuous integration.
13. Snapshot provenance is unrecorded. See the brief.

## Sections 6 to 11 and 13
* Security (6): no secrets found in the tracked files. The one trust boundary is the HTML, which is parsed as text only. Supply chain: three unpinned packages.
* Performance (7), hosting (8), cost (10): not applicable at Tier A.
* Reliability (9): the dependency failure case (site down or refusing) is handled by the fallback. Bad input raises instead of being set aside, which is fine at this size. The year rollover is a real time bug class (items 3 and 4).
* Process (11): stale README text, item 11.
* Definition of done (13): fails on tests in continuous integration, LICENSE, PROJECT_BRIEF and docs.

## Largest gaps, ranked by damage times likelihood
1. Unrecorded snapshot provenance. The whole result rests on it.
2. Live first design: results depend on what a remote site answers, and the site refuses scripts.
3. Parser brittleness (items 3 to 5). Certain to bite if generalized, harmless for 2021.
4. No output file or lineage.
5. Unpinned environment.
