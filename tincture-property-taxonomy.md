# The `tincture` CycloneDX property namespace

**Version:** `tincture-inventory/0.1` · 2026-09-28 · Licence: MIT (see `LICENSE` in this directory)

This document is the taxonomy of the `tincture` top-level namespace for CycloneDX `properties`,
as registered (or requested) in the [CycloneDX Property Taxonomy](https://github.com/CycloneDX/cyclonedx-property-taxonomy).
It is the canonical, public list: every property name a Tincture CBOM export may emit, the
component it appears on, and the format of its value. It ships with the Tincture formats
(`tincture-registry.json`, the schemas and the examples) so that consumers can vendor the whole
set. The mapping from Tincture's entities to standard CycloneDX fields is in the integration
guide; nothing here duplicates or overrides a standard CycloneDX field.

## Rules

- Names follow the CycloneDX taxonomy ABNF: segments joined by `:`, characters `A–Z a–z 0–9 - _`
  and space. Names are lower case.
- Values are strings, as CycloneDX requires. Integers are decimal without separators; booleans
  are `true` / `false`; RFC 3339 timestamps in UTC (`2026-09-28T12:00:00Z`); dates as
  `YYYY-MM-DD`; durations as `<n>y` (years) unless stated; lists are `,`-separated unless stated.
- Suite identifiers are four hex digits with prefix, `0x0101`, and refer to
  `tincture-registry.json`. The registry's `pq` attribute takes `n/a`, `vulnerable`, `hybrid`,
  `pq`; `unknown` is used for envelopes whose suite is not in the registry.
- Nothing secret, no context, storage locator or envelope fingerprint is ever a property value.
- The document-level `tincture:inventory:schema` names the taxonomy version that produced a CBOM.
  Names are never reused with a different meaning; a changed meaning is a new name.

**Where** column: `metadata` = `metadata.properties`; `tincture` = a `data` component
(`bom-ref` `tincture:<namespace>/<name>`); `suite` = a suite algorithm component
(`suite:0x…`); `primitive` = a primitive algorithm component (`alg:…`); `kv` = a key-version
component (`kv:…`); `recipients` = a recipient-directory component; `signer` = a blazon signer
public-key component; `blazon` = the blazon `file` component; `protocol` = the envelope-format
protocol component (`protocol:tnct/1`).

## Properties

### Inventory (document level)

| Property | Where | Value |
|---|---|---|
| `tincture:inventory:schema` | metadata | Taxonomy version: `tincture-inventory/0.1` |
| `tincture:inventory:scanned-at` | metadata | RFC 3339 time of the scan |
| `tincture:inventory:sources` | metadata | `;`-separated source identifiers (`fs:<path>`, `s3:<bucket/prefix>`, `db:<table.column>`); removed at `partner` and `public` redaction |
| `tincture:inventory:redaction` | metadata | `internal`, `partner` or `public` |
| `tincture:inventory:illustrative` | metadata | `true` only in specification examples whose numbers are invented; absent in real exports |
| `tincture:envelopes:malformed` | metadata | Integer: envelopes that could not be parsed |
| `tincture:audit:checkpoints` | metadata | Integer: audit checkpoints found |
| `tincture:audit:checkpoint-suite` | metadata | Suite ID signing the checkpoints |
| `tincture:audit:boundaries` | metadata | Integer: boundaries reporting |

### Envelope populations

| Property | Where | Value |
|---|---|---|
| `tincture:envelopes:count` | metadata, tincture, suite, kv, recipients, protocol | Integer: envelopes at rest found under this entity |
| `tincture:envelopes:bytes` | metadata, tincture, suite, kv, recipients, protocol | Integer: their total size in bytes |
| `tincture:envelopes:mode:direct` | tincture | Integer: envelopes in `direct` mode |
| `tincture:envelopes:mode:wrapped` | tincture | Integer: envelopes in `wrapped` mode |
| `tincture:envelopes:mode:sealed-to` | tincture | Integer: envelopes in `sealed-to` mode |

### Tinctures (data domains)

| Property | Where | Value |
|---|---|---|
| `tincture:id` | tincture | 32 hex characters: the tincture's derived identifier |
| `tincture:kind` | tincture | `symmetric` or `sealed-to` |
| `tincture:role` | tincture | `key-material` or `canary`; absent for ordinary tinctures (`clear` tinctures are never exported) |
| `tincture:horizon` | tincture | Confidentiality horizon as a duration, `<n>y` (also `s`, `m`, `h`, `d` units) |
| `tincture:min-tier` | tincture | Minimum key-protection tier: `T0`, `T1`, `T2` or `T3` |
| `tincture:context` | tincture | `required`, `optional` or `forbidden` |
| `tincture:padding` | tincture | Padding policy: `none`, `padme` or `buckets(<sizes>)` |
| `tincture:mode` | tincture | Declared mode: `direct`, `wrapped` or `auto` |
| `tincture:suites` | tincture | `,`-separated suite IDs the tincture may use |

### Quantum policy and debt

| Property | Where | Value |
|---|---|---|
| `tincture:quantum:deadline` | metadata, blazon | Date after which quantum-vulnerable protection is unacceptable |
| `tincture:quantum:migration` | metadata, blazon | Migration lead time, `<n>y` |
| `tincture:quantum:enforce` | metadata, blazon | `off`, `warn` or `enforce` |
| `tincture:quantum:slack` | metadata | Years between the deadline and now plus migration time, `<n>y` or `-<n>y` |
| `tincture:quantum:envelopes:not-applicable` | tincture | Integer: envelopes under suites with `pq = n/a` (symmetric only). The registry value `n/a` is spelled `not-applicable` in property names because `/` is outside the taxonomy grammar. |
| `tincture:quantum:envelopes:vulnerable` | tincture | Integer: envelopes under suites with `pq = vulnerable` |
| `tincture:quantum:envelopes:hybrid` | tincture | Integer: envelopes under suites with `pq = hybrid` |
| `tincture:quantum:envelopes:pq` | tincture | Integer: envelopes under suites with `pq = pq` |
| `tincture:quantum:envelopes:unknown` | tincture | Integer: envelopes under suites not in the registry |
| `tincture:quantum:status` | tincture | `,`-joined `pq` statuses of the suites in use |
| `tincture:quantum:exposed` | tincture | `true` if now plus the horizon passes the deadline |
| `tincture:quantum:debt:count` | metadata, tincture | Integer: envelopes already exposed to harvest-now-decrypt-later (direct debt) |
| `tincture:quantum:debt:bytes` | metadata, tincture | Integer: their total size in bytes |
| `tincture:quantum:inherited:count` | tincture | Integer: envelopes exposed through key lineage (inherited debt) |
| `tincture:quantum:inherited:bytes` | tincture | Integer: their total size in bytes |
| `tincture:quantum:lineage` | tincture | `,`-separated lineage markers of the tincture's keys, for example `bundle:0x0202` |

### Suites and primitive algorithms

| Property | Where | Value |
|---|---|---|
| `tincture:suite:id` | suite | Suite ID, `0x0101` |
| `tincture:suite:class` | suite | The registry `class` of the suite (for example `envelope-symmetric`, `envelope-sealed`, `signature`, `mac`) |
| `tincture:suite:state` | suite | Registry lifecycle state: `provisional`, `recommended`, `allowed`, `deprecated` or `forbidden` |
| `tincture:suite:pq` | suite | Registry quantum status: `n/a`, `vulnerable`, `hybrid` or `pq` |
| `tincture:suite:fips` | suite | `true` if every primitive is FIPS-approved |
| `tincture:suite:cnsa2` | suite | `true` if the suite meets CNSA 2.0 |
| `tincture:registry:version` | suite | Semantic version of `tincture-registry.json` |
| `tincture:pq` | primitive | `hybrid` on a combiner primitive that holds if either member holds |
| `tincture:primitive` | primitive | `key-wrap` or `secret-sharing` where CycloneDX's `primitive` is `other` |
| `tincture:padding` | primitive | `pss` where CycloneDX 1.6's `padding` enumeration has no value (RSASSA-PSS) |

### Key versions and recipients

| Property | Where | Value |
|---|---|---|
| `tincture:kv:tincture` | kv | Name of the tincture the key version belongs to |
| `tincture:kv:kind` | kv | `symmetric`, `kem-keypair`, `sign-keypair` or `agree-keypair` |
| `tincture:kv:state` | kv | Tincture lifecycle state verbatim: `pending`, `active`, `retiring`, `disabled` or `destroyed` |
| `tincture:kv:suites` | kv | `,`-separated suite IDs when a symmetric key version serves several suites |
| `tincture:kv:retired` | kv | RFC 3339 time the version entered `retiring` |
| `tincture:kv:destroyed` | kv | RFC 3339 time the version was destroyed |
| `tincture:kv:imported` | kv | `true` if the key material was imported rather than generated |
| `tincture:kv:revoked-reason` | kv | Reason recorded in the blazon's revocation list |
| `tincture:kv:lineage` | kv | Lineage marker: the suite under which the key travelled, for example `bundle:0x0202` |
| `tincture:tier` | kv, recipients | Key-protection tier: `T0`, `T1`, `T2` or `T3` |
| `tincture:pq-tier` | kv | Tier of the post-quantum half of a hybrid key pair when it differs from `tincture:tier` |
| `tincture:counters:seals` | kv | Integer: seal operations recorded for the version |
| `tincture:counters:opens` | kv | Integer: open operations recorded |
| `tincture:counters:kind` | kv | `hardware-monotonic` or `software` (a software counter can be rolled back by a snapshot restore) |
| `tincture:recipients:count` | recipients | Integer: recipient public keys in the directory |
| `tincture:recipients:suites` | recipients | `,`-separated suite IDs the recipients accept |
| `tincture:recipients:attestation` | recipients | `,`-separated attestation kinds: `android-key`, `app-attest`, `tpm-quote` |

### Blazon, signers and envelope format

| Property | Where | Value |
|---|---|---|
| `tincture:blazon:namespace` | blazon | The blazon namespace |
| `tincture:blazon:spec` | blazon | Specification version, `tincture/0.1` |
| `tincture:blazon:version` | blazon | Integer blazon version |
| `tincture:blazon:signers:threshold` | blazon | Integer: signatures required |
| `tincture:blazon:signers:count` | blazon | Integer: signers declared |
| `tincture:blazon:signers:pq` | blazon | `true` if the signer set includes a post-quantum or hybrid signer |
| `tincture:signer:suite` | signer | The signer's suite ID |
| `tincture:signer:pq` | signer | The suite's `pq` status |
| `tincture:format:magic` | protocol | Envelope magic, `TNCT` |
| `tincture:format:version` | protocol | Envelope format version, `0x01` |
| `tincture:format:text-prefix` | protocol | Text-form prefix, `tnct1.` |

## Notes

- **Registry `pq` values in names.** The registry spells the symmetric-only status `n/a`; in a
  property name it is written `not-applicable`, because `/` is outside the taxonomy grammar. The
  other values (`vulnerable`, `hybrid`, `pq`, `unknown`) are used as they are.
- **`tincture:padding`** appears on two component kinds with two value sets (a tincture's padding
  policy; a primitive's RSA padding). The component type disambiguates. The primitive use is
  unnecessary once CycloneDX 2.0's `padding: pss` is available.
- Properties absent from an export mean "not applicable" or "not measured", never zero.
- The worked example `examples/acme-prod.inventory.cbom.json` uses this taxonomy; the checker
  `tools/check_formats.py` fails if the example emits a name this document does not list.
