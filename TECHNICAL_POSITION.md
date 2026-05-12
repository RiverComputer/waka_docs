# Waka — Technical Position

On a technical level Waka is an assembly of open protocol primitives that turns a body of anchored evidence into a portable, witnessed claim. It anchors data across a range of sovereign storage substrates, runs that data through a composable witness layer — human and machine witness composed, per context, under a signed policy — and feeds the witnessed claims into an issuance system for the instruments built on them: certificates, ecological credits, on-chain triggers, and programmable remittance. The primitives are Decentralized Identifiers, the Verifiable Credentials data model, AT Protocol's Personal Data Server with `did:plc` and CGS, Common Expression Language, and the Hypercerts v2 lexicon space; identity in front and anchoring behind are both held to maximum versatility — any wallet and any DID method in, any of several durable, on-chain, and ecological substrates out — so the witness layer in the middle is never bound to a single stack. It introduces no new login and re-derives no distributed protocol.

This is the technical companion to the [Conceptual Position](./CONCEPTUAL_POSITION.md). The conceptual position says what Waka is and who it is for; this one accounts for what it is built on — the open standards underneath it, the three layers those standards compose into, the lexicon space the claims layer plugs into, and the roadmap toward programmable remittance. Read the conceptual position first.

---

## Built on the open standards of the web

Interoperable, protocol-agnostic software cannot be assembled on top of a proprietary account system. It has to be built at the most elemental, open-standard level of the web — the layer of identifiers, signatures, and data shapes that no single company owns. A great deal of Web3, and a great deal of the impact-evaluation tooling adjacent to it, has failed this test in the same way: each project asks the user to create a new login, or to adopt one more app, and the data that project produces then lives inside the app and dies with it. Nothing built that way is portable, and nothing portable can be built that way.

Waka is built on the protocols that are foundational to the design and implementation of the internet rather than on an account: Decentralized Identifiers (DIDs) for identity, the Verifiable Credentials (VC) data model for signed assertions, AT Protocol primitives — the Personal Data Server, `did:plc`, CGS, and CEL — for publication and machine evaluation, and the Hypercerts v2 lexicon space for the shape of a claim. Anchoring a record across several of these substrates rather than one is deliberate: each substrate does what it does best, and no single one becomes load-bearing for the whole system.

Earlier iterations of Waka were built foundationally on DIDs and the VC data model alone. The current version takes the AT Protocol primitives as more foundational, without compromising the versatility of identity login or of anchoring and issuance: the VC data model has been tucked inside the content of a claim, where it still carries cross-DID portability, now inside a more operationally mature publication substrate. Adopting Hypercerts v2 lexicons at the outer layer and publishing a small sidecar namespace for Waka's novel layers — a working `org.witness.*`, itself actively under development — keeps the project a standards participant rather than a standards captive.

---

## The three layers

Waka is three layers, and every feature in the system belongs to one of them.

- **The identity layer.** Authentication across any wallet and any DID method; the application session is a convenience for using the product, and the DID is the identity that signs records and survives the product.
- **The claims layer.** The middle of the system: a claim is a subject plus an accreting set of signed witness records, and the claims engine is the machinery that composes human and machine witnessing into those records. Its core position: composable witness methods and policies are a designable form of validation criteria for the creation of claims.
- **The anchoring and issuance layer.** Durable storage, sovereign publication, on-chain triggers, ecological anchoring, and portable evaluation records — spread across substrates so that none becomes load-bearing and a record can be carried into the systems downstream that need it.

The identity layer and the anchoring layer are held to the same constraint — maximum versatility at both ends — so that the claims layer in the middle is never bound to a single protocol substrate. The sections that follow take each layer in turn, with the most depth on the middle one: the claim object itself, the Hypercerts v2 lexicon space it plugs into, and the witness engine that produces it.

---

## The identity layer — DIDs

A Decentralized Identifier is a globally resolvable identifier whose holder controls it, independent of any registrar or platform. Waka uses DIDs as the root of identity: it treats the application session — currently a Supabase session — as a convenience, and the DID as the thing that actually signs a record. The floor is `did:pkh`, which derives a DID directly from a wallet address and requires no infrastructure at all, so every wallet user already has one; richer methods — `did:plc`, `did:key`, `did:ethr`, and others — sit above that floor and are adopted only when a project needs what they offer. One person can attach several DID-based signing contexts to a single session and sign with a MetaMask key, a Keplr key, or an AT-Protocol identity as the context calls for.

