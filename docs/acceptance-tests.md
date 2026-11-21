# Acceptance tests: CCNY Fall 2021 calendar scraper

Date: 20 Nov 2026. Status values: passes today (with evidence), fails today, not yet testable. Evidence runs were on 19 Nov 2026, against the snapshot only, from scripts in the session scratch folder, with the notebook's live request skipped.

## Per user flow
| # | Flow | Test in plain words | How checked | Status |
|---|---|---|---|---|
| AT1 | F1 | With the network off, one command turns the snapshot into a CSV of 58 rows from 36 entries, first date 2021 08 01, last 2022 01 01, plus a run record | pytest runs the command in a temporary folder and reads the CSV | Fails today: no command and no CSV. The notebook code gives the 58 rows in memory ("All checks passed") |
| AT2 | F2 | Running every notebook cell from the top makes no network request and ends with "All checks passed" | Run the notebook with outbound sockets blocked | Fails today: cell 6 calls the live page first (line 140) |
| AT3 | F3 | The SHA 256 in the provenance file matches the snapshot; a changed byte stops the run with a named error | pytest compares the hash, and again on a copy with one byte changed | Fails today: no provenance file. Current hash `d12ed799...aefce2` |
| AT4 | F4 | Two runs produce output files with the same SHA 256 | Run twice, compare hashes | Not yet testable: no output file. Proxy passes: 30 in memory runs gave one CSV text |
| AT5 | F5 | `December 24 - January 1`, `August 25 - 31, 2021` and `Feb 3` parse to the right dates, and `September 30 - 31` raises an error that names the label | pytest cases, at least 8 label cases in all | Fails today: all four raise bare `ValueError`; two messages do not name the full label |
| AT6 | F6 | A second hand saved term parses with its year passed in and its own counts | pytest on the second snapshot | Not yet testable: no second snapshot (option B, gated) |

## Per quality number
| # | Quality | Test in plain words | How checked | Status |
|---|---|---|---|---|
| AT7 | p95 time, F1 | 20 runs of the command, the 95th percentile is 2 s or less | Timed loop on the M2 laptop | Not yet testable. Proxy passes: 0.30 to 0.33 s whole process, 5 runs |
| AT8 | p95 time, F2 | Notebook run all finishes in 10 s or less with a warm kernel | Timed run | Not yet testable: needs AT2 first |
| AT9 | Offline (replaces availability) | Same as AT2, plus AT1 with sockets blocked | Socket guard in the tests | Fails today, as AT2 |
| AT10 | Data volume | Input is 1 MB or less; counts 36 and 58 hold | Size and count check in the tests | Passes today: 158,779 bytes, 36 entries, 58 rows |
| AT11 | Concurrency | Not applicable; one user, one run | None | Recorded only |
| AT12 | Cost | No paid service, no key, no network in the default path | Review of requirements and code | Passes today for cost: no service or key found. Network part fails, as AT2 |
| AT13 | Recovery | A fresh clone reaches the AT1 result in 15 minutes from the README | Timed fresh clone, packages from local cache | Fails today: README has no offline run step |
| AT14 | Data loss | The snapshot exists in git and on the remote with a recorded checksum | `git log -- data/`, AT3 | Fails today: in git (c2a0e44) but no recorded checksum |
| AT15 | Determinism | Same as AT4 | Same as AT4 | Not yet testable |
| AT16 | Terms | README names the source page, says the content is CUNY copyright, and says no automated request is made by default | Read the README | Fails today: README only gives the URL |
