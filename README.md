# Waka

<table>
  <tr>
    <td width="50%" valign="top">
      <video src="https://github.com/user-attachments/assets/7abf33c1-2f04-4297-99a7-30b280f6d8bd" controls></video>
      <p><strong>Data trust walkthrough</strong> · ~3 min · authenticated upload and direct anchoring</p>
    </td>
    <td width="50%" valign="top">
      <video src="https://github.com/user-attachments/assets/de9634a2-6495-4e3b-9077-7fec67d79f9f" controls></video>
      <p><strong>Claims engine walkthrough</strong> · ~6 min · full three-layer flow with witnessing</p>
    </td>
  </tr>
</table>

## What Waka is

Waka is **a perceptual system for distributed protocols, contracts, and agents to ingest verified information about the state of the world.** Smart contracts, conventional contracts, funding protocols, certification systems, grant programs, insurance underwriters, and autonomous agents all need verified information about the state of the world in order to act. Waka's job is to produce that verified information — under a verification design space the originating communities control — and to make it available to every downstream system through portable, protocol-agnostic anchors. In this sense Waka is not an app, not a database, and not an issuance system. It is a perception layer that sits upstream of every system that needs to act on claims.

Operationally, it is **a coordination interface for the creation and management of sovereign data and claims** — a working interface that communities, stewards, and institutions use every day to produce durable, witnessed records. It is a **versatile identity system** on the front, a **claims engine** in the middle, and a **versatile anchoring and issuance system** at the back. Identity is any wallet and any DID method, contained within a common container. Anchoring and issuance spans IPFS / Filecoin, AT Protocol PDS, Ethereum Attestation Service, Regen Network, and Hypercerts. Between them sits the claims engine — the core of Waka — which wraps raw resources and evidence in a structured process of human and machine witnessing, producing validated, portable claims that the communities producing them retain ownership of. The identity and anchoring layers are deliberately versatile so that the claims engine is never bound to a single protocol substrate, and so that the perception layer above can serve any downstream protocol that needs to read from it.

*Waka* *(te reo Māori)* — a canoe. A vessel that carries people and knowledge. The name is a working name, taken deliberately from the collaborative context in which the project was developed: a shared craft assembled by many hands, carrying the records, practices, and claims of the communities that travel in it. All example projects in the video demonstrations are real and under active development. Waka is being built in relational integrity with the communities, stewards, and institutions it is designed to serve — not as a technology in search of a use case.

