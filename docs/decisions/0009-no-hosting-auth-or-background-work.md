# 0009: No hosting, authentication or background work

Date: 23 Nov 2026. Status: proposed.

## Context
Tier A. The tool runs on the author's laptop and in CI. It serves nothing, has one user, holds no secrets, and scheduled scraping is out of scope (docs/brief.md).

## Decision
* Hosting: does not apply. The repository on GitHub is the only shared copy. Exit path: any git host; the data is plain files.
* Authentication: does not apply. Nothing to sign in to, no keys.
* Background work: does not apply. No schedule; runs are by hand or by CI on push.
* Phases 3 and 6 skip deploy, rollback on a host, monitoring and cost caps. Rollback is `git revert`.

## Reopen when
The tier changes, the output is published as a service, or a schedule is wanted. A schedule would also need written permission from CCNY (decision 0002).
