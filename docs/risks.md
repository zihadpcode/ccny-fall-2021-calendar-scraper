# Risks: CCNY Fall 2021 calendar scraper

Rebuilt 20 Nov 2026 from master prompt sections 6 to 11. Phase 0 risks are merged in; their old numbers are in the last column. Likelihood and damage: Low, Medium, High.

| # | Section | Risk | Likelihood | Damage | Mitigation | Phase | Was |
|---|---|---|---|---|---|---|---|
| R1 | 6 Security | Every notebook run sends an automated request to a site that refuses scripts; terms unclear, address may be blocked | High (cell 6, line 140, runs each time) | Medium | Snapshot first, live fetch behind an opt in flag that is off by default (decision 0002). Author reads `robots.txt` in a browser | 4, slice 3 | 2 |
| R2 | 9 Reliability | Parser misreads or crashes on label shapes not seen in 2021: cross month, cross year, explicit year, short month names, stray hyphen | High if generalized, Low for 2021 | Medium | Tests per label shape, year passed in, named errors | 5 | 4 |
| R3 | 9 Reliability | The snapshot is not a faithful copy of the Fall 2021 page | Low | High | Provenance file with SHA 256 and how it was saved; spot check three rows by eye | 4, slice 1 | 1 |
| R4 | 9 Reliability | The snapshot is lost or changed: it is the only copy of the input | Low | High | Keep it in git and on the remote; checksum test | 4, slice 1 | new |
| R5 | 9 Reliability | Live page changes term and the notebook stops at the heading check | High | Low | Snapshot as the default input | 4, slice 3 | 3 |
| R6 | 11 Process | Stale README ("Planned commits", Jupyter setup that leads to a live request) | High | Low | Replace with offline run steps; fresh clone test at phase close | 7 | new |
| R7 | 9 Reliability | Checks are `assert` statements, which vanish under `python -O` | Low | Medium | Explicit checks that raise named errors | 5 | new |
| R8 | 6 Security | Unpinned packages change behaviour or bring in a bad release | Medium | Low | Pin versions, ideally with hashes; needs your yes | 3 | 5 |
| R9 | 8 Hosting | Works in one environment, fails in another: `display` exists only in Jupyter; the system Python here lacks numpy unless the user site is on | Medium | Low | Code in `src/` without `display`; one pinned virtual environment | 3 | new |
| R10 | 6 Security | Snapshot carries CUNY copyright content and third party scripts in a repository of unknown visibility | Low | Low to Medium | Source and copyright note in README; option to keep only the table, after your yes | 7 | 7 |
| R11 | 11 Process | Scope creep into a general CUNY scraper | Medium | Medium | Out of scope list; option B gated on one more saved term | 0 | 6 |
| R12 | 11 Process | The heading edit in the original checkout needs a hand merge after restructuring | Medium | Low | Note in decision 0001; merge at the slice that moves cells | 4 | new |
| R13 | 11 Process | Code the author cannot explain after the move to `src/` | Low | Medium | Five questions at each phase close | Each | new |

Not applicable at Tier A: section 7 performance (0.3 s measured against a 2 s target), section 10 cost (no paid service, ceiling 0 dollars). Recorded so a later change in tier reopens them.
