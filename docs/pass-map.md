# Pass map

What each piece of maintenance shipped, newest first. The reasons are in the [decisions log](decisions-log.md).

| Date | Job | What shipped | Pull request |
| --- | --- | --- | --- |
| 2026-11-20 | remove-private-ips | The home network's addresses in `ansible/inventory/servers.ini`, `ansible/inventory/pihole.ini`, both cluster `group_vars/all.yml` files (cluster API address and load-balancer range) and `docker/wg-easy/docker-compose.yml` (VPN clients' DNS) replaced with placeholders from the documentation range `192.0.2.0/24`. Upstream example addresses kept. | [#31](https://github.com/HoneyBearTech/platform/pull/31) |
| 2026-11-20 | renovate-migrate | Dependency updates move from Dependabot to Renovate (`renovate.json`): weekly pull requests that move the pins in the root `requirements.txt` (compiled from `requirements.in`), majors off, nothing merges by itself; security fixes, including ones that need a major, still come, also for `ansible/k3s/requirements.txt`. `.github/dependabot.yml` removed. | Plan: [#28](https://github.com/HoneyBearTech/platform/pull/28), work: [#29](https://github.com/HoneyBearTech/platform/pull/29) |
