# 0003: Keep only the heading and calendar table in the public repository

Date: 23 Nov 2026. Status: proposed.

## Context
The public GitHub repository holds the full CCNY page, 158,779 bytes with 32 `script` elements (R10, abuse case 5). Phase 1 found the content is most likely CUNY copyright and found no text that grants reuse. The parser needs only the `h1` heading and the one table.

On 23 Nov 2026 a trimmed copy (heading plus table in a minimal HTML shell, 9,354 bytes, no scripts) gave the same 36 entries, 58 rows and byte equal CSV text as the full page. No network was used.

## Options
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Keep the full page | None | Whole page and its scripts redistributed | Yes | You judge the full copy fine for a course exercise |
| B. Full page with scripts stripped | 30 minutes | Still the whole page text; hand edit | Yes | You want it to render like the site |
| C. Heading and table only, by a recorded trim step; full page reachable at commit c2a0e44 | 45 minutes | Full page stays in public history | Yes | Recommended |
| D. C plus a history rewrite | 1 to 2 hours | Force push, broken clones; irreversible | No | Only on a CUNY request and your explicit yes |

## Decision
C. The trimmed file `data/raw/fall_2021_calendar_table.html` becomes the input of record with its own SHA 256 sidecar. `data/raw/SOURCES.md` records the source URL, the full page SHA 256 `d12ed799...aefce2`, commit c2a0e44, the trim method and the trimmed SHA 256. The README names the source and says the content belongs to CUNY.

## Keep or change
Change. Driven by R10 and abuse case 5, both rated Low, not by a measured failure. Chosen because it is cheap, reversible and output equal.

## Consequences
* The trimmed file no longer looks like the site in a browser.
* Removing the full page from the working tree deletes a tracked file; that step needs your yes in Phase 4.
* A capture of another term (option B) is trimmed the same way.
