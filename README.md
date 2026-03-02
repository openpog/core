# Open Publication Origin Gateway (OpenPOG) Core

Open Publication Origin Gateway (OpenPOG) Core is an open, vendor-neutral, royalty-free HTTP specification for machine-readable publication resolution.

Starting from a public publication URL, a conforming client can discover a participating OpenPOG surface, resolve that URL to one canonical publication record, retrieve publisher-controlled representation URLs, and verify retrieved bytes using publisher-declared integrity digests.

OpenPOG Core intentionally standardizes only the minimum interoperability seam.

## Project Status

- Specification version: `1.0.0`
- Status: Draft
- Last updated: `2026-03-01`

## Why OpenPOG Core Exists

OpenPOG Core defines the smallest interoperable contract needed to turn a public publication URL into a verified, machine-usable publisher record.

Core is designed to:

- standardize discovery,
- standardize URL-to-record resolution,
- define a minimal publication record model,
- preserve publisher authority over byte delivery and access control,
- enable deterministic integrity verification by clients,
- lower bilateral integration cost across publishers and consumers,
- provide a stable base for optional profiles.

## What Core Includes

OpenPOG Core v1 includes:

- authority-scoped discovery via `/.well-known/api-catalog` using an RFC 9727-structured API catalog with OpenPOG-specific trust narrowings (`GET` discovery content is authoritative),
- one mandatory resolve operation (`GET {resolve_endpoint}?url=...`) discovered via `urn:openpog:rel:resolve`,
- deterministic discovery entry selection by authority-root anchor matching,
- resolve input boundary: exactly one absolute URI in `url`, with only `http`/`https` schemes supported by Core,
- a canonical publication record model with gateway-scoped `canonical_url` identity, lifecycle `status` (`active`, `gone`, `unknown`), and optional `critical` extension signaling,
- typed link pointers for citation, policy, and descriptive context,
- representation integrity metadata with required `sha-256` support and HTTPS `origin_url`,
- machine-readable error guidance (`application/problem+json`).

## What Core Does Not Include

OpenPOG Core does not define:

- ranking or recommendation semantics,
- payment and entitlement protocols,
- delegated origin authorization,
- federated trust graph exchange,
- policy enforcement adjudication,
- batch resolution in Core.

These concerns are reserved for optional profiles layered on top of Core.

## Protocol At A Glance

1. Client derives URL authority from the input publication URL.
2. Client discovers OpenPOG metadata at `https://{authority}/.well-known/api-catalog`.
3. Client deterministically selects the discovery entry whose `anchor` matches the authority-root URI `https://{authority}/` and reads its `urn:openpog:rel:resolve` absolute endpoint URL.
4. Client resolves one absolute HTTP(S) publication URL with `GET {resolve_endpoint}?url={publication_url}`.
5. Gateway returns one canonical publication record on success.
6. Client retrieves bytes from a selected `origin_url`.
7. Client verifies bytes against declared `digests` before trust-sensitive use.

## Conformance Snapshot

### Core Gateway

A conforming gateway must:

- implement discovery and resolve behavior,
- enforce resolution and canonicalization behavior,
- return valid publication records,
- include representation digests with `sha-256` support,
- expose safe, machine-readable errors.

### Core Client

A conforming client must:

- perform authority-scoped discovery,
- send valid resolve requests,
- process Core publication records,
- verify representation digests before trust-sensitive use,
- fail explicitly when any extension identifier listed in `critical` is unsupported,
- fail closed on discovery failures and treat cross-host discovery redirects as untrusted unless explicit local policy allows,
- treat `206 Partial Content` retrievals as non-verifiable in Core unless a profile defines partial verification,
- tolerate unknown extension fields.

## Repository Contents

| File | Purpose |
|---|---|
| `SPECIFICATION.md` | Normative OpenPOG Core specification (`v1.0.0`, Draft) |
| `CONTRIBUTING.md` | Contribution process and quality expectations |
| `README.md` | Project overview and navigation |
| `LICENSE` | Apache-2.0 and CC-BY-4.0 licensing terms |

## Read The Spec

The normative specification lives in [SPECIFICATION.md](./SPECIFICATION.md).

Recommended reading order:

1. Introduction, status/versioning, and scope (Sections 1-4)
2. Terminology and architecture (Sections 5-6)
3. Discovery and core API surface (Sections 7-8)
4. Resolution, publication record, and representations (Sections 9-12)
5. Integrity, typed links, media/profiles, errors, and security/privacy (Sections 13-18)
6. Conformance and profile framework (Sections 19-21)
7. Standards context, examples, IANA, and appendices (Sections 22-27)

## Contributing

Contributions are welcome.

- Read [CONTRIBUTING.md](./CONTRIBUTING.md) before submitting changes.
- Use pull requests for proposed edits.
- Keep proposals concrete, minimal, and grounded in implementation needs.

## Author

Open Publication Origin Gateway was created by Sofiane Hocine ([@sohocine](https://github.com/sohocine)).

> [!NOTE]
> Disclosure: The original author uses AI tools as part of the design and drafting process for these specifications.

## License

- Code and specification contributions are licensed under [Apache License 2.0](./LICENSE).
- Documentation contributions (excluding specifications) are licensed under [CC-BY-4.0](./LICENSE).
