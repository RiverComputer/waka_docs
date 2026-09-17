# Waka API guide for agents

You're building a front end on Waka. Waka is the backend. It holds evidence (minutes, transcripts, photographs, audio, reports) and the claims that evidence supports, and it serves both through a public, read-only JSON API.

This guide works from two published Waka projects, both from the Kiwaatule 2030 work in the Nalubaaga Bioregion, Kampala:

| Project | Slug | What it is |
|---|---|---|
| **Kiwaatule-2030** | `kiwaatule-2030-resets` | The main record: 112 resources, 143 files, and 33 claims. It includes the full claim tree: five relational healing pathways composed into one meta-claim. |
| **R1 – Resident ⇋ Waste Relation** | `resident-waste-relation` | The Obutaka Gathering Cycle for healing the resident ⇋ waste relation: 80 resources across six streams, with no claims yet. |

Each project has a public page in Waka. Treat those pages as your starting reference, then build past them. The front end is yours to iterate on; the API is fixed.

- **API base:** `https://waka-six.vercel.app/api/v1`
- **Full API reference:** [API.md](API.md)
- **Reference pages:**
  - https://waka-six.vercel.app/share/kiwaatule-2030-resets
  - https://waka-six.vercel.app/share/resident-waste-relation

---

## Rules

1. **Show "Powered by Waka"** on every screen, linked to `https://waka-six.vercel.app`. A footer is fine. Every API response carries an `attribution` object with the text and URL; render from it.
2. **Read-only.** There are no write endpoints; don't invent any. People add evidence, write claims and witness claims by signing in to Waka. Where your UI invites someone to contribute, link to `https://waka-six.vercel.app/login`.
3. **Always name the project.** Every request goes through `/projects/{slug}/…`, and no endpoint lists projects. Keep both slugs in config.
4. **Don't store attachment URLs.** Files come as signed URLs that expire after **1 hour**. Fetch them fresh, and when an image or file fails to load, refetch the resource and retry once.
5. **Use the API, not the HTML pages.** Don't scrape `/share`, `/c` or `/e`.
6. **Follow `links.api`** on each object instead of building URLs yourself.
7. **Keep the record's own language.** Use pathway names as written, with both parts: *Re-Trusting (Obwesigwa)*. Keep Waka's terms: claim, evidence, witness, testament, verdict. Don't translate, shorten, or rename these.

---

## Project 1: Kiwaatule-2030 (`kiwaatule-2030-resets`)

### Shape of the record

**Claims: 33, in a three-tier tree plus three earlier claims.**

```
The Land ⇋ Stewarding Relation Has Been Reconnected in Kiwaatule      ← meta-claim
├─ Re-Trusting (Obwesigwa)                         4 testaments       ← pathways
├─ Re-Stewarding (Obuwanika)                       5 testaments
├─ Re-Landing (Obutaka)                            4 testaments
├─ Re-Naturing (Obutonde)                          5 testaments
└─ Re-Generating (Enkulakulaana eyo'kuntikko)      6 testaments
      └─ each testament cites its evidence (1–n resources)
```

- **The meta-claim** is `57e68ec1-b41f-4baa-829e-4b069cc831f3`. Its description names the five pathways and the Land ⇋ Stewarding relation (R0) they reconnect.
- **Pathways** hold testaments, not evidence directly.
- **Testaments** hold the evidence.
  - Most have been **witnessed once** (`witnessCount: 1`), with no criteria and no verdict yet.
  - Show that plainly, e.g. "Witnessed by 1", rather than implying a judgement.
- **Three earlier claims** sit outside the tree as extra roots in `rootClaimIds`:
  - *Re-Stewarding for the capcity to steward land* (the typo is in the data)
  - *Re-Trusting to support the capacity to steward*
  - *Trust rebuilt through collective visioning of a different future*
  
  Treat the tree as primary. List these separately, for example as "Earlier claims".

**Resources: 112, carrying 143 files.**
- **Files:** 76 image, 39 docx, 20 pdf, 6 doc, 2 other.
- **Initiatives:** 103 resources are tagged with at least one of **14**, including 1:1 Dialogues, Community Dialogues, Conference of Land Stewards, Mapping Inception / Execution / Dissemination, NVC Committee, Bylaws Drafting, Obutaka Gathering – Collective Governance and Kampala Futures Alliance in Obuntu. Initiatives are the natural way to browse the evidence.
- **Names carry series codes** from the field work: `NKD` (Neighbourhood Kinship Dialogue), `OTO` (one-on-one dialogues), `CMAP` (community mapping), `FOG` and others. Show the code; don't expand one unless the resource itself says what it means.
- **Dates:** 65 resources have a `timestamp` (event date), which is enough for a timeline.
- **Committed:** 54 have `committedAt` set, meaning their content is locked in Waka.

