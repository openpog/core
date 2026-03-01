# OpenPOG Core Specification

**Version:** 1.0.0  
**Status:** Draft  
**Date:** 2026-03-01

OpenPOG Core v1 defines a stateless, discoverable HTTP contract that resolves a public publication URL into a canonical machine record with publisher-controlled representation URLs and mandatory integrity digests.

## 1. Introduction

### 1.1 Executive Summary

Open Publication Origin Gateway (OpenPOG) Core is an open, vendor-neutral, royalty-free HTTP specification for machine-readable publication resolution in an agent-first web.

Starting from a public publication URL, a conforming client can:

- discover a participating OpenPOG surface through a predictable well-known entry point,
- resolve the input URL into one canonical publication record,
- obtain one or more publisher-controlled representation URLs,
- inspect typed links for citation, policy, and descriptive context,
- verify retrieved bytes against publisher-declared integrity digests.

OpenPOG Core intentionally standardizes only the minimum interoperability seam. It does not standardize ranking, recommendation, settlement, delegated identity, trust federation, or market structure in the base layer.

### 1.2 Motivation

The web already provides key primitives for discovery and linking, including well-known URIs, API catalog discovery, and standalone link documents. Those primitives still do not define a publication-shaped contract that answers one operational question end-to-end: given a publication URL, what is the canonical machine record, where are the authoritative representations, and how can a client verify integrity? [REF-05] [REF-15] [REF-16]

Without that contract, publishers and automated consumers repeatedly rebuild site-specific adapters, normalization rules, and retrieval logic. The result is integration drag, inconsistent sourcing quality, brittle attribution paths, and avoidable operational overhead.

OpenPOG Core addresses that gap with a conservative design:

- deterministic discovery,
- deterministic resolution,
- publisher-controlled delivery,
- mandatory byte-level integrity declarations,
- extension points for richer concerns outside the mandatory base.

This narrowness is intentional: it allows broad adoption without forcing a single business model, legal interpretation layer, or platform dependency.

### 1.3 Purpose

The purpose of OpenPOG Core is to define the smallest interoperable contract that allows any conforming client to turn a public publication URL into a verified, machine-usable publisher record.

OpenPOG Core exists to:

1. Standardize discovery.
2. Standardize URL-to-record resolution.
3. Standardize a minimal publication record model.
4. Preserve publisher authority over byte delivery and access control.
5. Enable deterministic integrity verification by clients.
6. Lower bilateral integration cost across publishers and consumers.
7. Provide a stable base for optional profiles.

### 1.4 Design Goals

OpenPOG Core is designed to:

- require as few mandatory concepts as possible,
- reuse established standards instead of redefining them,
- keep core operations stateless and cache-friendly,
- avoid tight coupling to any single product architecture,
- remain safe by default under partial failure,
- be extensible without breaking existing conforming clients.

### 1.5 Non-Goals

OpenPOG Core does not attempt to:

- adjudicate legal permissions,
- define ranking or recommendation semantics,
- define payment, settlement, or entitlement exchange,
- define delegated user authorization flows,
- require cross-gateway trust federation,
- mandate crawler policy interpretation as rights policy.

### 1.6 Relationship to the broader POG family

OpenPOG Core is the mandatory interoperability base. Broader POG capabilities are expressed as optional profiles layered on top of Core. A profile may add capabilities, but it cannot weaken or redefine Core invariants.

## 2. Status, Versioning, and Normative Language

### 2.1 Specification status

This document is a draft specification intended for implementation and interoperability testing. Normative requirements in this document define conformance behavior for OpenPOG Core v1.

### 2.2 Versioning policy

OpenPOG Core follows semantic versioning principles for protocol behavior; SemVer is informative guidance in this specification. [REF-28]

- Major version changes introduce incompatible behavior changes.
- Minor version changes are backward compatible and additive.
- Patch version changes are editorial or clarifying and do not alter required wire behavior.

### 2.3 Normative language (BCP 14)

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in BCP 14 when, and only when, they appear in all capitals. [REF-01] [REF-02]

### 2.4 Compatibility policy

A conforming implementation:

- MUST tolerate unknown object members,
- MUST fail explicitly when required semantics are unsupported,
- MUST NOT reinterpret known Core fields with incompatible meaning,
- SHOULD preserve additive forward compatibility by ignoring unknown profile-specific fields unless profile processing is mandatory.

## 3. Core Design Principles

### 3.1 Stateless bootstrap, not session protocol

Core defines a stateless resolution bootstrap. It does not require session establishment, token exchange, or protocol state synchronization between client and gateway.

### 3.2 One obvious discovery location per authority

Core discovery begins at one predictable location under the publication authority: `/.well-known/api-catalog`. [REF-05] [REF-15]

### 3.3 One mandatory business operation

Core mandates one business operation: resolve a publication URL into a canonical publication record.

### 3.4 Facts in core, interpretation in profiles

Core standardizes facts and transport semantics. Legal, commercial, and policy interpretation layers belong in optional profiles.

### 3.5 Publisher authority over byte delivery

The gateway resolves and describes. Publisher-controlled origins deliver representation bytes.

### 3.6 Mandatory integrity before optional audit

Representation digests are required in Core so a client can verify retrieved bytes. Richer audit systems remain optional profile concerns.

### 3.7 Profiles may extend, but not redefine core semantics

Profiles MAY add endpoints, fields, and procedures. Profiles MUST NOT alter the required meaning of `canonical_url`, `status`, `representations`, `links`, or Core discovery/resolve behavior.

## 4. Scope and Non-Goals

### 4.1 In scope

OpenPOG Core standardizes:

- well-known discovery for OpenPOG participation,
- API catalog-based endpoint discovery,
- one mandatory resolve operation,
- canonical publication record shape,
- representation metadata with integrity digests,
- typed outbound link publication,
- minimal error and conformance behavior.

### 4.2 Explicit non-goals

OpenPOG Core explicitly does not standardize:

- referral authentication workflows,
- signed receipts and settlement records,
- policy enforcement adjudication,
- delegated origin authorization,
- federated trust graph exchange,
- payment and entitlement protocols.

### 4.3 Deferred concerns reserved for profiles

The following concerns are reserved for optional profiles:

- search and ranking,
- batch resolution,
- referral and attribution telemetry,
- policy condition execution,
- ingestion pipelines,
- federation and trust overlays,
- payment and entitlement execution.

## 5. Terminology

### 5.1 Publication URL

