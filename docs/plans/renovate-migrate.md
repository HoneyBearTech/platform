# Plan: move dependency updates from Dependabot to Renovate

**What the owner asked for:** "remove Dependabot workflows, turn off Dependabot security updates, and turn on Renovate."

## What we found

- `.github/dependabot.yml` covers only the Python tools (`requirements.txt`, compiled by pip-compile from
  `requirements.in`), daily, with major versions ignored. Nothing merges by itself, and no checks are required on
  `main`. Dependabot's pull requests #9, #10 and #24 to #26 have been open since 2023.
- Renovate opened its own onboarding pull request today (#27). Merging it as it stands would turn on every Renovate
  manager at once: the Compose files under `docker/`, the Kubernetes values, the Ansible collections, the
  pre-commit hooks and the GitHub Actions, none of which Dependabot ever updated.

## What we'll change

1. **Add `renovate.json`** with the same scope as Dependabot: only the Python tools, read through `requirements.txt`
   with Renovate's pip-compile manager so the lock file is regenerated with each update. Major versions are not
   proposed, as before. Updates come weekly, early on Monday (America/Chicago), assigned to the owner, with the
   Dependency Dashboard issue. **Nothing merges by itself**; security fixes are still proposed. Renovate's periodic
   whole-file refresh is off.
   *Check:* Renovate's config validator in strict mode, and a dry run that reads `requirements.txt` and nothing else.
2. **Delete `.github/dependabot.yml`** in the same change.
3. **Job notes:** `docs/pass-map.md` and `docs/decisions-log.md` (new).

Nothing in `.github/workflows` changes.

## Who's on it

Engineer, Security Officer, Auditor. No Researcher or QA (one config file; the Auditor checks it in Renovate itself).

## Risks

- Renovate's hosted app re-runs pip-compile with a newer Python than the 3.8 that last compiled the file, so its first
  update may also move pins that depend on the Python version. Each update is reviewed by the owner before merging.

## Out of scope

- Widening Renovate to the Compose files, Kubernetes values, Ansible collections, pre-commit hooks or Actions (a
  later decision for the owner).
- Closing the 2023 Dependabot pull requests and Renovate's onboarding pull request #27 (the owner's call; this pull
  request replaces #27).
- The private IP addresses already in this public repository's inventories and values files (a separate job).
