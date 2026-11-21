# Requirements: CCNY Fall 2021 calendar scraper

Date: 20 Nov 2026. Tier A. Status: awaiting approval.

Defaults from the Phase 0 open questions are in docs/assumptions.md, rows 7 to 12. The most important one: the saved snapshot is the only input, and no scripted request goes to the CCNY site.

## 1. User flows, ordered by value

| # | Flow | Actor | Trigger | Steps | Result |
|---|---|---|---|---|---|
| F1 | Build the table offline | Author | Wants the Fall 2021 calendar as data | Run one command. It reads `data/fall_2021_calendar.html`, checks the heading and table shape, expands the date labels, runs the quality checks | A CSV of 58 rows with columns `date`, `dow`, `text`, plus a small run record: input SHA 256, entry count 36, row count 58 |
| F2 | Grade the required result | Grader | Opens the notebook | Run all cells from top to bottom | A DataFrame with a `datetime.date` index named `date` and columns `dow` and `text`, 58 rows, "All checks passed", and no network request |
| F3 | Confirm the input is the recorded one | Author or grader | Before trusting a result | Compare the SHA 256 in the provenance file with the snapshot | Match, or a clear stop that names the mismatch |
| F4 | Rerun and get the same answer | Author | After any code change | Run F1 twice | Both output files have the same SHA 256 |
| F5 | Learn why a label failed | Author | A date label the parser does not know | Run F1 or the tests | An error that names the label and the rule it broke. No half written output |
| F6 | Add another term (option B, later) | Author | Saves another term's page by hand in a browser | Add the file and its provenance line, run with the year passed in | A CSV for that term, with its own counts checked |

F6 is gated on assumption 11 and is not part of the 30 Nov 2026 success outcome.

## 2. Quality requirements

Measured on 19 Nov 2026 on the author's laptop (Apple M2, macOS, Python 3.12.1, pandas 3.0.3, Beautiful Soup 4.14.3). The measurement ran the notebook's own code cells 7, 8, 9 and 11 against the snapshot, from a script in the session scratch folder, with cell 6's live request skipped. Script: `ccny_p1_measure.py`, 30 runs in one process; plus 5 whole process runs of `ccny_check.py`.

| Quality | Requirement | Evidence or status |
|---|---|---|
| Response time, F1 whole command, p95 | 2 seconds or less from process start to CSV written, no network | Proxy measured: whole process 0.30 to 0.33 s over 5 runs; parse alone p95 0.041 s, median 0.023 s over 30 runs. The real command does not exist yet. Target, user to confirm |
| Response time, F2 notebook run all | 10 seconds or less, kernel already started | Not measured. Target, user to confirm |
| Availability | Not applicable as a service. Replaced by: F1 and F2 work with the network off, every run | Fails today: cell 6 asks the live page first (`fall_2021_calendar.ipynb` line 140) |
| Data volume | One input of 158,779 bytes, 36 entries, 58 rows. Ceiling: input of 1 MB or less, at most 10 terms under option B | Counts measured. Ceiling is a target, user to confirm |
| Concurrency | One user, one run at a time. Not applicable beyond that: laptop only, no shared state | By design |
| Monthly cost ceiling | 0 dollars. No paid service, no key | No paid service or key found in tracked files (Phase 0 scan) |
| Recovery time | A fresh clone reaches the F1 result in 15 minutes or less from README steps, packages already in the local cache | Not testable yet. README run steps are stale. Target, user to confirm |
| Acceptable data loss | Snapshot: none. It is the only copy of the input, since the live page refuses scripts and most likely shows another term now. Output CSV: any, since it is rebuilt from the snapshot | Snapshot is in git (commit c2a0e44) and on the GitHub remote. No checksum recorded yet |
| Determinism | Two runs give byte equal output | Proxy passes: 30 runs gave one CSV text, SHA 256 starting `7da98db87ea5e64a`. No output file yet |