A public absolute HTTP(S) URL supplied by a client as the resolution input.

### 5.2 Authority

The URI authority component (host plus optional port) derived from the input URL. [REF-03]

### 5.3 Gateway

An OpenPOG implementation that exposes discovery and resolve behavior.

### 5.4 Canonical URL

The stable, normalized public identifier for a publication record within a gateway. In Core, `canonical_url` identity scope is gateway-local; cross-gateway string equality is not proof of shared identity unless a profile defines that semantics.

### 5.5 Publication Record

The canonical machine-readable JSON object returned by the resolve operation.

### 5.6 Representation

A concrete retrievable form of a publication, identified by `origin_url`, `media_type`, and `digests`.

### 5.7 Origin URL

The publisher-controlled URL from which representation bytes are retrieved.

### 5.8 Digests

A mapping from digest algorithm keys to base64-encoded values used to verify representation bytes. `sha-256` is mandatory in Core.

### 5.9 Typed Link

A link object containing a relation type (`rel`) and target URI (`href`), optionally with metadata.

### 5.10 Profile

An optional extension contract identified by a URI that adds semantics without redefining Core.

## 6. Architectural Model

### 6.1 Core actors

Core defines three actors:

- Publisher: controls publication bytes and canonical metadata authority.
- Gateway: resolves input URLs to canonical records.
- Client: discovers, resolves, fetches bytes, and verifies integrity.

### 6.2 Core interaction model

A typical Core flow is:

1. Client derives authority from the input publication URL.
2. Client discovers OpenPOG via `https://{authority}/.well-known/api-catalog`.
3. Client locates the resolve endpoint in discovery metadata.
4. Client calls resolve with the publication URL.
5. Gateway returns a publication record.
6. Client selects a representation and fetches bytes from `origin_url`.
7. Client verifies bytes against a supported entry in `digests` before use.

### 6.3 Trust boundaries

Core separates trust surfaces:

- Gateway trust: correctness of metadata mapping and declared digests.
- Origin trust: byte delivery and access policy enforcement.
- Client trust decision: whether to accept a gateway and publisher authority chain.

### 6.4 Publisher responsibilities

A participating publisher or publisher-designated operator:

- MUST maintain authoritative mapping to canonical publication identity,
- MUST ensure `origin_url` targets remain publisher-controlled,
- MUST publish accurate digest entries for each representation,
- SHOULD maintain stable canonical identifiers over time.

### 6.5 Gateway responsibilities

A conforming gateway:

- MUST implement Core discovery and resolve behavior,
- MUST enforce Core record constraints,
- MUST return deterministic results for deterministic inputs,
- MUST avoid acting as a byte-serving proxy in Core mode.

### 6.6 Client responsibilities

A conforming client:

- MUST perform discovery before assuming endpoint locations,
- MUST send valid resolution inputs,
- MUST verify representation digests before trust-sensitive use,
- MUST treat unknown extension fields as non-fatal unless required by profile.

## 7. Discovery

### 7.1 Discovery entry point

For a participating authority, Core discovery starts at:

`GET https://{authority}/.well-known/api-catalog`

where `{authority}` is derived from the publication URL input.

### 7.2 Use of `/.well-known/`

Core discovery uses the IETF well-known URI mechanism and therefore inherits its general rules for stability, naming, and authority scoping. [REF-05]

### 7.3 Use of `api-catalog`

Core discovery uses the `api-catalog` well-known resource and relation defined for API discovery. [REF-15]

### 7.4 Required discovery document contents

At `/.well-known/api-catalog`, a conforming gateway MUST resolve HTTPS `GET` and `HEAD` requests. `GET` responses MUST expose an RFC 9727-compatible API catalog and MUST provide enough information for a client to locate the resolve endpoint. `HEAD` responses MUST include at minimum a `Link` header with relation `api-catalog` as required by RFC 9727, and in addition MUST include a `Link` header with the `urn:openpog:rel:resolve` relation plus a `Link` header with relation `profile` advertising `urn:openpog:core:v1`. [REF-15]

A conforming OpenPOG discovery document MUST provide:

- exactly one applicable link relation `urn:openpog:rel:resolve` with an absolute HTTPS URL target for the authority selected by `anchor`,
- one link relation `profile` in the selected entry that includes `urn:openpog:core:v1`,
- optional `service-desc` and `service-meta` links for additional documentation. [REF-25]

When multiple linkset entries are present, clients MUST use `anchor` for authority-scoped selection and apply only the single `urn:openpog:rel:resolve` target from the matching entry. The Core profile requirement above applies to that same selected entry.

Discovery profile signaling in this section is representation-level metadata for the discovery document (or the discovery-document URI when links are explicitly anchored there), and MUST NOT be interpreted as an authority identity claim.

### 7.5 Optional companion capability document

A gateway MAY provide a capability document referenced by `service-meta` with machine-readable details such as:

- supported Core version,
- optional profiles,
- endpoint rate limit hints,
- implementation metadata useful for diagnostics.

If present, it MUST be treated as descriptive and MUST NOT contradict Core-mandated behavior.

### 7.6 Media types for discovery

A conforming gateway MUST support `application/linkset+json` for discovery. The discovery Linkset SHOULD include the RFC 9727 profile URI (`https://www.rfc-editor.org/info/rfc9727`) in the `Content-Type` `profile` parameter, and the gateway MAY offer additional representations via content negotiation. The media-type `profile` parameter here is a discovery-document representation convention (document profile) and is not a substitute for OpenPOG protocol-profile signaling via `Link rel="profile"` and payload `profiles`. [REF-15] [REF-16] [REF-04] [REF-17]

### 7.7 Caching expectations for discovery documents

Gateways SHOULD send explicit caching metadata (`Cache-Control`, `ETag`, and optionally `Last-Modified`) for discovery responses. Clients MAY cache discovery responses according to HTTP caching rules. [REF-13]

### 7.8 Discovery failure behavior

If discovery fails due to network error, malformed payload, or missing required relation entries, clients MUST fail closed for Core resolution and treat the authority as non-participating for that attempt.

### 7.9 Discovery redirect trust rules

Clients MUST NOT automatically trust cross-host redirects for discovery. A client MAY follow a cross-host redirect only under explicit local policy.

## 8. Core API Surface

### 8.1 Overview

Core defines one mandatory business endpoint:

`GET /v1/resolve?url={publication_url}`

The absolute endpoint URL is obtained from discovery (`urn:openpog:rel:resolve`).

