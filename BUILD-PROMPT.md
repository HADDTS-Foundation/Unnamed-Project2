# Build Prompt — CTBP1 INTERACTOME ATLAS

> Paste this whole document to Claude Code as the brief for building the app. It specifies the
> mission, the non-negotiable principles, the architecture, the data model, the inference engine,
> the UI, the design system, and the test philosophy. Follow it exactly; where it gives formulas,
> thresholds or colour tokens, use them verbatim.

---

## 1. Mission

Build **CTBP1 Interactome Atlas** (the tool's name). The header wordmark is a **two-tone logotype in one
typeface (Hanken Grotesk)**: the subject **`CTBP1`** is bold (weight 700, `--on-surface` dark) and the
product name **`Interactome Atlas`** is the lighter accent (weight 400, `--secondary` teal) — "Interactome"
and "Atlas" share **one** treatment (same font, weight and colour), never a different font from `CTBP1`.
It is — a self-contained, offline, single-page , mobile first, web app that profiles the **top‑250
STRING interactors of the human gene CTBP1** and derives their biological / disease connections
through a **transparent inference engine**. It is a research instrument for a working scientist,
not a marketing demo: every number shown must be traceable to a public source, and the engine must
never special-case any partner gene.

Think "institutional modernist" bioinformatics console: dense, disciplined, authoritative.

---

## 2. Non‑negotiable principles (these override convenience)

1. **Offline‑first.** The app opens by double-clicking `index.html` (a `file://` page) with **no
   build step, no bundler, no framework, no network calls at load**. All data is bundled in a JS
   file. Links inside the app point to *live* sources for validation — **user-triggered, never at
   load** — but the app never needs them to run.
2. **Everything is sourced — and provenance comes first.** Every count, score, flag, interaction,
   pathway, phenotype and paper must carry a click-through to the exact live query/record that
   validates it. No unsourced numbers. No invented claims. State values plainly; never editorialise
   or round away precision. **Keep the provenance never more than one click away, not buried:** every
   shown value links directly to the live record that validates it. **Any value that is itself an
   outgoing link must carry a trailing `↗`** so the user can see it is clickable, including bare numbers
   (e.g. the ClinVar **P/LP / VUS / Total** counts render as `150 ↗`, `157 ↗`, `533 ↗`, not plain
   `150`). There is also a consolidated provenance strip
   (top, above the views) that gathers the gene IDs, *every* data source as a link, the snapshot/"Built"
   date, the "How was this built?" methods link, and the Export, all in one place. That strip is
   **collapsed by default** for a clean first paint but is **always one click away** via a persistent
   header **ⓘ Sources** toggle (it is never permanently removable). The deliberate trade: a clean
   initial view over forcing the sources in front of a first-time reader — acceptable only because the
   strip is one obvious click away and every individual number still carries its own source link.
3. **No gene is ever special-cased.** The engine receives only raw evidence and treats every gene
   identically. No partner symbol may appear in a scoring branch. The *only* gene-ish tokens
   allowed in the engine are: the subject hub `CTBP1`, a documented literature stop-list
   (`IMPACT, GAPDH, TBP, ACTB, B2M`).
4. **Editorial choice vs. data-driven membership.** It is legitimate to *choose which disease
   areas to display* (an editorial focus). It is **not** legitimate to decide *which genes* fall
   in them by hand. Area membership is 100% a function of the data, and the test suite proves it
   (see §9).
5. **Falsifiable tests.** The test harness asserts **generic invariants and data integrity**, never
   predetermined biological conclusions. It must be possible for the engine to disagree with the
   author's expectations and still pass.
6. **Substance over flash.** Prefer a table when a table is the right tool. Plain-language ⓘ
   definitions for every technical term. Provenance and real references over visual spectacle.
7. **Honest about its own limits (no false authority).** Every ⓘ glossary tooltip must **define the
   term *and* state plainly what it is not** — because much of what the tool shows is heuristic, not
   measured. Required framings, in the tooltips themselves: the **composite** is a *heuristic
   prioritisation score, not a probability or a measure of importance*, and its weights are an
   *editorial choice*; **STRING/IntAct** values are *confidence, not proof of direct binding*, so
   "Core complex"/"Physical interactor" are labels of strong support, not proven complexes;
   **co-mention / Literature** is *correlation, biased toward well-studied genes — not interaction*;
   **Network context** is *topology, not functional proof*; **mechanism tags** are *keyword matches,
   suggestive not evidential*; **Reactome/HPO** are *the gene's own annotations, not a shared-with-CTBP1
   or patient-specific claim*; **ClinVar P/LP and VUS** are *gene-level database tallies — not a
   clinical interpretation of any individual, and not medical advice*; and the **Fields** are
   *editorial rules the data is then filtered by, not objective facts*. The **AI-context** tip must warn
   that an LLM can over-interpret and that answers should be checked against the linked sources. This
   principle overrides any temptation to make a number look more authoritative than it is.
8. **Human voice in all user-facing copy.** No spaced em dash (` — `) as a sentence connector anywhere
   a user reads it (tooltips, notes, captions, labels, button titles, dropdown text, the AI-context
   dump, the README). The spaced em dash is a tell that reads as machine-written; use a comma,
   semicolon, colon, period, or parentheses instead, whichever fits the sentence. Keep the en dash in
   numeric ranges (`0–100`, `0–1`) and hyphens in compound words; a lone `—` as a table "no value"
   glyph is fine. (This applies to user-facing text; the dashes in source-code comments and in this
   spec document are not user-facing.)

---

## 3. Tech & architecture

Vanilla HTML/CSS/JS. No dependencies. Four front-end files + a stdlib-Python data pipeline + a Node
test harness.

Important: Based on the Design Files from Google Stich in the folder /stich

The file roles:

| File | Responsibility |
|---|---|
| `index.html` | UI shell, all CSS (the design system lives here), bundled `fonts/`. |
| `app.js` | Rendering, interaction, the drawer, views, discoveries, export. **Reads** `window.CTBP1_DATA` + `window.CTBP1_ENGINE`. Wrapped in an IIFE. (No live-network probe / "how to read" modal — methods live in this brief; the composite weights are fixed constants, not a live control.) |
| `engine.js` | The **pure inference engine** — no DOM, no hard-coded genes. Exposes `window.CTBP1_ENGINE`. All scoring/classification/paths live here. |
| `app-data.js` | The bundled evidence snapshot: `window.CTBP1_DATA = {…}`. Generated by the pipeline. |
| `data/*.py` | Reproducible fetch/build pipeline, **Python standard library only** (urllib, json, csv, zipfile, re). Each step parses `app-data.js`, mutates it, and rewrites it. |
| `data/verify.js` | Node test harness — `eval`s `app-data.js` + `engine.js` and asserts invariants. |
| `fonts/` | The DESIGN.md font families (Hanken Grotesk, Inter, JetBrains Mono) bundled locally as woff2 for offline rendering. |
| `README.md` | The GitHub front page — **how to download the whole tool for offline use** (Download‑ZIP / `git clone` / Release), how to open it (`index.html`, no server), which files are needed at runtime (everything except `data/` and `stich/`), the maintainer rebuild commands, and the sources/license note. |

Open with `?noboot` in the URL to skip the intro animation (used by the headless tests).

**Headless-testing note:** verify the DOM/computed-state via the DevTools protocol (Brave/Chrome
`--headless=new --remote-debugging-port=…`, driven by Node's built-in `WebSocket` + `Runtime.evaluate`).
The central `<canvas>` may render blank in some `--disable-gpu` screenshot captures — that is a
capture quirk, not a bug; assert state via the protocol, not pixels.

---

## 4. Data model (`window.CTBP1_DATA`)

Bundle a single JSON object, minified (`json.dumps(data, separators=(',',':'))`).

```
{
  gene: {                         // the CTBP1 hub
    sym:'CTBP1', name, summary,   // NCBI/RefSeq summary
    uniprotFunc, cofactor, subunit:[…],
    go:{MF:[…],BP:[…],CC:[…]}, reactome:[…],
    ids:{ ensembl:'ENSG00000159692', entrez:'1487', uniprot:'Q13363', string:'…' },
    litTotal, diseaseCount,
    dis:[{n,s},…], tract:[…],     // disease assocs + tractability buckets
    clinvar:{plp,vus,total}, phenotypes:[…], phenoCount,
    refs:[{pmid,t,a,y,j,c},…],    // landmark CTBP1 papers
    agingRefs:[…], mim
  },
  nodes:[ {                       // up to ~250 partners, each:
    sym, name, ensembl, entrez, uniprot, mim, rank,
    s:{ c,e,d,t,a,p,n,f },        // STRING combined + 7 channel scores (0–1)
    lit, dz, tract:[…],
    areas:{ "<EFO therapeutic area>": score, … },   // OT area aggregation
    dis:[{n,s},…],                // OT disease associations (top ~20, name+score)
    func, funcRefs:[…], refs:[…], syn:[…],
    comention:{title,abs,all},    // Europe PMC tiered co-mention counts
    intact:{type,direct,miscore,methods:[…],pmids:[…],count},
    clinvar:{plp,vus,total}, pathways:[…],
    phenotypes:[…], phenoCount,
    aging:{ genage:bool, longevity:bool, why, id, pmids:[…] }   // present only if a member
  }, … ],
  edges:[ {a,b,s}, … ],           // partner↔partner STRING edges
  meta:{ date, species, neighborhood, sources:[…], channelLegend, edgeCount, nodeCount }
}
```

STRING channel keys: `c`=combined, `e`=experiments, `d`=databases, `t`=text-mining, `a`=co-expression,
`p`=fusion, `n`=neighborhood, `f`=co-occurrence.

---

## 5. Data sources & pipeline (`data/`)

All reachable without keys. Each pipeline step parses `app-data.js` via
`^\s*window\.CTBP1_DATA\s*=\s*(\{.*\})\s*;\s*$` (re.S), mutates, rewrites.

| Source | Provides | Notes / gotchas |
|---|---|---|
| **STRING v12** | the ~250-gene neighbourhood, 7 channel scores, partner↔partner edges | top‑250 by combined score is a stated curation choice; nodes lacking a resolvable Ensembl+Entrez are dropped (so the bundle holds ≤250) |
| **Open Targets v4** (GraphQL) | per-gene disease associations (top‑20), EFO therapeutic-area aggregation, tractability, function descriptions | `areas` = sum of association score per `therapeuticAreas` entry |
| **IntAct** (EBI REST: `ebi.ac.uk/intact/ws/interaction/findInteractions/CTBP1`) | curated experimental interactions: type (incl. *direct interaction*), detection method, PMID, MI-score | PSICQUIC is retired — use this REST ws; keep human–human only |
| **Europe PMC** | tiered, synonym-aware co-mention counts (title / title+abstract / full-text) + the actual papers | see §8 for the exact query rules |
| **UniProtKB** (`Q13363`) | function, NAD cofactor, complex membership, function-evidence PMIDs | |
| **NCBI ClinVar** (eutils esearch, `retmode=json`) | per-gene P/LP · VUS · total variant counts | **Must** use the `[Filter]` tokens `clinsig_pathogenic`, `clinsig_likely_path`, `clinsig_vus` — NOT `[Clinical significance]` (Entrez maps that to free text → wrong counts). The in-app count links must mirror these exact tokens. |
| **HPO** (`ontology.jax.org/api/network/annotation/NCBIGene:<entrez>`) | clinical-phenotype terms + count | hpo.jax.org deep links hard‑404 (client SPA). Link the count to the API response; offer a **Monarch** (`monarchinitiative.org/NCBIGene:<entrez>`) browse link. |
| **Reactome ContentService** (`/data/mapping/UniProt/<acc>/pathways?species=9606`) | specific leaf pathways per gene | **Preferred** (specific leaves), but **fail-fast** — its endpoint can 5xx, so don't retry-storm it. MyGene's `pathway.reactome` is flat/arbitrary-order and surfaces broad umbrellas, so use it **only as a last-resort fallback when ContentService is unavailable, umbrella-filtered**. |
| **GenAge + LongevityMap** (HAGR, `genomics.senescence.info`) | the data-driven **Aging / longevity** membership | GenAge human ageing genes + LongevityMap *significant* longevity-association genes; bundle `node.aging` with provenance (`why`/`id` or PubMed `pmids`). HAGR can hard-block these zips (415) — **degrade gracefully** (preserve existing `node.aging`), never crash the pipeline or wipe aging. |
| **MyGene.info / NCBI Gene / GeneCards / Ensembl / PubMed / AlphaFold / PDBe / OMIM** | IDs, GO, deep links, structure models | watch for HGNC renames (symbol-scope lookups can `notfound`) and non-primary-assembly Ensembl IDs |

Pipeline order (each rewrites `app-data.js`): `fetch_core → enrich → topup → netfetch → build_data →
refs → diseases → evidence → annotate → genage → (fix-ups)`. Network steps must be idempotent.
`data/run_all.py` drives these in order (with a snapshot backup + `--from <step>` resume). The
neighborhood size is `TOPN` in `build_data.py` (with matching `[:N]` slices in
`enrich`/`topup`/`netfetch`) — set to **250**; run `run_all.py` (needs network — a few thousand live
API calls) to regenerate the bundled snapshot at that size.

---

## 6. The inference engine (`engine.js`)

A pure module. Receives only `CTBP1_DATA`. Same logic for every gene.

### 6.1 Connection score (weights are **fixed constants** `phys 0.5 / lit 0.3 / ctx 0.2` — no UI sliders)
- **Physical** `phys = clamp(s.e + 0.5·s.d)` — STRING **experiment + curated-DB channels only**.
  The combined score `s.c` is **deliberately excluded** (it folds in text-mining and would
  double-count literature, e.g. inflating a text-only pair to a fake "physical").
- **Literature** `lit = log10(litEff+1) / log10(MAXLIT+1)`, where `litEff = 0` for stop-listed
  symbols (`IMPACT, GAPDH, TBP, ACTB, B2M`), else `node.lit`.
- **Network context** `ctx = clamp(CTXRAW / MAXCTX)`, `CTXRAW` = summed partner↔partner edge
  weight (excluding the CTBP1 hub).
- **Composite** `= 100 · (W.phys·phys + W.lit·lit + W.ctx·ctx) / (W.phys+W.lit+W.ctx)`.

### 6.2 Connection type (keys off **physical** evidence, never the DB channel alone)
```
Core complex          if s.c ≥ 0.9 AND (s.e ≥ 0.5 OR IntAct direct)
Physical interactor   else if s.e ≥ 0.2 OR IntAct (direct|physical association)
Literature-linked     else if lit ≥ 0.6 AND phys < 0.45
Functional neighbour  else if ctx ≥ 0.45 AND phys < 0.45
Associated            otherwise
```
A DB-only pair must **never** be typed as a physical complex member.

### 6.3 The fields — five SECTOR fields + cross-cutting overlay/filter fields (read §2.4)
Ten **fields** (biology/disease lenses), in this order, each shown as a lens, a per-gene flag, and a
findings row. The **first five are SECTOR fields** (oncology, metabolic, neurodegeneration, CNS,
neurodevelopment) — these, and only these (`sector:true`), are the constellation's angular wedges and
decide a node's colour (its *dominant* field). The rest are **cross-cutting overlay/filter fields**:
they never own a wedge — they filter *every* view, and **Aging** additionally paints a gold halo on
its members. In the left panel all ten sit in one flat **"Fields"** list (no divider). **Which**
fields to show is editorial; **which genes** belong is decided only by the data via the field's
`kind` (`ot` = EFO area-sum > 0.15, `name` = OT disease-name match, `aging` = GenAge ∪ LongevityMap).
Adding/removing an `ot` field is just a `THEMES` entry (EFO key + threshold) — no engine logic.

| key | label | colour (`--area-<key>`) | sector? | `kind` & membership rule |
|---|---|---|---|---|
| `oncology` | **Oncology** | `#e11d48` | ✓ | `ot` — EFO sum `"cancer or benign tumor" > 0.15` |
| `metabolic` | **Metabolic disease** | `#0d9488` | ✓ | `ot` — EFO `"nutritional or metabolic disease" + "endocrine system disease" > 0.15` |
| `neurodegen` | **Neurodegeneration** | `#d97706` | ✓ | `name` — OT disease names match Alzheimer/Parkinson/ALS/Huntington/dementia/… |
| `cns` | **CNS / neuroscience** | `#7c3aed` | ✓ | `ot` — EFO `"nervous system disease" + "psychiatric disorder" > 0.15` |
| `neurodev` | **Neurodevelopment (incl. ASD)** | `#2563eb` | ✓ | `name` — OT names match autism/ASD + intellectual disability + developmental delay + DEE |
| `aging` | **Aging / longevity** | `#ca8a04` | — | `aging` — `node.aging` present (GenAge ∪ LongevityMap); also a gold halo |
| `immunity` | **Immunity** | `#16a34a` | — | `ot` — EFO `"immune system disease" > 0.15` |
| `cardiovascular` | **Cardiovascular** | `#db2777` | — | `ot` — EFO `"cardiovascular disease" > 0.15` |
| `hematologic` | **Hematologic (blood)** | `#c2410c` | — | `ot` — EFO `"hematologic disease" > 0.15` |
| `eye` | **Eye / vision** | `#0891b2` | — | `ot` — EFO `"disorder of visual system" > 0.15` |

**Field colour palette (light mode — pinned, canonical).** Hue-faithful to the original tokens but tuned
for visibility on the light DESIGN.md surface (saturated Tailwind-scale tones, spaced for mutual
distinctness on white). Define each in `index.html :root` as `--area-<key>` (e.g.
`--area-oncology:#e11d48`). Apply per DESIGN.md's component notes:
- **Constellation node fill & angular wedge** (the 5 `sector` fields only) = the solid `--area-<key>`.
- **Gene-category chips & flags** = a solid dot in `--area-<key>` + a pale tint background
  `color-mix(in srgb, var(--area-<key>) 12%, var(--surface-container-lowest))` + a `color-mix(… 30% …)`
  hairline + **dark text** (`--on-surface` `#0b1c30`) — never coloured text (per DESIGN.md chip spec).
  All chip text (gene-category chips, **Clinical-phenotype terms**, mechanism tags, GO terms) is set in
  the **body sans** (`--sans`, Inter) — the same family used everywhere — **never the mono face**;
  monospace is reserved for numbers/IDs (`numbers in tabular/mono for alignment`), so natural-language
  term labels like phenotype names must not render in JetBrains Mono.
- **Discovery-card top-border** and **Findings row left-border** = the solid `--area-<key>`.
- **Aging** is the only **gold** (`#ca8a04`) and the only overlay that paints a soft halo on its members —
  `color-mix(in srgb, var(--area-aging) 55%, transparent)` glow — and it never fills a wedge.
- Distinctness guards: gold is reserved for aging; amber `#d97706` = neurodegeneration and deep-orange
  `#c2410c` = hematologic are kept clear of it. `color-mix` is supported by the Chromium target; if you
  avoid it, precompute the 12 % / 30 % tints to static hexes.

Rules:
- **Disease-name floor** (applied uniformly, suppresses noise): a disease counts if `s ≥ 0.18`,
  **or** it is in the gene's top‑3 associations **and** `s ≥ 0.10`.
- For `ot` areas, `re` (a disease-name regex) is used **only** to list example diseases as
  provenance; membership is the area-sum.
- **Strength** (0–1, for ranking/colour): `ot` = area-sum / total-area-burden; `name` = top
  matching association score; `aging` = 0.6 (GenAge) / 0.45 (LongevityMap-only).
- **Dominant area** (drives the node colour) = the strongest **disease** area. **Aging is an
  overlay** and is excluded from the dominant choice unless the gene belongs to no disease area
  (so e.g. a curated-ageing cancer gene still colours by cancer).
- Each membership ("flag") carries `{key,label,theme,source,sev,top,matches}` where `top` is the
  exact sourced evidence (an OT disease + score, or a GenAge/LongevityMap reference). `sev =
  clamp(round(strength·3),1,3)`. **Flags ARE memberships** — no separate hand-picked severity list.

### 6.4 Mechanism tags (separate from disease areas)
Match function text uniformly: `redox` (NAD⁺/NADH/oxidoreductase/dehydrogenase/sirtuin), `chromatin`,
`repress` (co-repression), `wnt` (Wnt/EMT), `synaptic`, `apoptosis`. **NAD⁺/redox is a mechanism
tag — it is NOT the Aging area**.

### 6.5 Paths, discoveries, synthesis, roll-ups
- `path(from,to)`: every profiled gene is a **direct** STRING neighbour of CTBP1, so return the
  **direct edge**. (A max-product walk would detour through the ~0.999 corepressor-hub clique and
  read as a spurious "indirect" route — do not present one.)
- `discoveries(W)`: a blended, de-duplicated, diversity-capped feed (strongest connections, best
  exemplar per disease area, most co-mentioned, under-explored hypotheses = high physical + thin
  literature). One gene appears at most once.
- `synthesis(W)`: a data-derived lead+body (factual; CtBP1 is an NAD(H)-sensing corepressor).
- `themeSummary(W)`: membership per area (`themes[key] > 0`), exposure ranked by **gene count**.
- `findings(W)`: one row per (gene × area membership), each fully sourced.

Export: `THEMES, THEME_ORDER, MECH, classify, connection, analyse, path, discoveries,
themeSummary, themeExposure, synthesis, findings, …`.

---

## 7. The UI (`app.js` + `index.html`)

The **header + insight bar** form a *provenance strip* — *what* the data is, *where* it comes
from (every source linked), and *how it was built* — sitting above the views and **one click away**
via the header **ⓘ Sources** toggle (collapsed by default; see the Insight-bar bullet).

- **Header**: kept deliberately minimal — the controls (☰) icon, the brand lock-up (the **CTBP1
  Interactome Atlas** wordmark **first** — bold dark **`CTBP1`** + lighter teal **`Interactome Atlas`**,
  one typeface, two tones (the `.brandname b` weight-400 `--secondary` treatment covers *both*
  "Interactome" and "Atlas") — then a **smaller** HADDTS Foundation logo as the trailing secondary
  mark, separated by a hairline divider — never the logo first). The **whole brand lock-up is a "home"
  control**: clicking it (or the logo) behaves exactly like the drawer's **⌂ CTBP1 hub** button —
  `goHub()`, clearing any gene/lens selection and returning the drawer to the CTBP1 hub. It is **not** a
  link to `BUILD-PROMPT.md` (the "How it was built" methods link lives in the insight strip's
  `Method →` pair). The header also carries the sources toggle, now an **icon-only `ⓘ` button** (the
  "Sources" label is dropped; the `title`/`aria-label` still name it), the
  dossier (▤) icon, and, pinned at the **top-right**, a **dark-mode toggle** (☾ in light / ☀ in
  dark). The toggle flips `<html data-theme="dark">`; **light mode is the canonical design and stays
  exactly as specified** (the `:root` tokens), while **dark mode is a token-override-only theme**
  (`[data-theme="dark"]` re-defines the surface / text / accent custom properties — the pinned
  `--area-<key>` functional-area hues are left identical). The choice **persists** in `localStorage`
  (`ctbp1-theme`, offline-safe, default **light**), `initTheme()` sets it before first paint to avoid a
  flash, and the canvas constellation re-themes its two light-assuming colours (selected-node ring, hub
  fill) off the active theme. The few elements with **hardcoded translucent-white backgrounds** — the
  constellation **legend** (bottom-left) and the **hint** card (top-right "click a node …") — are
  overridden to a dark translucent card in dark mode (`rgba(16,24,40,…)`) so they don't glow bright.
  (The hint text reads "click a node to open its dossier · gold halo = aging-linked" — it must **not**
  mention "drag weights", since the weight sliders were removed.) Unlike the desktop-only `.iconbtn`s
  (hidden ≥1024px), the theme toggle is
  its own always-visible control (shown in both the mobile and desktop layouts). The build-date,
  Export, and "How was this built?" actions are **not** in the
  header — they live in the closable insight strip below, which the **ⓘ Sources** button opens/closes
  (the strip is **closed by default** — see next bullet). (Methods/glossary live in the build prompt now;
  the composite weights are fixed, so there is no Evidence-weighting control and no separate
  Re-analyze/Live-data/How-to-read button. Per-block AI copy stays on every drawer's `<pre>`; the
  copy-all hook also backs the Export action.)
- **Insight bar (the closable meta/provenance strip)**: a compact, link-first strip. Its first row is
  the gene **IDs + dataset meta**, and below it a one-line **"what it profiles + sources" caption**.
  - **First row — a list of named source links, each with a trailing `↗`, styled exactly like the gene
    dossier's "Open in databases" block (NOT `LABEL→value` mono pairs, NOT boxed pills, NOT a
    middot-separated band of label+value items).** Each source is shown as its **name** as a small
    **outlined pill** (`.links` style: `--sc-low` background, `--outline-variant` hairline, muted
    `--on-surface-variant` text, **cyan border on hover**) with a trailing external-link `↗` glyph — e.g.
    `STRING ↗ · Open Targets ↗ · UniProt ↗ · NCBI Gene ↗ · Ensembl ↗ · OMIM ↗` — the **same `.links`
    pill identity** the dossier's **Open in databases** section uses, so the strip row and the dossier
    block read as one family (reuse the same `.links` CSS class; do not invent a parallel style). Each
    link points to the CTBP1 hub's live record in that source (the IDs live **here, not in the header**).
    The **literal ID strings are no longer printed** in the strip — the row no longer reads
    `ENSEMBL ENSG00000159692 · ENTREZ 1487 · …`; the named pill *carries* the ID (it resolves to that
    record), so the bare ID value is dropped for a cleaner band. One treatment for the whole row: pill +
    `↗`, same size, **no per-item small-caps field labels and no `LABEL→value` pairs**. `Method ↗`
    (the "How it was built" link to this `BUILD-PROMPT.md`) renders in the **same named-link-with-`↗`
    style** as the sources. The **Export** action is the one deliberate exception to the uniform pill
    row: it reads **`⧉ Export AI Context of all Interactions`** with the **copy (`⧉`) glyph, not a `↗`**
    (it copies to the clipboard rather than opening a link), so it stands out as a clearly-labelled
    call-to-action.
    The dataset **meta** that used to sit in the header — `Built ‹date›`, `Genes ‹n›`, `Edges ‹m›` — are
    static counts (not links, so they take no `↗`); keep them as plain small-caps `LABEL value` items,
    visually subordinate, set **after** the link list (or fold them into the caption's lead line) so they
    never break the link row's rhythm.
  - **Caption**: a bold **lead line** ("‹n› STRING interactors of human CTBP1 — the top-250 by combined
    score…", snapshot date) above a labelled **Sources** line (STRING / Open Targets / Europe PMC /
    IntAct / ClinVar / HPO / Reactome / GenAge, each linked, middot-separated).
  - **Closed by default, toggleable.** The strip starts **collapsed** on load (class `hidden`); the
    header **ⓘ Sources** button toggles it open/closed and reflects state via `aria-pressed`, and the
    strip's own ✕ closes it. (It is a one-click reveal, not a one-way permanent dismiss — there must
    always be a way to bring it back.) No persistence: it reopens collapsed on the next load. (No
    synthesis sentence in the bar — `synthesis()` still feeds the hub AI block.)
  - **Export** copies the *entire* sourced AI context (the CTBP1 hub, all ten fields, and every
    interactor, via the `copyAllContext()` / `aiForAll()` dump) to the clipboard as plain text for
    pasting into an LLM. The button reads **`⧉ Export AI Context of all Interactions`** with the copy
    glyph; the size (~500,000 tokens) and the full "hub + all fields + every interactor" explanation
    live in its `title` tooltip.
  - **Offline recommendation.** The strip carries a clean, **neutral** notice card (`.offline-note`,
    `--sc-low` background with an `--outline-variant` hairline — **not** red): a **heading line**
    ("Consider running this tool offline", display font, on-surface) above a **muted body**: *"This page
    is served over the internet via GitHub Pages. For a permanent, fully self-contained copy that works
    anywhere with no connection, download it from the HADDTS Foundation on GitHub ↗"* (the link, cyan,
    points at the foundation's GitHub repo). (It currently lives inside the desktop-only,
    collapsed-by-default strip; promote it to an always-visible bar if every visitor must see it.)
  - **Desktop-only.** This closable strip is a **desktop affordance**: on viewports `< 1024px` it is
    **never opened/shown at all** (`@media(max-width:1023px){ .insight{display:none} }`), and the
    header **ⓘ Sources** toggle is hidden there too. The meta actions (Export especially) and the
    source links are therefore desktop-only; mobile keeps the header + views uncluttered.