Critical to the structure of this identity layer is `did:plc`. It is the AT-Protocol-native DID method — a self-authenticating, key-rotatable identifier backed by a public log — and its relevance here is that a record signed under a `did:plc` identity is automatically indexable by the Hypercerts v2 stack, which treats `did:plc` as its addressing primitive. Publishing under `did:plc` is therefore what lets a Waka record appear as a first-class object in the Hypercerts ecosystem rather than as a foreign import, and it costs the wallet user nothing: a project can adopt `did:plc` at the publishing layer while its members continue to log in with `did:pkh`.

Groups carry DIDs as well. Organizations and projects are issued group `did:plc` identifiers through CGS — AT Protocol's Certified Group Service — so that a collective subject, an organization or a stewardship project, can sign claims on its own behalf. A group identity does not replace the individual identities underneath it; it is composed from them, and the members still log in with any wallet. The result is a nested-container model — an organization DID over a project DID over the user DIDs — in which the project-level DID is the endpoint a covenant or other legal instrument can point at.

---

## The claims layer — verifiable credentials, extended

The Verifiable Credentials data model is conventionally used to make assertions about an identity: an issuer says a subject holds an attribute, a holder presents the credential, and a verifier checks it, all secured by public/private-key signing. Waka takes that same machinery and points it at data subjects instead. A claim in Waka is a subject — an activity, an observation, a decision — together with an accreting set of witness records, each one a VC-shaped attestation signed by a DID and carrying the method by which the witnessing was done. The defining property of the VC model carries over: a range of different witness processes can be deployed against the same subject, and the proof travels with the record.

Because the VC data model lives inside the content of the claim, a witness record signed under one DID method stays verifiable when the claim is read in a context built around another. A claim therefore carries three things together — its evidence (the linked resources), its method of verification (which witness processes were applied, by whom or by what), and its signatures (the DID-signed witness records) — so anyone reading it can see what was asserted and who or what stood behind the assertion, without having to ask. Repurposing verifiable credentials this way — witness over data subjects as a design space for verification, rather than attribute assertion about identities — is one of Waka's novel contributions; the witness engine described below is its implementation.

In bytes, a single witness record is a VC carried inside the content of a claim record — issuer, the subject being witnessed, the method, the criteria it was checked against, the evidence it looked at, a verdict, and a proof. `witnessMethod` is one of the human methods `peer` / `pgs` / `expert` / `self` or the machine methods `cel` / `schema` / `checksum` / `ai` / `agent` (illustrative — the `org.witness.*` lexicon namespace is a working choice, still under active development):

```json
{
  "@context": ["https://www.w3.org/ns/credentials/v2", "https://witness.org/ns/v1"],
  "type": ["VerifiableCredential", "WitnessRecord"],
  "issuer": "did:plc:ewvi7nxzyoun6zhxrhs64oiz",
  "validFrom": "2026-05-12T14:00:00Z",
  "credentialSubject": {
    "id": "at://did:plc:project/org.hypercerts.claim.activity/3kpz7m2x4c2zk",
    "witnessMethod": "schema",
    "criteriaSnapshot": { "schema": "org.witness.schema.mrv.reforestation.v1", "hash": "blake2b-256:7f3a91...c1" },
    "evidence": ["ipfs://bafybeihn5e2k...", "at://did:plc:project/app.certified.location/3kq8..."],
    "verdict": { "result": "partial", "score": 0.78 }
  },
  "proof": { "type": "DataIntegrityProof", "cryptosuite": "eddsa-jcs-2022", "verificationMethod": "did:plc:ewvi7nxzyoun6zhxrhs64oiz#atproto", "proofValue": "z58..." }
}
```

A different witness method changes `witnessMethod`, `criteriaSnapshot`, and the shape of `verdict` — a `pass`/`fail`, a score, structured output — and nothing else. Records accrete on a claim rather than overwrite: a `peer` attestation, an `expert` review, and the conformance check above are three records on one subject, not three versions of one record. How that VC is wrapped for publication, and how it sidecars the Hypercerts evaluation record, is the next section.

---

## The Hypercerts v2 lexicon space

Waka does not invent the shape of a claim. The notion of a claim corresponds to the **activity** lexicon of Hypercerts v2, and the VC-extended witness model described above sits, in lexicon terms, as a sidecar layer over the Hypercerts v2 **evaluation** record: Waka publishes a small set of records in its own namespace — a working `org.witness.*`, actively under development — for the layers Hypercerts v2 does not yet cover (the witness taxonomy, covenant trigger attestations, ecological institution containers), as a peer contributor to that ecosystem rather than a subordinate app.

