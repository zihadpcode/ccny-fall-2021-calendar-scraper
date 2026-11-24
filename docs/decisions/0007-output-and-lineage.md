# 0007: Output, data store and lineage

Date: 23 Nov 2026. Status: proposed.

## Context
Nothing is written to disk today; the result lives in notebook output. The data pipeline addendum asks for a record of which input made which output. F4 asks for byte equal reruns.

## Options
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. No file, notebook output only | None | AT1, AT4 cannot pass | Yes | Never |
| B. CSV plus a JSON run record (input SHA 256, counts, output SHA 256), no timestamps, both committed | 1 hour | Committed outputs must be rebuilt after a parser change; CI checks this | Yes | Recommended |
| C. B but outputs git ignored | 1 hour | Grader must run the code to see the result; no golden file | Yes | Outputs grow large |
| D. Parquet or SQLite | 1 to 2 hours | New package (pyarrow) or a store for 58 rows | Yes | Many terms queried together |

## Decision
B. Input integrity uses a `shasum` format sidecar next to the snapshot. A mismatch stops the run before parsing. Outputs are written to temporary files and moved into place, so a failed run leaves no half written file.

## Keep or change
Change (new), because AT1, AT3, AT4 and AT14 fail today.