- **Left panel**, top to bottom: **Trace connection** (**first**, above everything — pick an
  interactor and read its direct STRING edge to CTBP1), then **Fields (lenses)** — one flat list of
  all ten fields (five sector fields, then aging / immunity / cardiovascular / hematologic / eye);
  **Click a field to focus it** (every view filters to just that field; click the focused lens again
  to reset; the Findings area-chips mirror this) — then **Display limit** (how many top interactors to
  draw, its own section with a one-line note). **There is no Evidence-weighting control.** The §6.1
  composite weights are **fixed constants** (`phys 0.5 / lit 0.3 / ctx 0.2`); the former three
  physical/literature/network sliders are **dropped** — re-weighting moved genes only marginally, so a
  live control wasn't worth the clutter. (There is also **no Layout toggle** — the constellation has a
  single canonical **sector** layout; the former *Radial* option is dropped.) The left panel has **no
  footer** — the former bottom **⚙ Methods / ◎ Sources** links are removed (methods live in the insight
  strip's `Method →` link and the sources in the **ⓘ Sources** strip, so they were redundant).
- **Center — four views** (Constellation · Table · Findings · Discoveries — there is **no Network
  view**; the force-layout view was dropped from the project):
  1. **Constellation** — CTBP1 at centre; interactors placed by dominant area (angular sector +
     colour) and connection strength (radius); pulse = strong area assoc. Placement is **always by
     sector** — there is no alternative radial layout. (No "druggable" indicator:
     Open Targets *tractability* measures whether a molecule could engage a protein, not whether one
     should — e.g. a tumour suppressor like p53 would be *restored*, not inhibited — and it flags
     ~half the neighbourhood, so it carries little signal. The full tractability data stays one click
     away via the gene's Open Targets link.) Because **aging is an overlay** (never a gene's dominant
     area), the constellation has only the **five disease sectors** — aging gets no sector of its own.
     Instead, the genes that *are* aging members carry a soft **gold longevity halo** wherever they
     sit — an honest overlay (a property of genes); gold now denotes aging only. Papers are **never**
     placed as nodes in the gene map: CtBP1's curated ortholog-aware reading list (`gene.agingRefs`,
     incl. the landmark *C. elegans* `ctbp‑1` life-span paper, PMID 19164523) lives in the
     Aging/longevity lens dossier (§8), where literature belongs.
  2. **Table** — sortable evidence table.
  3. **Findings** — every (gene × area) membership, filterable by area chip (the chips mirror the
     left-panel lens focus, and vice-versa), each row sourced
     (OT disease+score, or GenAge/LongevityMap for aging).
  4. **Discoveries** — the blended, de-duplicated, diversity-capped lead feed (`discoveries(W)`) as a
     **first-class view alongside Findings**, rendered as a responsive card grid; click a card to
     focus that gene. (It used to be a strip pinned to the bottom of the layout — it is now its own
     tab, not a bottom dock.)
