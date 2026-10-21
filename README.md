# App Inventory — public site

Public GitHub Pages site for **App Inventory** (Brain Fart Games):

| Page | Path | Purpose |
|---|---|---|
| Home | [`index.html`](index.html) | Short product blurb |
| Support | [`support.html`](support.html) | Support contact — App Store Support URL |
| Privacy | [`privacy.html`](privacy.html) | Privacy Policy — App Store Privacy URL |
| Terms | [`terms.html`](terms.html) | Terms of Service |

**Support email:** bfgames.support@gmail.com

The private iPhone source repository is not the public URL for those fields.

## Published URLs

- Home: https://vh3.github.io/app-inventory-content/
- Support: https://vh3.github.io/app-inventory-content/support.html
- Privacy: https://vh3.github.io/app-inventory-content/privacy.html
- Terms: https://vh3.github.io/app-inventory-content/terms.html
- Recognition manifest: https://vh3.github.io/app-inventory-content/config/catalog-config-v1.json

## Recognition data

Same layout as `bfg-sudoku-site`: a small `config/` manifest and immutable
files under `content/`. The App downloads these only when someone taps
**Check for updated recognition data**.

| Path | Contents |
|---|---|
| `config/catalog-config-v1.json` | `catalogID`, `publishedAt`, filenames, sha256 |
| `content/IconDetector_<catalogID>.mlpackage.zip` | Core ML icon detector, store-method zip |
| `content/CatalogIcons_<catalogID>.zip` | Name index only (`catalog_index.json`) |

Raw third-party icon images are not published here. The catalog zip is the
name list the manifest’s hash covers. A later publish must keep that rule.

Publishing a new detector from the private project:

```bash
python3 training_pipeline/ingest/publish_catalog.py \
  --model ios_prototype/Resources/Models/IconDetector.mlpackage \
  --catalog ios_prototype/Resources/CatalogIcons \
  --repo-dir /path/to/app-inventory-content \
  --host vh3.github.io \
  --path-prefix /app-inventory-content/
```

That script zips whatever is in the catalog directory. Before pushing,
replace the catalog zip with the name index only if the directory still
contains icon PNGs. Then push `main`. Pages serves the branch root. There
is no GitHub Action.

## Enable GitHub Pages

1. Repo Settings → Pages
2. Source: Deploy from a branch
3. Branch: `main` / `/ (root)`
4. Save

Or:

```bash
gh api -X POST repos/vh3/app-inventory-content/pages \
  -f build_type=legacy \
  -f source[branch]=main \
  -f source[path]=/
```
