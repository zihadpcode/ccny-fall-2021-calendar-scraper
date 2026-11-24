# 0008: Tests and continuous integration

Date: 23 Nov 2026. Status: proposed.

## Context
The only checks are `assert` lines in notebook cell 13. AT2 and AT9 need proof that no network request happens. The repository is public on GitHub.

## Options: offline proof
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Conftest fixture that replaces `socket.socket.connect` and raises | 20 minutes | Misses a C extension that opens sockets itself; none is used here | Yes | Recommended |
| B. `pytest-socket` plugin | 10 minutes plus a package | New dependency | Yes | Many projects share it |
| C. Run tests with the network off by hand | None | Not repeatable | Yes | Never as the only proof |

## Options: notebook test
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. Execute the notebook with nbclient (already installed via `notebook`) in a test, with the socket guard set in the kernel by a first hidden cell or an environment check | 1 hour | Kernel start adds seconds to the suite | Yes | Recommended |
| B. No notebook test | None | AT2 unproven | Yes | Never |

## Options: CI
| Option | Cost | Risk | Reversible | Pick when |
|---|---|---|---|---|
| A. GitHub Actions, `ubuntu-latest`, Python 3.12 | 0 dollars on a public repository (GitHub Docs, 23 Nov 2026) | Runner image changes over time | Yes | Recommended |
| B. No CI, local runs only | None | Definition of done needs CI | Yes | Never at this tier's rules |

## Decision
pytest, socket guard fixture, notebook executed by nbclient, GitHub Actions on push and pull request. CI also rebuilds the outputs and compares them byte for byte with the committed files.

## Outside action
The workflow runs only after a push, which needs your yes. Sources: https://docs.github.com/en/billing/concepts/product-billing/github-actions, https://docs.github.com/en/billing/reference/actions-runner-pricing (searched 23 Nov 2026).
