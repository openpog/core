# OpenPOG Core Specification

**Version:** 1.0.0  
**Status:** Draft  
**Date:** 2026-03-28

OpenPOG Core v1 defines a stateless, discoverable HTTP contract that resolves a public publication URL into a canonical publication record with publisher-controlled representation URLs and mandatory integrity digests for deterministic representation data.

## 1. Introduction

### 1.1 Executive Summary

Open Publication Origin Gateway (OpenPOG) Core is an open, vendor-neutral, royalty-free HTTP specification for machine-readable publication resolution.

Starting from a public publication URL, a conforming client can:

- discover a participating OpenPOG surface through a predictable well-known entry point,
- resolve the input URL into one canonical publication record,
- obtain one or more publisher-controlled representation URLs,
- inspect typed links for citation, policy, and descriptive context,
- verify deterministic selected representation data against publisher-declared integrity digests.

OpenPOG Core intentionally standardizes only the minimum interoperability seam. It does not standardize ranking, recommendation, settlement, delegated identity, cross-gateway identity equivalence, trust federation, or market design.

### 1.2 Motivation

The web already provides key primitives for discovery and linking, including well-known URIs, API catalog discovery, and standalone linkset documents. Those primitives do not define a publication-specific contract that answers one operational question end-to-end: given a publication URL, what is the canonical publication record, where are the authoritative representations, and how can a client verify integrity? [REF-05] [REF-15] [REF-16]

Without that contract, publishers and automated consumers repeatedly rebuild site-specific adapters, normalization rules, and retrieval logic. The result is integration drag, inconsistent provenance quality, brittle attribution paths, and avoidable operational overhead.

OpenPOG Core addresses that gap with a conservative design:

- deterministic discovery,
- deterministic resolution,
- publisher-controlled delivery,
- mandatory byte-level integrity declarations,
- extension points for richer concerns outside the mandatory base.

This narrowness is intentional: it allows broad adoption without forcing a single business model, legal interpretation layer, or platform dependency.

OpenPOG Core is intended to complement, not replace, existing identifier and metadata systems such as DOI, Crossref, and schema.org. Its design scope is narrower: standardize the URL-to-record resolution seam so publishers and automated consumers can reduce bilateral adapter cost without replacing existing delivery, citation, access-control, or commercial infrastructure.

### 1.3 Purpose

The purpose of OpenPOG Core is to define the smallest interoperable contract that allows any conforming client to turn a public publication URL into a machine-usable publication record with integrity-verifiable representation metadata under the trust boundaries described in Section 6.3.

OpenPOG Core exists to:

1. Standardize discovery.
2. Standardize URL-to-record resolution.
3. Standardize a minimal publication record model.
4. Preserve publisher control over byte delivery and access control.
5. Enable deterministic integrity verification by clients.
6. Lower bilateral integration cost across publishers and consumers.
7. Provide a stable base for optional profiles.

### 1.4 Design Goals

OpenPOG Core is designed to:

- require as few mandatory concepts as possible,
- reuse established standards instead of redefining them,
- keep core operations stateless and cache-friendly,
- avoid tight coupling to any single product architecture,
- minimize marginal deployment cost by avoiding mandatory settlement, federation, or key-distribution infrastructure in Core,
- fail closed by default under partial failure,
- be extensible without breaking existing conforming clients.

### 1.5 Non-Goals

OpenPOG Core does not attempt to adjudicate legal permissions, ranking or recommendation semantics, payment or entitlement exchange, delegated origin authorization flows, cross-gateway publication identity equivalence or deduplication semantics, cross-gateway trust federation, or crawl-restriction signals as rights or licensing policy. See §4.2 for the normative non-goal list.

### 1.6 Relationship to the broader OpenPOG family

OpenPOG Core is the mandatory interoperability base. Broader OpenPOG capabilities are expressed as optional profiles layered on top of Core. Profiles MAY add capabilities, but they MUST NOT weaken or redefine Core invariants.

## 2. Status, Versioning, and Normative Language

### 2.1 Specification status

This document is a draft specification intended for implementation and interoperability testing. Normative requirements in this document define conformance behavior for OpenPOG Core v1.

Advancement from Draft status is expected to be informed by public interoperability test material and at least two independent interoperable implementations.

### 2.2 Versioning policy

OpenPOG Core follows semantic versioning principles for protocol behavior; SemVer serves as informative guidance in this specification. [REF-28]

- A Core major version increment MUST precede any incompatible behavior change.
- A Core minor version increment MUST be backward compatible and additive only.
- A Core patch version increment MUST be editorial or clarifying only and MUST NOT alter required wire behavior.

### 2.3 Normative language (BCP 14)

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **NOT RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in BCP 14 when, and only when, they appear in all capitals. [REF-01] [REF-02]

### 2.4 Compatibility policy

A conforming implementation:

- MUST tolerate unknown object members,
- MUST fail explicitly when required semantics are unsupported,
- MUST NOT reinterpret known Core fields with incompatible meaning,
- MUST preserve additive forward compatibility by ignoring unknown extension-specific fields unless an extension identified in the record's `critical` array defines stricter processing.

### 2.5 Disclaimer (Non-Normative)

This specification defines interoperability and wire behavior. It does not define or adjudicate legal rights, licensing permissions, or regulatory obligations.

- OpenPOG Core does not grant rights to access, reproduce, redistribute, or otherwise use content.
- Publisher terms, contracts, and applicable law remain authoritative for content access and use.
- Implementers are responsible for legal compliance, attribution handling, and policy enforcement in their deployments.
- Draft status means normative text may still change in response to implementation and interoperability feedback.

## 3. Core Design Principles

### 3.1 Stateless bootstrap, not session protocol

Core defines a stateless resolution bootstrap. It does not require session establishment, token exchange, or protocol state synchronization between client and gateway.

### 3.2 One obvious discovery location per authority

Core discovery begins at one predictable location under the input URL's URI authority: `/.well-known/api-catalog`. [REF-05] [REF-15]

### 3.3 One mandatory operation

Core keeps the mandatory surface area to one operation: resolve a publication URL into a canonical publication record.

### 3.4 Facts in Core, interpretation in profiles

Core standardizes facts and transport semantics. Legal, commercial, and policy interpretation layers belong in optional profiles.

### 3.5 Publisher authority over byte delivery

The gateway resolves and describes. Publisher-controlled origins deliver representation bytes.

### 3.6 Mandatory integrity before optional audit

Representation digests are required in Core so a client can verify deterministic selected representation data. Variant-aware, personalized, or otherwise client-specific delivery remains a profile concern.

### 3.7 Profiles may extend, but not redefine Core semantics

Profiles MAY add endpoints, fields, and procedures. Profiles MUST NOT alter the required meaning of `canonical_url`, `status`, `representations`, `links`, or Core discovery/resolve behavior.

## 4. Scope and Non-Goals

### 4.1 In scope

OpenPOG Core standardizes:

- well-known discovery for OpenPOG participation,
- API catalog-based endpoint discovery,
- one mandatory resolve operation,
- canonical publication record shape,
- representation metadata with integrity digests,
- typed outbound link declaration,
- minimal error and conformance behavior.

### 4.2 Explicit non-goals

OpenPOG Core explicitly does not standardize:

- referral-based authentication workflows,
- signed receipts and settlement records,
- policy adjudication,
- delegated origin authorization,
- cross-gateway publication identity equivalence,
- federated trust graph exchange,
- crawl-restriction signals as rights or licensing policy,
- payment and entitlement protocols.

### 4.3 Deferred concerns reserved for profiles

The following concerns are reserved for optional profiles:

