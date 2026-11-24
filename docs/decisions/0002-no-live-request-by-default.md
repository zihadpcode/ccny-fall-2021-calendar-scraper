# 0002: No live request to the CCNY site by default

Date: 20 Nov 2026. Status: accepted 22 Nov 2026, option B, with the Phase 1 approval.

## Context
The notebook asks `https://www.ccny.cuny.edu/registrar/fall` on every run (line 140). The site answered a scripted client with a 403 bot challenge (16 Nov 2026). A web search on 19 Nov 2026 found no CCNY or CUNY text that grants automated access; the CUNY acceptable use policy bars access "without authorization"; `robots.txt` was not read. Details: docs/requirements.md section 4.

## Options
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Remove the live request; the snapshot is the only input | 30 minutes | Loses the "download with requests" teaching step | Yes, from git | The assignment does not grade the live call |
| B. Keep the live request behind a flag, off by default, with a client name and a `robots.txt` check | 1 hour | A run with the flag on still meets the bot check | Yes | The assignment needs to show a `requests` call. Recommended |
| C. Keep today's behaviour | None | Sends a refused request each run; heading check stops the notebook if the page changes | Yes | Never, given the findings |
| D. Get past the bot check (browser headers, headless browser) | Hours | Against the site's evident intent; may breach policy | Hard to undo a block | Never; out of scope |

## Recommendation
B. It keeps the teaching step without sending anything by default. Change to A if you say the assignment does not need the call.

## Design detail (Phase 2, 23 Nov 2026)
With the flag on, the run reads `robots.txt` first, sends a plain client name, compares the heading, and stops with a named error on any failure. It never falls back silently and never replaces the snapshot; outputs are always built from the snapshot. See docs/design.md D1 and section 3.
