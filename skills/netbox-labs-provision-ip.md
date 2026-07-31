---
name: Provision an IP address from a prefix
description: Find an existing prefix in NetBox and allocate the next available IP address from it.
api: openapi/netbox-labs-openapi-original.json
operations: [ipam_prefixes_list, ipam_prefixes_available_ips_list, ipam_prefixes_available_ips_create]
---

# Provision an IP address from a NetBox prefix

Authenticate every request with `Authorization: Bearer <key>.<token>` (v2) or
`Authorization: Token <token>` (v1). The token must carry write permission for IPAM.

## Steps
1. **Find the prefix** — `ipam_prefixes_list` (`GET /api/ipam/prefixes/`), filtering by
   `prefix=`, `vrf_id=`, or `site_id=`. Read the prefix `id` from `results[]`.
2. **(Optional) preview capacity** — `ipam_prefixes_available_ips_list`
   (`GET /api/ipam/prefixes/{id}/available-ips/`) returns free addresses.
3. **Allocate** — `ipam_prefixes_available_ips_create`
   (`POST /api/ipam/prefixes/{id}/available-ips/`) to atomically reserve the next
   free IP. Post an empty object `{}` (or a list to grab several). NetBox assigns
   the address and returns the created ip-address object.

## Conventions
- Pagination: `limit`/`offset` (or cursor `start`); response has `count/next/previous/results`.
- Trace: send/inspect `X-Request-ID` to correlate the write with its change-log record.
- Errors: 400 = validation map, 401 = bad token, 403 = insufficient permission.
- No idempotency-key: do not blind-retry the POST; re-check availability first.
