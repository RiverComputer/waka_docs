# Waka — Technical Position

Waka is a protocol-oriented application for creating sovereign data trusts and producing portable, witnessed claims.

It has two operating paths.

**Sovereign anchoring** creates a durable digital record for a project, place, community, or stewardship body. It uses project identity, resource storage, content addressing, and external anchors to give a project custody over its own records.

**The claims engine** uses the full Waka architecture. Resources become claim subjects. Validation nodes apply human or machine witness methods to those subjects. Claims are composed from validated resources, and anchors make those claims portable into downstream systems.

This document is the technical companion to the [Conceptual Position](./CONCEPTUAL_POSITION.md). The conceptual position explains what Waka is and who it is for. This document explains how Waka is structured: its object model, protocol layers, open standards, claims engine, anchoring substrates, and implementation architecture.

---

## Architecture overview

Waka is organized around five core objects:

```txt
projects → resources → validation nodes → claims → anchors
```

| Object | Technical form | Role in Waka |
|---|---|---|
| **Projects** | Project containers with DIDs, membership, and application-layer state in Supabase | The governed context for a data trust or claims workflow. A project DID is the persistent digital address for the Waka/project record. |
| **Resources** | PDFs, images, videos, text records, RDF triples, JSON documents, geospatial files, observations, proposals, actions, and other stored objects | The subject matter of the system. Resources are the records held by a project. In the claims engine, resources can become the subjects validated through witness methods. |
| **Validation nodes** | Implementations of witness methods, shaped as Verifiable Credential-style records | The validation layer applied to resource subjects. A validation node records who or what witnessed a resource, which method was used, what criteria were applied, and what verdict was produced. |
| **Claims** | Human-readable assertions linked to resource subjects, evidence, validation records, and signatures | The portable assertion created from validated resources. A claim says what is being asserted and links that assertion to the resource subjects and validation process that support it. |
| **Anchors** | Hashes, content addresses, attestation UIDs, AT-URIs, CIDs, transaction references, or links into external claims and storage systems | The durability and interoperability layer. Anchors point to where records, claims, or attestations live outside the application database. |

The full architecture can be read as:

```txt
project identity → resource subjects → validation nodes → claims → anchors / issuance
```

Sovereign anchoring uses the project, resource, and anchor parts of this architecture. The claims engine adds validation nodes, Verifiable Credential-shaped witness records, claim composition, CEL-based policy logic, and issuance or trigger pathways.

---

## Why Waka is built on open standards

Sovereign records cannot depend on a proprietary account system.

Many impact, climate, and Web3-adjacent tools ask users to create another account, enter data into another application, and trust that the platform will remain the durable home of the record. That model works for software adoption, but it does not work for sovereignty. If a record cannot survive the application that created it, it is not truly portable. If data lives only inside one platform, the project does not fully control its own memory.

Waka is built at the level of identifiers, signatures, data shapes, validation policies, and content-addressed anchors. The application session is a convenience for using the product. The durable layer is the protocol layer beneath it.

The main primitives are:

- **Decentralized Identifiers (DIDs)** for identity and signing.
- **Verifiable Credential-shaped records** for portable witness attestations.
- **AT Protocol primitives** — including the Personal Data Server, `did:plc`, and CGS — for publication, project identity, and compatibility with Hypercerts v2.
- **Common Expression Language (CEL)** for deterministic validation-policy evaluation.
- **Hypercerts v2 lexicons** for shared claim and evaluation record shapes.
- **IPFS, Filecoin, Ethereum Attestation Service, Regen `x/data`, and related substrates** for durability, anchoring, on-chain triggers, and ecological-credit workflows.

The point is not that every project must use every substrate. The point is that Waka can compose with the systems a project already uses, without making any single one load-bearing for the entire record.

Earlier iterations of Waka were built primarily on DIDs and the Verifiable Credentials data model. The current architecture gives AT Protocol primitives a more foundational role for publication and claim compatibility, while preserving DID versatility at the identity layer and substrate versatility at the anchoring layer. The VC data model remains inside the content of a witness record, where it carries cross-DID portability within a more operationally mature publication environment.

Waka therefore aims to be a standards participant, not a standards captive. It conforms to shared record shapes where they exist, and extends them where land-based trusts, witness policies, covenant triggers, or ecological institution containers require additional expressiveness.

---

## Layer 1: identity and authentication

The identity layer answers: **who or what can sign?**

Waka uses Decentralized Identifiers as the root identity system. A person may log in through an application session, but the durable identity is the DID that signs a record. The application session helps someone use Waka. The DID lets the record travel beyond Waka.