**Waka** is deployed at **[waka-six.vercel.app](https://waka-six.vercel.app)**

**[Kiwaatule Sandbox Project — waka-six.vercel.app/share/kiwaatule-2030](https://waka-six.vercel.app/share/kiwaatule-2030?view=feed)** — a read-only public snapshot of the Nalubaga-Validate Community project. The sandbox includes the full graph view, claims feed, and map. This is for viewing and demonstration purposes only — you cannot write to Waka, expand resources, or download attachments from this view. All data shown is from a real project under active development.

---

## What Waka is for

Waka is a **step up from Google Drive** for anyone recording data that they intend to use for accounting, contracting, certification, or any other process where the data needs to be authoritative in order to do things with it. You keep full control and ownership of your data and your identity, and you get a structured way to turn your files, records, and resources into something consequential.

Waka is built on the principle of **measure once, report everywhere**. A community, a steward, or an institution should be able to record an observation, a measurement, or a decision once — and have that record flow into every accounting, certification, contracting, and funding system that needs it, without re-entering the data and without ceding ownership to the platforms that consume it downstream.

Beneath that plain framing, Waka is infrastructure for **claims sovereignty**. Data sovereignty means you own the raw information. Claims sovereignty means you own the validated assertions made about that information — the witnessed, verified, portable records that drive contracts, funding decisions, certifications, governance, and accountability.

> **Data doesn't run the world. The claims we make upon that data run the world.**

Waka is the layer that turns raw evidence into durable, verifiable, witnessed claims — under a verification design space that the communities producing the data control.

---

## Core principles

Waka is shaped by a handful of deliberate, opinionated commitments.

### Protocol agnostic at the root
Identity and authentication work across any wallet and any DID method. Claims and data can be anchored across a range of protocols — IPFS / Filecoin, EAS, AT Protocol PDS, Regen Network, Hypercerts. Protocol substrate choice is a feature, not a bug. Each protocol gets to do what it does best.

### Durable on a 50-year horizon
Waka is intended to be durable — a hard encoding of information and assertions about what has happened between people, the planet, and the many forms of interaction that produce consequence. The target is **fifty years**. Every architectural decision is appraised against that horizon.

### Claims sovereignty, not just data sovereignty
Waka does not just store raw data. It integrates human and machine verification to produce **validated** claims. A Waka record carries its evidence, its witnessing methodology, and its signatures with it, so anyone can verify what was claimed and who or what witnessed it, without having to ask.

### Verification is political — so we build a design space, not a single answer
Technical data verification is largely a political question: *who gets to say what data is valid or not?* Rather than asserting one answer, Waka creates a design space. The claims engine lets each project specify the witnessing and verification process that is appropriate to its context. A core value embedded in that design space: verification should **include and integrate the originators of the data** as much as possible.

### A durable save point for existing practice
Waka does not try to replace existing practices of resilience, stewardship, governance of AI and data, or commons management. Instead it makes those practices **legible and durable** — a save point for decision-making that already works, not a new governance system imposed from outside. Meet communities where they are.

### Relational integrity over technology in search of a use case
The project is built in context with real collaborators and real projects. This is a deliberate resistance to the pattern of building infrastructure first and hunting for adopters second.

---

## Technical positioning

*A short version follows here. For the full technical position document, see [**TECHNICAL_POSITION.md**](./TECHNICAL_POSITION.md).*

Waka's technical shape follows directly from the three definitions above. A claims engine is only useful if it can ingest identity from anywhere and emit anchored records anywhere. So Waka is built as a **versatile identity system and a versatile anchoring and issuance system wrapping a claims engine** — a deliberate sandwich in which the middle layer is the novel contribution and the outer layers are built for portability across substrates. The rest of this section walks through the consequences of that posture.

### Resistance to platform and protocol lock-in

Waka follows a common data model designed for maximum **interoperability, extensibility, and discoverability** with concurrent projects in the impact accounting, evaluation, and regional financing ecosystem. The system is assembled so that no single protocol becomes load-bearing:

- **DID identity** — any wallet, any DID method, contained within a common container
- **AT Protocol PDS** — sovereign authoring and publication
- **IPFS + Filecoin** — long-term durable storage
- **Ethereum attestation (EAS)** — interactive trigger layer
- **Regen Network (`x/data`)** — ecological anchoring

### Standards and lineage

Earlier iterations of Waka built foundationally on **DIDs and Verifiable Credentials**. The current version takes AT Protocol primitives — **PDS, CEL, CGS, and `did:plc` logic** — as more foundational, without compromising the versatility of identity login or anchoring and issuance. The VC data model still lives inside the claim content, carrying cross-DID portability.

Waka follows the **lexicon standards being developed around Hypercerts v2**, adopting **activities** as the atomic unit of claims construction. Waka is a peer contributor to the Hypercerts lexicon ecosystem — not a subordinate app — and publishes `eco.waka.*` extensions for its novel layers (witness taxonomy, covenant triggers, ecological institution containers).

### Upstream of issuance

The project is motivated by years of work within environmental accounting and crediting systems. The logic underlying Waka's claims is to sit **upstream of any particular issuance, evaluation, crediting, or certification ecosystem** — producing durable building blocks of verifiable truth that can aggregate and bundle into a range of anchorable or issuable instruments. Waka does not replace issuance systems; it makes them legible, accountable, and composable.

### The claims engine — human and machine witness

Waka wraps the VC core logic in a sophisticated **claims engine** that retains the defining feature of verifiable credentials: a range of witness processes can be deployed on data subjects. Two classes of witness compose:

- **Human witness** — attestation-based verification. Peer review, participatory guarantee systems, expert review, self-attestation. Credentials and proof of authority.
- **Machine witness** — algorithmic and AI-based conformance. CEL expression evaluation, schema conformance against registered methodologies, checksum / content-hash verification, AI query and classification, review agents.

Multiple witness methods can coexist on the same claim. Peer review and participatory guarantee and a CEL conformance check and an AI classification, all at once. Other systems force you to pick one. Waka lets them compose. Human–machine interaction at the verification layer is one of Waka's novel contributions to the Hypercerts v2 evaluation lexicon.

### The claims engine as an organizational perception system

The claims engine is a kind of organizational or institutional **perception system**. It takes raw input — resources and artifacts — and structures how well they conform to existing analogs, schemas, benchmarks, and symbolic references. Perception systems don't just observe the world; they measure what they observe against what they already know. This measuring of new input against established methodologies, certification standards, and schemas is a novel proposition of Waka. It's what turns a document store into a claims engine.

### Three core pillars — versatile identity and anchoring wrapping a claims engine

Waka is three layers, and every feature maps to one of them:

1. **Identity (front)** — a highly versatile authentication system. Any wallet, any DID method (`did:pkh`, `did:plc`, `did:key`, `did:ethr`, and more), contained within a common container. The Supabase session is application convenience; the DID is the sovereign identity. Org- and project-level group DIDs (`did:plc` via CGS) sit above individual user DIDs so that collective stewardship subjects can sign claims on their own behalf.
2. **Claims engine (middle)** — the novel core of Waka. Validation nodes, witness methods, schemas, methodologies, and benchmarks. Takes raw resources and artifacts and produces validated, portable claims through an integrated mix of human and machine witness. This is the perception layer described above; it is what separates Waka from a document store or an attestation service.
3. **Anchoring and issuance (rear)** — a versatile anchoring and issuance system. IPFS / Filecoin for long-term durable storage, EAS for Ethereum-side trigger attestations, Regen Network `x/data` for ecological anchoring, AT Protocol PDS for sovereign authoring and publication, Hypercerts for portable evaluation records. Each protocol does what it does best; no single substrate becomes load-bearing.

The identity layer and the anchoring layer are intentionally held to the same design constraint: **maximum versatility at both ends**, so that the claims engine in the middle can be used without pledging allegiance to any one stack. This wrapping is the architectural expression of Waka's protocol-agnostic thesis.

### Two example modes in the demos

- **Data trust example** — skips the claims engine. Authenticated users upload resources and anchor them directly across substrates. Owned Google Drive with on-chain receipts.
- **Claims engine example** — uses all three layers. Explores the VC witness model as a design space for verification. The full Waka thesis.

### Near-future roadmap: programmable remittance

Waka's motivation is in part derivative of work on **Ecological Institutions** — land covenants and stewardship agreements whose enforcement is automated by witnessed claims. Programmable remittance from funds using smart contracts with claims as input is in the near-term roadmap: the claims engine is not an end in itself but the input substrate for automated, legible, accountable remittance and enforcement.

---

## Video walkthroughs

Six short videos introduce the system end-to-end (~5 minutes each):

| # | Title | Link |
|---|-------|------|
| 1 | Introduction, Credentials, and Project Configuration | [Watch](https://www.loom.com/share/9515461fcd8d49db913fb4dcd58c5f59) |
| 2 | Claims Engine Basics | [Watch](https://www.loom.com/share/de660739c9ef41a39534876c80e4af46) |
| 3 | Validating Claims Using Schemas | [Watch](https://www.loom.com/share/1873113f7dea40b5b5a3d8393a5f4d64) |
| 4 | Validating Claims Across Different Ontologies | [Watch](https://www.loom.com/share/d1f8aced3f5f4375b90f835c17332c57) |
| 5 | Anchoring Claims and EAS Registered Schema | [Watch](https://www.loom.com/share/d6bb1b796a40493cbfe1d8e0b2ad7fed) |
| 6 | Anchoring on EAS, Hypercerts, and IPFS | [Watch](https://www.loom.com/share/317fa962ba6a494cacaf7adac64c55ce) |

All example projects in these walkthroughs are real and under active development.

---

---

## Architecture

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

**Identity hierarchy:**
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
- **Witness taxonomy lexicons** — `eco.waka.*` extensions for peer review, PGS, machine witness, methodology tags (in design)
- **Covenant trigger layer** — reference smart contract standard and programmable remittance from witnessed claims (in design)
- **Filecoin mirroring** — High Durability archival toggle (planned)

---

*Waka is a working name.*
