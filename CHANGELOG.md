# Changelog

## 0.1.2

- Require `saihm-adapters` 0.1.2 or later, which raises a clear `SaihmNoIdentityError` when
  live mode has no identity and bundles the current SAIHM client.
- Links point to https://saihm.net, this repository, the saihm-adapters repository and
  https://saihm-admin.github.io/saihm-demos/. The README says how to go live.
- This repository now holds the forwarding package itself. The earlier adapter code stays in
  its history and lives on in saihm-adapters.

## 0.1.1

`saihm-crewai` moved into the consolidated `saihm-adapters` package. Installing it pulls
`saihm-adapters[crewai]`; import from `saihm_adapters` (was `saihm_memory`).

