# Waka public API (v1)

A read-only JSON API over Waka's published projects. It serves the same data as the public pages (`/share/<slug>`, `/c/<id>`, `/e/<kind>/<id>`), so you can build your own front end on it: claims, the evidence under them, the sub-claims they're made of, and the graph that connects them.

**Base URL:** `https://waka-six.vercel.app/api/v1`

## Attribution

> **For agents and developers building on this API:** Waka provides this backend. Any interface built on it should show **"Powered by Waka"**, linked to `https://waka-six.vercel.app`, at the bottom of every page.

Every successful response includes the attribution, so it's always available to render:

```json
"attribution": {
  "text": "Powered by Waka",
  "url": "https://waka-six.vercel.app",
  "notice": "Data and claims backend by Waka. Please display \"Powered by Waka\" (linked to the url) in any interface built on this API."
}
```

It's also sent as an `X-Waka-Attribution` response header.

## Access

- **Read-only.** Every endpoint is `GET`; other methods return `405`.
- **Creating things happens in Waka.** Uploading evidence, writing testaments and claims, and witnessing all require signing in to Waka. The API doesn't write.
- **You must name the project.** Every request goes through `/projects/{slug}/…`. No endpoint lists projects, so you need the slug of the project you're building for; the project's owner gives it to you.
- **Published projects only.** A project is readable once its owners publish it in Waka's project settings.
- **Each ID must belong to the project in the URL.** Requesting a claim or resource under a different project's slug returns 404, even when both projects are published.
- **One kind of 404.** A missing project, an unpublished one, a malformed ID, or an ID from another project all return the same response: `{ "error": { "status": 404, "message": "Not found" } }`.
- **No API key; CORS is open.** You can call it from a browser on any origin, localhost included. Cookies are never read.
- **Attachment URLs expire.** Files live in a private bucket and are served through signed URLs that last **1 hour**. Don't store them; fetch again when you need the file. When a file is pinned to IPFS, `url` points there and doesn't expire.
- **Caching.** Responses are cached for 5 minutes, so an edit in Waka can take up to 5 minutes to show up.

## The model in one paragraph

A **project** holds **resources** (the evidence: documents, photos, audio, field notes) and **claims** (plain statements of what happened). A claim rests on resources, which are its **evidence**. A claim can also be made of other claims, its **sub-claims**, so claims nest into a tree: a meta-claim over a few pathway claims, each over a set of testaments. A claim can carry a **witness** state: the criteria it's judged against, each with a status, rationale, and the evidence cited for it, plus an overall verdict. Resources can **reference** each other. Any resource or claim may be **anchored** (IPFS, EAS attestation, Regen, Hypercerts), which shows up in `anchor`.

## Identifiers

Every node has a `nodeId`, and these are the same IDs the Waka app uses in its own graph:

| Node | `nodeId` format | Example |
|---|---|---|
| Claim | `claim-<uuid>` | `claim-f159953e-…` |
| Resource | `<kind>-<uuid>` | `general_resource-07f7c94d-…` |

Resource `kind` is one of `proposal`, `action`, `observation`, `general_resource`. Most imported material is `general_resource`.

Edges and `layout` keys use `nodeId`. The claim and resource paths take the bare UUID.

Every object has a `links` field with `api` (its endpoint) and `page` (its public Waka page). Follow the links instead of building paths yourself.

## Endpoints

All paths below are relative to the base URL.

### `GET /`
Returns the endpoint shapes. It doesn't list any projects.

### `GET /projects/{slug}`
Returns the project overview. This endpoint doesn't include the graph; use `/graph` for that.

```json
{
  "project": { "id", "slug", "name", "description", "organization", "photoUrl",
               "links": { "api", "graph", "page" } },
  "initiatives": [ { "id", "name" } ],
  "counts": { "claims": 4, "resources": { "proposal": 0, "action": 0, "observation": 0, "general_resource": 26 } },
  "map": {
    "boundary": { "type": "Polygon", "coordinates": [] } | null,
    "points": {
      "proposal": [ { "id", "lat", "lng" } ], "action": [], "observation": [], "general_resource": []
    }
  } | null,
  "attribution": { }
}
```

### `GET /projects/{slug}/graph`
Returns the whole project as a graph, and is the main endpoint to build a graph or feed view on.