Skipped at Tier A, and why: Core Web Vitals (no web page), uptime and error rate monitoring (no service), billing alerts and cost caps (no paid service), load testing (one user).

## 3. Data classification

| Data | Class | Where stored | Who can read |
|---|---|---|---|
| `data/fall_2021_calendar.html` snapshot | Public. Published by CCNY. Content copyright CUNY (see section 4). Holds the institutional address `no-reply@ccny.cuny.edu` and references to third party scripts (analytics, a chat bot at `bot.ivy.ai`). No personal data found by an email pattern search | Laptop, git history, GitHub remote | Anyone with repository access. The remote's visibility was not checked |
| Parsed DataFrame and future CSV | Public, derived | Notebook output today. `data/out/` later | Same as above |
| Run record (input SHA 256, counts, time) | Internal | `data/out/` later | Same as above |
| Saved notebook outputs | Internal. Include the 403 message and the URL (line 122). No local paths or user names found | `fall_2021_calendar.ipynb` | Same as above |
| Provenance file (saved date, method) | Internal | `data/raw/SOURCES.md` later | Same as above |
| Secrets | None exist. No key, token or password is needed or found | Nowhere | Nobody |

## 4. Licenses, data rights and terms of service

All checked on 19 Nov 2026 by web search and by reading policy pages. No scraping request was made, the live calendar page was not fetched, and `robots.txt` was not fetched.

| Item | Finding | Link |
|---|---|---|
| CCNY website privacy policy | Logs all access to cuny.edu; monitors traffic "for purposes of site management and security" and to find unauthorized attempts to change information. Says nothing about robots, crawling, automated access or reuse. Page shows "last updated 07/21/2026" | https://www.ccny.cuny.edu/about/websiteprivacy |
| CUNY acceptable use policy, CCNY copy | Applies to "Users" of CUNY computer resources, "whether affiliated with CUNY or not". Bars access "without authorization" (4.1.a). Says to "assume that material is copyrighted unless they know otherwise" (4.3.b). No text on bots or scrapers. Whether it reaches public website visitors is not stated | https://ccny.cuny.edu/it/computer-use-policy |
| Copyright of cuny.edu content | A CUNY School of Law page says "all contents of cuny.edu are copyright The City University of New York, all rights reserved", and bars commercial use of law.cuny.edu content without permission. It is another college's page; no CCNY page saying the same was found | https://www.law.cuny.edu/it/terms-of-use/ |
| CCNY website terms of use | No page found by search | Searches: "CUNY website terms of use automated access", "City College of New York website terms of use copyright policy" |
| `robots.txt` for www.ccny.cuny.edu | Not found by search. Not fetched, by instruction. Still open: the author can read https://www.ccny.cuny.edu/robots.txt in a browser | Search: "ccny.cuny.edu robots.txt" |
| Site behaviour | HTTP 403 bot challenge to a plain scripted client (Phase 0, 16 Nov 2026). This is a technical refusal of automated access whatever the written terms say | docs/brief.md |
| requests | Apache 2.0. Latest 2.34.2 (14 May 2026) | https://pypi.org/project/requests/ |
| beautifulsoup4 | MIT. Latest 4.15.0 (7 Jun 2026) | https://pypi.org/project/beautifulsoup4/ |
| pandas | BSD 3 Clause. Latest 3.1.1 (4 Nov 2026) | https://pypi.org/project/pandas/ |
| notebook | BSD 3 Clause. Latest stable 7.6.5 (6 Nov 2026) | https://pypi.org/project/notebook/ |
| This repository | No LICENSE file | Phase 0 audit |

What follows for this project, bounded by the above: no text found grants automated access; the site refuses it; the page content is most likely CUNY copyright. So the snapshot stays the input, no live request is made by default (decision 0002), and the README should name the source and say the content belongs to CUNY. Whether keeping the full page in a public repository is fair use for a course exercise is a question I cannot settle; the smaller option is to keep only the calendar table, which needs your yes.