**Edges:**
- **311 `reference`:** evidence cross-linked to other evidence.
- **94 `evidence`:** resources to claims.
- **29 `sub_claim`:** claims to claims.

**Map:** the project boundary is present, but no resource has a location pin.

**Not present yet:** anchors, saved graph layout, project photo.

### Worked examples

| What | Page | API |
|---|---|---|
| Meta-claim | `/c/57e68ec1-b41f-4baa-829e-4b069cc831f3` | `/projects/kiwaatule-2030-resets/claims/57e68ec1-b41f-4baa-829e-4b069cc831f3?depth=1` |
| Testament: *Dissolving Suspicion Through Transparent Dialogue* | `/c/57bd977a-4af3-4dcc-9301-62a0b427fb68` | `/projects/kiwaatule-2030-resets/claims/57bd977a-4af3-4dcc-9301-62a0b427fb68` |
| Evidence: *The Conference of Landstewards Report* (PDF) | `/e/general_resource/8dede0b3-0c69-4270-a250-98e7ac6ebdcc` | `/projects/kiwaatule-2030-resets/resources/general_resource/8dede0b3-0c69-4270-a250-98e7ac6ebdcc` |
| Evidence: *Neighbourhood Kinship Dialogue* (3 photos, 26 references) | `/e/proposal/1cc74881-582b-4a4d-9022-57b698ce2984` | `/projects/kiwaatule-2030-resets/resources/proposal/1cc74881-582b-4a4d-9022-57b698ce2984` |

---

## Project 2: R1 – Resident ⇋ Waste Relation (`resident-waste-relation`)

### Shape of the record

**No claims yet.** This project is evidence only. Build it as an evidence archive that claims can later attach to, not as an empty claims view.

**Resources: 80,** all `general_resource`, with no initiatives or references. They're organized by a **stream prefix** in the name, before the colon:

| Stream | Resources | What the names show |
|---|---|---|
| `STREAM1` | 13 | Orientation of the NVC committee and the Local Attuners; attuning-walk reflections (minutes, audio) |
| `STREAM2` | 5 | A waste-management policy brief; key informant interviews (transcripts, audio) |
| `STREAM3` | 8 | Key informant interviews and an elders' focus group on indigenous knowledge |
| `STREAM5` | 5 | Household conversations: the tool used, the collected data, the research assistants' debrief |
| `STREAM6` | 44 | Focus group discussions (men, women) and interviews with waste collectors, by zone (Central, Balintuma, Kigoowa 1, …) |
| `STREAM7` | 5 | The council of future generations: a walk, reflections, plant species found, a letter |

- **Streams:** there is no `STREAM4`, so don't invent one. The stream labels above are read off resource names, not defined in the data. Ask the project team before giving streams formal titles.
- **Parse the prefix** as the text before the first `:`. Show the rest of the name as the title.
- **Audio lives on Google Drive.** 29 resources have **no attached file**; their `description` holds a Google Drive link, and most are audio recordings. Detect URLs in `description` and render a clear "Open recording" link. Don't show these as broken or empty.
- **Attachment `type` is loose:**
  - `doc` covers both `.docx` and `.doc`.
  - One file typed `audio` is actually a `.docx`.
  - Decide how to render from `type` and the file-name extension together, and fall back to a download link.
- **Files:** 51 in total, of which 39 are documents, 7 images, 4 PDFs, and 1 is mistyped.
- **Dates:** only 1 resource has a `timestamp`, so don't build a timeline for this project.

**Map:** no boundary and no pins. Hide the map rather than showing an empty one.

### Worked examples

| What | Page | API |
|---|---|---|
| Drive-linked audio: STREAM3 elders' focus group | `/e/general_resource/6ac39b1c-f332-42a6-abec-17a222dd4736` | `/projects/resident-waste-relation/resources/general_resource/6ac39b1c-f332-42a6-abec-17a222dd4736` |
| Document: STREAM7 council of future generations minutes | `/e/general_resource/965c2382-c1ee-436f-9d9f-744526a31e44` | `/projects/resident-waste-relation/resources/general_resource/965c2382-c1ee-436f-9d9f-744526a31e44` |
| Photo: STREAM6 focus group, Balintuma Zone | `/e/general_resource/f7aa56ef-7249-4d07-832f-a0ea1a433cb3` | `/projects/resident-waste-relation/resources/general_resource/f7aa56ef-7249-4d07-832f-a0ea1a433cb3` |