- **Right drawer** — three context-aware modes: gene dossier, disease-lens panel, CTBP1 hub
  dossier. The drawer opens on the **CTBP1 hub** dossier by default; selecting a gene (any view) or a
  field-lens swaps the drawer to that dossier. Because the hub no longer shows automatically once you
  drill in, the **drawer header carries a `⌂ CTBP1 hub` button** that appears **only when a gene/lens
  dossier is open** and returns the drawer to the hub (clears the gene/lens selection) — so there is
  always a one-click way back to the subject. A **disease-lens panel** shows the area's membership rule, its member genes ranked by
  strength, and — for the **Aging/longevity** lens only — the curated, ortholog-aware reading list
  (`gene.agingRefs`, §8) clearly labelled as such. A gene dossier shows, in this order: IntAct, **Literature** co-mention, **Area memberships**, top
  disease associations, Pathways (Reactome), Clinical variants (ClinVar), Clinical phenotypes (HPO),
  mechanism tags, "Open in databases" deep links, then — **at the very bottom, just above the AI
  context block** — the **collapsible Connection** section and the **collapsible STRING channels**.
  - **Connection** and **STRING channels** are **de-emphasised** — useful but not prominent — so they
    are pushed to the **bottom of the dossier (directly above the AI context `<pre>`)** and each is a
    **collapsible `<details>` section ("zum aufklappen"), closed by default**. The collapsed summary
    still carries the **headline value** (Connection → `Composite ‹n›/100`; STRING channels →
    `Combined ‹s.c›`); expanding **Connection** reveals the three sub-scores (Physical / Literature /
    Network context) the rank is **built from** — that breakdown, plus the `composite` ⓘ glossary tip
    (which spells out the fixed `0.5 / 0.3 / 0.2` weighting), is the explanation of *what the rank is
    based on*. (An ⓘ inside a `<summary>` shows its tooltip on hover/focus without toggling the section.)
  - **Literature** co-mention is pulled **up — above Area memberships** — because the tiered,
    synonym-aware co-mention + the actual papers are a primary signal here; it is tiered rows linking
    to the exact Europe PMC query + the papers (no sub-heading over the paper list).
  - **Pathways (Reactome)** sits **before Clinical variants (ClinVar)**.
  - **Area memberships** = disease areas + aging, with provenance, area-coloured, no alarm icon.
