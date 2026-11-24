# Risks: CCNY Fall 2021 calendar scraper

Rebuilt 20 Nov 2026 from master prompt sections 6 to 11. Updated 23 Nov 2026 for the Phase 2 design: mitigations now name the decision and slice. Phase 0 risks are merged in; their old numbers are in the last column. Likelihood and damage: Low, Medium, High.

| # | Section | Risk | Likelihood | Damage | Mitigation | Phase | Was |
|---|---|---|---|---|---|---|---|
| R1 | 6 Security | Every notebook run sends an automated request to a site that refuses scripts; terms unclear, address may be blocked | High (cell 6, line 140, runs each time) | Medium | Snapshot first, live fetch behind `--live`, off by default, fails closed, socket guard in tests and CI (decisions 0002, 0008). Author reads `robots.txt` in a browser | 4, slice 4.3 | 2 |
| R2 | 9 Reliability | Parser misreads or crashes on label shapes not seen in 2021: cross month, cross year, explicit year, short month names, stray hyphen | High if generalized, Low for 2021 | Medium | Explicit label grammar, year passed in, named errors, weekday check on every day (decision 0006) | 4, slice 4.4 | 4 |
| R3 | 9 Reliability | The snapshot is not a faithful copy of the Fall 2021 page | Low | High | `data/raw/SOURCES.md` with both SHA 256 values and how it was saved; spot check three rows by eye (decision 0007) | 4, slice 4.1 | 1 |
| R4 | 9 Reliability | The snapshot is lost or changed: it is the only copy of the input | Low | High | Keep it in git and on the remote; checksum sidecar checked before every parse (decision 0007). After trimming, the full page stays at commit c2a0e44 | 4, slices 4.1 and 4.5 | new |
| R5 | 9 Reliability | Live page changes term and the notebook stops at the heading check | High | Low | Snapshot as the default input; with `--live`, outputs still come from the snapshot | 4, slice 4.3 | 3 |
| R6 | 11 Process | Stale README ("Planned commits", Jupyter setup that leads to a live request) | High | Low | Replace with offline run steps; fresh clone test at phase close | 7 | new |
| R7 | 9 Reliability | Checks are `assert` statements, which vanish under `python -O` | Low | Medium | Explicit checks that raise named errors (design section 3) | 3, walking skeleton | new |
| R8 | 6 Security | Unpinned packages change behaviour or bring in a bad release | Medium | Low | Exact pins in two requirements files; hashes later (decision 0004) | 3 | 5 |
| R9 | 8 Hosting | Works in one environment, fails in another: `display` exists only in Jupyter; the system Python here lacks numpy unless the user site is on | Medium | Low | Code in `calendar_scraper/` without `display`; one pinned environment; CI on Python 3.12 (decisions 0004, 0005) | 3 | new |
| R10 | 6 Security | Snapshot carries CUNY copyright content and third party scripts in a repository of unknown visibility | Low | Low to Medium | Keep only heading and table (decision 0003); source and copyright note in README | 4, slice 4.5 | 7 |
| R11 | 11 Process | Scope creep into a general CUNY scraper | Medium | Medium | Out of scope list; option B gated on one more saved term | 0 | 6 |
| R12 | 11 Process | The heading edit in the original checkout needs a hand merge after restructuring | Medium | Low | Merge by hand at slice 4.3, when the notebook imports the package | 4, slice 4.3 | new |
| R13 | 11 Process | Code the author cannot explain after the move to `calendar_scraper/` | Low | Medium | Five questions at each phase close | Each | new |
| R14 | 11 Process | The full page stays in public git history after trimming, so R10 is reduced, not closed | High | Low | Accept; history rewrite only on a CUNY request and your explicit yes (decision 0003 option D) | 4 | new |
| R15 | 9 Reliability | Committed outputs go stale after a parser change | Medium | Low | CI rebuilds and compares byte for byte (decision 0008) | 3 | new |
| R16 | 11 Process | Grader expects all code inside the notebook | Low | Medium | Ask before slice 4.3; fallback keeps the functions in cells and tests a copy (assumption 19) | 4 | new |
| R17 | 8 Hosting | Notebook test is slow or flaky because it starts a kernel | Medium | Low | Run it as one marked test; time it in Phase 3 | 3 | new |

Not applicable at Tier A: section 7 performance (0.3 s measured against a 2 s target; budget split in docs/design.md section 4), section 10 cost (no paid service, ceiling 0 dollars). Recorded so a later change in tier reopens them.