### 8.2 Required endpoint: `GET /v1/resolve?url={publication_url}`

A conforming gateway MUST expose one resolve endpoint that accepts a single `url` query parameter and returns exactly one publication record on success.

### 8.3 Request format

Request requirements:

- Query parameter `url` is REQUIRED.
- `url` MUST carry exactly one absolute URI as its percent-encoded textual value. [REF-03]
- Core resolve supports only `http` and `https` URI schemes; other absolute schemes MUST be rejected with `422` as defined in Section 8.6.
- A request with multiple `url` parameters MUST be rejected as invalid.
- Clients SHOULD send `Accept: application/json`.

### 8.4 Success response

On normal success, gateway MUST return:

- HTTP status `200 OK`,
- `Content-Type: application/json`,
- a valid Core Publication Record.

If conditional headers are satisfied, gateway MUST return `304 Not Modified` with no response body.

### 8.5 Not found behavior

If no matching publication identity exists, gateway MUST return `404 Not Found` and SHOULD return an RFC 9457 problem details payload. Known lifecycle states are encoded in `200` publication-record responses (`status="gone"` or `status="unknown"` when such records exist), and Core resolve does not use `410 Gone` to encode lifecycle state. [REF-09]

### 8.6 Validation errors

Gateway MUST return:

- `400 Bad Request` for malformed inputs (missing/invalid `url` parameter),
- `422 Unprocessable Content` for syntactically valid but unsupported input classes.

Malformed means request-shape or parse failures (for example missing or duplicate `url`, URL parse failure, or non-absolute URL); `422` applies to inputs that are syntactically valid and absolute but unsupported (for example an absolute URI with a non-HTTP(S) scheme).

### 8.7 Optional headers

Gateway MAY support and clients MAY use standard HTTP headers including:

- `If-None-Match`,
- `If-Modified-Since`,
- `Accept`.

Conditional behavior MUST follow HTTP semantics: satisfied validators (for example `If-None-Match` or `If-Modified-Since`) MUST return `304 Not Modified` with no body; otherwise normal success remains `200` with a publication record. [REF-04] [REF-13]

### 8.8 Method constraints

Only `GET` is required by Core for resolution. A gateway SHOULD return `405 Method Not Allowed` for unsupported methods and SHOULD advertise allowed methods via `Allow`.

Because the full publication URL is carried in the request target, deployments should expect request logging exposure and possible length limits. Operational environments with long URLs or stricter privacy requirements will likely require a POST profile.

## 9. Resolution Semantics

### 9.1 Input URL requirements

The `url` query parameter constraints are defined in Section 8.3. This section defines resolution-specific behavior applied after parameter validation.

Fragments, if provided, MUST be removed before identity matching because they do not identify retrievable HTTP resources. [REF-03]

### 9.2 Canonicalization rules

For identity matching, gateways MUST apply the following baseline normalization:

- lowercase URI scheme,
- lowercase host,
- remove default port (`:80` for HTTP, `:443` for HTTPS),
- normalize empty path to `/`.

This baseline normalization is intentionally minimal. It does not, by itself, imply full URI equivalence. Baseline canonicalization is deterministic within one gateway, but it does not guarantee cross-gateway canonical equivalence unless gateways apply the same stronger normalization policy. In Core, cross-gateway `canonical_url` string equality alone is not proof of shared identity unless a profile defines that semantics. Any stronger normalization, including percent-encoding normalization or dot-segment removal, is optional and is governed by Section 9.7. [REF-03]

### 9.3 Match behavior

Resolution MUST be deterministic. For the same normalized input and stable index state, a gateway MUST return the same canonical record identity.

### 9.4 Exact and normalized matches

Gateway SHOULD first attempt exact stored identity match and then normalized match. If both produce candidates, the gateway MUST return the candidate bound to the canonical normalized identity and MUST NOT produce ambiguous multi-record responses in Core.

### 9.5 Unknown resources

If no publication record (including placeholder/tombstone records) is known, gateway MUST return `404`. Gateway MAY return `200` with `status="unknown"` only when an explicit known placeholder/tombstone record exists in its authority model.

### 9.6 No heuristic query stripping by default

Gateways MUST NOT heuristically strip or reorder query parameters by default.

### 9.7 Documentation requirements for stronger normalization

A gateway MAY implement stronger normalization rules only when:

- rules are explicitly documented,
- rules are deterministic,
- rules are demonstrably authority-correct,
- rules do not violate Core uniqueness constraints.

## 10. Publication Record Model

### 10.1 Record purpose

The publication record is the canonical machine object that carries publication identity, lifecycle state, representation options, and typed outbound metadata links.

### 10.2 Required top-level fields

A Core publication record MUST include:

- `canonical_url` (string, absolute HTTP(S) URI),
- `status` (string, Core lifecycle state),
- `representations` (array, possibly empty for non-active states),
- `links` (array, possibly empty).

### 10.3 Optional top-level fields

Core-defined optional fields:

- `id` (gateway-local stable identifier),
- `updated_at` (RFC 3339 timestamp [REF-11]),
- `profiles` (array of profile URIs),
- `publisher` (object with optional descriptive metadata).

### 10.4 Field: `canonical_url`

`canonical_url` is the public identity anchor within a gateway and is gateway-scoped in Core.

Requirements:

- MUST be absolute HTTP(S),
- MUST be stable for the life of the record,
- MUST be unique within the gateway among active records, after normalization,
- MUST NOT be omitted.

Typed links (including `cite-as` and `canonical`) are citation/discovery metadata and MUST NOT redefine record identity.

### 10.5 Field: `status`

`status` indicates lifecycle state and processing expectations. Core states are defined in Section 11.

### 10.6 Field: `representations`

`representations` lists retrievable publication forms. Each representation MUST satisfy Section 12 constraints.

### 10.7 Field: `links`

`links` is an array of typed link objects:

- `rel` (required): registered relation token or extension relation URI,
- `href` (required): absolute URI,
- `type` (optional): media type,
- `title` (optional): human-readable label,
- `hreflang` (optional): language tag (BCP 47 / RFC 5646). [REF-23]

Relation and link syntax requirements follow Web Linking and IANA relation registry rules. [REF-06] [REF-10]

### 10.8 Unknown field tolerance

Clients MUST ignore unknown top-level fields and unknown link object members unless a required profile mandates stricter behavior.

### 10.9 Record example

