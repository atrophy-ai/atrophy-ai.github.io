# atrophy-site

Static marketing + policy website for the Atrophy iOS app.

- Plain HTML + CSS, **no JS**, no build step, no third-party CDN.
- Self-host fonts (Source Serif 4, Geist, Geist Mono) via woff2 in `assets/fonts/`.
- Required for App Store submission: `privacy.html` and `terms.html` are mandatory URLs.
- Hosted on GitHub Pages **org-Pages site** at `https://atrophy-ai.github.io/` (no subpath).

## Local preview

```sh
cd atrophy-site
python3 -m http.server 8000
# open http://localhost:8000
```

No build step. Edit, save, refresh.

## Deploy

1. **Repo:** push the contents of this directory to `atrophy-ai/atrophy-ai.github.io` on `main` branch. The repo name must match `<org>.github.io` exactly — that's GitHub's org-Pages convention and is what makes the site serve at the org root with no subpath.
2. **Pages:** in the repo's GitHub web UI → **Settings → Pages** → set Source to `Deploy from a branch`, branch `main`, folder `/ (root)`. Save.
3. **URL:** the site is live at `https://atrophy-ai.github.io/` within a minute.
4. **Custom domain (deferred to v1.1):** when `atrophy.app` (or another domain) is purchased, drop a `CNAME` file at the repo root containing the bare domain, add the GitHub Pages A/AAAA records and a `www` CNAME at the registrar, and enable "Enforce HTTPS" in repo settings once the cert provisions. No content changes needed.

### Live URL and path conventions

- Repo: `atrophy-ai/atrophy-ai.github.io` (org-Pages, the special repo whose name matches the org)
- Live URL: `https://atrophy-ai.github.io/`
- Local working directory name: `atrophy-site/` — purely a local-disk label, doesn't need to match the remote repo name.

Internal links use **document-relative paths** (`assets/css/style.css`, `privacy.html`) — never root-relative (`/assets/...`). This way every link works identically when the site eventually moves to a custom domain root, or to a future project-Pages location, or behind a path-prefix.

## Fonts

The CSS expects six woff2 files in `assets/fonts/`:

| File                         | Source                                                                                                              |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| `SourceSerif4-Regular.woff2` | [adobe-fonts/source-serif](https://github.com/adobe-fonts/source-serif), `WOFF2/TTF/SourceSerif4-Regular.ttf.woff2` |
| `SourceSerif4-Italic.woff2`  | same repo, `SourceSerif4-It.ttf.woff2`                                                                              |
| `SourceSerif4-Medium.woff2`  | same repo, `SourceSerif4-Medium.ttf.woff2`                                                                          |
| `Geist-Regular.woff2`        | [vercel/geist-font](https://github.com/vercel/geist-font), `Geist-Regular.woff2`                                    |
| `Geist-Medium.woff2`         | same repo, `Geist-Medium.woff2`                                                                                     |
| `GeistMono-Regular.woff2`    | same repo, `GeistMono-Regular.woff2`                                                                                |

Both font families are **SIL Open Font License 1.1**. When you drop the woff2 files into `assets/fonts/`, also commit the OFL text from each upstream repo (the OFL requires distributing the license alongside binary fonts):

- Source Serif 4 OFL: [adobe-fonts/source-serif/blob/release/LICENSE.md](https://github.com/adobe-fonts/source-serif/blob/release/LICENSE.md) → save as `assets/fonts/SourceSerif4-OFL.txt`
- Geist OFL: [vercel/geist-font/blob/main/LICENSE.TXT](https://github.com/vercel/geist-font/blob/main/LICENSE.TXT) → save as `assets/fonts/Geist-OFL.txt`

If the woff2 files are missing, the site still works — it falls back to Georgia (serif), system-ui (sans), and ui-monospace. The visual difference is minor; primarily the wordmark and headings get slightly different proportions.

## Visual identity

The CSS token block in `assets/css/style.css` mirrors the bone palette in the iOS app's `Atrophy/Theme/AtrophyTheme.swift` (cool editorial bone, Tiffany teal accent `#5FBFB8`). When the app palette changes, the site CSS variables should be updated to match.

## Locked copy

This string appears on every page footer and is brand-locked. Do not paraphrase:

- `self-awareness only · not a diagnosis`

There is also a small list of banned terms that the project explicitly avoids. The list lives in the iOS app's internal design notes, not in this repo. Before merging copy changes, sanity-grep against that list.

## Pages

| File           | Purpose          | Required by                          |
| -------------- | ---------------- | ------------------------------------ |
| `index.html`   | Landing page     | —                                    |
| `privacy.html` | Privacy policy   | App Store mandatory URL              |
| `terms.html`   | Terms of use     | App Store subscription mandatory URL |
| `support.html` | Contact + FAQ    | Linked from in-app Settings          |
| `404.html`     | Branded fallback | GitHub Pages serves on misses        |

A `methodology.html` page was deferred from v1: the construct, scoring, and limitations need more work before they hold up to public scrutiny. Will return in a later release.

Each page shares a manually-duplicated `<header class="site">` and `<footer class="fineprint">` block — no template engine. When the footer changes, change all 6 files.

## Notes for future-you

### `.nojekyll` is intentionally NOT in this repo

GitHub Pages runs Jekyll on every push by default. Jekyll mangles two things: files starting with `_` (it skips them) and content containing Liquid template syntax (`{{ }}`, `{% %}`). This site has neither, so Jekyll is a no-op here and we omit `.nojekyll`.

**Add an empty `.nojekyll` file to the repo root if you ever:**

- introduce a directory or file starting with `_` (e.g. `_redirects`, `_includes/`, `_data/`)
- write CSS or HTML containing literal `{{` or `{%` that you want served as-is (rare — JS template literals would do this)
- adopt a static site generator that emits files into `_site/` and you want to bypass Jekyll's processing of that
- want to skip Jekyll's per-deploy build time (a few seconds; not usually material)

The file is empty — `touch .nojekyll` in the repo root and commit. GitHub Pages picks it up automatically.
