# 0006: Date label parser

Date: 23 Nov 2026. Status: proposed.

## Context
`expand_dates` (cell 9) works for every label in the Fall 2021 table but crashes with bare `ValueError` on `December 24 - January 1`, `August 25 - 31, 2021`, `Feb 3` and `September 30 - 31` (AT5, R2). The year 2021 is written in twice.

## Options
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Keep as is | None | AT5 fails; crashes on any new shape | Yes | Fall 2021 only and no tests wanted |
| B. Keep a hand written function with an explicit grammar: `Month D`, `Month D - D`, `Month D - Month D`, each with an optional `, YYYY`; full or three letter month names; year passed in; a range whose end month is before its start month ends in the next year; named `LabelError` for anything else | 2 hours | A shape outside the grammar still stops, by design | Yes | Recommended |
| C. `dateutil.parser` (installed with pandas) for each side of a range | 2 hours | Guesses on fuzzy input; ranges still need custom code | Yes | Labels become free text |
| D. A date parsing package such as `dateparser` | 2 hours plus a new package | New dependency, many guesses | Yes | Many languages or formats |

## Decision
B. The page's own weekday column is checked for every day of every range, which catches a wrong year or a misread label. Hyphens count as a range only between two date parts, which closes defect 5.

## Keep or change
Keep the approach, change the rules, because AT5 fails today.
