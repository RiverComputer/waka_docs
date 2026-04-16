# Waka — Technical Position

A short technical position document, designed to be linkable from the project website alongside the [README](./README.md).

---

## Posture: resistance to platform and protocol lock-in

Waka is built to **resist platform or protocol lock-in** while following a common data model that ensures maximum **interoperability, extensibility, and discoverability** with concurrent projects in the impact accounting, evaluation, and regional financing ecosystem.

Choosing to anchor across a range of protocol substrates is a **feature, not a bug**. It lets each protocol do what it does best, and it prevents any single substrate from becoming load-bearing for the whole system.

Waka assembles the following substrates under a common data model:

| Layer | Substrate | Role |
|---|---|---|
| Identity container | Any wallet, any DID method | Versatile login, bring-your-own-credential |
| Authoring / publication | AT Protocol PDS | Signed, canonical, portable records |
| Long-term durability | IPFS + Filecoin | 50-year archival with economic storage proofs |
| Interactive triggers | Ethereum Attestation Service | On-chain attestations that fire covenant contracts |
| Ecological anchoring | Regen Network `x/data` | BLAKE2b-256 anchoring for ecological-credit workflows |
| Machine conformance | CEL (Common Expression Language) | Deterministic schema and rule evaluation |
| Group publishing identity | CGS (AT Protocol Certified Group Service) | Org/project group `did:plc` so users still log in with any wallet |

---

## Standards and lineage

Earlier iterations of Waka were built foundationally on **Decentralized Identifiers** and the **Verifiable Credentials** data model.

The current version takes AT Protocol primitives — **PDS, CEL, CGS, and `did:plc` logic** — as more foundational, without compromising versatility of identity login or protocol anchoring and issuance. The VC data model still lives inside the content of claim records, carrying cross-DID witness portability. Nothing about VCs has been given up; they have been tucked inside a more operationally mature publication substrate.

Waka follows the lexicon standards being developed around **Hypercerts v2**, adopting **activities** as the atomic or central unit of claims construction. Waka is a **peer contributor to the Hypercerts lexicon ecosystem**, not a subordinate app. It adopts Hypercerts lexicons at the outer layer, and publishes `eco.waka.*` extensions for its novel layers:

- witness taxonomy (peer review, participatory guarantee, expert review, self-attestation, CEL conformance, AI witness, schema conformance, checksum verification)
- ecological institution containers (land covenants whose enforcement is automated by witnessed claims)
- covenant trigger attestations (EAS-class attestations that fire smart contracts when witness thresholds are met)

This is how Waka stays a standards participant without becoming a standards captive.

---

## Motivation — upstream of issuance

The project is motivated by multiple years of experience working within the environmental accounting and crediting systems. The logic underlying Waka's claims is to sit **upstream of any particular issuance, evaluation, crediting, or certification ecosystem**.

Waka's job is to produce **durable building blocks of verifiable truth** that can be aggregated and bundled together to generate a range of different anchorable or issuable instruments. It is not itself an issuance, crediting, or funding system. It sits one layer upstream, producing claims that drive those systems.

This is why protocol agnosticism matters so much: to be meaningfully upstream of any issuance ecosystem, Waka has to work with all of them. Committing to one stack would collapse the moat.

### Measure once, report everywhere

The operational consequence of sitting upstream is **measure once, report everywhere**. A record entered once — an observation, a measurement, a decision, a piece of evidence — should be able to flow into every accounting, certification, contracting, and funding system that needs it, without being re-entered and without being captured by any of the platforms that consume it downstream.

The common data model, the portable witness records, and the protocol-agnostic anchoring layer are the machinery that makes this possible. A Waka claim is not shaped to fit a particular reporting regime; it is shaped to be reusable across as many of them as its witness methodology supports. Each downstream system reads the same durable substrate, applies its own rubrics, and decides what to do with what it finds.

---

## The claims engine — witness as a verification design space

Waka wraps a sophisticated **claims engine** around the VC core logic. The claims engine retains the defining feature of the VC data model: **a range of different witness processes or protocols can be deployed on data subjects**. Waka builds on two classes of witness:

### Human witness — attestation-based

A person or group asserting something about a claim.

- **Peer review** — multiple witnesses, no role constraint
- **Participatory guarantee system (PGS)** — quorum-based group attestation
- **Expert review** — single credentialed auditor
- **Self-attestation** — the originator asserting their own claim

Human witness covers credentials, proof of authority, and peer participatory verification.

### Machine witness — algorithmic and AI-based

An algorithm, AI model, or schema-conformance check asserting something about a claim.

