# Decisions log

Why maintenance changes were made the way they were, newest first. What shipped is in the
[pass map](pass-map.md).

## 2026-11-20: remove-private-ips

**Decision:** the home network's addresses are replaced in place with placeholders from `192.0.2.0/24`, the
range reserved for documentation, one per host, with a comment saying to put your own addresses there.

- **Placeholders, not untracked local files:** nothing in the repository reads these inventories by itself
  (they are passed with `-i`), so a gitignored local copy with an example file would add moving parts for no
  gain. The documentation range exists on no real network, so running an inventory as it stands reaches no
  machine.
- **History left as is:** the old addresses stay in earlier commits. Removing them would mean rewriting every
  commit and force-pushing `main`; they are private-range addresses, reachable only from inside the home
  network.
- **Upstream examples kept** (they describe no real network, so later searches can skip them): the k3s
  cluster test machines and ranges in `molecule/`, `ansible/k3s/molecule/`, `.github/workflows/test.yml` and
  `kubernetes/kube-prometheus-stack/values.yml`; the example load-balancer addresses in the Traefik values; the
  example proxy targets and the `/16` allow-list in `docker/traefik/data/config.yml`; Graylog's default
  `http_publish_uri` comment; the Pterodactyl node example; the container and cluster network ranges (Docker,
  k3s, WireGuard, Pterodactyl); `ansible/README.md`'s `ssh-copy-id` example and the GitLab runner's commented
  host alias.
- **Host names kept:** the names in the inventories and their comments are out of scope.

**Rejected review findings:**
- "The `192.168.30.x` addresses in the monitoring and Traefik values might be real": they are the upstream
  cluster project's sample addresses, the same ones its test machines use; the home network is on another
  subnet.

## 2026-11-20: renovate-migrate

**Decision:** Renovate replaces Dependabot for dependency updates, with the same scope: the root
`requirements.txt`, majors off, nothing merged automatically, but weekly instead of daily.

- **Lock pins, not ranges:** every package in `requirements.in` is an open `>=` range, so Renovate's default
  strategy proposed nothing. `rangeStrategy: update-lockfile` moves the pins in `requirements.txt` and leaves
  the ranges alone.
- **Security fixes:** the rule that turns majors off would also have dropped a security fix that needs a major
  (cryptography 38.0.3 to 39.0.1, open as a Dependabot pull request). `vulnerabilityAlerts.enabled` brings those
  back, still for review.
- **`ansible/k3s/requirements.txt`:** Dependabot's security updates covered it. Renovate reads it only for
  security fixes (routine updates are off), through the pip_requirements manager, limited to that file with
  `includePaths` so the root `requirements.txt` is handled only by pip-compile (otherwise a security fix could
  edit it without regenerating it).
- **Lock-file maintenance is off:** Renovate's pip-compile manager turns it on by default.
- **Kept as is:** the old Dependabot pull requests (#9, #10, #24-26) and Renovate's onboarding pull request (#27)
  are left for the owner to close.

**Rejected review findings:**
- "`dependencyDashboard` must be `true`, not `{ "enabled": true }`": the strict validator requires the object
  form.
