# Waka

*Waka* (te reo Māori) — a canoe. A vessel that carries people and knowledge over time and space.

Waka was initially developed in collaboration with a confederation of iwi in the Northland region of Aotearoa (New Zealand). As a working title, the name reflects an ethos of technical innovation informed by a situated understanding of context and culture.

## Video walkthroughs

Two short video introductions. Start with the first — it's the fastest picture of what the system does. The second goes deeper into the claims engine.

<table>
  <tr>
    <td width="50%" valign="top">
      <video src="https://github.com/user-attachments/assets/7abf33c1-2f04-4297-99a7-30b280f6d8bd" controls></video>
      <p><strong>1 · Sovereign data trust</strong> · ~3 min<br/>Waka as a step up from Google Drive. Authenticated upload, community ownership of the records, and direct anchoring to durable public infrastructure. The shortest tour of the system end to end.</p>
    </td>
    <td width="50%" valign="top">
      <video src="https://github.com/user-attachments/assets/de9634a2-6495-4e3b-9077-7fec67d79f9f" controls></video>
      <p><strong>2 · Claims engine</strong> · ~6 min<br/>The full three-layer flow — identity, claims engine, and anchoring — showing how raw resources become witnessed, validated claims under a verification process the community producing the data controls.</p>
    </td>
  </tr>
</table>

## What Waka is

Waka is a digital vessel for sovereign data and claims management, and a medium of machine-readable witness about the state of the world. It is an engine of data-driven storytelling that upholds the contextual integrity of information through the first principles of its design.

In contrast to dominant narratives about the big, abstract, scaleless, placeless quality of data, most data is localized to particular people and contexts. And increasingly, localized information — the kind not easily scraped from the internet and public documents — is needed to inform a range of sociotechnical infrastructures: autonomous systems, agent training data and context, smart contracts, asset evaluations, risk assessments, fund remittance, policy decisions.

As the convergence of distributed and autonomous systems intensifies, individuals and groups require greater control — not only over the systems that store data, but over the means by which data is deemed authoritative and factual. Both our storage systems and the processes that confer authority on what we store are fundamentally antiquated. They defer power to central authorities: the platforms that host and profit from the data, and the credentialing regimes that decide whose say-so counts as fact.

Waka is built as a vessel for localized record, where not only the data, but the claims made upon that data, are situated with contextual integrity. Two capabilities make that possible.

- **Sovereign storage.** Data storage and access stay contextual to the communities Waka serves, rather than in service to the platforms that host the data and profit from it. Waka anchors information — distributed, long-format — across a range of sovereign storage substrates.
- **Programmable witness records.** Waka provides a claims engine: a design space of human and algorithmic witness methods that assert factuality and authority over bodies of evidence. Instead of a PDF and a credential, the process by which a body of evidence becomes a claim is something a community can specify, inspect, and run — and run again.

To witness is to see something happen and carry that sight into a collective act of sensemaking — *did this or that occur* — as part of a social process rather than alone. Programming witness means building that act out of modular parts: in Waka's claims engine, composable witness methods and policies are a designable form of validation criteria for the creation of claims. A witness method is one way of seeing; a witness policy is the signed, portable rule that composes methods into a verdict. (More on the notion itself in [On Witness](./docs/ON_WITNESS.md).)

Those claims can then be instrumentalized — to issue certificates, build crediting systems, trigger smart contracts, and a great deal else.

> **Data doesn't run the world. Claims do.**

Operationally, Waka is a coordination interface for the creation and management of sovereign data and claims. People use it to gather evidence, run that evidence through an explicit process of witnessing, and produce durable, witnessed records — records that carry their evidence, their method of verification, and their signatures with them, so anyone reading one can see what was claimed and who or what witnessed it.

Stepping back, Waka is a perceptual system: it takes raw input and structures how that input is witnessed, and how it conforms to established methods, schemas, and lines of authority, before any downstream system acts on it. The scope is deliberately narrow — Waka is not an attempt to re-derive distributed protocols or to settle how anyone knows anything. It is a working interface and a small set of well-chosen primitives, and the mechanics live in the claims engine and its witness methods.

## What Waka is for

A partial list of what you can build with Waka:

- **The next generation of land trust models** — land titles and stewardship covenants with digital addressing built in, so a parcel of land can be a sovereign repository of memory, currency, and contracts: a record of what has happened on it, a place value can settle, an endpoint that legal instruments can point at.
- **A sovereign system for community, tribal, and organizational consent** — a durable record of who agreed to what, under whose authority.
- **A monitoring and reporting repository for environmental protection and stewardship** — observations, measurements, and management decisions, witnessed and durable, owned by the people doing the work.
- **A sovereign claims registry for risk mitigation** — structured in the formats insurance providers can ingest, so the work a community does to reduce its risk is legible to the people pricing it.
- **An instrument for social workers and civic actors** — mapping responsibility and authority around hard cases like child protective services, social work, and contested duty of care, where the question is often who was supposed to act, and on whose say-so.