- **AI block** — every drawer + the hub dossier has a copy-to-clipboard `<pre>` dumping *all shown
  values + source URLs* as plain text, ready to paste into an LLM. The AI-block heading carries an
  **ⓘ glossary tip** (`aictx`) that explains the workflow in plain, professional language: *the export
  is everything shown here — values, scores and the source links — so copy it with ⧉ Copy, paste it
  into your preferred AI assistant as context, then ask your question; the model can read the figures
  and follow the links to verify them.* The gene dump is **the shown values only** — it must **not**
  include the de-emphasised scoring internals: **no** `Composite ‹n›/100 (weights …) · physical … ·
  literature … · network …` line and **no** `STRING channels: combined … | experiments … | …` line
  (rank and connection type still appear; the gene's STRING-network link is kept). The **per-drawer
  copy buttons share one recognisable identity** — the same **⧉ clipboard icon** + a short **"Copy"**
  label + a cyan accent — so a user can spot them at a glance. The button is just **⧉ Copy** (its
  AI-block heading, "AI context — ‹gene/lens/CTBP1›", already supplies the scope; the scope is repeated
  in the `title` tooltip). Every AI dump is headed `CTBP1 INTERACTOME ATLAS: …`. The global counterpart
  is the insight-strip **Export** button, **`⧉ Export AI Context of all Interactions`**, which copies
  *everything*; it keeps the shared **⧉ copy glyph** and cyan accent but is fully labelled (it is a
  call-to-action, not a uniform pill).
- **Discoveries feed** — its own **view/tab** (a responsive card grid, not a bottom strip), click to focus.
- **Tooltips** — ⓘ glossary tooltips must be **instant** (a custom body-level tooltip, NOT the
  native `title=` attribute, which has a ~0.5–1 s browser delay). Position above the icon, flip
  below near the top, clamp to the viewport, ~70 ms fade, also show on keyboard focus.
- **Intro** — a brief boot animation; `?noboot` skips it.

---

## 8. Provenance & literature rules (exact)

- **Co-mention** is synonym-aware and tiered: in title / in title+abstract / anywhere in full
  text. Each count links to the **exact** Europe PMC query that produced it (the in-app query
  builder and the pipeline's query builder must be byte-identical so counts reproduce).
- **Exclude the CTBP1 lncRNA loci** from every co-mention query: append
  `NOT "CTBP1-AS2" NOT "CTBP1-DT" NOT "CTBP1-AS1"`. Do **not** exclude `"CTBP1-AS"` — "AS" is a
  stopword that nukes the result set.
- **Synonyms**: include real aliases but **drop ambiguous homographs** that name an unrelated gene
  (curated blocklist, e.g. `GLP1, P18, PC2, PH1, C21, DC42, IRA1`) — they cannot be detected
  syntactically.
- **References** are the synonym-aware, **citation-ranked** co-mention papers (prefer
  title/abstract, fall back to full text), not a bare strict-symbol query.
- **Stop-listed / housekeeping symbols** (`IMPACT, GAPDH, TBP, ACTB, B2M`) show an "ambiguous /
  house-keeping" caveat and are excluded from the literature score.
- **Aging/longevity literature for CTBP1 is ortholog-aware** (CtBP1 / CTBP‑1 / ctbp‑1), bundled as
  `gene.agingRefs`, and **rendered inside the Aging/longevity lens dossier** (the disease‑lens panel
  in the right drawer) — clearly labelled as a *curated reading list, not a discovery claim*. It is
  the one place a partner-gene lens carries hub-level CTBP1 papers, because a human‑only `"CTBP1"`
  co-mention search structurally misses the model-organism orthologue work. The list **must include**
  the landmark *C. elegans* `ctbp‑1` life‑span paper: Chen S, Whetstine JR, Ghosh S, Hanover JA,
  Gali RR, Grosu P, Shi Y. "The conserved NAD(H)-dependent corepressor CTBP‑1 regulates
  *Caenorhabditis elegans* life span." *Proc Natl Acad Sci U S A.* 2009;106(5):1496‑1501.
  **PMID 19164523 · PMCID PMC2635826 · DOI 10.1073/pnas.0802674106.** (This is a curation directive
  for the data/UI, not a test assertion — §9 still forbids the harness from pinning any paper.)

---

## 9. Test harness (`data/verify.js`) — falsifiable, no pinned conclusions

Run the **exact** `engine.js` against `app-data.js` and assert **generic invariants only**:
- **Anti-bias core**: recompute each area's membership *straight from the raw data* (using the
  same EFO areas / disease-name regex / GenAge bundle) and assert it **equals** the engine's
  members. If a gene were hand-placed, this fails.
- Exactly the 10 chosen field keys exist (exactly 5 are `sector` fields); removed/renamed keys gone.
- Per-gene **displayed areas == lens membership == flags** (consistent everywhere).
- Every disease-area flag cites a real OT disease from the gene's own associations; every aging
  flag cites GenAge/LongevityMap evidence.
- `findings()` = sum of memberships, each sourced + scored.
- Structural: `analyse()` returns all nodes sorted by composite; every node has a valid connection
  type; every profiled gene is a **direct** STRING neighbour; Core/Physical never from DB channel
  alone.
- Data integrity: no unresolved ID stubs (every node has Ensembl + Entrez); ClinVar present &
  `P/LP ≤ total`; pathways have no broad umbrellas; ambiguous aliases dropped; co-mention tiers
  monotonic; references present with valid PMIDs; HPO counts consistent.
- **Forbidden**: any assertion pinning a named gene to a rank/area/type, or requiring a specific
  disease/paper to appear.

Also keep `node --check app.js engine.js` clean.

---

## 10. Design system — defined by the Stitch design files in `/stich`

The design system is **not** specified inline here. It lives in the Google Stitch export under
`/stich`; read it and apply it verbatim.

- **`stich/CTBP1_atlas/DESIGN.md`** — the canonical token set: the full colour palette (surfaces,
  on-surface, outlines, primary / secondary / tertiary, semantic states), the typography scale and
  font families, rounding, spacing, elevation, and per-component styling (buttons, gene-category
  chips, data tables, inputs, discovery cards, sidebars). Implement these as CSS custom properties
  in `index.html :root` and follow its component notes.
- **`stich/<view>/code.html` + `screen.png`** — the reference layout and composition for the views
  (constellation / table / findings / discoveries, in mobile and `desktop_*_full` variants),
  including the header/insight strip, the left panel, and the right dossier drawer. Match this
  structure. (The Stitch export also contains a *network* reference, but the **Network view is dropped
  from the build** — ignore it; likewise there is no Layout/Radial toggle.)
- **`logos/`** — the HADDTS Foundation brand marks; use the supplied lockups. In the
  header the **CTBP1 Interactome Atlas** wordmark comes **first** and the **compact** HADDTS Foundation lockup
  follows at a **smaller** size (a secondary “by” mark, ~22 px, after a hairline divider) — never the
  logo first. The lockup is **theme-aware**: two `<img>`s of the *same* artwork/geometry are bundled —
  `logo-vert-colored.svg` (navy `#002255` wordmark + cyan `#03e2f2` icon) for **light** mode and
  `logo-vert-white.svg` (white `#ffffff` wordmark + the same cyan icon) for **dark** mode — toggled by
  CSS (`.logo-dark` is hidden by default; `[data-theme="dark"]` hides `.logo-light` and shows
  `.logo-dark`). Keep the two SVGs pixel-identical except for the wordmark fill so the swap is seamless.
  For brand-asset completeness, **every** colored SVG mark in `logos/` ships a `-white` dark-mode
  counterpart produced the same way (navy `#002255` → white `#ffffff`, cyan `#03e2f2` icon kept):
  `logo-vert-white.svg`, `logo-horiz-white.svg`, `HADDTS Foundation-white.svg`, plus a white-monochrome
  `logo-horiz-white-mono.svg` (from the all-black `logo-horiz-bw.svg`). Only the **vertical** pair is
  wired into the header; the rest are there for any dark-surface use. (The raster `*.png` and the `.ai`
  source can't be recoloured from text — re-export them from the vectors if a dark PNG is ever needed.)

Keep the overall posture the rest of this brief calls for: an institutional-modernist, dense,
provenance-first research console, with numbers in tabular/mono for alignment.

**Offline caveat — this app's constraint, which Stitch does not capture (§2.1):** Stitch assumes
web-hosted fonts, but this app must run from `file://` with no network. Bundle **every** font family
named in DESIGN.md (currently Hanken Grotesk, Inter, JetBrains Mono) locally as `@font-face` woff2
under `fonts/` with relative URLs — **no** web `@import` / Google-Fonts `<link>` — falling back to
`-apple-system, …`.

**Functional area colours** are **pinned** in §6.3 as light-mode hexes (`--area-<key>`), with the full
chip / tint / halo recipe spelled out there. Apply those exactly; nothing here is left to the builder's
judgement.

---

## 11. Acceptance criteria

- Opens offline from `file://` with no console errors; no network needed to function.
- The five disease areas (+ the aging/longevity overlay) render as lenses/flags/findings; membership
  is provably data-driven (`verify.js` membership==recomputation checks pass).
- Every shown value has a working source link; the per-drawer AI blocks produce complete, sourced
  plain text.
- `data/verify.js` passes with **generic invariants only** (no pinned-gene assertions).
- The DESIGN.md fonts actually render (bundled locally), with its documented type & weight hierarchy
- The engine contains no partner-gene special-casing (grep-clean per §2.3).

Build it to be correct and honest first, beautiful second — but it should be both.