The sidecar is an ATProto record that strong-references the Hypercerts records it enriches and carries the VC above inside `content` (illustrative — names provisional):

```json
{
  "$type": "org.witness.record",
  "claim":      { "uri": "at://did:plc:project/org.hypercerts.claim.activity/3kpz7m2x4c2zk", "cid": "bafyreih5e..." },
  "evaluation": { "uri": "at://did:plc:evaluator/org.hypercerts.context.evaluation/3kq8m2x4c2zk", "cid": "bafyreid2x..." },
  "witnessMethod": "schema",
  "criteriaSnapshot": { "schema": "org.witness.schema.mrv.reforestation.v1", "hash": "blake2b-256:7f3a91...c1" },
  "verdict": { "result": "partial", "score": 0.78 },
  "content": { "...": "the WitnessRecord VC shown above — re-stated here so it travels with the record" }
}
```

The Hypercerts `org.hypercerts.context.evaluation` record stays untouched in the evaluator's repo; an indexer that does not know `org.witness.*` simply does not see the enrichment. That is the contribution pattern Hypercerts' own documentation describes — the same one `org.hyperboards.*` already uses alongside `org.hypercerts.*` in a shared repository.

What makes this more than lexicon housekeeping is the mode it runs in. Hypercerts v2 is AT-Protocol-native; Waka conforms to its data shape but does so in a **DID-protocol-agnostic** mode, which means the same claim can be authenticated, anchored, and issued into Web3 systems — Ethereum attestations, on-chain registries, smart contracts — and not only into AT Protocol. Waka therefore does three things at once: it conforms to the data shape of Hypercerts v2; it extends the evaluation class of that shape through the claims engine; and it opens a more versatile authentication, anchoring, and issuance space around the shape than the AT-Protocol-native context provides on its own.

Two consequences follow. First, a Waka claim is cross-compatible with crediting, certification, and Hypercerts registries out of the box — the same durable object can be read by an ecological-credit registry, a certification body, and a Hypercerts indexer, each applying its own rubric. Second, the shape makes a new generation of Ethereum smart contracts possible: contracts whose conditions are evaluated against witnessed, portable claims produced by systems like Waka, rather than against ad-hoc oracles.

---

## The claims engine — composable witness methods

The composable witness system is under active development. At its root it does one thing: it lets human witness and machine witness be composed into dynamic validation and verification systems, configured per context rather than fixed in advance.

The distinction it composes across is the one to start from. **Human-based validation** is, broadly, attestation — a person or group asserting something — and it is usually one of three things: role-based, grounded in a proof of authority, or a peer review with no role constraint at all. **Machine-based validation** falls into conformance (does this input satisfy a rule or schema), queries (does this corpus answer this question), and, increasingly, the use of agents to validate particular assertions or claims against bodies of information. Most verification systems pick one of these and build around it. The claims engine treats them as primitives instead: combine human and machine witness in whatever proportion a context calls for, and you can reconstruct almost any existing form of verification — peer review, a participatory guarantee system, an expert audit, a methodology-conformance check, an algorithmic review — and compose several of them on the same claim.

Within the two classes, the methods Waka exposes are:

- **Human witness** — *peer review* (multiple witnesses, no role constraint), *participatory guarantee system* (quorum-based group attestation), *expert review* (a single credentialed auditor), *self-attestation* (the originator asserting their own claim). This is where credentials and proof of authority enter the system.
- **Machine witness** — *CEL expression evaluation* (deterministic rule checks over measurements), *schema conformance* (scoring raw input against a registered methodology, e.g. an MRV schema), *checksum / content-hash verification* (tamper-evidence as a first-class witness method), *AI classification and query* (model-based evaluation with confidence scores), *algorithmic review agents* (composable checks with verifiable inputs).

What composes the methods is a **witness policy** — `org.witness.policy`, a signed, versioned record that names which methods are admissible, what a witnessing party must be (a role, or a credential the party holds), and how the results combine. It never names specific signer DIDs; the binding of a role to concrete DIDs lives in a separate `org.witness.registry` record the policy references, so another project adopts the same policy by pointing it at a different registry. Common Expression Language carries the combination rule — the same expression, evaluated over the witness records on a claim, returns the same verdict wherever it runs (illustrative — names provisional):

