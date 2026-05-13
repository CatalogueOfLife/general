# ChecklistBank for Bones & RADAR — a technical introduction

*Working draft, M. Döring, 2026-05-13*

This is a short technical orientation to ChecklistBank (CLB) and a concrete
proposal for how Bones & RADAR (B&R) could plug into it: GitHub-hosted ColDP
→ automatic import → matching against COL XR → project sector sync →
vernacular merge → immutable, citable releases.

---

## 1. What ChecklistBank is

[ChecklistBank](https://www.checklistbank.org) is the platform that hosts the
Catalogue of Life (COL) and a few hundred other taxonomic datasets. It is the
operational backbone behind:

- The annual **Catalogue of Life Checklist** and the rolling **COL eXtended Release (COL XR)**
- A general taxonomic dataset repository (think "GitHub for checklists") with versioning, matching, search and DOI-minted downloads.

CLB also serves COL data into the GBIF Backbone build, but the Backbone
itself is assembled by GBIF separately, not inside CLB.

Key entry points:

- UI: <https://www.checklistbank.org> · dev: <https://dev.checklistbank.org>
- API: <https://api.checklistbank.org> · dev: <https://api.dev.checklistbank.org>
- API docs (OpenAPI / Swagger): <https://api.checklistbank.org/openapi>
- About / help pages: <https://www.checklistbank.org/about>
- Source code: <https://github.com/CatalogueOfLife/backend> · <https://github.com/CatalogueOfLife/checklistbank>

Architecturally CLB is a Java/Dropwizard service on Postgres 17 +
Elasticsearch, fronted by Varnish. A blue-green pair of read-write and
read-only nodes serves the API. None of that should matter to B&R day-to-day,
but it explains why imports, syncs and releases are async, job-based
operations.

---

## 2. Dataset kinds

CLB has three flavours of dataset and the distinction matters for the
workflow below:

| Kind | What it is | Typical example |
|---|---|---|
| **External** | A checklist imported from outside CLB (file, URL or git repo). Read-only in the UI; re-imported on update. | "Dutch Species Register", a GBIF checklist |
| **Project** | An editable dataset that is **assembled from sectors** drawn out of other datasets. Has its own editorial decisions, name usage IDs, sync history. | Catalogue of Life itself; a national checklist project; B&R |
| **Release** | A frozen snapshot of a Project. Immutable, citable (DOI), downloadable. | "COL 2026 Annual Checklist", "COL XR 2026-04" |

The COL editorial team works on the **COL project**; users see the
**Releases** (annual + monthly XR). The same pattern is exactly what B&R
would use.

---

## 3. Input format options

We have **two viable input formats** for B&R, and we're not yet committed
either way. The trade-off is roughly *expressiveness* (ColDP) vs *human
ergonomics* (TextTree).

### 3a. ColDP — the canonical format

[ColDP (Catalogue of Life Data Package)](https://github.com/CatalogueOfLife/coldp)
is CLB's native format. It is a small set of TSV/CSV files plus a `metadata.yaml`,
in a single zip or directory. The full schema:
<https://github.com/CatalogueOfLife/coldp/blob/master/README.md>.

Core files: `NameUsage.tsv` (or split `Name.tsv` + `Taxon.tsv` + `Synonym.tsv`),
plus optional `VernacularName`, `Distribution`, `Reference`, `TypeMaterial`,
`SpeciesInteraction`, `TaxonProperty`, `Media`, `TreatmentMetadata` and more.

Strengths:

- **Round-trips losslessly** through CLB — what you put in, you get back out.
- **Fully expressive**: multi-language vernaculars with attribution and
  references, distributions with gazetteer codes, full reference records,
  type material, species interactions, etc.
- Diff-friendly per-file in git (vernacular changes show only in
  `VernacularName.tsv`).

Costs:

- Multiple files to keep in sync; one tabular file per data class.
- Hierarchy is encoded by `parentID` columns, not visually — harder to eyeball.
- Editors generally need a spreadsheet tool or scripts; "just open the file
  and see the tree" doesn't work.

### 3b. TextTree — a human-editable alternative

[TextTree](https://github.com/gbif/text-tree) is a much simpler format: **one
plain-text file** in which indentation expresses the taxonomic tree, with
optional rank tags and extended properties in `{}` braces.

```
Aves [class]
  Charadriiformes [order]
    Scolopacidae [family]
      Limosa Brisson, 1760 [genus] {ID=br-2345}
        Limosa limosa (Linnaeus, 1758) [species] {ID=br-2346, VERN=grutto, DIST=NL, REF=R12}
          =Scolopax limosa Linnaeus, 1758 [species] {ID=br-syn-99}
        Limosa lapponica (Linnaeus, 1758) [species] {ID=br-2347, VERN=rosse_grutto, DIST=NL, ENV=marine}
```

Grammar in one paragraph: each line is a name usage; two-space indent =
child of the line above; `=` prefix = heterotypic synonym, `≡` = homotypic
synonym; `?` prefix = provisionally accepted; `[rank]` sets the rank;
`{KEY=value, KEY=value}` attaches extended properties. Underscores in
property values decode to spaces (so `rosse_grutto` becomes `rosse grutto`).
Multiple values for the same key are pipe-separated (`REF=R12|R34`).

Recognised property keys (CLB's TextTree vocab):

| Key | Meaning |
|---|---|
| `ID` | Stable identifier for the row (use this for B&R IDs like `br-…`) |
| `VERN` | Vernacular name(s) |
| `DIST` | Distribution (region codes, pipe-separated) |
| `ENV` | Environment (e.g. `marine`, `freshwater`, `terrestrial`) |
| `REF` | Taxonomic reference ID(s) — resolve against an optional `reference.bib` next to the tree file |
| `PUB` | Published-in reference ID |
| `LINK` | URL link |
| `CODE` | Nomenclatural code (`ICZN`, `ICN`, etc.) |
| `NOM` | Nomenclatural status |
| `TYPE` | Type species or genus |
| `TM` | Type material (`status:specimen citation`, pipe-separated) |
| `EST` | Species estimate (number; prefix `†` for extinct) |
| `CHRONO` | Temporal range |
| `SRC` | Source dataset ID |

Strengths:

- **One file, visually the tree.** A taxonomist can open, read and edit it
  in any text editor. Git diffs are immediately meaningful.
- Adding a species or a vernacular is a one-line change.
- Extended properties give you the ~80% of cases B&R likely needs
  (vernaculars, distributions, an ID, a reference link).

Costs:

- Less expressive: a vernacular gets a string, not a language tag plus
  attribution plus reference. Similarly distributions are bare codes.
- No structured Reference records unless you ship a `reference.bib`
  alongside the tree.
- Not round-trip — CLB will export your B&R release as ColDP, not as
  TextTree. (TextTree export is available, but extended properties are
  preserved only for the keys above.)

### Recommendation (for discussion)

If B&R's editorial reality is *one or two scientists curating in a text
editor* and the data model is "names, ranks, synonyms, a vernacular, a
distribution code, an ID", **start with TextTree**. We can always migrate
to ColDP later — CLB can already export ColDP from a TextTree-sourced
dataset.

If B&R needs **multi-language vernaculars with provenance**, structured
references, type material, or anything else from the long tail, the
expressiveness gap is real and **ColDP is the right call from day one**.

---

## 4. Imports

CLB supports three import sources, configured per dataset in
**Settings → Import**:

1. **Upload** — drag-and-drop a zip in the UI, or `POST` the archive to
   `/dataset/{key}/import` ([API](https://api.checklistbank.org/openapi#/Importer)).
2. **URL** — CLB fetches a URL on a schedule (or on demand). HTTP caching
   headers (`ETag`, `Last-Modified`) are honoured, so re-imports are no-ops
   when the source hasn't changed.
3. **Git / GitHub** — point CLB at a repo (or a release artifact URL). This
   is what we want for B&R; see §9.

Every import is a job: validate → load → match names against the **name
index** (NIDX) → index in ES → notify. Failures are surfaced in the
[import history](https://www.checklistbank.org/dataset/3LR/imports) per
dataset with logs and counts.

There are **GitHub webhook recipes** in use today (the COL plenary and a few
source datasets work this way): a push to `main` triggers a webhook that
calls the CLB import endpoint with the repo's zipball URL. We can wire B&R
the same way.

---

## 5. Name matching and COL XR

CLB maintains a global **Name Index (NIDX)** — every scientific name string
seen in any dataset is canonicalised, parsed and given an `nidx` id. On
import, every name in your dataset is matched against the index.

`nidx` ids are an **internal join key**, not a citable identifier: they
stay constant between full rebuilds of the name index, but a rebuild
reshuffles them. Don't expose them externally; persistent identifiers for
B&R come from your own ID column and from `COL:` ids assigned during
matching (below).

The **COL eXtended Release (COL XR)** is a monthly release of the full COL
project, with **persistent, citable identifiers** (`COL:xxxxx`) for every
accepted name and synonym. It's the reference target you want B&R to match
against, because:

- It gives you identifiers you can keep across releases.
- The **match report** flags every B&R name that does *not* resolve in COL
  XR — those are either novel taxa, misspellings, or disagreements worth
  curatorial attention.
- Matches can be reviewed and overridden in the UI under **Names → Matching**.

In URLs and API paths, the magic key **`3LXR`** always resolves to the
current public eXtended Release (i.e. the latest XR of project `3` = COL).
Use that key when wiring up matching so you don't have to chase the
month-specific dataset number — e.g.
`https://www.checklistbank.org/dataset/3LXR/about` or
`/dataset/{your-key}/nameusage/match?target=3LXR`.

Docs: <https://www.checklistbank.org/about#col-xr> · matching API
endpoints under `/dataset/{key}/nameusage/match` in the
[OpenAPI spec](https://api.checklistbank.org/openapi).

---

## 6. Projects and sector syncs

A CLB **Project** is built by defining **Sectors**: pointers that say
"graft this subtree from source dataset *X* under this parent in my
project." Four sync modes:

| Mode | What it does |
|---|---|
| **Attach** | Hangs the source subtree under a project parent verbatim. Source taxonomy wins. |
| **Union** | Adds source taxa where they don't already exist; keeps project taxa where they do. |
| **Merge** | Reconciles source and project at the name level, preferring project decisions. Used for vernaculars, distributions, references rolled in from many sources. |
| **Hierarchy** | Delegates the *higher classification* (above genus), the *synonymy* and the accepted/synonym *status decision* to a target taxonomy. The project only curates genera and below; the sync pulls in everything above and the synonyms around it. New in 2026 — currently on the `feature/hierarchy-sync` backend branch; this is the mode designed for B&R. |

Sectors are re-synced on demand or on a schedule. A sync re-runs matching,
recomputes the project tree, and writes a sync report (added / modified /
deleted counts, ambiguities). The COL project today has ~200 sectors
covering the tree of life — this is the same machinery B&R would use.

### How Hierarchy sync works (in brief)

A Hierarchy sector points at a target dataset — for B&R that target is
**COL** (with `useXRelease=true`, so the sync resolves it to the latest
public XR at the time it runs). Then for every project usage the sync:

1. **Reads the `COL:` identifier** that earlier name-matching already
   stamped on it (no name-string matching is needed at this stage).
2. **Copies the entire parent classification above genus** from COL XR
   into the project. Already-imported ancestors are wiped and reimported
   on every re-run, so the higher tree stays in lock-step with XR.
3. **Realigns the status**: if a project usage was accepted but COL XR
   says synonym (or vice versa), the project usage is demoted or promoted
   and re-anchored under the right accepted parent.
4. **Pulls in synonyms** of every accepted taxon from COL XR — so B&R's
   accepted names get the full synonymy COL has on them, without anyone
   having to copy it by hand.

Project usages that lack a `COL:` identifier are skipped in v1 (a
name-string fallback is on the roadmap). So **good matching coverage in
step 5 of the workflow is what makes the Hierarchy sync useful**.

Pipeline spec: [`hierarchy-sync.md` in the backend repo](https://github.com/CatalogueOfLife/backend/blob/feature/hierarchy-sync/hierarchy-sync.md).

Docs: <https://www.checklistbank.org/about/editor#sectors>

---

## 7. Releases, DOIs and downloads

From a Project, an editor can create a **Release** at any time:

- The release is an immutable copy of the project state, with its own
  dataset key, its own UI page, and its own download artifacts.
- A **DOI** can be minted via DataCite at release time (the CLB–DataCite
  integration is already wired for COL; same flow for B&R).
- Downloads are generated in every supported export format (see below) and
  served via the [archives endpoint](https://api.checklistbank.org/openapi#/Exporter).
- Releases are **append-only**: you can deprecate them but the IDs and
  files don't change.

**Export formats** available per dataset/release:

- ColDP (the canonical round-trip)
- DwC-A (Darwin Core Archive — for GBIF and friends)
- TextTree (a hierarchical text dump — useful for diffs)
- a couple of legacy ACEF dialects

Citation suggestion: link the DOI in publications and tools; the
human-readable landing page (`/dataset/{key}`) resolves the DOI.

---

## 8. Embedding releases on B&R's website

B&R won't have to build its own UI. CoL maintains a small React component
library, **[portal-components](https://github.com/CatalogueOfLife/portal-components)**
(published as `col-browser`), that drops into any HTML page and renders
five widgets for any dataset in ChecklistBank:

| Component | Renders | Demo on `catalogueoflife.org` |
|---|---|---|
| `Tree`    | Browsable taxonomic tree | <https://www.catalogueoflife.org/data/browse> |
| `Search`  | Faceted search + tabular results | <https://www.catalogueoflife.org/data/search> |
| `Taxon`   | Full taxon detail page (classification, synonymy, vernaculars, distributions, references, media) | <https://www.catalogueoflife.org/data/taxon/623QT> |
| `Dataset` | Dataset / release landing page with metrics & taxonomic coverage | <https://www.catalogueoflife.org/data/dataset/2073> |
| `BibTex`  | Small icon that downloads a BibTeX citation for a dataset | (inline on the above) |

The components are vanilla React 16, distributed as a UMD bundle on
jsDelivr; no build step required on the consumer side. A two-script
snippet wires up a working tree browser on any page:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/CatalogueOfLife/portal-components@latest/umd/main.css">
<script src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
<script src="https://cdn.jsdelivr.net/gh/CatalogueOfLife/portal-components@latest/umd/col-browser.min.js"></script>

<div id="tree"></div>
<script>
  ReactDOM.render(
    React.createElement(ColBrowser.Tree, {
      catalogueKey: 99999,                 // ← the B&R release dataset key
      pathToTaxon: '/checklist/taxon/',
      citation: 'bottom'
    }),
    document.getElementById('tree')
  );
</script>
```

For B&R this means:

- **Zero backend infra.** The components hit `api.checklistbank.org`
  directly. B&R's web team only writes HTML.
- **Pick any dataset key.** Point a page at the latest B&R release, a
  specific historical release, or the live B&R project — every dataset
  in CLB is addressable. (For "always latest release of project N", the
  same `NLR` / `NLXR` magic-key trick from §5 works for any project.)
- **Per-release URLs.** Because each release has its own dataset key,
  permalinks like `/checklist/v2026/taxon/…` keep working forever — the
  underlying data is immutable.
- **Project-aware mode.** Passing `type="project"` to `Tree` adds source
  attribution per node (useful if B&R wants to show which underlying
  source contributed each taxon).

Production examples: catalogueoflife.org (all five components),
<https://pterophoroidea.hobern.net>, <https://alucitoidea.hobern.net>
(third-party portals built on top of CLB datasets).

Repo: <https://github.com/CatalogueOfLife/portal-components>

---

\newpage

## 9. Proposed B&R workflow

```
┌──────────────────────────┐    push       ┌────────────────────┐
│  github.com/B&R/bones-   │ ────────────▶ │  GitHub webhook    │
│  checklist  (ColDP OR    │   to main     │  → CLB import API  │
│  TextTree — TBD)         │               │                    │
└──────────────────────────┘               └──────────┬─────────┘
                                                      │ async import
                                                      ▼
                                  ┌─────────────────────────────────┐
                                  │  CLB external dataset           │
                                  │  "Bones & RADAR (source)"       │
                                  │  → matched against COL XR       │
                                  │  → match report = QA backlog    │
                                  └──────────┬──────────────────────┘
                                             │ sector(s)
                                             ▼
                  ┌──────────────────────────────────────────────┐
                  │  B&R Project (curates genera + species)      │
                  │  ├─ sector: B&R source       (attach/union)  │
                  │  ├─ sector: COL XR           (HIERARCHY)     │
                  │  │      → higher classification              │
                  │  │      → synonymy of accepted taxa          │
                  │  │      → accepted/synonym status decisions  │
                  │  ├─ sector: vernaculars NL-A (merge)         │
                  │  └─ sector: vernaculars NL-B (merge)         │
                  └──────────┬───────────────────────────────────┘
                             │ release
                             ▼
                  ┌───────────────────────────┐
                  │  B&R Release 2026.x       │  DOI · ColDP · DwC-A · TextTree
                  │  (immutable, downloadable)│
                  └───────────────────────────┘
```

Step by step:

1. **Checklist in GitHub.** B&R maintains its checklist in a public GitHub
   repo, in either **ColDP** or **TextTree** format (see §3 — the choice
   is open). Easiest start: click "Use this template" on
   [`data-template-coldp`](https://github.com/CatalogueOfLife/data-template-coldp)
   or [`data-template-textree`](https://github.com/CatalogueOfLife/data-template-textree)
   in the COL org. Branching / PR review is B&R's normal editorial
   workflow. Tag releases if you want, but `main` tracking is fine.
2. **Webhook → automatic import.** A GitHub webhook on `push` calls a CLB
   import endpoint with the zipball URL. CLB validates, loads, indexes.
   Auth on the CLB API is plain **HTTP Basic** against a GBIF account with
   editor rights on the B&R dataset — credentials go in the webhook URL or
   an `Authorization` header. Failures land back on GitHub (we can post a
   commit status from CLB if useful).
3. **Auto-match to COL XR.** Every import re-runs name matching. The match
   report becomes the QA backlog: "39 names did not match COL XR — review."
   Curators can fix in the source file (preferred) or override matches in
   CLB.
4. **Project sector sync.** A B&R Project ingests the B&R source dataset
   via one sector (attach or union, TBD per branch). The project curates
   only **genera, species and lower**; everything above genus is left to
   the next step.
5. **Hierarchy sync to COL XR.** A single Hierarchy-mode sector targets
   COL (`useXRelease=true` → resolves to the latest XR at sync time) and
   delivers three things at once (see §6):
   - the entire **higher classification** above genus, fresh from XR;
   - the **synonymy** for each B&R accepted name, from XR;
   - **status decisions**: if XR has demoted a B&R-accepted name to a
     synonym (or vice versa), the project usage is flipped and re-anchored.
   This is the load-bearing step. It only works for project usages that
   carry a `COL:` identifier — which is exactly what step 3 produces. The
   quality of step 3 sets the ceiling for the quality of step 5.
6. **Vernacular merge from other Dutch sources.** Additional sectors in
   **merge** mode pull `VernacularName` records from other Dutch checklists
   into the B&R project, keyed off the matched names. Source attribution is
   preserved per vernacular row.
7. **Immutable releases with DOI.** When B&R is ready, an editor creates a
   release: DOI minted, archive files frozen, landing page live. Citable
   from publications, harvestable by GBIF, downloadable by anyone.

---

## 10. Open questions for the meeting

- **Input format: ColDP or TextTree?** (See §3.) Drives everything
  downstream — repo layout, editor tooling, validation, the kinds of
  metadata B&R can carry. Recommend deciding this first.
- **One repo or two?** B&R covers two kingdoms with separate editorial
  teams: **BonesInfo** (botany) and **RADAR** (zoology). Single combined
  source repo, or one repo per team? Trade-off: one repo = one CLB
  dataset, one release, one DOI, simpler hierarchy sync; two repos = clean
  team boundaries, independent release cadence, but two CLB datasets and
  two projects (or one project with two source sectors).
- **Access pattern for downstream consumers.** Do consumers need
  **live API access** to B&R releases on `api.checklistbank.org`
  (`/dataset/{releaseKey}/…` queries, name search, name matching against
  B&R), or are **release downloads** (ColDP / DwC-A zips published with a
  DOI) sufficient? API access is essentially free given the data is
  already in CLB, but it shapes expectations and CLB-side load.
- **Validation gate.** Run a lint/validate step in GitHub Actions before
  letting CLB pull? (Yes, recommended — we can share a workflow for either
  format.)
- **Identifier policy.** Should B&R mint its own stable IDs (`BR:xxxxx`) in
  the source file, independent of COL XR matches? Strongly recommended —
  matching fills `COL:` IDs *additionally*.
- **Vernacular sources.** Which Dutch datasets, and are they already in
  CLB? If not, they need to be imported first (same mechanism).
- **Release cadence.** Annual? Tied to a Dutch national release calendar?
- **Access control.** Who at B&R gets editor rights on the project? CLB
  uses GBIF accounts.
- **Sync to COL.** Is the long-term intent for B&R (or a subset of it) to
  itself become a sector inside the main COL project? That's a separate
  conversation but worth flagging.

---

## 11. Resources

- ChecklistBank: <https://www.checklistbank.org>
- API: <https://api.checklistbank.org/openapi>
- About / help: <https://www.checklistbank.org/about>
- ColDP spec: <https://github.com/CatalogueOfLife/coldp>
- TextTree spec & parser: <https://github.com/gbif/text-tree>
- Backend source (issues live here): <https://github.com/CatalogueOfLife/backend>
- Portal components (React/UMD widgets, §8): <https://github.com/CatalogueOfLife/portal-components>
- COL data issues / feedback: <https://github.com/CatalogueOfLife/data>
- COL XR landing: <https://www.checklistbank.org/dataset/3LXR/about>
  *(`3LXR` is a magic alias that always resolves to the latest public
  eXtended Release of project 3 = COL; works in URLs and API paths)*

**Dataset repo templates** (click "Use this template" on GitHub to get a
working starter repo):

- ColDP: <https://github.com/CatalogueOfLife/data-template-coldp>
- TextTree: <https://github.com/CatalogueOfLife/data-template-textree>

**Real examples** to copy from — all the `data-*` repos in the COL org:
<https://github.com/orgs/CatalogueOfLife/repositories?q=data-> (e.g.
`data-itis`, `data-wcvp`, `data-worms`, `data-euro-leps` …).