These range from the practical to the speculative. At the practical end, Waka is a step up from Google Drive: file storage and governance you control, with composable verification on top that turns your files and records into witnessed claims. At the far end, Waka is the tooling for a category of institution that does not really exist yet — ecological institutions, sovereign land trusts, sovereign data trusts, living covenants — agreements whose terms are tracked, witnessed, and over time enforced by the records the community itself produces. That category is the subject of a body of work on ecological institutions, and Waka is the part of it you can run.

Underneath both readings, Waka produces validated data. Data sovereignty means you own the raw information. Claims sovereignty means you own the validated assertions made about it — the witnessed, verified, portable records that drive contracts, funding, certification, governance, and accountability. Record an observation, a measurement, or a decision once, and have it flow into every accounting, certification, contracting, and funding system that needs it, without re-entering it and without ceding ownership to the platforms downstream — measure once, report everywhere.

## The three layers

The two capabilities above — sovereign storage and programmable witness — are delivered as three layers, and every feature belongs to one of them:

- **A versatile identity system on the front.** Log in with any wallet and any DID method. The application session is a convenience for using the product; the DID is the sovereign identity. A system like this has to run on the open standards that already define the internet, rather than ask everyone to log into one app.
- **A claims engine in the middle.** The claims engine wraps raw resources and evidence in an explicit, composable process of human and machine witnessing, and produces validated, portable claims. The claims engine is the perception layer, and it is the part of Waka that is genuinely new.
- **A versatile anchoring and issuance system at the back.** Durable storage, sovereign publication, on-chain triggers, ecological anchoring, portable evaluation records — spread across the substrates that each do those jobs best, so no single protocol becomes load-bearing and what Waka produces ports cleanly into the existing infrastructure for crediting, certification, contracting, evaluation, and insurance.

The identity layer and the anchoring layer are held to the same constraint — maximum versatility at both ends — so the claims engine in the middle is never bound to one stack. The internals of all three are in the [Technical Position](./TECHNICAL_POSITION.md).

## Core principles

Waka is shaped by a handful of deliberate, opinionated commitments.

### Protocol agnostic at the root
Identity and authentication work across any wallet and any DID method. Claims and data anchor across a range of protocols — IPFS / Filecoin, EAS, AT Protocol PDS, Regen Network, Hypercerts. Substrate choice is a feature, not a bug: each protocol does what it does best, and to sit meaningfully upstream of every issuance, crediting, and certification ecosystem, Waka has to be able to work with all of them.

### Durable on a 50-year horizon
Waka is meant to last — a hard encoding of information and assertions about what has happened between people, the planet, and the many forms of interaction that produce consequence. The target is fifty years, and every architectural decision is appraised against that horizon: storage substrates, identity substrates, and claim formats that do not depend on a single company surviving.

### Claims sovereignty, not just data sovereignty
Waka does not just store raw data; it integrates human and machine verification to produce validated claims. A Waka record carries its evidence, its witnessing methodology, and its signatures with it, so anyone can verify what was claimed and who or what witnessed it without having to ask.

### Verification is both a technical and political process
Whether a piece of data counts as valid is not a neutral technical fact. It is an overlap of authority and technical resolution — who gets to say, and by what method. Rather than ship one answer, Waka builds a design space: each project specifies the witnessing and verification process appropriate to its context, and the design space is built so the originators of the data are included and integrated as far as possible.

### A durable save point for existing practice
Waka does not try to replace existing practices of resilience, stewardship, governance of AI and data, or commons management. It makes those practices legible and durable — a save point for decision-making that already works, not a new governance system imposed from outside. Meet communities where they are.

### Relational integrity over technology in search of a use case
The project is built in context with real collaborators and real projects. The example projects in the demonstrations are real and under active development.

## See it running

**Waka** is deployed at **[waka-six.vercel.app](https://waka-six.vercel.app)**

**[Kiwaatule Sandbox Project — waka-six.vercel.app/share/kiwaatule-2030](https://waka-six.vercel.app/share/kiwaatule-2030?view=feed)** — a read-only public snapshot of the Nalubaga-Validate Community project, with the full graph view, claims feed, and map. The sandbox is for viewing and demonstration only — you cannot write to Waka, expand resources, or download attachments from it. All data shown is from a real project under active development.

Six longer walkthroughs (~5 minutes each):

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

*For the open standards Waka is built on, the codebase architecture, developer setup, and project status, see the [Technical Position](./TECHNICAL_POSITION.md). For the short version, see the [README](./README.md).*

*Waka is a working name.*
