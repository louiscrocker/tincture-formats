# Tincture formats

Machine-readable formats for **Tincture**, a data-protection framework from
BADWOLF Software ([Lou Crocker](https://github.com/louiscrocker)): typed cryptographic domains, signed policy
manifests, envelope encryption with key and algorithm rotation, and a keyless inventory of
protected data at rest that exports as a CycloneDX CBOM.

This repository holds only the parts other tools need to read or write. It is published under the
MIT licence so that any tool can ship them. The Tincture specification and runtime are separate
and not part of this repository.

**Status:** working draft, version 0.1.0 (2026-09-28). Until Tincture's first release the content
may change under this version number; consumers should pin by vendoring a copy.

| File | What it is |
|---|---|
| `tincture-registry.json` | Every registered algorithm suite: 16-bit ID, name, constituent algorithms, lifecycle state (`provisional`, `recommended`, `allowed`, `deprecated`, `forbidden`), FIPS / WebCrypto / CNSA 2.0 flags, quantum status (`n/a`, `vulnerable`, `hybrid`, `pq`), the names other tools use for the same construction (`aliases`), and the names for which a suite is the nearest registered replacement (`nearestFor`). Also lists constructions that are never registered, and ones that are simply not registered, with suggested replacements. |
| `tincture-registry.schema.json` | JSON Schema (draft 2020-12) for the registry. |
| `blazon.schema.json` | JSON Schema (draft 2020-12) for a **compiled blazon**, Tincture's signed policy manifest: tinctures (data domains), profiles (named operations), pipelines, rotation and quantum policy, and signers. Structural validity only; semantic rules are enforced by the compiler and runtime. |
| `tincture-property-taxonomy.md` | The `tincture` CycloneDX property namespace: every `tincture:*` property a Tincture CBOM export may carry, where it appears and its value format. This is the public document referenced by the namespace registration in [CycloneDX/cyclonedx-property-taxonomy](https://github.com/CycloneDX/cyclonedx-property-taxonomy). |
| `examples/acme-prod.blazon.json` | A worked example as a compiled blazon, with identifiers derived per the specification. Unsigned, with placeholder signer keys. |
| `examples/acme-prod.inventory.cbom.json` | The same deployment's inventory as a CycloneDX 1.6 CBOM. Structure, suites, identifiers and OIDs are real; the counts, key versions and dates are invented and labelled as such inside the document. It validates against the official CycloneDX 1.6 schema. |

## Conventions

- Suite IDs are never reused, even after a suite becomes `forbidden`. New suites are additions.
- Lifecycle states only move forward: `provisional` → `recommended` or `allowed` → `deprecated` → `forbidden`.
- A deployment's blazon may narrow a suite's state (for example treat an `allowed` suite as `deprecated`) but never widen it.
- In `tincture:*` property names, the registry's quantum status `n/a` is written `not-applicable`, because `/` is outside the CycloneDX property-name grammar.

## Versioning

The registry carries its own semantic version. Adding a suite, or moving a suite's state forward,
is a minor version. Changes to the meaning of an existing ID never happen.

## Licence

MIT. See `LICENSE`.