At the floor, Waka can support `did:pkh`, which derives a DID directly from a wallet address. This gives wallet users an immediate signing context without requiring new infrastructure. Richer methods such as `did:plc`, `did:key`, `did:ethr`, and others can be adopted when a project needs key rotation, ecosystem-specific compatibility, or different custody properties.

`did:plc` is especially important because it is native to AT Protocol. A record signed or published under a `did:plc` identity can be addressed within the AT Protocol ecosystem and read by Hypercerts v2-compatible infrastructure. This allows Waka to publish first-class records in that ecosystem while still allowing participants to authenticate through other wallets or DID methods.

Groups can carry DIDs as well. Through CGS, AT Protocol’s Certified Group Service, organizations and projects can be issued group `did:plc` identities. This makes it possible for a collective subject — an organization, stewardship body, land project, or covenant container — to sign or publish records on its own behalf.

For Waka, this is not only a publishing convenience. CGS allows a whole project to gather around a single project DID. That DID becomes the persistent digital address for the Waka itself: a secure way to address the project record in legal contracts, stewardship agreements, covenants, funding instruments, or other documents that need to point at the project as a durable subject. The project DID holds the continuity of the record even as individual members, wallets, signers, or governance roles change over time.

The resulting identity model is nested:

- **User DID** — the person or participant signing records.
- **Project DID** — the stewardship subject, place-based container, or covenant endpoint.
- **Organization DID** — the larger collective or coordinating body.

The project DID does not itself drive issuance systems. Claims do that work. The project DID gives those claims and records a persistent project address.

---

## Layer 2: claims engine

The claims engine answers: **how does a resource become a validated claim?**

In sovereign anchoring, resources are stored and anchored as project records. In the claims engine, selected resources become claim subjects. A claim is built from three elements:

1. **Subject** — the resource or resource-derived assertion being claimed.
2. **Evidence** — the records that support the claim.
3. **Witness records** — signed attestations or machine validation records describing how the subject was validated.

This is where Waka extends the Verifiable Credentials model. Conventionally, VCs are used to make assertions about identities: an issuer says something about a subject, and a verifier checks the proof. Waka uses the same pattern but points it at resource subjects and project records. A witness record becomes a signed or machine-produced statement about a resource subject.

A Waka witness record carries:

- the issuer or witness DID, when signed by a human or organizational actor;
- the subject being witnessed;
- the witness method;
- the criteria or policy applied;
- the evidence reviewed;
- the verdict or output;
- the cryptographic proof or machine-verifiable output.

A resource subject can be validated by a human witness or a machine witness. A human witness produces a signed attestation. A machine witness produces a validation record from an algorithmic, schema-based, rule-based, or AI-assisted process. Both are represented through a shared witness-record shape so they can be attached to the same claim.

Witness records accrete on a claim rather than overwrite it. A human signature, a schema-conformance check, and an AI-assisted query can all sit on the same claim. They are not separate versions. They are a growing body of witness around one subject.

CEL appears at the policy layer. It allows Waka to express deterministic rules for how validation records combine: which witness methods are admissible, what threshold is required, which credentials, signer classes, or machine processes count, and what verdict is produced.

Machine witness is a general category for non-human validation methods. Current and expected methods include:

- **CEL expression evaluation** — deterministic rule checks over structured data.
- **Schema conformance** — checking an input against a registered methodology or rubric.
- **Checksum or content-hash verification** — tamper-evidence as a validation method.
- **AI classification or query** — model-based review with structured outputs.
- **Algorithmic review agents** — composable checks over verifiable inputs.

Human witness is the part of the system where patterns of authority, signature, and governance matter most. Those patterns are under active development. The current technical point is narrower: a human signer can produce a validation record about a resource subject.

The validation policy names:

- which witness methods are admissible;
- which credentials, signer classes, or machine processes are accepted;
- how validation records combine;
- what verdict is produced.

More elaborate role and permissioning patterns are part of the development roadmap rather than current core functionality.

A simplified validation policy might look like this:

```jsonc
{
  "$type": "org.witness.policy",
  "version": "2026-05-12.1",
  "methods": ["human", "cel", "schema", "checksum", "ai", "agent"],
  "combine": {
    "lang": "cel",
    "expr": [
      "records.exists(r, r.witnessMethod == 'schema' && r.verdict.score >= 0.7)",
      "  || records.exists(r, r.witnessMethod == 'human' && r.verdict.result == 'pass')"
    ]
  }
}
```

The claims engine treats validation as a design space. Who can validate a resource subject? By what method? With what threshold? Under which policy? These questions belong inside the claims engine, while more elaborate role and permissioning systems remain part of the roadmap.

---

## Witness records as Verifiable Credentials

A Waka witness record can be expressed as a Verifiable Credential-shaped object inside the content of a claim.

This lets the proof travel with the record. It also means that a witness record signed under one DID method can remain verifiable when the claim is read in a different context.