---

## The reference pages, surface by surface

Waka's public site has three surfaces. Page paths are relative to `https://waka-six.vercel.app`; API paths are relative to the API base.

### Project: `/share/{slug}`

- **Left column:** the project panel. Name, a collapsible description, the map, and resource and claim counts.
- **Centre:** two toggles, **Feed / Graph** and **Resources / Claims**.
  - Feed mode is a list of cards.
  - Graph mode is a node graph. In claims mode it lays claims out in tiers along `sub_claim` edges: evidence on the left, testaments next, then pathways, and the meta-claim on the far right.
- **Right column:** an inspector for the selected item.
- **API:**
  - `GET /projects/{slug}` for the panel, map and counts.
  - `GET /projects/{slug}/graph` for the feed and graph.

### Claim: `/c/{id}`

- **Sections, top to bottom:**
  1. Breadcrumb (organization · project)
  2. Title and description
  3. Anchor status
  4. **Witness:** schema, verdict, and the criteria with their rationale and cited evidence
  5. **Evidence:** expandable cards
  6. **Supporting claims & evidence:** the recursive tree
  7. **Provenance**
- **API:** `GET /projects/{slug}/claims/{id}?depth=n`

### Evidence: `/e/{kind}/{id}`

- **Sections, top to bottom:**
  1. Title and description
  2. Dates:
     - **Dated** is `timestamp`, when the event happened.
     - **Recorded** is `createdAt`, when it was uploaded.
     - **Content locked** is `committedAt`.
  3. **Attachments,** by type:
     - images inline
     - PDFs in a viewer
     - audio in a player
     - DOCX as readable text
     - anything else as a download
  4. **Location**
  5. **Cited by:** the claims that rest on this evidence
  6. **Connected resources**
  7. **Provenance**
- **API:** `GET /projects/{slug}/resources/{kind}/{id}`

---

## API essentials

| Endpoint | Returns |
|---|---|
| `GET /projects/{slug}` | Project (name, description, organization, photoUrl), initiatives, counts, map (`boundary` GeoJSON plus `points` by kind) |
| `GET /projects/{slug}/graph` | `nodes` (claims and resources), `edges`, `rootClaimIds`, `layout` |
| `GET /projects/{slug}/claims/{id}?depth=n` | `claim` (with `witness`, `parents`, nested `subClaims`) and a `resources` map |
| `GET /projects/{slug}/resources/{kind}/{id}` | `resource` (with attachments, `location`, `references`, `citedBy`) |

**Node IDs**
- A claim is `claim-<uuid>`. A resource is `<kind>-<uuid>`, where kind is `proposal`, `action`, `observation` or `general_resource`.
- Paths take the bare UUID.
- Split a node ID at its **first** hyphen.

**Edges**
- `evidence` runs from a resource to a claim.
- `sub_claim` runs from a parent claim to a child claim.
- `reference` connects two resources, with no direction.

**Claim trees**
- Evidence inside a tree is given by node ID. Look each one up in the response's top-level `resources` map.
- `subClaims: []` means the claim has no children. `subClaims: null` means it has children beyond your `depth`; fetch that claim to expand it.

**Witness**
- `witness` is `null` when a claim has no witness signal at all.
- `verdict` values:

  | Value | Label |
  |---|---|
  | `satisfied` | Satisfied |
  | `partial` | Partially satisfied |
  | `not_satisfied` | Not satisfied |
  | `pending` | Not yet evaluated |
  | `null` | Show no verdict |

**Attachments**
- Each attachment is `{ name, type, url, signedUrl, ipfsCid }`. Use `url`.

**Responses**
- Errors are always `{ "error": { "status": 404, "message": "Not found" } }`, whatever the cause.
- CORS is open and no key is needed.
- Responses are cached for about 5 minutes.

---

## Design guidance

These follow from what's in the two records. They're a starting point, not a spec.