```json
{
  "canonical_url": "https://publisher.example/articles/2026/openpog-core",
  "status": "active",
  "representations": [
    {
      "id": "html-main",
      "origin_url": "https://publisher.example/articles/2026/openpog-core",
      "media_type": "text/html",
      "digests": {
        "sha-256": "R2aZ6W5r4fMZ29cN2A9q8h20V6uYQxV3Ko4E9taQfEA="
      }
    }
  ],
  "links": [
    {
      "rel": "cite-as",
      "href": "https://publisher.example/articles/2026/openpog-core"
    },
    {
      "rel": "license",
      "href": "https://publisher.example/policy/license"
    }
  ],
  "updated_at": "2026-03-01T12:30:00Z"
}
```

## 11. Lifecycle States

### 11.1 Allowed core states

Core reserves three state tokens:

- `active`
- `gone`
- `unknown`

### 11.2 `active`

`active` means the publication record is currently valid for normal resolution. An active record MUST include at least one representation.

### 11.3 `gone`

`gone` means the publication is intentionally no longer available as a normal active resource. Gateway MAY keep metadata discoverable for historical or citation continuity. When a known record is `gone`, resolve MUST return `200` with `status="gone"`.

### 11.4 `unknown`

`unknown` means the gateway cannot currently establish authoritative state for the identity, or holds an explicit placeholder without active metadata. In wire responses, a known explicit placeholder/tombstone record with `status="unknown"` resolves as `200`; if no record is known, the gateway MUST return `404` per Section 9.5. Records with `status="unknown"` MUST set `representations` to an empty array.

### 11.5 State transition guidance

Recommended transitions:

- `active` to `gone` when publication is intentionally retired.
- `active` to `unknown` for unresolved authority disruptions.
- `unknown` to `active` when authoritative metadata is restored.
- `gone` to `active` only when authority explicitly reactivates the identity.

### 11.6 Backward-compatible state extension rules

Profiles MAY define additional states only as URI-form identifiers. Clients that do not understand an extension state MUST treat it as semantically equivalent to `unknown` for safety.

## 12. Representation Model

### 12.1 Representation purpose

A representation describes one retrievable byte form of a publication and the integrity metadata needed to validate retrieval.

### 12.2 Required representation fields

Each representation MUST include:

- `id`,
- `origin_url`,
- `media_type`,
- `digests`.

### 12.3 Field: `id`

`id` is a gateway-local representation identifier.

Requirements:

- MUST be non-empty,
- MUST be unique within one publication record,
- SHOULD be stable across non-breaking metadata updates.

### 12.4 Field: `origin_url`

`origin_url` is the retrieval URL for representation bytes.

Requirements:

- MUST be an absolute HTTPS URI,
- MUST be publisher-controlled.

For Core publisher-control authority constraints, an `origin_url` is considered publisher-controlled only when its authority is either:

- identical to the authority of `canonical_url`,
- within the same registrable domain as `canonical_url` (i.e., sharing the same public-suffix-plus-one domain as determined by the WHATWG URL Standard and the Public Suffix List). [REF-29] [REF-30]

This same-authority/same-registrable-domain rule is an interoperability hostname policy for Core; it is not cryptographic or legal proof of control.

Delegated origin authorization is out of scope for Core. Any broader delegated-origin trust model MUST be defined by a profile before clients rely on it.

`origin_url` is required to be HTTPS (not HTTP) to ensure representation bytes are delivered over authenticated, encrypted transport, which is a prerequisite for meaningful integrity verification. [REF-04]

Clients MUST treat only the first two cases as conforming unless a profile explicitly defines delegated-origin evaluation.

### 12.5 Field: `media_type`

`media_type` identifies expected representation format.

Requirements:

- MUST be syntactically valid per HTTP semantics,
- SHOULD use registered media types where possible. [REF-04] [REF-14]

### 12.6 Field: `digests`

`digests` is an object whose member names are digest algorithm keys and whose values are base64-encoded digest values. The `sha-256` member is mandatory in Core.

Requirements are defined in Section 13.

### 12.7 Optional representation metadata

Core optional representation metadata:

- `language` (BCP 47/RFC 5646 tag),
- `content_length` (non-negative integer octet count of the complete representation byte sequence used for Section 13 verification),
- `last_modified` (RFC 3339 timestamp [REF-11]),
- `profiles` (array of profile URIs).

If present, `content_length` MUST exactly match that complete representation octet count.

### 12.8 Representation selection by client

Client representation selection is client policy. A client SHOULD:

- filter by acceptable media types,
- prefer highest-trust origin and policy fit,
- verify `sha-256` at minimum before consuming bytes.

### 12.9 Representation example

```json
{
  "id": "pdf-main",
  "origin_url": "https://publisher.example/articles/2026/openpog-core.pdf",
  "media_type": "application/pdf",
  "digests": {
    "sha-256": "c1u7xQ9mUQGg4n8qvJv+za0v7C3acPjzQkDboSWvxfo="
  },
  "language": "en",
  "content_length": 582123,
  "last_modified": "2026-03-01T12:00:00Z"
}
```

## 13. Integrity Verification

### 13.1 Digests requirement

Each representation MUST include a `digests` object. The object MUST contain `sha-256`. It MAY contain additional digest entries.

### 13.2 Digests format

Digest requirements:

- member names MUST be valid algorithm keys from the HTTP Digest Fields registry,
- gateways and clients MUST support `sha-256`,
- each member value MUST be RFC 4648 base64 encoding of raw digest bytes,
- `digests` is a JSON member mapping in Core (not an RFC 8941 Structured Field wire serialization), but algorithm identifiers and digest semantics SHOULD align with HTTP Digest Fields terminology. [REF-19] [REF-31] [REF-12] [REF-26]

### 13.3 Client verification procedure

For each selected representation, the client MUST:

1. Retrieve the complete representation octet sequence from `origin_url`.
2. Select a supported digest algorithm from `digests` (at minimum `sha-256`).
3. Compute the digest using that algorithm.
4. Base64-encode the computed bytes.
5. Compare against the declared `digests` entry for that algorithm.
6. Treat the representation as valid only on exact match.

Core verification is defined over the complete representation octet sequence; `206 Partial Content` responses are non-verifiable in Core unless a profile defines partial-verification semantics.

### 13.4 Verification failure handling

On mismatch, client MUST treat that representation as unverified and MUST NOT use it for trust-sensitive processing. Client MAY attempt another representation if another supported digest entry or representation is available.