A simplified witness record looks like this:

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://witness.org/ns/v1"
  ],
  "type": ["VerifiableCredential", "WitnessRecord"],
  "issuer": "did:plc:ewvi7nxzyoun6zhxrhs64oiz",
  "validFrom": "2026-05-12T14:00:00Z",
  "credentialSubject": {
    "id": "at://did:plc:project/org.hypercerts.claim.activity/3kpz7m2x4c2zk",
    "witnessMethod": "schema",
    "criteriaSnapshot": {
      "schema": "org.witness.schema.mrv.reforestation.v1",
      "hash": "blake2b-256:7f3a91...c1"
    },
    "evidence": [
      "ipfs://bafybeihn5e2k...",
      "at://did:plc:project/app.certified.location/3kq8..."
    ],
    "verdict": {
      "result": "partial",
      "score": 0.78
    }
  },
  "proof": {
    "type": "DataIntegrityProof",
    "cryptosuite": "eddsa-jcs-2022",
    "verificationMethod": "did:plc:ewvi7nxzyoun6zhxrhs64oiz#atproto",
    "proofValue": "z58..."
  }
}
```

Different witness methods change the method, criteria, evidence, and verdict. The underlying pattern remains the same. This is what makes witness modular.

---

## Hypercerts v2 compatibility

Waka does not need to invent the entire shape of a claim.

Where possible, Waka aligns with Hypercerts v2. The Waka claim corresponds most closely to the Hypercerts v2 activity record, while Waka’s witness layer enriches the evaluation record space with additional information about witness methods, policies, registries, criteria, and verdicts.

Waka can publish sidecar records in a provisional `org.witness.*` namespace. These records strong-reference the Hypercerts records they enrich and carry the VC-shaped witness content inside them.

A simplified sidecar record might look like this:

```json
{
  "$type": "org.witness.record",
  "claim": {
    "uri": "at://did:plc:project/org.hypercerts.claim.activity/3kpz7m2x4c2zk",
    "cid": "bafyreih5e..."
  },
  "evaluation": {
    "uri": "at://did:plc:evaluator/org.hypercerts.context.evaluation/3kq8m2x4c2zk",
    "cid": "bafyreid2x..."
  },
  "witnessMethod": "schema",
  "criteriaSnapshot": {
    "schema": "org.witness.schema.mrv.reforestation.v1",
    "hash": "blake2b-256:7f3a91...c1"
  },
  "verdict": {
    "result": "partial",
    "score": 0.78
  },
  "content": {
    "...": "the WitnessRecord VC travels here"
  }
}
```

An indexer that understands only Hypercerts v2 can read the claim and evaluation records it recognizes. A system that also understands `org.witness.*` can read the richer witness layer.

This gives Waka a compatibility path without lock-in. It can participate in the Hypercerts ecosystem, while also remaining useful to Ethereum attestations, ecological-credit registries, local governance systems, covenant contracts, and other downstream infrastructures.

---

## Layer 3: anchoring and issuance

The anchoring and issuance layer answers: **where does the record persist, and what can it do?**

A Waka record can be published, archived, attested, mirrored, or anchored across multiple substrates. Each substrate has a role. None has to become the entire system.

| Substrate | Role |
|---|---|
| Any wallet / DID method | Versatile login and signing |
| AT Protocol PDS | Canonical signed publication |
| CGS | Group `did:plc` identity for organizations and projects |
| IPFS / Filecoin | Content-addressed storage and long-term durability |
| Ethereum Attestation Service | On-chain attestations and covenant triggers |
| Regen `x/data` | Ecological-data anchoring and crediting workflows |
| Hypercerts v2 | Claim and evaluation record compatibility |
| CEL | Deterministic policy and rule evaluation |
| Ceramic *(candidate)* | Verifiable claim streams for downstream consumers |

The practical goal is portability. A project should be able to enter a record once and then use it across many systems. A claim might be published on a PDS, archived through IPFS and Filecoin, attested through EAS, anchored for a Regen workflow, and indexed through Hypercerts-compatible records.

This is the operational meaning of **measure once, report everywhere**.

Waka sits upstream of any particular issuance, crediting, certification, or funding ecosystem. Its job is to produce durable building blocks of verifiable truth that other systems can read, evaluate, and act upon.

A single project record or claim may produce several substrate-native identifiers:

```txt
PDS     at://did:plc:ewvi7nxzyoun6zhxrhs64oiz/org.witness.composition/3kq7lm2x4c2zk
IPFS    ipfs://bafybeihn5e2k...          (+ optional Filecoin storage deal)
EAS     0x9c8f1a...e2                    (attestation UID)
Regen   blake2b-256:7f3a91...c1          (anchored via x/data MsgAnchor)
HC v2   org.hypercerts.context.evaluation record in the evaluator's PDS
```

In sovereign anchoring, this gives a project durable custody over its records.

In claims engine mode, it gives a claim enough portability to become useful across certification, funding, contract, and governance systems.

---

## Codebase architecture

The current Waka implementation is a Next.js application backed by Supabase, with protocol integrations for identity, claims, anchoring, attestations, and publication.

Supabase should be understood as **application-layer storage**. It stores the working state of the app: projects, resources, membership, graph state, validation nodes, claim composition, and anchor metadata. It is not the sovereignty layer by itself. Sovereignty comes from DID-based identities, signed records, content addressing, and external anchors that allow project records and claims to persist beyond the application database.

### Stack by architectural layer

| Layer | Implementation components | Role |
|---|---|---|
| **Application layer** | Next.js App Router, TypeScript, Tailwind CSS v4, Supabase, ReactFlow | Product interface, graph UI, project state, resource management, claim composition |
| **Identity / auth layer** | Supabase Auth, wallet login, DIDs, `did:pkh`, `did:plc`, CGS | User sessions, signer identity, project identity, organization identity, group publication |
| **Claims engine layer** | Validation nodes, VC-shaped witness records, CEL, schema conformance, AI / algorithmic validators, claim composition logic | Validation of resource subjects, witness records, policy evaluation, claim creation |
| **Anchoring / issuance layer** | Pinata / IPFS, Filecoin, EAS SDK, AT Protocol PDS, Hypercerts v2 records, Regen `x/data`, candidate Ceramic integration | Durable storage, publication, attestations, external anchors, downstream claims and issuance systems |

### Key directories

```txt
app/
  actions/          # Server actions for auth, resources, claims, anchoring
  components/       # Project UI, claims engine, graph, inspectors
  projects/[id]/    # Project detail pages
  share/[slug]/     # Public read-only snapshot pages

