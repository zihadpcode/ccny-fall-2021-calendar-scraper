# Design: CCNY Fall 2021 calendar scraper

Phase 2. Date: 23 Nov 2026. Status: draft, awaiting approval. Tier A, laptop only, public data.

This is an existing system. Every decision starts from keep. A change is proposed only where a Phase 1 number, acceptance test or risk shows the current design fails, and each such place names the evidence.

Inputs from Phase 1 that drive this design:

1. F1: one offline command turns the snapshot into a 58 row CSV, p95 2 s or less. Proxy measured about 0.3 s for the whole process.
2. Offline on every run replaces availability. Fails today: notebook cell 6 asks the live page first (AT2, AT9).
3. The snapshot is the only copy of the input and needs a recorded checksum (AT3, AT14). Current SHA 256 `d12ed79993f04b6d94fe086ee08ada09a5df6a97aa21347a4a84255b58aefce2`.
4. Decision 0002 (accepted 22 Nov 2026): the live request stays, behind a flag that is off by default.
5. No CCNY or CUNY text found grants automated access. The page content is most likely CUNY copyright, and the public repository holds the full page (R10).
6. Parser crashes on four label shapes not seen in 2021 (AT5, R2). Checks are `assert` statements (R7).

## Evidence gathered for this phase

All runs on 23 Nov 2026 on the author's laptop, system Python 3.12.1, pandas 3.0.3, Beautiful Soup 4.14.3, from a script in the session scratch folder. No network request was made. The live page and `robots.txt` were not fetched.

| Check | Result |
|---|---|
| Full page contents | 158,779 bytes, 1 table, 32 `script` elements |
| Trimmed copy: page heading plus the one table, wrapped in a minimal HTML shell | 9,354 bytes, 0 scripts. SHA 256 `e15f91f40a6c5eba657cb8b4fe8aa769f1c1d85349964c5f8f96b7e6cc9799a0` for that exact shell |
| Notebook logic (cells 6 to 13, live request skipped) on the full page, 30 runs | 36 entries, 58 rows, CSV SHA 256 starting `7da98db87ea5e64a`. Median 0.024 s, p95 0.048 s |
| Same logic on the trimmed copy, 30 runs | Same 36 and 58, same CSV text, byte equal. Median 0.006 s, p95 0.006 s |
| `import pandas, bs4` as a fresh process, 5 runs | 0.28 to 0.42 s |
| Original virtual environment (listed, not run) | Python 3.12.1, pandas 3.0.6, beautifulsoup4 4.15.0, requests 2.34.2, notebook 7.6.3, nbclient 0.11.0, nbconvert 7.17.1. No pytest |

Prices and limits used below were checked on 23 Nov 2026 by web search of GitHub Docs: https://docs.github.com/en/billing/concepts/product-billing/github-actions and https://docs.github.com/en/billing/reference/actions-runner-pricing.

## 1. Major decisions

| # | Decision | Keep or change | Recommendation | Note |
|---|---|---|---|---|
| D1 | Input source and the live request | Change, as already accepted | Snapshot is the only default input. Live request behind `--live`, off by default, fails closed | docs/decisions/0002-no-live-request-by-default.md |
| D2 | What the public repository holds | Change | Keep only the page heading and the calendar table. The full page stays in git history | docs/decisions/0003-snapshot-content-in-public-repo.md |
| D3 | Language, packages, pinning | Keep the stack, change pinning | Python 3.12, requests, Beautiful Soup, pandas. Exact pins in two requirements files. pytest is the only new package | docs/decisions/0004-runtime-and-dependencies.md |
| D4 | Code layout | Change | Logic moves to a flat package `calendar_scraper/` at the repository root. The notebook stays and imports it | docs/decisions/0005-code-layout.md |
| D5 | Date label parser | Keep the hand written approach, change its rules | Explicit label grammar, year passed in, named errors | docs/decisions/0006-date-label-parser.md |
| D6 | Output, data store and lineage | Change (new) | CSV plus a JSON run record, both committed, no timestamps. Checksum sidecar for the input | docs/decisions/0007-output-and-lineage.md |
| D7 | Tests and continuous integration | Change (new) | pytest with a socket guard, notebook run headless with nbclient, GitHub Actions on the public repository | docs/decisions/0008-tests-and-ci.md |
| D8 | Hosting, authentication, background work | Does not apply | Laptop only, one user, no schedule | docs/decisions/0009-no-hosting-auth-or-background-work.md |

### D1. Input source and the live request

Already decided in 0002, option B. Design detail added here:

| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Remove the live request | 30 minutes | Loses the `requests` teaching step | Yes | You say the assignment does not grade the call |
| B. Flag off by default; with the flag on, read `robots.txt`, send a plain client name, compare the heading, and stop with a named error on any failure. Never overwrite or fall back silently to the snapshot | 1 hour | A run with the flag on still meets the bot check and fails | Yes | Accepted 22 Nov 2026. Recommended |
| C. Flag on, silent fallback to the snapshot (today's shape behind a flag) | 45 minutes | Hides which input was used; a 200 with another term stops the run (abuse case 2) | Yes | Never |

Why fail closed rather than fall back: with the flag on, the user asked for the live page. A silent fallback is the defect in cell 6 today. The live result is parsed only in memory and printed; it never replaces the snapshot of record.

### D2. What the public repository holds

Keep or change: change. Evidence: R10 (CUNY copyright content and third party scripts in a public repository) and abuse case 5 (opening the snapshot runs 32 scripts). Neither is a measured failure; both are rated Low. The change is cheap, reversible, and the trimmed copy gives byte equal output (checked above).

| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Keep the full page as is | None | Redistributes the whole CUNY page and its scripts | Yes | You judge a full page copy fine for a course exercise |
| B. Keep the full page, strip scripts | 30 minutes | Still the whole page text; a hand edit with no clean checksum story | Yes | You want the page to look right in a browser |
| C. Keep only the heading and the calendar table, made by a recorded trim step; the full page stays reachable at commit c2a0e44 | 45 minutes | The full page is still in public history. Calendar facts remain CUNY content, but far less of it | Yes, `git show c2a0e44:data/fall_2021_calendar.html` | Recommended |
| D. C plus a history rewrite to drop the full page | 1 to 2 hours | Destructive force push; breaks every clone; the page was public anyway | No | Only if CUNY asks, and only with your explicit yes |

Recommendation: C. The trimmed copy becomes the input of record with its own checksum. Provenance records both checksums and the trim method. D is not proposed.

### D3 to D8

Each has its full options table in its decision note. Summary:

* D3. Keep Python 3.12 and the three named packages; the assignment names them. Exact pins remove R8 drift. pytest is chosen over the standard `unittest` for clearer test output and fixtures for a temporary folder; it is one dev only package and needs your yes. Hashes in the pin files are deferred: they need a network download to compute.
* D4. A flat package at the root runs with `python -m calendar_scraper` from a fresh clone with no install step. A `src/` layout would need `pip install -e .` and a build backend. A single script file would leave the notebook copying code. So: flat package, two modules.
* D5. Keep the hand written function, because the label shapes are few and the page's own weekday column checks every result. `dateutil` is already installed with pandas, but it does not read ranges, so it would not remove the custom code.
* D6. CSV is what the success outcome names. The run record makes lineage visible. Parquet or SQLite would add a package or a store for 58 rows.
* D7. GitHub Actions standard runners are free and unlimited for public repositories (checked 23 Nov 2026). nbclient is already in the environment through `notebook`, so the notebook test adds nothing.
* D8. Laptop only, no users to sign in, no schedule (scheduled scraping is out of scope).

## 2. Data model and how it changes

No database. Files only. Every machine read file carries a schema number or a fixed format.

| Entity | Shape | Stored where | Class | Lifetime | Change plan |
|---|---|---|---|---|---|
| Snapshot of record | HTML: one `h1` with "Fall 2021 Academic Calendar" and one table, header row then rows of three cells | `data/raw/fall_2021_calendar_table.html` (after D2; today `data/fall_2021_calendar.html`) | Public, CUNY content | Kept in git forever | Never edited. A new capture is a new file with a new checksum |
| Checksum sidecar | One line, `shasum -a 256` format: `<64 hex>  <file name>` | `data/raw/fall_2021_calendar_table.html.sha256` | Internal | With the snapshot | Fixed format, checkable with `shasum -a 256 -c` |
| Provenance | Markdown: source URL, how and when saved (unknown today), trim method, full page SHA 256 and commit c2a0e44, trimmed SHA 256 | `data/raw/SOURCES.md` | Internal | Kept | One section per input file |
| Entry | `Entry(date_label: str, dow_source: str, text: str)`, frozen dataclass | Memory | Public | One run | Unchanged from cell 8 records |
| Row | `Row(date: datetime.date, dow: str, text: str)` | Memory | Public | One run | Unchanged from cell 11 |
| Frame | pandas DataFrame, index `date` of `datetime.date`, columns `dow`, `text`, stable sorted | Memory, notebook | Public | One run | Required result shape, frozen |
| Output CSV | Header `date,dow,text`, ISO dates, UTF 8, `\n` line ends, pandas default quoting | `data/out/fall_2021_calendar.csv` | Public, derived | Rebuilt any time; committed as a golden file | Column change means a new file name |
| Run record | JSON, sorted keys, 2 space indent: `{schema: 1, input_file: str, input_sha256: str, term_year: int, entries: int, rows: int, first_date: str, last_date: str, output_file: str, output_sha256: str}` | `data/out/fall_2021_calendar.run.json` | Internal | Rebuilt with the CSV | Additive fields only; a breaking change bumps `schema`. No timestamps or package versions, so two runs are byte equal |

Time: all values are calendar dates with no time zone. Nothing reads the clock, so there are no time zone or clock bugs. The term year is passed in, never read from today's date.

Option B (later) adds one snapshot, sidecar and output pair per term, named `<term>_calendar...`, and an expected count pair per term in code. Nothing else changes.

## 3. Component interfaces

Python with type hints. All errors derive from one base class so the command can map them to exit codes and the notebook shows one clear message.

```python
class CalendarError(Exception):
    code: str            # short stable name, e.g. "checksum_mismatch"

# io.py
def read_expected_sha256(sidecar: Path) -> str
    # raises SnapshotMissing (file absent), SidecarFormat (not 64 hex plus a name)
def read_snapshot(path: Path, expected_sha256: str) -> bytes
    # raises SnapshotMissing, ChecksumMismatch(expected, found)
def write_outputs(frame: pd.DataFrame, record: RunRecord, out_dir: Path) -> RunRecord
    # writes CSV and record to temporary files, then os.replace; no half written output
    # raises OutputError on any OS error; leaves earlier outputs untouched

# parse.py
def extract_entries(html: bytes, expected_heading: str) -> list[Entry]
    # raises WrongPage (heading missing or different),
    #        TableShape (no table, header row not ["DATES", "DAYS", ""] as in the snapshot,
    #                    a row without exactly three cells; message names the row number)
def expand_label(label: str, term_year: int) -> list[date]
    # raises LabelError(label, rule); rule names the broken rule, e.g. "day out of range"
def expand_entries(entries: list[Entry], term_year: int) -> list[Row]
    # raises LabelError, WeekdayMismatch(label, day, page_says, computed)
    # checks the weekday of every day the page names, not only first and last
def build_frame(rows: list[Row]) -> pd.DataFrame
def check_quality(entries: list[Entry], frame: pd.DataFrame, expected: Expected) -> None
    # Expected(entries=36, rows=58, first=date(2021, 8, 1), last=date(2022, 1, 1))
    # raises CountMismatch(name, expected, found), QualityError(rule)
    # explicit if/raise, never assert (R7)

# live.py, imported and called only when the flag is on
def fetch_live(url: str, client_name: str, timeout_s: float = 10.0) -> bytes
    # reads robots.txt with urllib.robotparser first
    # raises RobotsDisallowed, LiveRefused(status), LiveFailed(reason)
    # never writes a file

# __main__.py
python -m calendar_scraper [--input PATH] [--out DIR] [--term-year 2021] [--live]
```

Exit codes: 0 done; 2 bad arguments (argparse); 3 input integrity (`SnapshotMissing`, `SidecarFormat`, `ChecksumMismatch`); 4 parse or quality (`WrongPage`, `TableShape`, `LabelError`, `WeekdayMismatch`, `CountMismatch`, `QualityError`); 5 live (`RobotsDisallowed`, `LiveRefused`, `LiveFailed`); 6 `OutputError`. Each error prints one line: code, then the message. No traceback by default.

With `--live`, the command fetches, extracts and expands the live page in memory, prints its counts next to the snapshot's, and still writes outputs only from the snapshot. That keeps F4 true whatever the site answers.

Notebook contract: the notebook sets `USE_LIVE = False`, imports these functions, keeps its teaching text and `display` calls, and ends with the same "All checks passed" lines (F2).

## 4. p95 budget split

Partly applies. There is no request path or server. The F1 command is the only timed path, budget 2 s at p95, no network.

| Step | Budget | Measured (23 Nov 2026) |
|---|---|---|
| Process start and imports (pandas, Beautiful Soup) | 1.0 s | 0.28 to 0.42 s, 5 runs |
| Read snapshot and check SHA 256 | 0.05 s | Not measured; 9 KB file |
| Extract, expand, build frame, quality checks | 0.3 s | p95 0.006 s trimmed, 0.048 s full page, 30 runs |
| Write CSV and run record | 0.1 s | Not measured |
| Slack | 0.55 s | |
| Total | 2.0 s | Whole process proxy 0.30 to 0.33 s (Phase 1) |

F2, notebook run all with a warm kernel: 10 s. The same steps plus `display` calls; not measured yet (AT8).

With `--live`, the fetch is outside the budget. It has a 10 s connect and read timeout for `robots.txt` and again for the page, so the worst case adds about 40 s before a named error.

## 5. Environments and configuration

| Environment | What | Differences |
|---|---|---|
| Local | Author's laptop, a virtual environment on Python 3.12.1 from `requirements.txt` and `requirements_dev.txt` | Can pass `--live`. Outputs go to `data/out/` |
| Test | pytest on the same environment | A conftest fixture blocks outbound sockets for every test except the ones that mock `fetch_live`. Outputs go to a temporary folder. Live flag is never on |
| CI | GitHub Actions, `ubuntu-latest`, Python 3.12 via `actions/setup-python`, install from the pinned files | Same as test. Also checks that a fresh build matches the committed CSV and run record byte for byte |
| Grader | A fresh clone on any machine with Python 3.12, README steps | Same as local without the flag |
| Production or demo | Does not apply. Nothing is served or deployed | |

Configuration is only command line arguments with defaults in code. There are no secrets and no environment variables. Arguments are validated by argparse at start; the input path must exist and the term year must be between 2000 and 2100.

## 6. Monthly cost

| Scale | What it means | Cost a month | Basis |
|---|---|---|---|
| Now | One term, a few local runs and a few CI runs a week | 0 dollars | No paid service. Standard GitHub hosted runners are free and unlimited on public repositories (GitHub Docs, 23 Nov 2026) |
| Ten times | Ten terms under option B, ten times the CI runs | 0 dollars | Same. Ten trimmed snapshots are about 100 KB |
| If the repository becomes private | | 0 dollars until the included allowance runs out | GitHub Free includes 12 dollars of compute a month; Linux standard runners cost 0.006 dollars a minute, about 2,000 minutes. A CI run is estimated at 1 to 2 minutes, not measured |

First limit hit: not a billing limit. At ten times scale the project reaches its own data volume ceiling of 10 terms (docs/requirements.md). Before that, the first practical limit is the label grammar: a new term with a label shape not in the grammar stops with `LabelError` (R2).

## 7. Architecture

```mermaid
flowchart LR
    subgraph repo[Repository, public on GitHub]
        snap[data/raw snapshot of record<br/>heading plus table]
        sha[checksum sidecar]
        src[data/raw/SOURCES.md]
        out[data/out CSV and run record]
    end
    cli[python -m calendar_scraper]
    nb[fall_2021_calendar.ipynb]
    io[io.py<br/>read and verify, write atomically]
    parse[parse.py<br/>extract, expand, check]
    live[live.py<br/>only with --live]
    site[(www.ccny.cuny.edu<br/>not contacted by default)]
    ci[GitHub Actions<br/>pytest, socket guard]

    cli --> io
    nb --> io
    io --> snap
    io --> sha
    io --> parse
    parse --> io
    io --> out
    cli -. flag on .-> live
    nb -. USE_LIVE True .-> live
    live -. robots.txt then page .-> site
    ci --> cli
    ci --> nb
```

## 8. Walking skeleton

The thinnest path that touches every component: `python -m calendar_scraper` reads the snapshot, checks its SHA 256 against the sidecar, extracts and expands with today's logic, runs the quality checks as explicit raises, and writes the CSV and run record. One pytest test runs it in a temporary folder with sockets blocked and asserts 58 rows. One CI workflow runs that test on push. There is no host; the target is a fresh clone plus CI.

Existing code that already forms it, moved with minimal edits:

| Skeleton part | Existing code |
|---|---|
| Read snapshot, heading check | Cell 6, snapshot branch and heading check |
| Extract entries | Cells 7 and 8 |
| Expand labels | Cell 9 `expand_dates`, unchanged at first; hardened in Phase 4 |
| Expand rows, weekday check | Cell 11, `assert` becomes `raise` |
| Quality checks | Cell 13, `assert` becomes `raise` |
| New code | Checksum read and compare, CSV and record writer, argument parsing, one test, one workflow file |

Not in the skeleton: the live flag, the trimmed snapshot, label hardening, the notebook rewrite. Each is a Phase 4 slice in docs/revamp-plan.md.