### 13.5 Security limitations

Digest verification does not provide:

- legal authorization,
- publisher identity attestation by itself,
- freshness guarantees beyond fetched bytes.

Digest only proves byte equality against metadata claims if metadata channel is trusted.

### 13.6 Relationship between verification and trust

Digest verification is necessary but not sufficient for trust. End-to-end trust also depends on secure transport, correct authority mapping, and client trust policy.

## 14. Typed Links and Policy Pointers

### 14.1 Purpose of typed links

Typed links provide portable pointers to citation, policy, and descriptive resources without embedding legal interpretation into Core.

### 14.2 Recommended relations

Core recommends these relation types when available:

- `license`
- `terms-of-service`
- `cite-as`
- `describedby`
- `canonical`
- `privacy-policy`

Relation names should come from the IANA Link Relation Types registry when possible. `canonical` is defined by RFC 6596. [REF-07] `terms-of-service` and `privacy-policy` are defined by RFC 6903. [REF-10] [REF-24]

### 14.3 `license`

Use `license` to point to publisher license terms for the publication or representation context.

### 14.4 `terms-of-service`

Use `terms-of-service` to point to applicable terms for service use and access conditions.

### 14.5 `cite-as`

Use `cite-as` to provide preferred citation URI when it differs from or refines default citation behavior. `cite-as` is citation metadata and MUST NOT change `canonical_url` semantics or resolve behavior. [REF-08]

### 14.6 `describedby`

Use `describedby` to link to descriptive metadata resources (for example JSON-LD, BibTeX, Crossref metadata, or documentation).

### 14.7 Link relation extensibility

If no registered relation applies, producers MAY use extension relation URIs as allowed by Web Linking. [REF-06]

### 14.8 Core rule: links are discoverability, not adjudication

Core links are discoverability pointers. They do not, by themselves, adjudicate legality or policy outcomes.

## 15. Media Types, Profiles, and Schemas

### 15.1 JSON as the base encoding

Core request and response payloads use JSON encoded as UTF-8. [REF-18]

### 15.2 JSON Schema requirement

Core documents and payloads SHOULD be machine-validated with JSON Schema Draft 2020-12. Implementations SHOULD expose schemas for discovery and publication record documents. Appendix A schemas are minimal structural validators for Core payloads. They do not, by themselves, prove full conformance to all normative requirements in this specification, especially registry-bound values, trust-boundary checks, and publisher-control assertions. When using the URN-based schema identifiers in Appendix A, implementations SHOULD bundle or pre-register the schema set used for validation. [REF-20] [REF-21]

### 15.3 Schema versioning

Schema identifiers (`$id`) MUST be stable within a Core minor version. Breaking schema changes require a Core major version increment.

### 15.4 Profile identifiers

Profile identifiers MUST be URIs. Implementations MAY advertise OpenPOG protocol profiles in payload `profiles` fields and `Link` headers with relation `profile`. OpenPOG protocol-profile identifiers advertise applicable processing semantics and are not a version-negotiation mechanism. The media-type `profile` parameter used for discovery representations in Section 7.6 is a document-profile signal and is not a substitute for protocol-profile signaling. [REF-17]

### 15.5 Extension discovery

Extensions SHOULD be discoverable via:

- `profiles` arrays in responses,
- `Link: <profile-uri>; rel="profile"` headers,
- optional capability documents referenced from discovery.

### 15.6 Unknown profile behavior

If a profile is unknown:

- clients MUST continue Core processing when possible,
- clients MUST fail explicitly if profile support is required for correctness.

## 16. Error Model

### 16.1 General principles

Core errors SHOULD be explicit, machine-readable, and safe-by-default.

### 16.2 Invalid request

Invalid request errors (for example missing `url` or malformed URI) MUST return `400`. Gateways SHOULD return problem details.

### 16.3 Unsupported input

Supported syntax but unsupported semantics (for example an absolute non-HTTP(S) URI) MUST return `422`. Gateways SHOULD return problem details.

### 16.4 Not found

Unknown publication identity MUST return `404` per Section 8.5.

### 16.5 Server failure

Gateway-side failures SHOULD return `5xx` with opaque but diagnosable problem details.

### 16.6 Problem details compatibility

Error payloads SHOULD use `application/problem+json` as defined by RFC 9457. [REF-09]

### 16.7 Error payload examples

The `instance` field in these examples uses UUID URN notation as defined in RFC 9562. [REF-27]

```json
{
  "type": "https://openpog.org/problems/invalid-request",
  "title": "Invalid request",
  "status": 400,
  "detail": "Query parameter 'url' is required and must be an absolute URI.",
  "instance": "urn:uuid:7f8ad5e5-f2c2-46e7-8e53-e2638fb4aa4f"
}
```

```json
{
  "type": "https://openpog.org/problems/not-found",
  "title": "Publication not found",
  "status": 404,
  "detail": "No publication identity matched the supplied URL.",
  "instance": "urn:uuid:c266d253-f8ca-4f88-93e8-f9a6e8f6da89"
}
```

## 17. Security Considerations

### 17.1 No byte proxying in core

Core gateways MUST NOT proxy publisher bytes in Core mode. This reduces gateway attack surface and preserves publisher delivery authority.

### 17.2 Redirect target safety

If a client follows redirects during origin retrieval, each redirect hop MUST remain HTTPS, MUST remain publisher-controlled under Section 12.4 constraints, MUST avoid unsafe open redirect behavior, and MUST NOT expand trust beyond that publisher-control boundary. Clients SHOULD enforce bounded redirect depth with a RECOMMENDED limit of 5 hops.

### 17.3 Origin URL trust boundaries

Clients SHOULD treat `origin_url` and any redirect target as untrusted until transport, authority, and digest verification checks pass. Digest verification MUST be performed against the final response body after redirects.

### 17.4 Digest misuse and mismatch handling

Implementations MUST treat digest mismatches as integrity failures and SHOULD instrument security monitoring for repeated mismatches.

### 17.5 Metadata integrity assumptions

Core relies on secure transport and trustworthy gateway metadata publication. Digest verification does not compensate for fully compromised metadata channels.

### 17.6 Abuse, rate control, and operational safeguards

Gateways SHOULD implement abuse controls such as:

- request rate limiting,
- anomaly detection,
- cache hardening,
- bounded error verbosity.

## 18. Privacy Considerations

### 18.1 Discovery as a public surface

