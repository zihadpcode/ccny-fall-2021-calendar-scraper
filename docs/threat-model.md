# Threat model: CCNY Fall 2021 calendar scraper (Tier A, short)

Date: 20 Nov 2026. Line numbers are in `fall_2021_calendar.ipynb` unless another file is named. Cell numbers count all cells from 0.

## Assets
* The snapshot `data/fall_2021_calendar.html`: the only copy of the input.
* Correct output: 58 rows with the right dates.
* The author's standing with CCNY: a network address that is not blocked, and no breach of its policies.
* The author's laptop and virtual environment.

## Actors
* The author and a grader. Trusted.
* The CCNY site and its bot protection. Outside, not hostile, but refuses scripts.
* Package publishers on PyPI. Outside, untrusted.
* Third party script hosts named in the snapshot (analytics, `bot.ivy.ai`). Outside, untrusted.
* Anyone who can change the repository. Trusted today: one author.

## Entry points and trust boundaries
| Entry point | Boundary crossed | Where |
|---|---|---|
| Live HTTP response | Internet to notebook | Line 140, `requests.get(URL, timeout=30)` in cell 6 |
| Snapshot file | Disk to parser | Line 137 path, line 152 `BeautifulSoup(html, "html.parser")` |
| Package install | PyPI to virtual environment | `requirements.txt` lines 1 to 4, unpinned |
| Snapshot opened in a browser | Disk to browser, which runs its scripts | `data/fall_2021_calendar.html` line 1785, script from `bot.ivy.ai` |

## Top abuse cases
| # | Abuse case | Control today | Gap |
|---|---|---|---|
| 1 | Each notebook run sends an automated request to a site that refuses scripts, against unstated or unread terms | A 30 s timeout (line 140) and a fallback on `RequestException` (line 146) | No off switch. No client name. `robots.txt` unread. Fix: decision 0002 |
| 2 | The live page answers 200 with another term or a challenge page, and wrong dates enter the result | Heading check raises (line 156). Fails closed | The notebook stops instead of using the snapshot |
| 3 | The snapshot is edited or swapped and dates change quietly | Heading check (line 156), three cell check (line 299), weekday asserts (line 496), count asserts (line 600) | No checksum. Asserts vanish under `python -O` |
| 4 | A malicious or changed package is installed | None | Unpinned names, no hashes, no lockfile |
| 5 | Opening the snapshot in a browser runs third party scripts | The parser reads text only and runs no script (line 152) | Nothing stops a browser open. Stripping scripts from the snapshot needs your yes |

Not applicable at Tier A: authentication, access control between users, secrets handling (no secrets exist), cost abuse (no paid service).
