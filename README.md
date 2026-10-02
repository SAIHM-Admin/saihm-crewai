# saihm-crewai

**SAIHM external memory for CrewAI — a `StorageBackend` your crew owns: one encrypted source of truth every agent in the crew shares, portable across every model, and provably erasable.**

<a href="https://saihm.net/overview"><img src="https://saihm.net/media/saihm-short-overview-play.jpg" alt="Watch: SAIHM in 6 minutes" width="480"></a>

**New to SAIHM?** [Watch the 6-minute overview](https://saihm.net/overview) (captions and transcript), or [read the SAIHM manual (PDF)](https://saihm.net/manual).

`SaihmStorageBackend` is a drop-in [`crewai.memory.storage.backend.StorageBackend`](https://docs.crewai.com/en/concepts/memory) — the protocol CrewAI's unified memory resolves through. Register it once and that crew's long-term memory becomes memory the user actually owns: portable across models *and* frameworks, non-custodial (sealed client-side; Python never holds a key), and **provably erasable** (GDPR Art. 17 — `reset` crypto-shreds the cells, it does not merely hide them).

```bash
pip install saihm-crewai      # needs crewai >= 1.15 and Node.js >= 20 on PATH (see "How it works")
```

```python
from crewai.memory.storage.factory import set_memory_storage_factory
from saihm_memory import SaihmStorageBackend

# Register once at startup; return None for other specs to defer to CrewAI's own selection.
set_memory_storage_factory(
    lambda spec: SaihmStorageBackend() if spec == "saihm" else None   # sandbox by default — no account, offline
)
```

Any CrewAI memory built afterwards for the `"saihm"` storage spec is then backed by SAIHM.

Or drive the store directly (any Python app):

```python
from crewai.memory.types import MemoryRecord
from saihm_memory import SaihmStorageBackend

store = SaihmStorageBackend()                    # local blind sandbox, no signup
store.save([MemoryRecord(content="the user is vegetarian", categories=["diet"])])
hits = store.search(query_embedding, limit=5)    # -> [(MemoryRecord, score), ...] best-first
store.reset()                                    # crypto-shred everything this store wrote
```

`SaihmStorageBackend` implements the full `StorageBackend` protocol — `save` / `search` / `delete` / `update` / `get_record` / `list_records` / `get_scope_info` / `list_scopes` / `list_categories` / `count` / `reset`, plus async `asave` / `asearch` / `adelete`. Each record is one encrypted SAIHM cell.

> **CrewAI version.** CrewAI replaced its memory storage API between 1.9 and 1.15 (`storage.interface.Storage` and `ExternalMemory` gave way to `storage.backend.StorageBackend`, `types.MemoryRecord`, and the `storage.factory` hook). This package targets the current API and therefore requires **crewai >= 1.15**; it is verified against 1.15.14.

## Why not just the built-in storage?

CrewAI's default storage is only as portable as the process it runs in, and only as private as whoever holds the store behind it. SAIHM changes the ownership model — same `StorageBackend` protocol, a fundamentally different guarantee about *who owns the memory*:

- **Yours across every model.** The same memory opens under Claude, GPT, DeepSeek, Qwen, Kimi, or GLM — and under the LangGraph, LangChain, and AutoGen adapters. Switch models or frameworks and the crew's memory follows it. No migration, no re-embedding, no vendor lock-in.
- **The store is blind.** Every entry is sealed client-side with post-quantum cryptography (ML-DSA-65 / ML-KEM-768 / AES-256-GCM) before it ever leaves your process. The endpoint holds ciphertext it cannot read; Python never holds a key.
- **Erasure you can prove.** `reset` and `delete` destroy each cell's key, so its ciphertext becomes unrecoverable noise — a cryptographic shred, not a soft-delete. That is GDPR Art. 17 built into the backend. On the hosted tier each shred is recorded in SAIHM's audit chain; in the offline sandbox the identical code path runs locally, with no external record.
- **Free to start, offline in seconds.** Runs against a local blind sandbox with no account and no network; point it at the hosted free tier when you're ready to persist.

Give your crew long-term memory your users actually own — portable, private, and provably forgettable — without leaving the `StorageBackend` protocol CrewAI already uses.

## How it works

All cryptography runs in a small **Node sidecar** (built on [`@saihm/mcp-server-pro`](https://www.npmjs.com/package/@saihm/mcp-server-pro), ML-DSA-65 / ML-KEM-768 / AES-256-GCM); Python drives it over [MCP](https://modelcontextprotocol.io) stdio and holds no keys — one audited crypto implementation, not a second one ported to Python. That is why **Node.js ≥ 20 is required**. The sidecar ships inside the wheel as source only; its Node dependencies are installed once into a per-user cache (`~/.cache/saihm-crewai`) on first use, then reused offline.

- **Sandbox (default):** no configuration → a local, in-process *blind* endpoint (ciphertext only). Great for trying it with zero signup; memory lasts for the client's life.
- **Live:** set `SAIHM_ENDPOINT_URL` + `SAIHM_MASTER_SECRET_HEX` and either `SAIHM_TIER=FREE` (free tier — run `npx -y @saihm/mcp-server-pro free-join` once) or `SAIHM_AUTH_HEADER` (Pro; join at <https://saihm.coti.global/join>) → durable, hosted, blind memory.

Always `close()` the backend (or close a shared client); a dropped client is reaped on GC, but explicit close is cleaner.

## Ranking (blind store)

SAIHM is a *blind* store — the endpoint holds ciphertext only and cannot run a server-side vector index — so `search` ranks **client-side**:

- **Semantic, using CrewAI's own embeddings.** `search` takes a `query_embedding` and CrewAI puts an `embedding` on each `MemoryRecord`, so ranking is cosine similarity computed in your process. The embedding is sealed into the cell alongside the content; the endpoint sees neither. This adapter never embeds anything itself — whatever embedding model CrewAI is configured with is the one that applies.
- **Recency fallback.** When no embeddings are present (for example the offline sandbox, where nothing has been embedded), `search` degrades to newest-first rather than returning nothing.
- **`min_score`, `limit`, `scope_prefix`, `categories`, and `metadata_filter` are all honored** — filtering happens before ranking.

Each result is a `(MemoryRecord, score)` tuple, best-first. Exact recall (`get_record`, `list_records`) and erasure (`delete`, `reset`) are always exact, never approximate.

`scope_prefix` matches on **path boundaries**: `/demo` covers `/demo` and `/demo/inner` but never `/demo2` or `/demonstrate`. That distinction matters because `delete` and `reset` crypto-shred irreversibly.

A cell written outside this adapter (e.g. by the LangGraph, LangChain, or AutoGen SAIHM adapters, or a raw `remember`) is left untouched — one owned store can hold facts from several adapters without collision, and `reset` only ever shreds the entries this backend wrote.

## Related

- **Try it in your browser (offline, no signup):** <https://citw2.github.io/saihm-demos/>
- **Runnable end-to-end demo:** <https://github.com/citw2/saihm-crewai>
- **Other adapters:** [LangGraph](https://github.com/SAIHM-Admin/saihm-langgraph) · [LangChain/LlamaIndex](https://github.com/citw2/saihm-langchain) · [AutoGen](https://github.com/citw2/saihm-autogen)
- **What SAIHM is:** <https://saihm.coti.global>

## License

Apache-2.0.
