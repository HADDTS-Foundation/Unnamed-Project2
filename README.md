# CTBP1 Interactome Atlas

An **offline, single‑page web app** that profiles the **top‑250 STRING interactors of the human gene
CTBP1** and derives their biological / disease connections through a transparent, fully‑sourced
inference engine. Every number links back to the public record that validates it.

It runs entirely from your computer: **no install, no build step, no server, and no internet
connection** are required to use it. All data and fonts are bundled in the download; the only time the
app touches the network is when *you* click a source link to open a live record (STRING, Open Targets,
Europe PMC, ClinVar, …).

---

## Download for offline use

You have three ways to get the whole tool from GitHub. **Option A needs no software at all.**

### Option A: Download the ZIP (easiest)

1. On the GitHub project page, click the green **`< > Code`** button.
2. Choose **Download ZIP**.
3. **Unzip** the downloaded file anywhere you like (Desktop, a USB stick, a shared drive, anywhere).
4. Open the unzipped folder and **double‑click `index.html`**. It opens in your default browser and is
   ready to use, completely offline.

That's it. You can copy that folder to any machine and it will keep working with no internet.

### Option B: Clone with git

```bash
git clone https://github.com/<OWNER>/<REPO>.git
cd <REPO>
# then open index.html (double-click it, or:)
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

To update later: `git pull`.

### Option C: Download a tagged release

If the project publishes **Releases**, open the **Releases** page and download the latest
`Source code (zip)` asset; it is a fixed, citable snapshot. Then unzip and open `index.html` as in
Option A.

---

## Running it

- **Just open `index.html`** (a `file://` page) in any modern browser: Chrome, Brave, Edge, Firefox,
  or Safari. No web server is needed.
- Keep the folder **intact**: `index.html` loads `app.js`, `engine.js`, `app-data.js` and the
  bundled `fonts/` and `logos/` from alongside it. Moving `index.html` out on its own will break it;
  move the **whole folder** instead.
- Use the **☾ / ☀ toggle** at the top‑right to switch between light and dark mode (your choice is
  remembered on that browser).

> **Optional fallback.** A few hardened browser configurations restrict pages opened directly from
> `file://`. If the app ever looks broken that way, serve the folder locally instead: from inside the
> folder run `python3 -m http.server 8000` and visit `http://localhost:8000`. This is still 100%
> offline (localhost only); it is only a workaround for strict `file://` policies.

---

## What's in the download

| Path | What it is |
|---|---|
| `index.html` | The app shell + all styling. **Open this.** |
| `app.js` | UI rendering, views, the dossier drawer, and the AI‑context export. |
| `engine.js` | The pure inference engine (scoring, disease‑area membership, discoveries). |
| `app-data.js` | The bundled, sourced evidence snapshot (the CTBP1 hub + ~250 interactors). |
| `fonts/`, `logos/` | Bundled web fonts and brand marks, so the app renders offline. |
| `data/` | The data‑build pipeline + test harness (**not needed to run the app**, see below). |
| `BUILD-PROMPT.md` | The full specification the app was built from. |

Everything required to *use* the tool is the first five rows; the `data/` folder is only for
maintainers who want to regenerate the snapshot.

---

## For maintainers: rebuilding the data snapshot

The bundled snapshot is reproducible from public sources (this **does** need the internet and Python's
standard library only):

```bash
python3 data/run_all.py        # fetches STRING / Open Targets / Europe PMC / … and rewrites app-data.js
node data/verify.js            # data‑integrity test harness (should report all checks passing)
```

`data/verify.js` asserts generic invariants only (no biological conclusions are hard‑coded), so it is
safe to run any time to confirm the bundle is internally consistent.

---

## Sources & license

The data is derived from public resources (**STRING, Open Targets, IntAct, Europe PMC, UniProt,
NCBI ClinVar, HPO, Reactome, GenAge / LongevityMap**), each credited in‑app (open the **ⓘ Sources**
strip) and linked at the point of use. Some of these require attribution (e.g. STRING and UniProt are
CC BY 4.0); see the in‑app sources for details.

_License: to be finalized; see `LICENSE` once added._

---

*Built and maintained by the HADDTS Foundation.*
