# app-inventory-content — public recognition-data host

Public static content for the **iPhone App Inventory** prototype's optional
recognition-data updates (BL-009.4/.5, DEC-025). Hosts a versioned icon
detector Core ML model and icon catalog, published from
`terms_of_service_monitor/screen_captures/training_pipeline/ingest/publish_catalog.py`
(BL-009.4) after an on-demand retrain (`retrain_all.sh`, BL-009.3).

Modeled directly on the connected `bfg-sudoku-site` sibling project's own
shipped `config/app-config-v1.json` pattern (B063) -- same `config/` +
`content/` split, same plain-GitHub-Pages-from-`main`-root deploy, no
CI/CD needed for this repo itself. See PRD `iPhone_App_Inventory_PRD_v1.md`
DEC-025 / EVID-052 for the full design writeup and how this diverges from
that precedent (integrity hashing, explicit-only app-side pull).

**This repo holds only derived recognition artifacts — never user
screenshots, crops, or any personal data.** The model and catalog were
built entirely from this project's own hand-curated `screen_captures/`
photos and synthetic training data; nothing a tester submits via the app's
diagnostics pathway (REQ-026) is ever republished here in any form.

## Layout

| Path | Contents |
|---|---|
| `config/catalog-config-v1.json` | Schema-versioned manifest: `catalogID`, `publishedAt`, filenames + sha256 hashes for the current model/catalog artifacts |
| `content/IconDetector_<catalogID>.mlpackage.zip` | The exported Core ML icon detector, zipped |
| `content/CatalogIcons_<catalogID>.zip` | The icon catalog (PNGs + `catalog_index.json`), zipped |

Artifacts are immutable under their own `catalogID` — publishing a new
version writes new files under a new ID rather than overwriting an old one,
matching `bfg-sudoku-site`'s own "content remains immutable under stable
IDs" convention.

## Enable GitHub Pages

1. Repo Settings → Pages
2. Source: Deploy from a branch
3. Branch: `main` / `/ (root)`
4. Save, wait for the site to become live

Or via CLI (owner):

```bash
gh api -X POST repos/<owner>/app-inventory-content/pages \
  -f build_type=legacy \
  -f "source[branch]=main" \
  -f "source[path]=/"
```

## Publishing a new version

From `terms_of_service_monitor/screen_captures/`:

```bash
python3 training_pipeline/ingest/publish_catalog.py \
  --model ../ios_prototype/Resources/Models/IconDetector.mlpackage \
  --catalog ../ios_prototype/Resources/CatalogIcons \
  --repo-dir /path/to/app-inventory-content \
  --host <owner>.github.io \
  --path-prefix /app-inventory-content/ \
  --git-commit
```

`--git-commit` stages and commits locally only — it never pushes (this
session's tooling had no GitHub credentials available to do that safely;
see the PRD's R053 diary entry). Push from your own authenticated
terminal:

```bash
cd /path/to/app-inventory-content
gh repo create <owner>/app-inventory-content --public --source=. --remote=origin
git push -u origin main
```

(or create the repo first in the GitHub web UI, then `git remote add origin
https://github.com/<owner>/app-inventory-content.git && git push -u origin main`.)