- search and ranking,
- batch resolution,
- referral and attribution telemetry,
- policy condition execution,
- ingestion pipelines,
- cross-gateway identity and equivalence signaling,
- federation and trust overlays,
- payment and entitlement execution.

## 5. Terminology

### 5.1 Publication URL

A public absolute HTTP(S) URL supplied by a client as the resolution input.

### 5.2 Authority

The URI authority component (host plus optional port) derived from the input URL. [REF-03]

### 5.3 Gateway

A server that implements the discovery and resolve operations defined in this specification on behalf of a Publisher.

### 5.4 Canonical URL

The stable, normalized public identifier for a publication record within a gateway. In Core, `canonical_url` identity scope is gateway-local. Cross-gateway publication identity equivalence is out of scope for OpenPOG Core v1, and implementations MUST NOT treat cross-gateway `canonical_url` string equality as proof of shared identity. Implementations seeking cross-gateway identity anchors SHOULD prefer explicit globally persistent identifiers surfaced through typed links such as `cite-as`. [REF-08]

### 5.5 Publication Record

The canonical machine-readable JSON object returned by the resolve operation.

### 5.6 Representation

A concrete retrievable form of a publication, identified by `origin_url`, `media_type`, and `digests`. Representation constraints are defined in Section 12.

### 5.7 Origin URL

The publisher-controlled URL from which selected representation data is retrieved.

### 5.8 Digests

A mapping from digest algorithm keys to base64-encoded values used to verify selected representation data. `sha-256` is mandatory in Core.

### 5.9 Typed Link

A link object containing a relation type (`rel`) and target URI (`href`), optionally with metadata.

### 5.10 Profile

An optional extension contract identified by a URI that adds semantics without redefining Core.

### 5.11 Critical

An array of extension identifiers that are mandatory for safe processing. Each identifier names an extension that clients MUST understand before they can safely process the record.

### 5.12 Selected Representation

The representation chosen by a client for retrieval and verification from a publication record.

### 5.13 Trust-Sensitive Use

Any downstream action that depends on verified integrity or authoritative metadata, such as rendering, indexing, redistribution, or automated reasoning.

### 5.14 Publisher-designated operator

An operator authorized by the Publisher to manage OpenPOG metadata or gateway behavior on the Publisher's behalf.

### 5.15 Wire Behavior

The observable HTTP request and response behavior mandated by this specification.

## 6. Architectural Model

### 6.1 Core actors

Core defines three actors:

- Publisher: controls publication bytes and is the authoritative source for publication metadata.
- Gateway: resolves input URLs to canonical records.
- Client: discovers, resolves, fetches bytes, and verifies integrity.

### 6.2 Core interaction model

A conforming Core flow is:

1. Client derives authority from the input publication URL.
2. Client discovers OpenPOG via `https://{authority}/.well-known/api-catalog`.
3. Client locates the resolve endpoint in the discovery document.
4. Client calls resolve with the publication URL.
5. Gateway returns a publication record.
6. Client selects a representation and fetches bytes from `origin_url`.
7. Client verifies the selected representation bytes against a supported `digests` entry, at minimum `sha-256`, before trust-sensitive use.

### 6.3 Trust boundaries

Core separates trust surfaces:

- Gateway trust: correctness of metadata mapping and declared digests.
- Origin trust: byte delivery and access policy enforcement.
- Client trust decision: whether to accept a gateway and the Publisher it claims to represent.

### 6.4 Publisher responsibilities

A participating Publisher or Publisher-designated operator:

- MUST maintain authoritative mapping to canonical publication identity,
- MUST ensure all `origin_url` values point to publisher-controlled URLs,
- MUST publish accurate digest entries for each representation,
- SHOULD maintain stable canonical identifiers over time.

### 6.5 Gateway responsibilities

A conforming gateway:

- MUST implement Core discovery and resolve behavior,
- MUST enforce Core record constraints,
- MUST return deterministic results for the same normalized input and unchanged committed index state,
- MUST treat the client-supplied publication URL as lookup input data rather than a required network fetch target for Core resolve semantics,
- MUST NOT proxy publisher bytes.

### 6.6 Client responsibilities

A conforming client:

- MUST perform discovery before assuming endpoint locations,
- MUST send valid resolution inputs as defined in Section 8.3,
- MUST verify representation digests before trust-sensitive use,
- MUST treat unknown extension fields as non-fatal unless an extension identified in the record's `critical` array defines stricter processing.

## 7. Discovery

### 7.1 Discovery entry point

For a participating authority, Core discovery starts at:

`GET https://{authority}/.well-known/api-catalog`

where `{authority}` is derived from the publication URL input.

### 7.2 Use of `/.well-known/`

Core discovery uses the IETF well-known URI mechanism and therefore inherits its general rules for stability, naming, and authority scoping. [REF-05]

### 7.3 Use of `api-catalog`

Core discovery uses the `api-catalog` well-known resource and relation defined for API discovery. [REF-15]

OpenPOG Core deliberately narrows RFC 9727's permissive trust model by requiring same-authority anchor matching (Section 7.4) and rejecting cross-host discovery redirects by default (Section 7.9). RFC 9727 allows catalogs hosted at any URI and permits redirects to other hosts; OpenPOG restricts these as safety constraints. These are OpenPOG-specific narrowings, not requirements of RFC 9727 itself.

### 7.4 Required discovery document contents

At `/.well-known/api-catalog`, a conforming gateway MUST respond to HTTPS `GET` and `HEAD` requests.

`GET` responses MUST expose an RFC 9727-structured API catalog (subject to the OpenPOG-specific trust narrowings described in Section 7.3) and MUST provide enough information for a client to locate the resolve endpoint.

`HEAD` responses MUST include at minimum a `Link` header with relation `api-catalog` as required by RFC 9727. `HEAD` responses MAY additionally include advisory OpenPOG `Link` hints (`https://openpog.org/rel/resolve` and/or `profile` advertising `https://openpog.org/core/v1`), and when such hints are present they MUST include an explicit `anchor` parameter. The `GET` discovery document is authoritative; `HEAD` hints are advisory only. [REF-15]

A conforming OpenPOG discovery document MUST provide:

- exactly one applicable link relation `https://openpog.org/rel/resolve` with an absolute HTTPS URL target for the authority selected by `anchor`,
- one link relation `profile` in the selected entry that includes `https://openpog.org/core/v1`,
- optional `service-desc` and `service-meta` links for additional documentation. [REF-25]

A conforming gateway MUST set the `anchor` of its OpenPOG linkset entry to the authority-root form `https://{authority}/` (scheme, lowercase host, optional non-default port, and exactly `/` as the path). Anchors with non-root paths MUST NOT be used for OpenPOG entries.

When multiple linkset entries are present, clients MUST select the applicable entry as follows:

1. Derive `{authority}` from the input publication URL.
2. Construct the authority-root URI `https://{authority}/` with lowercase host, default-port normalization, and exactly `/` as the path. Match only entries whose `anchor` equals this authority-root URI by exact string comparison after those normalization steps.
3. If exactly one entry matches, verify that it contains exactly one `https://openpog.org/rel/resolve` target and a `profile` relation including `https://openpog.org/core/v1`, then use that entry.
4. If zero or multiple entries match, discovery fails and clients MUST fail closed per Section 7.8.

Discovery profile signaling in this section is representation-level metadata for the discovery document (or the discovery-document URI when links are explicitly anchored there). Clients MUST NOT interpret it as an authority identity claim.

### 7.5 Optional companion capability document

A gateway MAY provide a capability document referenced by `service-meta` with machine-readable details such as:

