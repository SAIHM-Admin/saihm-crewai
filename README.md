# saihm-crewai

**`saihm-crewai` has moved. It is now the CrewAI adapter inside the consolidated [`saihm-adapters`](https://pypi.org/project/saihm-adapters/) package** — the same `StorageBackend` that gives a crew long-term memory the user owns: one encrypted source of truth shared across the whole crew, portable across models *and* frameworks, sealed client-side (with the key file, Python never reads your key), and provably erasable (GDPR Art. 17).

Installing `saihm-crewai` now pulls `saihm-adapters[crewai]`. This package ships no code of its own.

## Migration — one line

The import root changed from `saihm_memory` to `saihm_adapters`:

```python
from saihm_adapters import SaihmStorageBackend   # was: from saihm_memory import SaihmStorageBackend
```

`SaihmMemoryClient`, `Memory`, `SaihmTimeout`, and the start-up errors `SaihmStartupError` / `SaihmNoIdentityError` come from `saihm_adapters`:

```python
from saihm_adapters import SaihmMemoryClient, Memory, SaihmTimeout, SaihmNoIdentityError
```

To keep memories beyond one process, create an identity once with `npx -y @saihm/mcp-server-pro free-join` and set `SAIHM_ENDPOINT_URL=https://saihm.net/mcp`; the [saihm-adapters README](https://github.com/SAIHM-Admin/saihm-adapters#go-live) has the details. Without it, the client uses a local sandbox whose memory ends with the process.

This is a clean break: after upgrading, `import saihm_memory` will fail with `ModuleNotFoundError`. That is intentional: the old import root is gone, so the one change to make is obvious.

## Install

```bash
pip install saihm-crewai        # -> installs saihm-adapters[crewai]; needs crewai >= 1.15 and Node.js >= 20 on PATH
```

or depend on the consolidated package directly:

```bash
pip install "saihm-adapters[crewai]"
```

`SaihmStorageBackend` remains a drop-in `crewai.memory.storage.backend.StorageBackend`, registered through `set_memory_storage_factory`. Everything else about the adapter is unchanged; only the package that carries it and its import root have consolidated.

## Related

- **Consolidated package:** <https://pypi.org/project/saihm-adapters/>
- **Source and getting started (including how to go live):** <https://github.com/SAIHM-Admin/saihm-adapters>
- **Demos:** <https://saihm-admin.github.io/saihm-demos/>
- **What SAIHM is:** <https://saihm.net>

## License

Apache-2.0.