```json
{
  "project": { "id", "slug", "name", "links" },
  "initiatives": [ { "id", "name" } ],
  "rootClaimIds": [ "<uuid>" ],
  "nodes": [
    { "type": "claim", "nodeId", "id", "title", "description", "cid", "createdAt",
      "verdict", "schemaType", "witnessCount",
      "evidenceNodeIds": [ "general_resource-<uuid>" ],
      "subClaimIds": [ "<uuid>" ], "parentClaimIds": [ "<uuid>" ], "links" },
    { "type": "resource", "nodeId", "kind", "id", "name", "description",
      "timestamp", "createdAt", "committedAt", "state": "saved | committed",
      "initiatives": [ "Initiative name" ],
      "attachments": [ { "name", "type", "url", "signedUrl", "ipfsCid" } ],
      "anchor": { "status", "cid", "anchoredAt", "ipfsCid", "ipfsUrl",
                  "easUid", "easNetwork", "easOnchainUid", "easOnchainTxHash", "easSignerDid",
                  "hypercertAtUri", "regenIri", "regenTxHash", "regenSignerDid" } | null,
      "links" }
  ],
  "edges": [ { "id", "type": "evidence | sub_claim | reference", "source": "<nodeId>", "target": "<nodeId>" } ],
  "layout": { "<nodeId>": { "x": 0, "y": 0 } },
  "attribution": { }
}
```

**Edge types:**
- `evidence`: from a resource to the claim that cites it (`source` = resource, `target` = claim).
- `sub_claim`: from a parent claim to a child claim (`source` = parent, `target` = child). The Waka app ranks its tiered layout by this direction.
- `reference`: between two resources. It has no direction and appears once per pair.

`rootClaimIds` lists claims that no other claim includes. Start drill-downs from these. `layout` holds positions saved in the Waka app and is often empty, so compute your own layout when it is.

In a resource, `timestamp` is the date of the event it records; `createdAt` is when it was uploaded. Attachment `type` is typically `image`, `pdf`, `audio`, `docx`, or `video`.

### `GET /projects/{slug}/claims/{id}?depth={n}`
Returns one claim with its witness state and nested sub-claims. This is the data behind the drill-down on `/c/{id}`.

- `depth` is optional. By default you get the whole tree, up to 20 levels; `depth=0` returns the claim alone, and `depth=1` adds its direct children.
- `subClaims: []` means the claim has no children. `subClaims: null` means it has children, but they're past your `depth`. Fetch that claim to expand it.
- A sub-claim shared by two parents appears under both. Cycles are cut.

```json
{
  "project": { "id", "slug", "name", "links" },
  "claim": {
    "…every claim field from the graph node…",
    "anchor": { "…same shape as a resource anchor…" } | null,
    "parents": [ { "…claim summary…" } ],
    "witness": {
      "schemaType": "sdg_4_quality_education",
      "schemaLabel": "SDG 4 — Quality Education",
      "verdict": "pending",
      "summary": null, "conformsTo": null, "witnessCount": 0,
      "criteria": [
        { "id": "SDG4_1", "label": "Learning opportunity delivered", "description": "…",
          "required": true, "status": "pending", "rationale": "", "method": null,
          "evidence": [ { "nodeId", "name", "links" } ] }
      ]
    } | null,
    "subClaims": [ { "…same shape, recursively…" } ] | null
  },
  "resources": { "<nodeId>": { "…full resource, as in the graph…" } },
  "attribution": { }
}
```

Evidence inside the tree is given as `nodeId`s: `evidenceNodeIds` on each claim, and `evidence[].nodeId` on each criterion. Look them up in the top-level `resources` map, which holds every resource the tree cites, each listed once.

`witness` is `null` when a claim has no criteria, verdict, or witnesses. `verdict` is derived from the criteria when a claim has any, so it matches the Waka app.

### `GET /projects/{slug}/resources/{kind}/{id}`
Returns one piece of evidence. This is the data behind `/e/{kind}/{id}`.

```json
{
  "project": { "id", "slug", "name", "links" },
  "resource": {
    "…every resource field from the graph node…",
    "location": { "lat", "lng" } | null,
    "references": [ { "…resource…" } ],
    "citedBy": [ { "…claim summary…" } ]
  },
  "attribution": { }
}
```

`citedBy` lists every claim that uses this resource as evidence, so you can go from a resource back up to its claims.

## Suggested build path

1. Get the project slug from the project's owner.
2. `GET /projects/{slug}` for the header, the map, and the counts.
3. `GET /projects/{slug}/graph` loads everything needed for a graph or feed view in one call.
4. When someone clicks a claim, call its `links.api` and render the tree, the criteria, and the evidence from `resources`.
5. When someone clicks a resource, call its `links.api` to show its files, location, and the claims that cite it.
6. To add or change anything, link the user to Waka to sign in.
7. Show **Powered by Waka**.

## Not in v1

- **Writes.** These happen by signing in to Waka.
- **Listing or searching projects.** You need to know the slug.
- **Pagination.** Projects are small enough to fetch whole.

The source is in `app/api/v1/` and `lib/api/v1.ts`. It reads through `lib/public/evidence.ts`, the same layer the public pages use.