```jsonc
{
  "$type": "org.witness.policy",
  "version": "2026-05-12.1",
  "methods": ["peer", "pgs", "expert", "self", "cel", "schema", "checksum", "ai", "agent"],   // the admissible set
  "roles":   { "auditor": { "registry": "at://did:plc:project/org.witness.registry/auditors" } },  // role → DIDs binds in the registry, not here
  "combine": {
    "lang": "cel",
    // the records on the claim clear the policy when EITHER branch is true:
    "expr": [
      "size(records.filter(r, r.witnessMethod == 'peer' && r.verdict.result == 'pass')) >= 3",
      "  && records.exists(r, r.witnessMethod == 'schema' && r.verdict.score >= 0.7)",
      "  || records.exists(r, r.witnessMethod == 'expert' && r.party in roles.auditor && r.verdict.result == 'pass')"
    ]
  }
}
```

A low-trust path and a high-quorum-plus-automation path can satisfy the same claim; a project picks the methods, the branches, and the registry — Waka picks none of them.

Underneath the catalog is the design-space argument. Deciding what counts as valid input — measured against established methods, schemas, certification standards, or lines of authority — is itself a design space, and treating it as one is the proposition. In practice a project can design the space of authority around its data: who is authorized to witness, by which method, with what quorum or weight — letting local and external stakeholders enter into structured collaborations around the auditing and verification of the data, with the originators of the data included as far as possible. The political question (who gets to say) and the technical question (by what method) are answered together, per context, inside the engine. Human–machine interaction at the verification layer, composed this way, is what the `org.witness.*` sidecar layer adds over the Hypercerts v2 evaluation record.

---

## The anchoring and issuance layer — substrate roles

Anchoring is spread deliberately across substrates so that each does what it does best and none becomes load-bearing:

| Layer | Substrate | Role |
|---|---|---|
| Identity container | Any wallet, any DID method | Versatile login; bring-your-own-credential |
| Authoring / publication | AT Protocol PDS | Signed, canonical, portable records |
| Group publishing identity | CGS (AT Protocol Certified Group Service) | Org/project group `did:plc` while users still log in with any wallet |
| Long-term durability | IPFS + Filecoin | 50-year archival with economic storage proofs |
| Interactive triggers | Ethereum Attestation Service | On-chain attestations that fire covenant contracts when witness thresholds are met |
| Ecological anchoring | Regen Network `x/data` | BLAKE2b-256 anchoring for ecological-credit workflows |
| Portable evaluation records | Hypercerts v2 | Claim records cross-compatible with crediting/certification registries |
| Stream publication / data feed *(candidate)* | Ceramic | A verifiable claim stream with built-in Ethereum-anchored ordering; a feed downstream consumers in the Ceramic / ComposeDB ecosystem can subscribe to and verify — overlaps with the PDS role rather than the EAS one; under evaluation given Ceramic's current trajectory |
| Machine conformance | CEL (Common Expression Language) | Deterministic schema and rule evaluation inside machine witness |

The point of spreading the work this way is that a single claim is portable across the whole table: it can be archived for durability on IPFS/Filecoin, published canonically on a PDS, used to fire a trigger attestation on EAS, anchored for an ecological-credit workflow on Regen, indexed as an evaluation record by Hypercerts, and — where downstream consumers live there — published as a Ceramic stream — without being re-shaped for any one of them. Concretely, one authoring step leaves the same object addressable at a handful of substrate-native identifiers (illustrative):

```
PDS     at://did:plc:ewvi7nxzyoun6zhxrhs64oiz/org.witness.composition/3kq7lm2x4c2zk
IPFS    ipfs://bafybeihn5e2k...          (+ a Filecoin storage deal for the evidence bundle)
EAS     0x9c8f1a...e2                    (attestation UID; fires the covenant contract)
Regen   blake2b-256:7f3a91...c1          (anchored via x/data MsgAnchor)
HC v2   org.hypercerts.context.evaluation record in the evaluator's PDS, sidecar-linked
```

This is why Waka sits upstream of any particular issuance, crediting, or certification ecosystem rather than being one: its job is to produce durable building blocks of verifiable truth that aggregate and bundle into a range of issuable instruments. The operational consequence is **measure once, report everywhere** — a record entered once flows into every accounting, certification, contracting, and funding system that needs it, without re-entry and without capture by the platforms downstream.

---

## Roadmap — programmable remittance

Waka's motivation is in part derivative of work on ecological institutions — land titles and stewardship covenants with an embedded sovereign digital record, the project-level DID as their endpoint. The near-term roadmap extends the anchoring layer into automated downstream triggers: smart contracts whose covenant conditions are fired by witnessed claims once thresholds are met; conventional contracting systems — insurance payouts, grants, service agreements — keyed to claims; and programmable remittance from funds, where a smart contract takes claims as input and disburses stewardship payments, tenure extensions, or ecological-credit settlements. The claims engine is not an end in itself; it is the input substrate for automated, legible, accountable remittance and enforcement — the layer that makes living covenants programmable without removing human oversight.

