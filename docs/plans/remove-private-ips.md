# Plan: remove the home network's addresses from this public repository

**What the owner asked for:** "remove the private IPs from platform"

## What we found

- About 100 private (LAN-style) addresses appear in 38 files. Almost all of them are examples that came with the
  upstream projects this repository was built from: the cluster test machines, the example reverse-proxy targets, the
  example load-balancer address, the standard container and cluster network ranges. They describe no real network.
- The real home-network addresses are in five files:
  - `ansible/inventory/servers.ini` and `ansible/inventory/pihole.ini`: each server's and DNS box's address;
  - `ansible/inventory/group_vars/all.yml` and `ansible/k3s/inventory/cluster/group_vars/all.yml`: the cluster's
    API address and its load-balancer address range;
  - `docker/wg-easy/docker-compose.yml`: the DNS server handed to VPN clients.
- The other inventories (`picluster.ini`, `pi-test.ini`, the k3s `hosts.ini`) already use host names, not addresses.
- Nothing in the repository reads `servers.ini` or `pihole.ini` automatically: they are passed by hand with `-i`.

## What we'll change

1. **Replace each real address** in those five files with an address from the range reserved for documentation
   (`192.0.2.0/24`, which never exists on a real network), keeping one distinct placeholder per host so the files
   still read the same way, and a short comment saying to put your own addresses there. The host-name comments
   stay. The load-balancer range keeps its shape (a start and an end address).
   *Check:* a search of the whole repository for the old addresses finds nothing; the YAML and INI files still parse
   (`yamllint`, `ansible-inventory --list` on both inventories).
2. **Leave the upstream examples alone** (they are listed in the decisions log so a later search doesn't flag them
   again).
3. **Job notes:** `docs/pass-map.md` and `docs/decisions-log.md`.

Nothing in `.github/workflows` changes.

## Who's on it

Engineer, QA, Security Officer, Auditor. No Researcher (the search above is the research).

## Risks

- **The old addresses stay in the git history.** Anyone can still see them in earlier commits. Removing them from
  the history means rewriting every commit and force-pushing `main`, which the team never does; the owner can do
  that if wanted (they are private-range addresses, reachable only from inside the home network, so the
  exposure is small).
- Anyone running these inventories as they are after this change would target placeholder addresses that don't
  exist, so nothing would be changed by mistake; they need their own copy with real addresses.

## Out of scope

- Rewriting the git history (see Risks).
- The host names in the inventories and comments.
- The upstream example addresses, and the address in `.github/workflows/test.yml` (a cluster test range, and
  workflows are not changed by the team).