- supported Core version,
- optional profiles,
- endpoint rate limit policy or hints,
- explicitly allowed delegated origin authorities for representation delivery,
- implementation metadata useful for diagnostics.

If present, it MAY carry descriptive metadata and, where this specification says so, explicit validation metadata. It MUST NOT contradict Core-mandated behavior. In particular, `allowed_origin_authorities` has delegated-origin validation semantics under Section 12.4, and the document SHOULD validate against the capability schema in Appendix A.4.

The capability document is authority-scoped: it describes only the discovery authority selected under Section 7.4, even when one backend platform serves multiple authorities. Multi-tenant operators MUST publish a distinct capability document for each participating authority.

When a capability document publishes delegated origin authorities, it SHOULD expose them as an `allowed_origin_authorities` array of absolute HTTPS authority-root URIs (`https://{authority}/`, including non-default port when applicable and no path beyond `/`). Clients MUST treat a non-empty `allowed_origin_authorities` array as the discovery authority's explicit and exhaustive delegated-origin allowlist for the purpose of Section 12.4. An absent or empty array means no explicit delegated-origin allowlist has been published.

### 7.6 Media types for discovery

A conforming gateway MUST support `application/linkset+json` for discovery. The discovery Linkset SHOULD include the RFC 9727 profile URI (`https://www.rfc-editor.org/info/rfc9727`) in the `Content-Type` `profile` parameter, and the gateway MAY offer additional content-negotiated serializations. The media-type `profile` parameter here is a discovery-document representation convention (document profile) and is not a substitute for OpenPOG protocol-profile signaling via `Link rel="profile"` and payload `profiles`. [REF-15] [REF-16] [REF-04] [REF-17]

### 7.7 Caching expectations for discovery documents

Gateways MUST include an explicit `Cache-Control` header in discovery responses and SHOULD also send `ETag` and optionally `Last-Modified`. Clients MAY cache discovery responses according to HTTP caching rules. [REF-13]

### 7.8 Discovery failure behavior

If discovery fails due to network error, unexpected HTTP status, malformed payload, or missing required relation entries, clients MUST fail closed for Core resolution and treat the authority as non-participating for that attempt.

### 7.9 Discovery redirect trust rules

Clients MUST NOT follow cross-host discovery redirects without explicit local policy. A client MAY follow a cross-host redirect only under explicit local policy, such as an operator-configured allowlist of trusted redirect hosts.

## 8. Core API Surface

### 8.1 Overview

Core defines one mandatory operation:

`GET {resolve_endpoint}?url=<percent-encoded publication URL>`

or

`POST {resolve_endpoint}` with JSON body `{"url":"{publication_url}"}`

`resolve_endpoint` is discovered via `https://openpog.org/rel/resolve`.

### 8.2 Required operation: `resolve`

A conforming gateway MUST expose one discovered resolve endpoint (`https://openpog.org/rel/resolve`) that supports equivalent GET and POST bindings and returns exactly one publication record payload on success. `/v1/resolve` is a common example, not a required fixed path.

### 8.3 Request format

Request requirements:

- GET requests MUST include exactly one occurrence of the `url` query parameter.
- POST requests MUST use `Content-Type: application/json` (optionally with `charset=utf-8`) and MUST include a JSON object body containing a `url` member.
- For both GET and POST, `url` MUST carry exactly one absolute HTTP(S) URI as its textual value. If a fragment component is present, gateways MUST strip it before identity matching. [REF-03]
- Core resolve supports only `http` and `https` URI schemes; other absolute schemes MUST be rejected with `400` as defined in Section 8.6.
- A GET request with multiple `url` parameters MUST be rejected as invalid.
- A POST request with malformed JSON or with a missing or non-string `url` member MUST be rejected as invalid.
- Gateways MUST ignore unknown GET query parameters beyond `url`; unknown parameters MUST NOT alter resolve behavior.
- Gateways MUST ignore unknown POST object members beyond `url`; unknown members MUST NOT alter resolve behavior.
- Clients SHOULD send `Accept: application/json`, and gateways MAY ignore `Accept` because Core resolve responses are always JSON.

All invalid-input rejections in this section return `400 Bad Request` per Section 8.6 unless Section 8.6 specifies a more specific HTTP status.

### 8.4 Success response

On normal success, the gateway MUST return:

- HTTP status `200 OK`,
- `Content-Type: application/json`,
- a valid Core Publication Record.

The gateway MUST include an explicit `Cache-Control` header on `200` resolve responses. The gateway MUST include an `ETag` header on `200` GET resolve responses to enable conditional revalidation via `If-None-Match`. Conditional request handling is defined in Section 8.7. [REF-04] [REF-13]

### 8.5 Not found behavior

If no matching publication identity exists, the gateway MUST return `404 Not Found` and SHOULD return an RFC 9457 problem details payload. Explicit placeholder records with `status="unknown"` MAY be returned as `200`; if no record is known, the gateway MUST return `404`. Known lifecycle states are encoded in `200` publication-record responses, and Core resolve does not use `410 Gone` to encode lifecycle state. This separates transport outcome from publication lifecycle: HTTP status reports request handling, while lifecycle state is carried by record `status`. [REF-09]

### 8.6 Validation errors

The gateway MUST return:

- `400 Bad Request` for all invalid inputs, whether malformed (missing/invalid `url` parameter) or syntactically valid but unsupported (for example an absolute URI with a non-HTTP(S) scheme).
- `415 Unsupported Media Type` for POST resolve requests whose `Content-Type` is not `application/json` or `application/json; charset=utf-8`.

Gateways MUST distinguish malformed from unsupported cases in the RFC 9457 problem details `type` URI and `detail` field rather than through separate HTTP status codes. [REF-09]

### 8.7 Optional headers

The gateway MAY support and clients MAY use standard HTTP headers including:

- `If-None-Match` on GET resolve requests,
- `Accept`.

Validators apply to the entire publication-record representation, including `representations`, `digests`, `links`, `profiles`, and extension members. Conditional behavior on GET MUST follow HTTP semantics: when an `If-None-Match` precondition does not match any current validator value, the gateway MUST return `200` with the updated publication record; when it does match, the gateway MUST return `304 Not Modified` with no body. POST resolve responses follow normal HTTP semantics and clients MUST NOT assume `304 Not Modified` behavior for POST. Profiles MAY define additional conditional request headers such as `If-Modified-Since` when gateways can supply a meaningful `Last-Modified`. [REF-04] [REF-13]

### 8.8 Method constraints

`GET` and `POST` are required by Core for resolution. A gateway MUST return `405 Method Not Allowed` for unsupported methods and MUST advertise allowed methods via `Allow`.

Because the full publication URL is carried in the GET request target, deployments SHOULD expect request logging exposure and possible length limits when using GET. Clients SHOULD prefer POST when URL length, request-target logging, or privacy requirements make GET unsuitable.

## 9. Resolution Semantics

### 9.1 Input URL requirements

The `url` query parameter constraints are defined in Section 8.3. This section defines resolution-specific behavior applied after parameter validation.

Core resolve is a metadata lookup operation. Gateways MUST treat the supplied publication URL as input data and MUST NOT dereference it on the network as part of Core resolve semantics.

Fragments, if provided, MUST be removed before identity matching because they do not identify retrievable HTTP resources. [REF-03]

### 9.2 Canonicalization rules

For identity matching, gateways MUST apply the following baseline normalization:

- lowercase URI scheme,
- lowercase host,
- remove default port (`:80` for HTTP, `:443` for HTTPS),
- normalize empty path to `/`.