- **Lead with people and relations, not with the data model.** The graph is a way to check the record; it doesn't need to be the front door. A pathway, a stream, a dialogue or a zone is more recognizable to the people in this record than a node.
- **Keep every claim one step from its evidence.** A testament without its minutes, photos and recordings is just an assertion. Opening any claim should show what it rests on.
- **Use two ways in, one per project.** For Kiwaatule-2030, go down the tree (meta-claim → pathway → testament → evidence) or across by initiative. For R1, go by stream, then by participant group or zone as the names show.
- **Show that the record is unfinished.** Most testaments are witnessed but not evaluated, R1 has no claims yet, and nothing is anchored. Show that honestly with quiet labels ("Witnessed by 1", "No claims yet", "Not yet anchored"). Don't hide it, and don't sound alarms.
- **Treat audio as first-class,** and Drive-hosted recordings too. Much of the knowledge in R1 is spoken: elders, focus groups, reflections.
- **Design for low bandwidth.** Load claim trees lazily with `depth=1`, lazy-load images, and don't fetch every resource up front for R1's 80 items. The graph endpoint already returns everything in one call.
- **Credit Waka and point contributions back to it.** "Powered by Waka" on every screen, and a clear path to sign in to Waka to add to the record.

### Optional visual continuity with Waka

- **Type:** Geist for text, Geist Mono for codes and IDs.
- **Light theme:**
  - Page `#f5f5f7`, panels `#ffffff`, borders `#d2d2d7`
  - Text `#1d1d1f` / `#515154` / `#86868b`
  - Accent `#0071e3`
- **Dark theme:**
  - Page `#000000`, panels `#1c1c1e`
  - Text `#f5f5f7`
  - Accent `#2997ff`
- **Tags and pills:** outline only, with a transparent fill and a coloured border and text.
- **Verdict colours:**
  - satisfied `#16a34a`
  - partial `#d97706`
  - not satisfied `#dc2626`
  - pending: grey

---

## Start here

**1. Config and a small client.** Any stack works. In TypeScript:

```ts
const API = 'https://waka-six.vercel.app/api/v1'
export const PROJECTS = {
  kiwaatule: 'kiwaatule-2030-resets',
  wasteRelation: 'resident-waste-relation',
} as const

async function waka<T>(slug: string, path = ''): Promise<T> {
  const res = await fetch(`${API}/projects/${slug}${path}`)
  if (!res.ok) throw new Error(`Waka API ${res.status} on ${slug}${path}`)
  return res.json() as Promise<T>
}

export const getProject = (slug: string) => waka<ProjectResponse>(slug)
export const getGraph = (slug: string) => waka<GraphResponse>(slug, '/graph')
export const getClaim = (slug: string, id: string, depth?: number) =>
  waka<ClaimResponse>(slug, `/claims/${id}${depth == null ? '' : `?depth=${depth}`}`)
export const getResource = (slug: string, kind: string, id: string) =>
  waka<ResourceResponse>(slug, `/resources/${kind}/${id}`)

/** 'general_resource-07f7…' → { kind: 'general_resource', id: '07f7…' } */
export function parseNodeId(nodeId: string) {
  const i = nodeId.indexOf('-')
  return { kind: nodeId.slice(0, i), id: nodeId.slice(i + 1) }
}

/** 'STREAM6: Minutes of the Focus Group…' → { stream: 'STREAM6', title: 'Minutes of the Focus Group…' } */
export function parseStream(name: string) {
  const m = name.match(/^(STREAM\d+):\s*(.*)$/)
  return m ? { stream: m[1], title: m[2] } : { stream: null, title: name }
}
```

**2. Write types from real responses.** Fetch the project, graph, one claim and one resource from **each** project, save the JSON as fixtures, and type from those. The two projects differ, so fixtures from both catch the edge cases.

**3. First milestone.**
- A landing view with both projects, each with its name and description.
- **Kiwaatule-2030:**
  - The meta-claim, with its five pathways.
  - Open a pathway to see its testaments, then a testament to see its evidence.
  - An initiative filter over the evidence.
- **R1:**
  - Evidence grouped by stream.
  - Documents and photos open inline where possible.
  - Drive recordings open as links.
- A single evidence view shared by both projects.
- "Powered by Waka" in the footer.

**4. Then evolve** toward what the people in this record need: residents, the NVC committee, land stewards, and the funders and authorities who need to check the work.

### Done when

- All 24 testaments are reachable from the meta-claim through their pathways, and the 3 earlier claims are listed too.
- All 80 R1 resources appear under their streams, and every Drive-linked recording has a working link.
- Every attachment either renders or downloads, including mistyped files.
- An image still loads after the page has been open for over an hour.
- Empty data (no verdict, no location, no anchor, no timestamp, no claims) gets a quiet label, never a blank or an error.
- "Powered by Waka" is visible on every screen.
