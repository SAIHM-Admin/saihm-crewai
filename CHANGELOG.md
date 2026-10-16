# Changelog

## 0.1.3

- Require `saihm-adapters` 0.1.3 or later. With `mcp` 2.x installed, 0.1.2 passed a call the
  service refused off as a success; 0.1.3 raises it. 0.1.3 also reports an unfinished
  free-join at start-up with `SaihmNoIdentityError`, raises `SaihmStartupError` for every
  start-up failure, and pins sidecar dependencies that had no known vulnerabilities when it
  was built.
- The README follows the `@saihm/mcp-server-pro` README: start free, add SAIHM to your app,
  what it costs (with the [pricing page](https://saihm.net/pricing)) and support. It now
  shows a runnable CrewAI registration (`set_memory_storage_factory`, then `Memory(storage="saihm")`).
- Package links add Issues and Changelog.

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