This baseline normalization is intentionally minimal. It does not, by itself, imply full URI equivalence. Baseline canonicalization is deterministic within one gateway, but it does not guarantee cross-gateway canonical equivalence. In Core, cross-gateway `canonical_url` string equality alone is not proof of shared identity, and cross-gateway equivalence is explicitly out of scope for Core v1. Gateways MUST NOT apply percent-encoding normalization as part of baseline identity matching. Any stronger normalization, including dot-segment removal, is optional and is governed by Section 9.7. [REF-03]

### 9.3 Match behavior

Resolution MUST be deterministic. For the same normalized input and unchanged committed index state, a gateway MUST return the same canonical record identity.

### 9.4 Exact and normalized matches

The gateway SHOULD first attempt exact stored identity match and then normalized match. If both produce candidates that resolve to the same record, the gateway returns that record. If exact and normalized matches produce different records, the gateway's index is in violation of the Section 10.4 uniqueness invariant; the gateway MUST treat this as an internal consistency failure and return `500 Internal Server Error` with RFC 9457 problem details until the ambiguity is resolved.

### 9.5 Unknown resources

If no publication record is known, the gateway MUST return `404`. The gateway MAY return `200` with `status="unknown"` only when an explicit known placeholder record exists in its authority model.

### 9.6 No heuristic query stripping

Gateways MUST NOT heuristically strip or reorder query parameters.

### 9.7 Documentation requirements for stronger normalization

A gateway MAY implement stronger normalization rules only when:

- rules are explicitly documented,
- rules are deterministic,
- rules are demonstrably authority-correct,
- rules do not violate Core uniqueness constraints.

Any stronger normalization that strips or reorders query parameters MUST be documented in this section.

## 10. Publication Record Model

### 10.1 Record purpose

The publication record is the canonical machine object that carries publication identity, lifecycle state, representation options, and typed outbound metadata links.

### 10.2 Required top-level fields

A Core publication record MUST include:

- `canonical_url` (string, absolute HTTP(S) URI),
- `status` (string, Core lifecycle state),
- `representations` (array, non-empty for `active` records, MAY be non-empty for `gone` records, and MUST be empty for `unknown` records),
- `links` (array, possibly empty).

Records with `status="active"` MUST also include `updated_at`.

### 10.3 Optional top-level fields

Core-defined optional fields:

- `id` (gateway-local stable identifier; opaque to clients in Core, while gateways MAY use descriptive values for operational convenience),
- `updated_at` (RFC 3339 timestamp [REF-11]; REQUIRED when `status="active"`, OPTIONAL otherwise),
- `profiles` (array of profile URIs advertised for this record),
- `critical` (array of extension identifiers that are mandatory for safe processing; see Section 15.6),
- `publisher` (object with optional descriptive metadata; descriptive only in Core and not authoritative for identity).

When present, `updated_at` MUST be an RFC 3339 timestamp [REF-11] representing the last material change to the publication record. Any material change to `status`, `representations` (including any `digests` entries within them), `links`, `profiles`, `critical`, or other semantics visible to clients MUST change `updated_at`.

### 10.4 Field: `canonical_url`

`canonical_url` is the public identity anchor within a gateway and is gateway-scoped in Core.

Requirements:

- MUST be absolute HTTP(S),
- MUST be stable for the life of the record,
- MUST be unique within the gateway across all records (`active`, `gone`, and `unknown`) after applying at minimum the baseline normalization rules in Section 9.2 and any additional normalization rules documented under Section 9.7,
- MUST NOT be omitted.

Lifecycle changes MUST be expressed as status transitions of the same record and MUST NOT create duplicate records with the same `canonical_url`.

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
- `hreflang` (optional): language tag (RFC 5646). [REF-23]

Relation and link syntax requirements follow Web Linking and IANA relation registry rules. [REF-06] [REF-10]

### 10.8 Unknown field tolerance

Clients MUST ignore unknown top-level fields and unknown link object members unless an extension identified in the record's `critical` array defines stricter processing.

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
      "href": "https://doi.org/10.5555/openpog.core.2026"
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

### 11.1 Allowed Core states

Core reserves three state tokens:

- `active`
- `gone`
- `unknown`

### 11.2 `active`

`active` means the publication record is currently valid for normal resolution. An active record MUST include at least one representation.

### 11.3 `gone`

`gone` means the publication is intentionally no longer available as a normal active resource. The gateway MAY keep metadata discoverable for historical or citation continuity. Records with `status="gone"` MAY include representations for archival or citation continuity, but clients MUST NOT treat such representations as currently authoritative active content. Representations in `gone` records MUST satisfy all constraints in Section 12. When a known record is `gone`, resolve MUST return `200` with `status="gone"`.

### 11.4 `unknown`

`unknown` means the gateway cannot currently establish authoritative state for the identity, or holds an explicit placeholder without active metadata. In wire responses, a known explicit placeholder record with `status="unknown"` resolves as `200`; if no record is known, the gateway MUST return `404` per Section 9.5. Records with `status="unknown"` MUST set `representations` to an empty array.

### 11.5 State transition guidance

Recommended transitions:

- `active` to `gone` when publication is intentionally retired.
- `active` to `unknown` for unresolved authority disruptions.
- `unknown` to `active` when authoritative metadata is restored.
- `gone` to `active` only when authority explicitly reactivates the identity.

The reactivation mechanism is implementation-specific and outside Core; Core only requires that gateways not silently reactivate `gone` records without a deliberate administrative action.

### 11.6 Backward-compatible state extension rules

Profiles MAY define additional states only as URI-form identifiers. Clients that do not understand an extension state MUST treat it as semantically equivalent to `unknown` for safety. This downgrade favors safety over completeness: a client can suppress an otherwise usable record by treating an unfamiliar extension state as `unknown`. When a client downgrades an unrecognized extension state to `unknown`, it MUST treat `representations` as empty for resolution and trust purposes, regardless of the actual array content. However, if the extension state is defined by a profile listed in the record's `critical` field, the `critical` processing rule in Section 15.6 takes precedence and the client MUST fail explicitly rather than downgrading to `unknown`.

## 12. Representation Model

### 12.1 Representation purpose

A representation describes one retrievable byte form of a publication and the integrity metadata needed to validate retrieval.

In Core, one representation corresponds to one deterministic byte artifact under the retrieval semantics exposed for that representation. Gateways MUST NOT advertise user-specific, session-specific, A/B-tested, geography-specific, or otherwise client-variant bytes as one Core representation unless a profile defines variant-aware verification semantics.

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
- SHOULD be stable across non-breaking metadata updates. A non-breaking metadata update is one that does not change `origin_url`, `media_type`, or any `digests` value. Changes to those fields represent a new byte artifact and SHOULD be published as a new representation with a new `id`.

### 12.4 Field: `origin_url`

`origin_url` is the retrieval URL for representation bytes. Unlike `canonical_url`, `origin_url` is HTTPS-only in Core.

Requirements:

- MUST be an absolute HTTPS URI,
- MUST be publisher-controlled.

For Core publisher-control authority constraints, an `origin_url` MUST be considered publisher-controlled only when its authority is either identical to the authority of `canonical_url` or explicitly listed in the authority's `allowed_origin_authorities` capability metadata.

The same-authority case is the Core baseline. A non-empty `allowed_origin_authorities` array is exhaustive for delegated-origin validation under that discovery authority: once published, publishers MUST include all intended delegated delivery origins in the allowlist before publishing it. Clients MUST NOT treat same-registrable-domain proximity as sufficient publisher-control evidence. Implementations MAY apply stricter local policy.

Delegated origin authorization is out of scope for Core. Any broader delegated-origin trust model MUST be defined by a profile before clients rely on it.