Discovery endpoints are generally public and can reveal that an authority participates in OpenPOG.

### 18.2 Resolution request observability

Gateway operators can observe resolve requests. Clients SHOULD avoid transmitting unnecessary identifiers in request parameters or headers.

### 18.3 Client minimization guidance

Clients SHOULD minimize personally identifiable metadata in resolution flows and SHOULD avoid coupling user identifiers to publication URL resolution unless necessary.

### 18.4 Logging considerations

Operators SHOULD:

- log only what is operationally necessary,
- define clear retention limits,
- protect logs as sensitive operational data.

### 18.5 Publisher visibility boundaries

Publishers observe direct byte retrieval from `origin_url`. Gateways observe resolve traffic. Core does not hide this split visibility model.

## 19. Conformance

### 19.1 Core Gateway conformance

A conforming Core Gateway implementation MUST:

- implement discovery in Section 7,
- implement resolve endpoint semantics in Section 8,
- enforce resolution and canonicalization behavior in Section 9,
- return valid publication records per Sections 10-12,
- include representation digests and support `sha-256`,
- expose safe, machine-readable errors.

### 19.2 Core Client conformance

A conforming Core Client implementation MUST:

- perform authority-scoped discovery,
- send valid resolve requests,
- process Core publication records,
- verify representation digests before trust-sensitive use,
- fail closed on discovery failures and reject cross-host discovery redirects unless explicit local policy allows,
- treat `206 Partial Content` retrievals as non-verifiable in Core unless a profile defines partial-verification semantics,
- tolerate unknown extension fields.

### 19.3 Optional Publisher metadata guidance

Publishers are not required to run gateways directly, but participating publisher metadata providers SHOULD:

- maintain canonical identity consistency,
- keep representation metadata current,
- publish stable policy and citation links.

### 19.4 Conformance claims

Conformance claims SHOULD identify role and version, for example:

- `OpenPOG-Core/1.0 Gateway`
- `OpenPOG-Core/1.0 Client`

### 19.5 Forward compatibility obligations

Conforming implementations MUST:

- preserve unknown fields,
- avoid assuming exhaustive enums for extension-capable fields,
- fail explicitly when required profile semantics are unavailable.

## 20. Profiles Framework

### 20.1 Profiles overview

Profiles extend Core with additional behavior while preserving Core baseline interoperability.

### 20.2 Core versus profile boundary

Core defines mandatory baseline semantics. Profiles define optional semantics on top of that baseline.

### 20.3 Profile declaration rules

A profile declaration MUST use a URI identifier and SHOULD be discoverable through payload and/or `Link rel="profile"` signaling. [REF-03] [REF-06] [REF-17]

### 20.4 Conflict rules

If a profile conflicts with Core, Core wins. Non-conforming profiles are outside OpenPOG Core conformance.

### 20.5 Profile registry guidance

Profile ecosystems SHOULD maintain a public registry documenting:

- profile URI,
- versioning model,
- compatibility notes,
- reference specification location.

Where possible, profile registries SHOULD follow the profile URI registry guidance in RFC 7284. [REF-32]

## 21. Reserved Optional Profiles (Non-Core)

### 21.1 Search profile

Reserved for query and ranking interfaces.

### 21.2 Batch resolution profile

Reserved for multi-URL resolution transactions.

### 21.3 Referral profile

Reserved for referral signaling and optional attribution pathways.

### 21.4 Audit profile

Reserved for audit evidence structures and verification trails.

### 21.5 Policy profile

Reserved for richer machine-executable policy conditions.

### 21.6 Ingestion profile

Reserved for publisher-to-gateway ingestion and synchronization contracts.

### 21.7 Federation profile

Reserved for cross-gateway trust, peering, and routing semantics.

### 21.8 Payment and entitlement profile

Reserved for settlement, billing, and entitlement workflows.

## 22. Relationship to Existing Web Standards

### 22.1 Well-known URIs

Core uses the well-known URI mechanism for predictable bootstrap discovery. [REF-05]

### 22.2 `api-catalog`

Core uses `api-catalog` for API discovery instead of inventing a custom discovery protocol. [REF-15]

### 22.3 Linkset

Core discovery aligns with standalone linkset document publication in `application/linkset+json`. [REF-16]

### 22.4 Web linking

Core typed links use web linking relation semantics and relation registries. [REF-06] [REF-10]

### 22.5 JSON Schema

Core payload validation uses JSON Schema conventions for machine-checkable constraints. [REF-20] [REF-21]

### 22.6 Why OpenPOG Core adds a publication-specific contract on top of these building blocks

Existing standards provide primitives. OpenPOG Core provides composition and interoperability constraints tailored to publication resolution, representation selection, and integrity verification.

## 23. Examples

### 23.1 Minimal discovery document

```json
{
  "linkset": [
    {
      "anchor": "https://publisher.example/",
      "urn:openpog:rel:resolve": [
        {
          "href": "https://publisher.example/v1/resolve",
          "type": "application/json"
        }
      ],
      "profile": [
        {
          "href": "urn:openpog:core:v1"
        }
      ],
      "service-desc": [
        {
          "href": "https://publisher.example/v1/openapi.json",
          "type": "application/openapi+json"
        }
      ]
    }
  ]
}
```

### 23.2 Minimal resolve request

```http
GET /v1/resolve?url=https%3A%2F%2Fpublisher.example%2Farticles%2F2026%2Fopenpog-core HTTP/1.1
Host: publisher.example
Accept: application/json
```

### 23.3 Minimal resolve response

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=60

