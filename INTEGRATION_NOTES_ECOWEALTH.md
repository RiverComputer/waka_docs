# Integration note — EcoWealth

Draft from [EcoWealth](https://vealth.net) on how Waka fits the retirement layer downstream. Happy to reshape, split, or withdraw if it's not the right fit for this repo.

## Who we are

EcoWealth runs a small revenue-bearing platform that routes surplus toward verified ecological retirement on **Regen Network** and **Klima 2.0** (Base). Every retirement produces a public receipt; the receipt is the product.

For context: we've also contributed upstream to Regen — 13 PRs merged into `regen-network/agentic-tokenomics` covering CosmWasm modules M008–M015, agent characterizations, cadCAD sims, and governance drafts.

## Why Waka matters to us

Today our receipts cite a **credit batch**. They do not cite the **witnessed measurement** behind the credit. That's a real gap and it's upstream of us.

Waka looks like the right layer to close it. The claims-sovereignty framing in the README is exactly the property we need: a receipt that carries its evidence, not just its certification.

## The three missions we route capital toward

Our surplus routes toward three missions, and all three have the same upstream gap Waka solves:

- **Living Building Challenge** — net-positive energy, water, and materials claims require a year of witnessed performance data. Today those claims live in PDFs. As a Waka claim bundle (sensor data + PGS witness + expert review + CEL conformance), an LBC petal certification becomes a portable, auditable asset instead of a filing.
- **Michelin-quality food** — provenance, sourcing, and ingredient-integrity claims are exactly the kind of multi-party, partially-machine-witnessable assertions the claims engine composes. A dish's supply chain as a chain of Waka claims is a real thing a restaurant could stand behind.
- **SDG outcomes** — the unsolved problem in SDG financing is that outcome measurement is unverifiable, so capital flows to process, not outcome. Waka-anchored outcome claims (with originator-inclusive witnessing) are what an outcome-linked funding instrument actually needs to settle against.

In all three, our role is the same — retirement + receipt at the tail end. Waka is what makes the receipt mean something.

## Proposed loop

```
Waka claim (witnessed)
  → anchored on Regen x/data
    → EcoWealth retire x402 reads the anchor
      → Klima / Regen retirement memo embeds the claim CID
        → public receipt surfaces the witnessed evidence
```

## Three pieces on our side

1. **`x402/waka/claim/:cid`** — read-only. Fetch the claim by CID, verify the Regen `x/data` anchor, return witnesses + methodology + evidence links + anchor tx.
2. **`x402/klima/retire` and `x402/regen/retire`** — accept an optional `wakaClaimCid`. Embed the CID in the retirement memo. Fail loudly if the anchor doesn't verify.
3. **Receipt page** — render retirement cert + Waka claim summary together, with links to the PDS record, Regen anchor tx, and retire tx.

## What would unlock the smallest first step

Not blocking — just flagging:

- A public, unauthenticated way to fetch a claim record by CID (or a documented PDS lexicon pattern).
- A published example of the `eco.waka.*` claim record shape.
- One real test claim CID we can wire against end-to-end.

## Smallest first step

One shell script: fetch a Waka claim by CID → verify the Regen anchor → retire 1 credit on Base citing the CID in the memo. If it round-trips, our x402 endpoints are just wrappers. We'd open-source the script and link it back here.

## Why a PR and not an issue

The README names downstream systems that act on claims as the audience. We're one of those. A PR felt like the cleanest way to propose a concrete fit. If premature, close it and we'll move to private.

— EcoWealth