- **CEL expression evaluation** — deterministic rule checks over measurements
- **Schema conformance** — scoring raw input against registered methodologies (e.g., Regen MRV)
- **Checksum / content-hash verification** — tamper-evidence as a first-class witness method
- **AI classification and query** — LLM-based evaluation with confidence scores
- **Algorithmic review agents** — composable checks with verifiable inputs

### Composition

Multiple witness methods can coexist on the same claim. Peer review + PGS quorum + CEL conformance + AI classification, all at once. Other systems force you to pick one. Waka lets them compose.

**Human–machine interaction at the verification layer** is one of Waka's novel contributions — an extension to the Hypercerts v2 evaluation lexicon, building on the logic of verifiable credentials and DIDs.

### A design space, not a single answer

Technical data verification is largely a political question: *who gets to say what data is valid or not?* The claims engine creates a **design space** around the political and technical substrate through which we verify data — it does not pretend there is one right answer. Each project defines the witnessing methodology appropriate to its context, and the validation and verification of information is built to **include and integrate the originators of the data** as much as possible.

---

## Claims engine as organizational perception system

The claims engine is a kind of **organizational or institutional perception system**. It takes the raw input of resources and artifacts, and structures — through human or machine means — how well that raw input conforms to existing analogs, schemas, benchmarks, and symbolic references.

> Perception systems aren't just observing the world. They're taking that raw observation and measuring it according to what they already know.

This **measuring of new input against established methodologies, certification standards, schemas, and symbolic references** is the novel proposition of Waka. It is what separates the claims engine from a document store, an attestation service, or a blockchain indexer. Waka turns raw evidence into structured, measured, comparable claims — inside a design space the community of practice controls.

---

## Claims sovereignty

> **Data doesn't run the world. The claims we make upon that data run the world.**

Waka is a tool for **claims sovereignty** as much as it is a process of doing data sovereignty. Raw data is inert. Claims — validated assertions about data, witnessed under explicit processes, attached to identities — are what drive contracts, agreements, funding allocation, certification, insurance, and institutional decisions.

Owning your data is a prerequisite. Owning the claims made upon it is the prize.

---

## The three core pillars

Waka is three layers:

1. **Identity** — a highly versatile authentication system on the front. Any wallet, any DID method. The application session is convenience; the DID is the sovereign identity.
2. **Claims engine** — the middle layer. Validation nodes, witness methods, schemas, benchmarks, measurement. Produces witnessed claims from raw resources.
3. **Anchoring and issuance** — a versatile anchoring and issuance system at the rear. IPFS/Filecoin for durability, EAS for triggers, Regen for ecological anchoring, Hypercerts for portable evaluation records, PDS for sovereign publication.

Every feature in Waka maps to one of these three pillars.

### Two example modes demonstrate the layering

- **Data trust example** — skips the claims engine. Authenticated users upload resources and anchor them directly across substrates. This is Waka as a step up from Google Drive for anyone recording data they intend to use for accounting, contracting, or certification — data that needs to be authoritative in order to do things with it. Full sovereignty over storage and access, structured upload, direct anchoring.
- **Claims engine example** — uses all three layers. Explores the VC witness model to create a design space for verification. This is the full Waka thesis.

Both modes are available. Which one a given project uses depends on whether it needs structured claims or just sovereign archival.

---

## Near-future roadmap — programmable remittance

Waka's motivation is in part derivative of work on **Ecological Institutions** — the idea that a land title or stewardship covenant can have an embedded sovereign digital record with the project DID as its endpoint. As such, Waka is highly motivated to integrate claims issuance into:

- **Smart contracts** — covenant triggers fired by witnessed claims when thresholds are met
- **Conventional contracting systems** — insurance payouts, grants, service agreements
- **Programmable remittance from funds** — smart contracts with claims as input, disbursing stewardship payments, tenure extensions, and ecological-credit settlements

This is the near-future roadmap for Waka development. The claims engine is not an end in itself. It is the input substrate for automated, legible, accountable remittance and enforcement — the layer that makes "living covenants" programmable without giving up human oversight.

---

## Peer contribution and collaboration

Ultimately, Waka is a **participant in and extension of the standards developed by Hypercerts**, allowing it to interact and collaborate with a range of other projects drawing on that system. This peer posture — neither subordinate to nor competitive with Hypercerts — is how Waka's protocol-agnostic ambition stays coherent in a real ecosystem of other builders.

The claims engine, the witness taxonomy, the covenant trigger layer, and the ecological institution container are Waka's novel contributions. Everything else — the identity substrate, the publication substrate, the durability substrate, the trigger substrate — is contributed by peers in the ecosystem, and Waka is designed to compose with those peers rather than replace them.

---

*See the [README](./README.md) for the accessible introduction, the video walkthroughs, architecture details, and developer setup.*