{
  "canonical_url": "https://publisher.example/articles/2026/openpog-core",
  "status": "active",
  "representations": [
    {
      "id": "html-main",
      "origin_url": "https://publisher.example/articles/2026/openpog-core",
      "media_type": "text/html",
      "digests": {
        "sha-256": "R2aZ6W5r4fMZ29cN2A9q8h20V6uYQxV3Ko4E9taQfEA="
      }
    }
  ],
  "links": []
}
```

### 23.4 Verification workflow

```text
1) Resolve publication URL through OpenPOG.
2) Select preferred representation by media type policy.
3) Fetch bytes from origin_url.
4) Compute digest with a supported key from representation.digests (at minimum sha-256).
5) Compare the computed base64 value to representation.digests[algorithm].
6) Accept bytes only if equal.
```

### 23.5 Example with typed links

```json
{
  "canonical_url": "https://publisher.example/articles/2026/openpog-core",
  "status": "active",
  "representations": [
    {
      "id": "html-main",
      "origin_url": "https://publisher.example/articles/2026/openpog-core",
      "media_type": "text/html",
      "digests": {
        "sha-256": "R2aZ6W5r4fMZ29cN2A9q8h20V6uYQxV3Ko4E9taQfEA="
      }
    }
  ],
  "links": [
    {
      "rel": "cite-as",
      "href": "https://doi.org/10.5555/openpog.core.2026"
    },
    {
      "rel": "license",
      "href": "https://publisher.example/policy/license"
    },
    {
      "rel": "terms-of-service",
      "href": "https://publisher.example/policy/terms"
    },
    {
      "rel": "describedby",
      "href": "https://publisher.example/articles/2026/openpog-core.jsonld",
      "type": "application/ld+json"
    }
  ]
}
```

## 24. IANA and Registry Considerations

### 24.1 Well-known URI considerations, if standardized formally

OpenPOG Core v1 reuses the existing `api-catalog` well-known URI and therefore does not require a new well-known URI registration in this revision. Future revisions MAY define a dedicated well-known URI if justified and standardized. Any future IANA registration work should follow current IANA policy and process guidance. [REF-15] [REF-22] [REF-33]

### 24.2 Media type considerations, if needed in a future revision

OpenPOG Core v1 uses existing media types (`application/json`, `application/linkset+json`, `application/problem+json`) and introduces no new media type registrations. [REF-18] [REF-16] [REF-09] [REF-14]

### 24.3 Relation and profile registration guidance

Relation types SHOULD use IANA registered tokens when available. Extension relations SHOULD be URI-based. Core v1 uses `urn:openpog:rel:resolve` as an extension relation URI; any future transition to a registered token or alternate relation URI MUST include explicit compatibility signaling. Profile identifiers SHOULD remain globally unique URIs and SHOULD be documented in a publicly accessible registry. [REF-03] [REF-06] [REF-10] [REF-17]

## 25. Appendix A: Minimal Structural JSON Schemas

These schemas validate the minimal structural shape of Core payloads. They are not complete conformance tests for every normative rule in this specification.

### 25.1 Discovery document schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "urn:openpog:schema:discovery:v1",
  "type": "object",
  "required": ["linkset"],
  "properties": {
    "linkset": {
      "type": "array",
      "minItems": 1,
      "items": {
        "$ref": "#/$defs/linksetEntry"
      }
    }
  },
  "$defs": {
    "linkRef": {
      "type": "object",
      "required": ["href"],
      "properties": {
        "href": {
          "type": "string",
          "format": "uri"
        },
        "type": {
          "type": "string"
        },
        "title": {
          "type": "string"
        }
      },
      "additionalProperties": true
    },
    "linkRefArray": {
      "type": "array",
      "minItems": 1,
      "items": {
        "$ref": "#/$defs/linkRef"
      }
    },
    "linksetEntry": {
      "type": "object",
      "required": ["anchor", "urn:openpog:rel:resolve", "profile"],
      "properties": {
        "anchor": {
          "type": "string",
          "format": "uri"
        },
        "urn:openpog:rel:resolve": {
          "allOf": [
            {
              "$ref": "#/$defs/linkRefArray"
            },
            {
              "maxItems": 1,
              "contains": {
                "type": "object",
                "required": ["href"],
                "properties": {
                  "href": {
                    "type": "string",
                    "pattern": "^https://"
                  }
                }
              }
            }
          ]
        },
        "profile": {
          "allOf": [
            {
              "$ref": "#/$defs/linkRefArray"
            },
            {
              "contains": {
                "type": "object",
                "required": ["href"],
                "properties": {
                  "href": {
                    "const": "urn:openpog:core:v1"
                  }
                }
              }
            }
          ]
        },
        "service-desc": {
          "$ref": "#/$defs/linkRefArray"
        },
        "service-meta": {
          "$ref": "#/$defs/linkRefArray"
        }
      },
      "additionalProperties": true
    }
  },
  "additionalProperties": true
}
```

### 25.2 Publication Record schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "urn:openpog:schema:publication-record:v1",
  "type": "object",
  "required": ["canonical_url", "status", "representations", "links"],
  "properties": {
    "id": {
      "type": "string",
      "minLength": 1
    },
    "canonical_url": {
      "type": "string",
      "format": "uri",
      "pattern": "^https?://"
    },
    "status": {
      "anyOf": [
        {
          "type": "string",
          "enum": ["active", "gone", "unknown"]
        },
        {
          "type": "string",
          "format": "uri"
        }
      ]
    },
    "representations": {
      "type": "array",
      "items": {
        "$ref": "#/$defs/representation"
      }
    },
    "links": {
      "type": "array",
      "items": {
        "$ref": "#/$defs/typedLink"
      }
    },
    "updated_at": {
      "type": "string",
      "format": "date-time"
    },
    "profiles": {
      "type": "array",
      "items": {
        "type": "string",
        "format": "uri"
      }
    },
    "publisher": {
      "type": "object",
      "additionalProperties": true
    }
  },
  "allOf": [
    {
      "if": {
        "properties": {
          "status": {
            "const": "active"
          }
        }
      },
      "then": {
        "properties": {
          "representations": {
            "minItems": 1
          }
        }
      }
    },
    {
      "if": {
        "properties": {
          "status": {
            "const": "unknown"
          }
        }
      },
      "then": {
        "properties": {
          "representations": {
            "maxItems": 0
          }
        }
      }
    }
  ],
  "$defs": {
    "digests": {
      "type": "object",
      "required": ["sha-256"],
      "properties": {
        "sha-256": {
          "type": "string",
          "pattern": "^[A-Za-z0-9+/]+={0,2}$"
        }
      },
      "patternProperties": {
        "^[a-z0-9_-]+$": {
          "type": "string",
          "pattern": "^[A-Za-z0-9+/]+={0,2}$"
        }
      },
      "additionalProperties": false
    },
    "representation": {
      "$ref": "urn:openpog:schema:representation:v1"
    },
    "typedLink": {
      "type": "object",
      "required": ["rel", "href"],
      "properties": {
        "rel": {
          "type": "string",
          "minLength": 1
        },
        "href": {
          "type": "string",
          "format": "uri"
        },
        "type": {
          "type": "string"
        },
        "title": {
          "type": "string"
        },
        "hreflang": {
          "type": "string"
        }
      },
      "additionalProperties": true
    }
  },
  "additionalProperties": true
}
```

### 25.3 Representation schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "urn:openpog:schema:representation:v1",
  "type": "object",
  "required": ["id", "origin_url", "media_type", "digests"],
  "properties": {
    "id": {
      "type": "string",
      "minLength": 1
    },
    "origin_url": {
      "type": "string",
      "format": "uri",
      "pattern": "^https://"
    },
    "media_type": {
      "type": "string",
      "minLength": 1
    },
    "digests": {
      "type": "object",
      "required": ["sha-256"],
      "properties": {
        "sha-256": {
          "type": "string",
          "pattern": "^[A-Za-z0-9+/]+={0,2}$"
        }
      },
      "patternProperties": {
        "^[a-z0-9_-]+$": {
          "type": "string",
          "pattern": "^[A-Za-z0-9+/]+={0,2}$"
        }
      },
      "additionalProperties": false
    },
    "language": {
      "type": "string"
    },
    "content_length": {
      "type": "integer",
      "minimum": 0
    },
    "last_modified": {
      "type": "string",
      "format": "date-time"
    },
    "profiles": {
      "type": "array",
      "items": {
        "type": "string",
        "format": "uri"
      }
    }
  },
  "additionalProperties": true
}
```

