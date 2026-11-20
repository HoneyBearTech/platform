# Decisions log

Why maintenance changes were made the way they were, newest first. What shipped is in the
[pass map](pass-map.md).

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
