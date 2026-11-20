# Pass map

What each piece of maintenance shipped, newest first. The reasons are in the [decisions log](decisions-log.md).

| Date | Job | What shipped | Pull request |
| --- | --- | --- | --- |
| 2026-11-20 | renovate-migrate | Dependency updates move from Dependabot to Renovate (`renovate.json`): weekly pull requests that move the pins in the root `requirements.txt` (compiled from `requirements.in`), majors off, nothing merges by itself; security fixes, including ones that need a major, still come, also for `ansible/k3s/requirements.txt`. `.github/dependabot.yml` removed. | Plan: [#28](https://github.com/HoneyBearTech/platform/pull/28) |