`origin_url` is required to be HTTPS (not HTTP) to ensure representation bytes are delivered over authenticated, encrypted transport, which is a prerequisite for meaningful integrity verification. [REF-04]

Core does not define delegated user authorization. An `origin_url` MAY enforce publisher-controlled access control and MAY return `401` or `403` to clients that lack authorization; such responses do not by themselves invalidate the record, but Core verification is only possible when the client can retrieve the selected representation bytes.

Clients SHOULD treat the same-authority case and the explicit-allowlist case as the Core baseline. Clients MAY apply stricter local policy, but MUST NOT relax the publisher-control rule in this section.

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
- `last_modified` (RFC 3339 timestamp [REF-11]),
- `profiles` (array of profile URIs; descriptive only in Core and not part of `critical` processing unless a future profile explicitly defines that behavior).

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
  "last_modified": "2026-03-01T12:00:00Z"
}
```

## 13. Integrity Verification

### 13.1 Digests requirement

Each representation MUST include a `digests` object. The object MUST contain `sha-256`. It MAY contain additional digest entries.

In Core, declared digests apply to the complete deterministic selected representation data for that representation. When one publication is available in multiple stable byte variants (for example different media types or languages), each variant MUST be published as a separate representation with its own `id`, `media_type`, and `digests`.

### 13.2 Digests format

Digest requirements:

- member names MUST be lowercase and MUST exactly match valid algorithm keys from the HTTP Digest Fields registry,
- gateways and clients MUST support `sha-256`,
- each member value MUST be strict base64 as defined in RFC 4648 Section 4, with `=` padding and no whitespace or line breaks. [REF-12]
- `digests` is a JSON member mapping in Core (not an RFC 9651 Structured Field wire serialization), but algorithm identifiers and digest semantics SHOULD align with HTTP Digest Fields terminology and RFC 9651 token syntax. [REF-19] [REF-26] [REF-31]

### 13.3 Client verification procedure

For each selected representation, the client MUST:

1. Retrieve the complete selected representation data from `origin_url` as defined by RFC 9530 (the representation data of the selected representation, after any content coding has been decoded). [REF-19]
2. Verify `sha-256` at minimum; if a profile mandates a stronger algorithm and the client supports it, the client MAY also verify that algorithm.
3. Compute the digest using the selected algorithm.
4. Base64-encode the digest output bytes.
5. Compare against the declared `digests` entry for that algorithm.
6. Treat the representation as valid only on exact match.

Core verification is defined over the complete selected representation data as specified by RFC 9530; clients MUST decode any content coding (such as `gzip` or `br`) before computing the digest. Clients MUST NOT compute or compare Core digests against `206 Partial Content` response bodies. `206 Partial Content` responses are non-verifiable in Core unless a profile defines partial-verification semantics. [REF-19]

Gateways MUST NOT publish a Core representation whose bytes vary according to user identity, cookies, session state, A/B test state, geography, or other client-specific inputs unless a profile defines variant-aware verification semantics for that representation. If such caller-variant delivery exists, it is outside Core verification and MUST NOT be represented as one digest-bearing Core representation.

### 13.4 Verification failure handling

On mismatch, the client MUST treat that representation as unverified and MUST NOT use it for trust-sensitive processing. Client MAY attempt another representation if another supported digest entry or representation is available.

### 13.5 Security limitations

Digest verification does not provide:

- legal authorization,
- publisher identity attestation by itself,
- freshness guarantees beyond fetched bytes.

Digest verification proves only byte equality against declared metadata claims, and only when the metadata channel itself is trusted. It does not detect replay of stale but valid bytes.

### 13.6 Relationship between verification and trust

Digest verification is necessary but not sufficient for trust. End-to-end trust also depends on secure transport, correct authority mapping, and client trust policy.

## 14. Typed Links and Policy Pointers

### 14.1 Purpose of typed links

Typed links provide portable pointers to citation, policy, and descriptive resources without embedding legal interpretation into Core.

### 14.2 Recommended relations

When the following relations apply, producers SHOULD include them:

- `license`
- `terms-of-service`
- `cite-as`
- `describedby`
- `canonical`
- `privacy-policy`

Relation names SHOULD come from the IANA Link Relation Types registry when possible. `canonical` is defined by RFC 6596. `terms-of-service` and `privacy-policy` are defined by RFC 6903. [REF-07] [REF-10] [REF-24] `canonical` and `privacy-policy` follow their registered semantics even though Core does not define dedicated processing rules for them. The `canonical` link relation is citation metadata and MUST NOT be confused with `canonical_url` record identity.

### 14.3 `license`

Use `license` to point to publisher license terms for the publication or representation context. [REF-10]

### 14.4 `terms-of-service`

Use `terms-of-service` to point to applicable terms for service use and access conditions.

### 14.5 `cite-as`

Use `cite-as` to provide preferred citation URI when it differs from or refines default citation behavior. `cite-as` is citation metadata and MUST NOT change `canonical_url` semantics or resolve behavior. When available, globally persistent `cite-as` targets (for example DOI or Handle URIs) SHOULD be preferred for cross-gateway identity correlation, but only as correlation metadata; they do not redefine record identity. [REF-08]

### 14.6 `describedby`

Use `describedby` to link to descriptive metadata resources (for example JSON-LD, BibTeX, Crossref metadata, or documentation). [REF-10]

### 14.7 Link relation extensibility

If no registered relation applies, producers MAY use extension relation URIs as allowed by Web Linking. Clients SHOULD ignore unrecognized extension relation URIs in `links`. [REF-06]

### 14.8 Core rule: links are discoverability, not adjudication

Core links are discoverability pointers. They do not, by themselves, adjudicate legality or policy outcomes.

### 14.9 `canonical`

Use `canonical` to point to a canonical external resource when one exists. It is citation metadata and MUST NOT redefine the gateway-local `canonical_url` field.

### 14.10 `privacy-policy`

Use `privacy-policy` to point to a publisher privacy policy or equivalent notice relevant to the publication or service context.

## 15. Media Types, Profiles, and Schemas

### 15.1 JSON as the base encoding

Core JSON documents and response payloads MUST be encoded as UTF-8. [REF-18]

### 15.2 JSON Schema requirement

Core documents and payloads SHOULD be machine-validated with JSON Schema Draft 2020-12. Implementations SHOULD expose schemas for discovery, publication record, and representation documents. Appendix A schemas are minimal structural validators for Core payloads. They do not, by themselves, prove full conformance to all normative requirements in this specification, especially registry-bound values, trust-boundary checks, publisher-control assertions, and cross-field application rules. JSON Schema `format` is annotation-only unless a validator enables the format-assertion vocabulary or an equivalent application-layer check. When using the project-controlled schema identifiers in Appendix A, implementations SHOULD bundle or make the full schema set available to their validation tooling before deployment. [REF-20] [REF-21]

### 15.3 Schema versioning

Schema identifiers (`$id`) MUST be stable within a Core minor version. Breaking schema changes MUST require a Core major version increment.

### 15.4 Profile identifiers

Profile identifiers MUST be URIs. Implementations MAY advertise OpenPOG protocol profiles in payload `profiles` fields and `Link rel="profile"` headers. OpenPOG protocol-profile identifiers advertise applicable processing semantics and are not a version-negotiation mechanism; protocol version identification is instead carried by Core versioning and conformance claims (Section 19.4). The media-type `profile` parameter used for discovery representations in Section 7.6 is a document-profile signal and is not a substitute for protocol-profile signaling. [REF-17]

### 15.5 Extension discovery

Extensions SHOULD be discoverable via:

- `profiles` arrays in responses,
- `Link: <profile-uri>; rel="profile"` headers,
- optional capability documents referenced from discovery.

### 15.6 Unknown profile behavior and criticality

`profiles` identifies additional OpenPOG profiles applied to the record. `critical` identifies OpenPOG extension identifiers that are mandatory for safe processing. This separation keeps `profile` descriptive in the sense of RFC 6906 (a profile adds constraints and conventions without changing base media-type semantics), while `critical` is an OpenPOG-specific processing rule that signals must-understand extensions, similar to the `crit` header parameter in JWS (RFC 7515). [REF-17] [REF-37]

Processing rules:

- clients MAY ignore unknown URIs that appear only in `profiles`,
- clients that recognize a URI in `profiles` SHOULD apply that profile's processing rules even when the URI does not appear in `critical`,
- clients MUST fail explicitly if any URI in `critical` is unsupported,
- every URI in `critical` MUST also appear in `profiles`,
- if `critical` is non-empty, `profiles` MUST be present,
- if `critical` is absent or empty, no extension is mandatory and clients MAY safely apply Core-only processing.

An extension identifier is unsupported unless the implementation both recognizes that identifier and applies all normative processing rules it requires. Recognition alone without correct processing does not constitute support. Representation-level `profiles` arrays are descriptive only in Core; the `critical` processing rules in this section apply only to the record-level `critical` field. The Appendix A schemas are intentionally minimal and do not fully enforce the requirement that `critical` be a subset of `profiles`; implementations MUST enforce that rule at the application layer.

## 16. Error Model

### 16.1 General principles

Core errors MUST be explicit, machine-readable, and conservative in what they disclose by default. Gateways MUST NOT include internal file paths, stack traces, or implementation-specific detail in error responses returned to clients.

### 16.2 Invalid request

Invalid request errors (for example missing `url`, malformed URI, or malformed POST JSON body) MUST return `400` and MUST return RFC 9457 problem details when a body is present. POST requests with an unsupported media type MUST return `415 Unsupported Media Type` and MUST return RFC 9457 problem details when a body is present.

### 16.3 Unsupported input

Supported syntax but unsupported semantics (for example an absolute non-HTTP(S) URI) MUST return `400`. Gateways MUST distinguish this from malformed inputs using distinct stable RFC 9457 problem details `type` URIs, such as the examples in Section 16.7. [REF-09]

### 16.4 Not found

Unknown publication identity MUST return `404` per Section 8.5.

### 16.5 Server failure

Gateway-side failures SHOULD return `5xx`. When a structured body is returned, it SHOULD use problem details that are opaque to clients but sufficient for operator diagnosis.

### 16.6 Problem details compatibility

Error payloads MUST use `application/problem+json` as defined by RFC 9457 whenever a payload is returned. [REF-09]

### 16.7 Error payload examples

The `instance` field in these examples uses UUID URN notation as defined in RFC 9562. [REF-27]

```json
{
  "type": "https://openpog.org/problems/invalid-request",
  "title": "Invalid request",
  "status": 400,
  "detail": "Query parameter 'url' is required and must be an absolute HTTP(S) URI.",
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

```json
{
  "type": "https://openpog.org/problems/unsupported-scheme",
  "title": "Unsupported URI scheme",
  "status": 400,
  "detail": "The supplied URL uses scheme 'ftp', which is not supported. Core resolve accepts only 'http' and 'https'.",
  "instance": "urn:uuid:a1b2c3d4-e5f6-4890-abcd-ef1234567890"
}
```

```json
{
  "type": "https://openpog.org/problems/unsupported-method",
  "title": "Method not allowed",
  "status": 405,
  "detail": "The request method is not supported for Core resolve.",
  "instance": "urn:uuid:2d4a0d6b-bf8a-47da-8d57-8bc7dddbd7d2"
}
```

```json
{
  "type": "https://openpog.org/problems/unsupported-media-type",
  "title": "Unsupported media type",
  "status": 415,
  "detail": "POST resolve requests must use application/json.",
  "instance": "urn:uuid:9e9b4f16-0e5a-4d2d-9a9e-7a1adf6b1a58"
}
```

```json
{
  "type": "https://openpog.org/problems/rate-limited",
  "title": "Too many requests",
  "status": 429,
  "detail": "The gateway is rate limiting requests; retry later.",
  "instance": "urn:uuid:7b91f978-3c1c-4c1b-8c85-1a4370fd0f21"
}
```

## 17. Security Considerations

### 17.1 No byte proxying in Core

Core gateways MUST NOT proxy publisher bytes. This reduces gateway attack surface and preserves publisher delivery authority.

### 17.2 Redirect target safety

If a client follows redirects during origin retrieval, each redirect hop MUST remain HTTPS, MUST remain publisher-controlled under the publisher-control baseline defined in Section 12.4, MUST avoid unsafe open redirect behavior, and MUST NOT expand trust beyond that publisher-control boundary. Because `origin_url` itself is HTTPS-only in Core, HTTP-to-HTTPS upgrade redirects are outside Core. Clients SHOULD enforce bounded redirect depth with a recommended limit of 5 hops.

### 17.3 Origin URL trust boundaries

Clients SHOULD treat `origin_url` and any redirect target as untrusted until transport, authority, and digest verification checks pass. Digest verification MUST be performed against selected representation data after redirects.

### 17.4 Digest misuse and mismatch handling

Implementations MUST treat digest mismatches as integrity failures and SHOULD instrument security monitoring for repeated mismatches.

### 17.5 Metadata integrity assumptions

Core relies on secure transport and a trustworthy gateway metadata publication channel. Digest verification does not compensate for a compromised discovery or resolve channel.

In same-party deployments, where the publisher and gateway operator are the same administrative party, Core digest verification can provide meaningful byte-level integrity under that trust assumption. In third-party gateway deployments, Core alone does not remove the need to trust the gateway as a metadata authority; clients SHOULD treat the gateway as a trust anchor unless a signing profile or other documented metadata-authenticity mechanism is in use.

Deployments requiring metadata authenticity beyond transport trust SHOULD use a signing mechanism such as HTTP Message Signatures. [REF-36]

### 17.6 Abuse, rate control, and operational safeguards

Gateways MUST implement request rate limiting, cache-poisoning resistance, and bounded error verbosity. Gateways SHOULD also monitor for sustained request-rate spikes, repeated error patterns, and related anomalies and produce operational alerts.
When throttling requests, gateways SHOULD return `429 Too Many Requests` and SHOULD include `Retry-After`. Advisory rate-limit policy MAY be published in the capability document described in Section 7.5.

### 17.7 Client-supplied URL handling and SSRF resistance

Core resolve is a metadata lookup operation. Gateways MUST NOT dereference the client-supplied publication URL on the network as part of Core resolve semantics.

Implementations that perform non-Core dereferencing of client-supplied publication URLs (for example for validation, ingestion, or other local workflows) MUST apply SSRF protections, including:

- post-DNS-resolution checks for loopback, link-local, private, and other reserved address ranges,
- bounded redirect handling,
- rejection of non-HTTP(S) schemes for such dereferencing paths.

## 18. Privacy Considerations

### 18.1 Discovery as a public surface

Discovery endpoints are generally public and can reveal that an authority participates in OpenPOG.

### 18.2 Resolution request observability

Gateway operators can observe resolve requests. Clients SHOULD avoid transmitting unnecessary identifiers in request parameters or headers and SHOULD prefer POST resolve when GET request-target logging, URL length limits, or privacy requirements make GET resolution unsuitable. Choosing POST reduces URL-level logging exposure but does not prevent IP-level correlation at the gateway.

### 18.3 Client minimization guidance

Clients SHOULD minimize personally identifiable metadata in resolution flows and SHOULD avoid coupling user identifiers to publication URL resolution unless necessary.

### 18.4 Logging considerations

Operators SHOULD:

- log only what is operationally necessary,
- avoid retaining full publication URLs when redacted, truncated, or hashed forms are sufficient for operations, recognizing that truncation can still leak identifying information,
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
- include representation digests and support `sha-256` per Section 13,
- treat client-supplied publication URLs as input data rather than network fetch targets for Core resolve, and apply Section 17.7 protections to any non-Core dereferencing,
- expose safe, machine-readable errors consistent with Section 16,
- enforce `critical` and `profiles` processing rules per Section 15.6,
- apply the security constraints in Section 17.

### 19.2 Core Client conformance

A conforming Core Client implementation MUST:

- perform authority-scoped discovery per Section 7,
- send valid resolve requests per Section 8,
- process Core publication records per Sections 10-12,
- verify representation digests as defined in Section 13 for any retrieved representation and in all cases before trust-sensitive use,
- fail closed on discovery failures and MUST reject cross-host discovery redirects unless explicit local policy allows,
- treat `206 Partial Content` retrievals as non-verifiable in Core unless a profile defines partial-verification semantics,
- tolerate unknown extension fields as defined in Sections 2.4 and 10.8.

### 19.3 Optional Publisher metadata guidance

Publishers are not required to run gateways directly, but publishers and publisher-designated metadata operators SHOULD:

- maintain canonical identity consistency,
- keep representation metadata current,
- avoid publishing personalized or caller-variant origin URLs as one Core representation,
- publish stable policy and citation links.

### 19.4 Conformance claims

Conformance claims SHOULD identify role and version, for example:

- `OpenPOG-Core/1.0 Gateway`
- `OpenPOG-Core/1.0 Client`

When a public OpenPOG conformance corpus or interoperability suite is available, claims SHOULD also identify the suite or corpus version used.

### 19.5 Forward compatibility obligations

Conforming implementations MUST:

- tolerate unknown fields and preserve them when storing, forwarding, or reserializing records,
- avoid assuming exhaustive enums for extension-capable fields,
- fail explicitly when an extension identifier listed in `critical` is unsupported.

## 20. Profiles Framework

### 20.1 Profiles overview

Profiles extend Core with additional behavior while preserving Core baseline interoperability.

### 20.2 Core versus profile boundary

Core defines mandatory baseline semantics. Profiles define optional semantics on top of that baseline.

### 20.3 Profile declaration rules

A profile declaration MUST use a URI identifier and SHOULD be discoverable through payload and/or `Link rel="profile"` signaling. [REF-03] [REF-06] [REF-17]

### 20.4 Conflict rules

If a profile conflicts with Core, Core wins. For purposes of this section, a Core invariant is any requirement stated with MUST or MUST NOT in Sections 1–19. A conflict is any profile rule that weakens, contradicts, or overrides a Core invariant or mandatory behavior. Non-conforming profiles are outside OpenPOG Core conformance.

### 20.5 Profile registry guidance

Official OpenPOG profiles and project-controlled URI identifiers MUST be documented in a public registry maintained by the OpenPOG specification maintainers. Until a separate registry is established, this specification serves as the authoritative initial registry for the Core v1 identifiers it defines, including `https://openpog.org/core/v1`, `https://openpog.org/rel/resolve`, `https://openpog.org/schema/discovery/v1`, `https://openpog.org/schema/publication-record/v1`, and `https://openpog.org/schema/representation/v1`. Registry entries MUST include:

- profile URI,
- versioning model,
- compatibility notes,
- reference specification location,
- change-control contact or process.

For the Core v1 identifiers defined by this document, the initial registry entries are instantiated by this specification with the following shared metadata:

- versioning model: Core semantic versioning per Section 2.2,
- compatibility notes: compatibility, schema-versioning, and conformance requirements are defined by Sections 2, 15.3, and 19,
- reference specification location: this specification,
- change-control process: OpenPOG specification maintainers.

Until a permanent registry authority is established, third-party experimental extensions SHOULD use HTTP(S) URI identifiers under their own administrative control rather than identifiers in the project-controlled `https://openpog.org/` URI space. Unregistered use of the project-controlled `https://openpog.org/` URI space is non-conforming, and clients MUST NOT assume semantics for such identifiers unless explicit local policy or direct implementation support says otherwise.

Where possible, profile registries SHOULD follow the profile URI registry guidance in RFC 7284, the IANA Profile URIs Registry, and applicable IANA registration-process guidance. [REF-32] [REF-35] [REF-33]

## 21. Reserved Optional Profiles (Non-Core)

The entries in this section are placeholders only. They are not registered profile identifiers under Section 20.5 and MUST NOT be used as deployed profile identifiers until a separate profile specification assigns identifiers and corresponding registry metadata. Gateways MUST NOT publish `profiles` or `critical` entries using identifiers from this section.

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

Reserved for settlement, billing, and entitlement workflows. Any such profile will require strong security, integrity, anti-replay, and authorization requirements.

## 22. Relationship to Existing Web Standards

### 22.1 Well-known URIs

Core uses the well-known URI mechanism for predictable bootstrap discovery. [REF-05]

### 22.2 `api-catalog`

Core uses `api-catalog` as the bootstrap discovery mechanism and layers OpenPOG-specific constraints on top. [REF-15]

### 22.3 Linkset

Core discovery aligns with standalone linkset document publication in `application/linkset+json`. [REF-16]

### 22.4 Web linking

Core typed links use web linking relation semantics and relation registries. [REF-06] [REF-10]

### 22.5 JSON Schema

Core payload validation uses JSON Schema conventions for machine-checkable constraints. [REF-20] [REF-21]

### 22.6 Why OpenPOG Core adds a publication-specific contract on top of these building blocks

Existing standards provide primitives. OpenPOG Core uses `api-catalog` as the bootstrap discovery mechanism and adds publication-specific composition and interoperability constraints tailored to publication resolution, representation selection, and integrity verification.

## 23. Examples

### 23.1 Minimal discovery document

```json
{
  "linkset": [
    {
      "anchor": "https://publisher.example/",
      "https://openpog.org/rel/resolve": [
        {
          "href": "https://publisher.example/v1/resolve",
          "type": "application/json"
        }
      ],
      "profile": [
        {
          "href": "https://openpog.org/core/v1"
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
ETag: "record-4f8c9a"

{
  "canonical_url": "https://publisher.example/articles/2026/openpog-core",
  "status": "active",
  "updated_at": "2026-03-01T12:30:00Z",
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
4) Decode any content coding on the selected representation data before hashing.
5) Compute digest with a supported key from representation.digests (at minimum sha-256).
6) Compare the computed base64 value to representation.digests[algorithm].
7) Accept bytes only if equal.
```

### 23.5 Example with typed links

```json
{
  "canonical_url": "https://publisher.example/articles/2026/openpog-core",
  "status": "active",
  "updated_at": "2026-03-01T12:30:00Z",
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

### 24.1 Well-known URI registration considerations

OpenPOG Core v1 reuses the existing `api-catalog` well-known URI and therefore does not require a new well-known URI registration in this revision. Future revisions MAY define a dedicated well-known URI if justified and standardized. Any future IANA registration work should follow current IANA policy and process guidance. [REF-15] [REF-22] [REF-33]

### 24.2 Media type registration considerations

OpenPOG Core v1 uses existing media types (`application/json`, `application/linkset+json`, `application/problem+json`) and introduces no new media type registrations. Media-type identifiers should align with the IANA Media Types Registry. [REF-18] [REF-16] [REF-09] [REF-14] [REF-34]

### 24.3 Relation and profile registration guidance

Relation types SHOULD use IANA registered tokens when available. Extension relations MUST be URI-based. Core v1 uses `https://openpog.org/rel/resolve` as an extension relation URI; any future transition to a registered token or alternate relation URI MUST include explicit compatibility signaling. Profile identifiers SHOULD remain globally unique URIs and SHOULD be documented in a publicly accessible registry. Any future registration of a dedicated resolve relation token should follow the future-work direction in Section 27.2 and applicable registration-process guidance. [REF-03] [REF-06] [REF-10] [REF-17] [REF-33]

## 25. Appendix A: Minimal Structural JSON Schemas

These schemas validate the minimal structural shape of Core payloads. They are not complete conformance tests for every normative rule in this specification.

### 25.1 Discovery document schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://openpog.org/schema/discovery/v1",
  "type": "object",
  "required": ["linkset"],
  "properties": {
    "linkset": {
      "type": "array",
      "minItems": 1,
      "items": {
        "type": "object",
        "additionalProperties": true
      },
      "contains": {
        "$ref": "#/$defs/linksetEntry"
      },
      "minContains": 1
    }
  },
  "$defs": {
    "linkRef": {
      "type": "object",
      "required": ["href"],
      "properties": {
        "href": {
          "type": "string",
          "format": "uri",
          "pattern": "^[A-Za-z][A-Za-z0-9+.-]*:"
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
      "required": ["anchor", "https://openpog.org/rel/resolve", "profile"],
      "properties": {
        "anchor": {
          "type": "string",
          "format": "uri",
          "pattern": "^https://[^/:]+(?::[0-9]+)?/$"
        },
        "https://openpog.org/rel/resolve": {
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
                    "const": "https://openpog.org/core/v1"
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
  "$id": "https://openpog.org/schema/publication-record/v1",
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
          "format": "uri",
          "pattern": "^[A-Za-z][A-Za-z0-9+.-]*:"
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
        "format": "uri",
        "pattern": "^[A-Za-z][A-Za-z0-9+.-]*:"
      }
    },
    "critical": {
      "type": "array",
      "items": {
        "type": "string",
        "format": "uri",
        "pattern": "^[A-Za-z][A-Za-z0-9+.-]*:"
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
        "required": ["status"],
        "properties": {
          "status": {
            "const": "active"
          }
        }
      },
      "then": {
        "required": ["updated_at"],
        "properties": {
          "representations": {
            "minItems": 1
          }
        }
      }
    },
    {
      "if": {
        "required": ["status"],
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
    "representation": {
      "$ref": "https://openpog.org/schema/representation/v1"
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
          "format": "uri",
          "pattern": "^[A-Za-z][A-Za-z0-9+.-]*:"
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
  "$id": "https://openpog.org/schema/representation/v1",
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
          "pattern": "^[A-Za-z0-9+/]{43}=$"
        }
      },
      "patternProperties": {
        "^[a-z][a-z0-9-]*$": {
          "type": "string",
          "pattern": "^[A-Za-z0-9+/]+={0,2}$"
        }
      },
      "additionalProperties": false
    },
    "language": {
      "type": "string"
    },
    "last_modified": {
      "type": "string",
      "format": "date-time"
    },
    "profiles": {
      "type": "array",
      "items": {
        "type": "string",
        "format": "uri",
        "pattern": "^[A-Za-z][A-Za-z0-9+.-]*:"
      }
    }
  },
  "additionalProperties": true
}
```

### 25.4 Capability document schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://openpog.org/schema/capability/v1",
  "type": "object",
  "properties": {
    "supported_core_version": {
      "type": "string"
    },
    "supported_profiles": {
      "type": "array",
      "items": {
        "type": "string",
        "format": "uri",
        "pattern": "^[A-Za-z][A-Za-z0-9+.-]*:"
      }
    },
    "allowed_origin_authorities": {
      "type": "array",
      "items": {
        "type": "string",
        "format": "uri",
        "pattern": "^https://[^/:]+(?::[0-9]+)?/$"
      }
    },
    "rate_limit_policy": {
      "type": "object",
      "additionalProperties": true
    },
    "implementation": {
      "type": "object",
      "additionalProperties": true
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
- implement digest verification as a mandatory step,
- keep a pluggable representation selection strategy,
- provide explicit failure categories for discovery, resolve, retrieval, and integrity.

### 26.2 Caching guidance

Recommended caching strategy:

- cache discovery with a short-to-medium TTL,
- cache successful resolve responses with conditional revalidation,
- avoid caching integrity failures as permanent without a retry policy,
- respect origin-provided cache controls during representation retrieval. [REF-13]

### 26.3 Deployment guidance for publishers

Publishers should:

- keep canonical URL policy stable,
- avoid frequent identity churn,
- automate digest generation at publish time,
- publish authority-scoped capability metadata when one backend platform serves multiple participating authorities,
- keep `allowed_origin_authorities` capability metadata synchronized with actual delegated delivery origins when delegated origins are used,
- avoid exposing client-variant or personalized bytes as one Core representation,
- prefer explicit allowlists for delegated delivery origins rather than relying on registrable-domain heuristics,
- test discovery and resolve behavior in continuous integration,
- monitor integrity mismatch reports,
- treat OpenPOG as a complement to existing DOI, Crossref, schema.org, and access-control infrastructure rather than a replacement for those systems.

### 26.4 Migration guidance from broader OpenPOG-family deployments

Deployments migrating from broader OpenPOG-family variants should:

- preserve existing identifiers and representation metadata,
- map richer fields into optional profile namespaces,
- keep Core responses minimal and deterministic,
- avoid requiring non-Core auth/session workflows for Core resolve.

## 27. Appendix C: Change Log

### 27.1 Changes from earlier OpenPOG-family drafts

Compared with broader historical OpenPOG-family drafts, OpenPOG Core v1:

- narrows mandatory scope to discovery, resolve, canonical records, representations, typed links, and integrity,
- removes referral, authentication, receipt, payment, and federation workflows from Core requirements,
- enforces one mandatory operation (`resolve`) with equivalent GET and POST bindings,
- simplifies lifecycle to `active`, `gone`, and `unknown` in Core,
- formalizes digest-required representation verification for deterministic representation data,
- makes cross-gateway identity equivalence an explicit non-goal of Core v1,
- strengthens freshness, delegated-origin, privacy, and SSRF guidance for production deployments.

### 27.2 Open questions for future revisions

Open questions for future revision work include:

- Should a dedicated OpenPOG relation token for resolve be registered in IANA?
- Should profile capability negotiation be standardized beyond URI declaration and the `critical` mechanism?
- Should cryptographic metadata signatures be standardized as a profile layer?
- Should cross-gateway trust and replay protection be standardized in federation profiles?

## References

The following references include both normative and informative sources.

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
- [REF-31] RFC 9651: https://www.rfc-editor.org/rfc/rfc9651
- [REF-32] RFC 7284: https://www.rfc-editor.org/rfc/rfc7284
- [REF-33] RFC 8126: https://www.rfc-editor.org/rfc/rfc8126
- [REF-34] IANA Media Types Registry: https://www.iana.org/assignments/media-types/media-types.xhtml
- [REF-35] IANA Profile URIs Registry: https://www.iana.org/assignments/profile-uris/profile-uris.xhtml
- [REF-36] RFC 9421: https://www.rfc-editor.org/rfc/rfc9421
- [REF-37] RFC 7515: https://www.rfc-editor.org/rfc/rfc7515
