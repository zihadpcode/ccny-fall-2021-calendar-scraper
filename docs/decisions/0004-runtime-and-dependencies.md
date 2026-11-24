# 0004: Runtime and dependencies

Date: 23 Nov 2026. Status: proposed.

## Context
`requirements.txt` lists four unpinned names (R8). The working environment has Python 3.12.1, pandas 3.0.6, beautifulsoup4 4.15.0, requests 2.34.2, notebook 7.6.3. The README requires requests, Beautiful Soup and pandas. There is no test runner.

## Options: pinning
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Keep unpinned names | None | Silent behaviour change on a new release (R8) | Yes | Never, given R8 |
| B. Exact pins of the full set from `pip freeze`, split into `requirements.txt` (run and notebook) and `requirements_dev.txt` (adds pytest) | 30 minutes | Pins age; needs a periodic update | Yes | Recommended |
| C. B plus hashes (`--require-hashes`) | 1 hour | Computing hashes needs a network download or a new tool | Yes | After B, if you want supply chain checks |
| D. uv or Poetry lockfile | 1 hour plus a new tool | New tool to learn and install | Yes | Several projects share it |

## Options: test runner
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| unittest, standard library | None | More boilerplate, plainer failure output | Yes | You refuse any new package |
| pytest | One dev only package, network install | Small | Yes | Recommended |

## Decision
Keep Python 3.12 and requests, Beautiful Soup, pandas, notebook. Pin exactly as in B. Add pytest as the only new package, dev only. No `lxml` or `html5lib`; the standard `html.parser` stays.

## Keep or change
Keep the stack. Change the pinning, because R8 is open.

## Outside action
Installing pytest is a network install and needs your yes.
