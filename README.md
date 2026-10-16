# saihm-crewai — memory for CrewAI crews

**Portable memory for CrewAI crews.** A CrewAI memory `StorageBackend` that gives your whole
crew one memory, encrypted on your machine and portable across models and frameworks. Any
memory can be erased for good.

[![PyPI version](https://img.shields.io/pypi/v/saihm-crewai.svg)](https://pypi.org/project/saihm-crewai/)
[![license](https://img.shields.io/pypi/l/saihm-crewai.svg)](https://github.com/SAIHM-Admin/saihm-crewai/blob/main/LICENSE)
[![Star on GitHub](https://img.shields.io/badge/GitHub-Star-181717?logo=github&logoColor=white)](https://github.com/SAIHM-Admin/saihm-adapters)
[![Sponsor SAIHM](https://img.shields.io/badge/Sponsor-SAIHM-ea4aaa?logo=githubsponsors&logoColor=white)](https://github.com/sponsors/SAIHM-Admin)
[![Subscribe on YouTube](https://img.shields.io/badge/YouTube-Subscribe-FF0000?logo=youtube&logoColor=white)](https://www.youtube.com/@SAIHM-Memory?sub_confirmation=1)
[![Follow on X](https://img.shields.io/badge/X-Follow-000000?logo=x&logoColor=white)](https://x.com/SAIHMemory)

**`saihm-crewai` is now the CrewAI adapter inside the consolidated
[`saihm-adapters`](https://pypi.org/project/saihm-adapters/) package.** Installing it pulls
`saihm-adapters[crewai]`. This package ships no code of its own.

**New to SAIHM?** [Watch the 6-minute overview](https://saihm.net/overview) (captions and transcript).
**Prefer to read?** [The SAIHM manual](https://saihm.net/manual) (PDF) is a decision-maker's guide to SAIHM.

**Encrypted at rest, by default.** Every memory is encrypted on your own machine before it
goes anywhere, and SAIHM stores only that ciphertext. Nobody else can read your memories —
not the storage, not SAIHM. With your key in a key file, Python never reads the key. With
the default free-join key file, the client also keeps a local copy of your memories, so
recall is fast. That copy is not encrypted, and only your user account can read it. Setting
`SAIHM_RECALL_CACHE=0` in every app that uses this key, MCP hosts included, stops new
copies. A copy written earlier stays, and `delete` and `reset` on `SaihmStorageBackend` no
longer update it. To remove it, delete the `recall.*` files beside your key file
(`~/.saihm/recall.*`, or in `SAIHM_HOME`; a `SAIHM_RECALL_CACHE_PATH` file too)
([details](https://github.com/SAIHM-Admin/saihm-adapters#how-it-works)).

## Start free — one command

```sh
npx -y @saihm/mcp-server-pro free-join
```

No card and no wallet. The command creates your key on this machine and shows a link and a
short code. Open the link, sign in to GitHub, enter the code and approve; the command then
finishes by itself and shows where your key is saved. If a join is interrupted, join again
on the same machine: it reuses the key it already created. The
[saihm-adapters README](https://github.com/SAIHM-Admin/saihm-adapters#start-free--one-command)
has the details, including how to start without a GitHub account.

## Add SAIHM to your crew

```bash
pip install saihm-crewai        # -> installs saihm-adapters[crewai]; needs CrewAI >= 1.15 and Node.js >= 20 on PATH
```

Register the backend once at start-up, then ask for SAIHM storage:

```python
from crewai import Memory
from crewai.memory.storage.factory import set_memory_storage_factory

from saihm_adapters import SaihmStorageBackend

set_memory_storage_factory(lambda spec: SaihmStorageBackend() if spec == "saihm" else None)
memory = Memory(storage="saihm")  # give it to a crew: Crew(agents=..., tasks=..., memory=memory)
```

Return `None` for other storage specs, as above, so CrewAI's own choices still apply to them.
With no `SAIHM_ENDPOINT_URL` set, the backend uses a local sandbox whose memory ends with the
process, so you can try it with no sign-up. To go live once free-join has finished:

```bash
export SAIHM_ENDPOINT_URL=https://saihm.net/mcp
```

`SaihmStorageBackend` is a drop-in `crewai.memory.storage.backend.StorageBackend`. In live
mode, `SaihmStorageBackend()` raises `SaihmNoIdentityError` when this machine has no active
identity, with the command that fixes it, and a call the service refuses raises
`RuntimeError` with the service's reason.

## Migration — one line

The import root changed from `saihm_memory` to `saihm_adapters`:

```python
from saihm_adapters import SaihmStorageBackend   # was: from saihm_memory import SaihmStorageBackend
```

`SaihmMemoryClient`, `Memory`, `SaihmTimeout`, and the start-up errors `SaihmStartupError` / `SaihmNoIdentityError` come from `saihm_adapters`:

```python
from saihm_adapters import SaihmMemoryClient, Memory, SaihmTimeout, SaihmNoIdentityError
```

After upgrading, `import saihm_memory` fails with `ModuleNotFoundError`, which marks the one line to change.

## What it costs

Start free. The free tier is a fixed, one-time allowance for trying SAIHM on real
infrastructure — it doesn't reset or refill. **No card, and nothing to cancel.** Paid
plans are monthly; prices are on the [pricing page](https://saihm.net/pricing).
[Upgrading](https://github.com/SAIHM-Admin/saihm-adapters#what-it-costs) keeps the same key
and every memory you already have.

## Support SAIHM

- **Star it on [GitHub](https://github.com/SAIHM-Admin/saihm-adapters).** Stars help other
  developers find SAIHM.
- **Share it** with a colleague, your team, or anyone whose agents keep forgetting.
- **Follow [@SAIHMemory on X](https://x.com/SAIHMemory)** for news and updates.
- **Sponsor it** through [GitHub Sponsors](https://github.com/sponsors/SAIHM-Admin), monthly or one-time.
- **Follow along on YouTube:** like the videos,
  [subscribe to the channel](https://www.youtube.com/@SAIHM-Memory?sub_confirmation=1), and turn on notifications
  with the bell 🔔 for new videos and updates.
- **Questions or problems?** See [contact and support](https://saihm.net/contact).

More: the [consolidated package](https://pypi.org/project/saihm-adapters/), its
[source and full guide](https://github.com/SAIHM-Admin/saihm-adapters), the
[demos](https://saihm-admin.github.io/saihm-demos/), and [what SAIHM is](https://saihm.net).

## License

Apache-2.0 © SAIHM
