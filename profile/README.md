# actpkg — components on the protocol

**Curated WebAssembly components for the [ACT toolchain](https://github.com/actcore).** Each one is sandboxed by [wasmtime](https://wasmtime.dev), capability-bounded by an `act:component` manifest, and signed by GitHub Actions attestation on publish.

Browse, search, and inspect at [**actpkg.dev**](https://actpkg.dev).

## How it works

Every component in this org is published as a signed OCI artifact at `ghcr.io/actpkg/<name>:<version>`. Pull and run with:

```bash
npx -y @actcore/act run ghcr.io/actpkg/<name>:latest --mcp \
  --fs-policy allowlist --fs-allow /path/to/data
```

Each component declares the capabilities it needs (`wasi:filesystem`, `wasi:http`, etc.) inside its WASM custom section. The host reads that declaration without executing the component, intersects it with the operator's runtime grants, and enforces the result. A component asking for nothing gets nothing — even `--http-policy open` from the operator doesn't let it reach the network.

## What's here

**Data & state**

- [`sqlite`](https://github.com/actpkg/sqlite) — SQLite via rusqlite (also the `sqlite-vec` variant)
- [`filesystem`](https://github.com/actpkg/filesystem) — Generic read / write / list / stat / delete
- [`openwallet`](https://github.com/actpkg/openwallet) — OpenWallet-compatible local key vault

**Network bridges**

- [`http-client`](https://github.com/actpkg/http-client) — Generic HTTP fetch (GET / POST / PUT / DELETE)
- [`mcp-bridge`](https://github.com/actpkg/mcp-bridge) — Wrap a remote MCP server, re-expose its tools
- [`openapi-bridge`](https://github.com/actpkg/openapi-bridge) — Any OpenAPI 3.x spec → ACT tools at runtime
- [`act-http-bridge`](https://github.com/actpkg/act-http-bridge) — Proxy a remote ACT-HTTP host

**Utilities**

- [`crypto`](https://github.com/actpkg/crypto) — SHA / HMAC / Ed25519 / X25519
- [`encoding`](https://github.com/actpkg/encoding) — Base64 / base32 / hex / URL-encoding / UUID
- [`random`](https://github.com/actpkg/random) — Cryptographic randomness, UUID v4 + v7
- [`time`](https://github.com/actpkg/time) — Clock access, timezone math

**Language embedders**

- [`python-eval`](https://github.com/actpkg/python-eval) — Sandboxed CPython via [`componentize-py`](https://github.com/bytecodealliance/componentize-py)

## Publishing your own

External contributions land here after passing the quality + security gates we apply to first-party components:

- **Capability declaration** in `act.toml` — what the component needs, structured.
- **CI publish workflow** — signed via GitHub Actions attestation on every release tag.
- **Source repo linked** — every artifact traceable back to the commit it was built from.
- **Permissive license** — MIT-or-Apache-2.0.

The bar will rise as the registry grows: cosign signing, SBOM publication, dependency vulnerability scanning, and reproducible-build verification are on the roadmap.

To submit, open a PR adding your `<name>` to [`actpkg.dev/scripts/crawl-components.sh`](https://github.com/actcore/actpkg.dev/blob/main/scripts/crawl-components.sh). Daily build picks it up.

License across the org: MIT-or-Apache-2.0.