lib/
  ontologies/       # Schema definitions and methodology structures
  supabase/         # Supabase clients and middleware

scripts/
  export-public-snapshot.ts
```

---

## Developer setup

### Prerequisites

- Node.js 18+
- A Supabase project with PostgreSQL and Auth
- Optional: Pinata account for IPFS
- Optional: Ethereum wallet and EAS Sepolia deployment for attestations
- Optional: AT Protocol credentials for Hypercerts-compatible publication

### Environment

Create `.env.local` with:

```env
# Required
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
NEXT_PUBLIC_SITE_URL=http://localhost:3000

# IPFS / Pinata
PINATA_JWT=
NEXT_PUBLIC_GATEWAY_URL=

# EAS
EAS_PRIVATE_KEY=
EAS_SCHEMA_UID=

# AT Protocol / Hypercerts
ATPROTO_SERVICE_HANDLE=
ATPROTO_APP_PASSWORD=
```

### Run

```bash
npm install
npm run dev
```

### Database

Run migrations in order from `supabase/migrations/`.

See `docs/RUN_MIGRATIONS.md` for the full migration sequence.

### Exporting a public snapshot

```bash
npx tsx scripts/export-public-snapshot.ts <projectId> <slug>
```

This copies attachments to the public storage bucket and writes a snapshot JSON file to:

```txt
app/share/_snapshots/<slug>.json
```

Commit the generated file and deploy to publish the public page.

---

## Status

Waka is in active development.

The current core loop is complete in its present form:

```txt
resource collection → witnessed claims → multi-layer anchoring
```

Current or completed:

- resource collection;
- project containers;
- public snapshot export;
- validation nodes;
- schema conformance;
- claim construction;
- EAS on-chain attestation.

In development:

- CGS integration for project and organization `did:plc` publication;
- `org.witness.*` sidecar lexicons;
- witness taxonomy records for machine witness, policies, registries, and methodology tags;
- role and permissioning patterns for human witness;
- covenant trigger contracts;
- programmable remittance from witnessed claims;
- Filecoin mirroring for high-durability archival.

---

## Roadmap: programmable remittance

Waka is motivated by work on ecological institutions and living covenants: land titles, stewardship agreements, and project-level digital records that can become endpoints for governance, funding, and accountability.

The near-term roadmap extends the anchoring layer into programmable downstream action. Witnessed claims should be able to trigger smart contracts, inform grant disbursements, support insurance claims, extend tenure agreements, settle ecological-credit instruments, or release stewardship payments.

The claims engine is not an end in itself. It is the input substrate for accountable remittance and enforcement. Its purpose is to make living covenants programmable without removing human oversight.

---

*See the [Conceptual Position](./CONCEPTUAL_POSITION.md) for the conceptual overview and video walkthroughs, or the [README](./README.md) for the short version.*
