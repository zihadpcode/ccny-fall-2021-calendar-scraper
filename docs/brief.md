# Phase 0 brief: CCNY Fall 2021 calendar scraper

Date: 16 Nov 2026. Status: awaiting approval.

## Problem statement
* Who: the author, and a grader who needs the calendar as a table.
* Today: the notebook asks the live page first, gets refused, and silently relies on a saved copy. The parser only understands the exact label shapes that appear in the Fall 2021 table.
* What changes: the saved snapshot becomes the stated input, the parsing lives in a tested function, and the result is written to a file with a record of which input made it.

## Success outcome
By 30 Nov 2026: one command, with no network, turns `data/fall_2021_calendar.html` into a CSV of exactly 58 rows whose SHA 256 is the same on two runs, and a test file with at least 8 label cases passes (single day, same month range, cross month range, explicit year, bad day, bad month name).

## Out of scope
* Any attempt to get past the site's bot check (changed headers to pose as a browser, headless browsers, proxies).
* Scheduled or repeated scraping.
* Other colleges.
* Publishing the calendar as a service.

## What I checked on the live site (16 Nov 2026)
* One request to `https://www.ccny.cuny.edu/registrar/fall` with a plain client name returned HTTP 403 and a page titled "Just a moment...", which is a bot challenge. I made no second request.
* The notebook's own saved output shows the same 403 (cell 6 output).
* So the URL still resolves, but I could not see what it shows today. The path has no year in it, so it most likely shows the current fall term, not 2021. That is an assumption, not a finding.
* Terms: one web search found no CUNY or CCNY terms text about automated access. The CCNY website privacy policy says traffic is monitored for attempts to damage the site: https://www.ccny.cuny.edu/about/websiteprivacy. I did not fetch `robots.txt`, to stay within one request. Both remain open.

## Should it generalize past Fall 2021?
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Fall 2021 only, snapshot is the input, live fetch removed from the default path | 1 to 2 hours | None found | Yes | The assignment result is the goal. Recommended now |
| B. Parser handles any term from hand saved snapshots, year passed in | 3 to 5 hours | Other terms may use table shapes not seen yet | Yes | You want a reusable tool, after A is done |
| C. Live scraping of each term | Unknown | The site refuses scripted requests today. Terms unchecked | Hard to undo a block | Only with written permission from the college |

Recommendation: A now, B as a later slice driven by one more saved term. Not C.

## Prior art
Not rechecked on the web today beyond the one search above. Confirm in Phase 1.

| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| Build: keep the Beautiful Soup row loop | Done | Low | Yes | The assignment names the tools. Recommended |
| Adopt: `pandas.read_html` on the snapshot | 15 minutes | Needs lxml or html5lib, a new dependency | Yes | The tool list is not fixed |
| Adopt: an official calendar feed or PDF from the registrar | Unknown | May not exist for 2021 | Yes | You need current terms, not 2021 |

## Riskiest assumption
The snapshot is a faithful copy of the real Fall 2021 page. It has no saved date and no checksum, and it was added on 30 Sep 2026 while its generator tag says Drupal 10, so it was captured long after the term.

Cheapest experiment: you tell me how the file was saved (browser save, on which date), and we compare three rows by eye with the page in your browser. Ten minutes, no scripted request.

## Rigor tier
Tier A. Laptop only, public data. Skipped: hosting, monitoring, cost caps, threat model. Kept in full: correctness, tests, the data pipeline addendum.

## Open questions
| Question | Resolve by |
|---|---|
| How and when was `data/fall_2021_calendar.html` saved? | 20 Nov 2026 |
| What does the live URL show in your browser today? | 20 Nov 2026 |
| Does the assignment require a live `requests` call, or is the snapshot enough? | 20 Nov 2026 |
| Do CUNY terms or `robots.txt` allow automated access? Read both in a browser | 23 Nov 2026 |
| Option A only, or A then B? | 20 Nov 2026 |
