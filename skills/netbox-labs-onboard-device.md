---
name: Onboard a device with interfaces at a site
description: Create a device at a NetBox site, add interfaces, and assign an IP to an interface.
api: openapi/netbox-labs-openapi-original.json
operations: [dcim_sites_list, dcim_devices_create, dcim_interfaces_create, ipam_ip_addresses_create]
---

# Onboard a device into NetBox

Authenticate with `Authorization: Bearer <key>.<token>` (v2). Requires write
permission for DCIM and IPAM.

## Steps
1. **Resolve the site** — `dcim_sites_list` (`GET /api/dcim/sites/`), filter by
   `slug=` or `name=`, keep the site `id`.
2. **Create the device** — `dcim_devices_create` (`POST /api/dcim/devices/`) with
   `name`, `device_type`, `role`, and `site` (the id from step 1). Keep the device `id`.
3. **Add interfaces** — `dcim_interfaces_create` (`POST /api/dcim/interfaces/`) with
   `device` = the device id, plus `name` and `type` (e.g. `1000base-t`).
4. **Assign an IP** — `ipam_ip_addresses_create` (`POST /api/ipam/ip-addresses/`)
   with `address`, `assigned_object_type=dcim.interface`, and
   `assigned_object_id` = the interface id.

## Conventions
- Nested references (site/role/type) accept an integer id or a brief `{slug}`/`{name}` object.
- Use `brief=true` on list calls to reduce payload while resolving ids.
- Errors are field-keyed JSON maps on 400; check `X-Request-ID` in change-log lookups.