## 26. Appendix B: Implementation Guidance

### 26.1 Generic client implementation notes

A robust client implementation should:

- cache discovery by authority,
- enforce strict URI input validation,
- implement digest verification as mandatory step,
- keep pluggable representation selection strategy,
- provide explicit failure categories for discovery, resolve, retrieval, and integrity.

### 26.2 Caching guidance

Recommended caching strategy:

- cache discovery with short-to-medium TTL,
- cache successful resolve responses with conditional revalidation,
- avoid caching integrity failures as permanent without retry policy,
- respect origin-provided cache controls during representation retrieval. [REF-13]

### 26.3 Deployment guidance for publishers

Publishers should:

- keep canonical URL policy stable,
- avoid frequent identity churn,
- automate digest generation at publish time,
- test discovery and resolve behavior in continuous integration,
- monitor integrity mismatch reports.

### 26.4 Migration guidance from broader POG deployments

Deployments migrating from broader POG variants should:

- preserve existing identifiers and representation metadata,
- map richer fields into optional profile namespaces,
- keep Core responses minimal and deterministic,
- avoid requiring non-Core auth/session workflows for Core resolve.

## 27. Appendix C: Change Log

### 27.1 Changes from earlier POG drafts

Compared with broader historical POG drafts, OpenPOG Core v1:

- narrows mandatory scope to discovery, resolve, canonical records, representations, typed links, and integrity,
- removes referral/auth/receipt/payment/federation from Core requirements,
- enforces one mandatory business operation (`resolve`),
- simplifies lifecycle to `active`, `gone`, and `unknown` in Core,
- formalizes digest-required representation verification.

### 27.2 Open questions for future profiles

Open questions for profile work include:

- Should a dedicated OpenPOG relation token for resolve be registered in IANA?
- Should profile capability negotiation be standardized beyond URI declaration?
- Should cryptographic metadata signatures be standardized as a profile layer?
- Should cross-gateway trust and replay protection be standardized in federation profiles?

## Authoritative References

- [REF-01] RFC 2119: https://www.rfc-editor.org/rfc/rfc2119
- [REF-02] RFC 8174: https://www.rfc-editor.org/rfc/rfc8174
- [REF-03] RFC 3986: https://www.rfc-editor.org/rfc/rfc3986
- [REF-04] RFC 9110: https://www.rfc-editor.org/rfc/rfc9110
- [REF-05] RFC 8615: https://www.rfc-editor.org/rfc/rfc8615
- [REF-06] RFC 8288: https://www.rfc-editor.org/rfc/rfc8288
- [REF-07] RFC 6596: https://www.rfc-editor.org/rfc/rfc6596
- [REF-08] RFC 8574: https://www.rfc-editor.org/rfc/rfc8574
- [REF-09] RFC 9457: https://www.rfc-editor.org/rfc/rfc9457
- [REF-10] IANA Link Relation Types Registry: https://www.iana.org/assignments/link-relations/link-relations.xhtml
- [REF-11] RFC 3339: https://www.rfc-editor.org/rfc/rfc3339
- [REF-12] RFC 4648: https://www.rfc-editor.org/rfc/rfc4648
- [REF-13] RFC 9111: https://www.rfc-editor.org/rfc/rfc9111
- [REF-14] RFC 6838: https://www.rfc-editor.org/rfc/rfc6838
- [REF-15] RFC 9727: https://www.rfc-editor.org/rfc/rfc9727
- [REF-16] RFC 9264: https://www.rfc-editor.org/rfc/rfc9264
- [REF-17] RFC 6906: https://www.rfc-editor.org/rfc/rfc6906
- [REF-18] RFC 8259: https://www.rfc-editor.org/rfc/rfc8259
- [REF-19] RFC 9530: https://www.rfc-editor.org/rfc/rfc9530
- [REF-20] JSON Schema Core (Draft 2020-12): https://json-schema.org/draft/2020-12/json-schema-core
- [REF-21] JSON Schema Validation (Draft 2020-12): https://json-schema.org/draft/2020-12/json-schema-validation
- [REF-22] IANA Well-Known URIs Registry: https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml
- [REF-23] RFC 5646: https://www.rfc-editor.org/rfc/rfc5646
- [REF-24] RFC 6903: https://www.rfc-editor.org/rfc/rfc6903
- [REF-25] RFC 8631: https://www.rfc-editor.org/rfc/rfc8631
- [REF-26] IANA Hash Algorithms for HTTP Digest Fields: https://www.iana.org/assignments/http-digest-hash-alg/http-digest-hash-alg.xhtml
- [REF-27] RFC 9562: https://www.rfc-editor.org/rfc/rfc9562
- [REF-28] Semantic Versioning 2.0.0 (informative): https://semver.org/
- [REF-29] WHATWG URL Standard (registrable domain): https://url.spec.whatwg.org/#host-registrable-domain
- [REF-30] Mozilla Public Suffix List: https://publicsuffix.org/list/public_suffix_list.dat
- [REF-31] RFC 8941: https://www.rfc-editor.org/rfc/rfc8941
- [REF-32] RFC 7284: https://www.rfc-editor.org/rfc/rfc7284
- [REF-33] RFC 8126: https://www.rfc-editor.org/rfc/rfc8126