---

## The two example modes in the demos

- **Data trust example** — skips the claims engine. Authenticated users upload resources and anchor them directly across substrates: sovereign storage and access with on-chain receipts.
- **Claims engine example** — uses all three layers, exercising the VC witness model as a design space for verification. The full thesis.

Both modes are live; which one a project uses depends on whether it needs structured claims or only sovereign archival.

---

## Codebase architecture

**Stack:** Next.js (App Router) · TypeScript · Tailwind CSS v4 · Supabase (PostgreSQL + Auth + RLS) · ReactFlow · Pinata (IPFS) · EAS SDK · AT Protocol

**Key directories:**
```
app/
  actions/          # Server actions (auth, resources, claims, anchoring)
  components/       # UI — ProjectPageClient, ClaimsEngine, ResourceInspector, ProjectGraphClient
  projects/[id]/    # Project detail pages
  share/[slug]/     # Public read-only snapshot pages
lib/
  ontologies/       # Schema definitions (SDG, Ubuntu, ecological, etc.)
  supabase/         # Supabase clients (server, client, middleware)
scripts/
  export-public-snapshot.ts  # Snapshot export for public share pages
```

**Data model:**
- **Resources** — proposals, actions, observations, general resources per project. Nodes in an evidence graph.
- **Validation nodes** — witness-method containers producing witness records (human or machine) against a claim subject.
- **Claims** — the witnessed subject (analog to `org.hypercerts.claim.activity`). Carries title, description, linked resources, and an accreting set of witness proofs.
- **Anchors** — content-addressed records pointing at PDS AT-URIs, IPFS CIDs, EAS attestation UIDs, and Regen BLAKE2b hashes.

**Identity hierarchy** (the nested-container model from *The identity layer* above):
- **Org DID** (`did:plc`) — collective identity, membership, cross-project coordination
- **Project DID** (`did:plc`) — sovereign stewardship subject, the endpoint embedded in covenants and legal documents
- **User DID** — any method (`did:pkh`, `did:plc`, `did:ethr`, `did:key`), used for authentication and as witness signing key inside org/project records

---

## Developer setup

### Prerequisites
- Node.js 18+
- A Supabase project (PostgreSQL + Auth)
- (Optional) Pinata account for IPFS
- (Optional) Ethereum wallet + EAS Sepolia deployment for attestations
- (Optional) AT Protocol credentials for Hypercerts

### Environment
Create `.env.local` with:

```env
# Required
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
NEXT_PUBLIC_SITE_URL=http://localhost:3000

# IPFS (Pinata)
PINATA_JWT=
NEXT_PUBLIC_GATEWAY_URL=

# EAS (Ethereum Attestation Service)
EAS_PRIVATE_KEY=
EAS_SCHEMA_UID=

# Hypercerts (AT Protocol)
ATPROTO_SERVICE_HANDLE=
ATPROTO_APP_PASSWORD=
```

### Run

```bash
npm install
npm run dev
```

### Database
Run migrations in order from `supabase/migrations/`. See `docs/RUN_MIGRATIONS.md` for the full sequence.

### Exporting a public snapshot

```bash
npx tsx scripts/export-public-snapshot.ts <projectId> <slug>
```

Copies attachments to the public storage bucket and writes a snapshot JSON to `app/share/_snapshots/<slug>.json`. Commit the file and deploy to publish the public page.

---

## Status

Active development. The core loop — resource collection → witnessed claims → multi-layer anchoring — is complete in its current form. Ongoing work:

- **M2** — EAS on-chain attestation (complete)
- **M3** — Validation node as schema conformance engine (complete)
- **CGS integration** — project/org-level group `did:plc` publication via Hypercerts' Certified Group Service (planned)
- **Witness taxonomy lexicons** — `org.witness.*` sidecar records for peer review, PGS, machine witness, policies, registries, methodology tags (namespace under active development)
- **Covenant trigger layer** — reference smart contract standard and programmable remittance from witnessed claims (in design)
- **Filecoin mirroring** — High Durability archival toggle (planned)

---

*See the [Conceptual Position](./CONCEPTUAL_POSITION.md) for the conceptual overview and the video walkthroughs, or the [README](./README.md) for the short version.*
