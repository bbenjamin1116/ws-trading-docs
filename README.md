# ws-trading-docs

User documentation for **WSTerminal** — the FVG Pyramid Futures Engine by W.S. Trading Co.

Lives at <https://docs.ws-trading.co>.

## Local development

```bash
pip install -r requirements.txt
mkdocs serve
```

Opens a live-reloading preview at <http://127.0.0.1:8000>.

## Deploy

Deployed automatically by Cloudflare Pages on every push to `master`.

- **Build command:** `pip install -r requirements.txt && mkdocs build`
- **Build output directory:** `site/`
- **Custom domain:** `docs.ws-trading.co`

## Adding a new page

1. Create a Markdown file under `docs/` (in an existing subdir or a new one).
2. Add it to the `nav:` block in `mkdocs.yml`.
3. Commit + push. Cloudflare Pages rebuilds in ~30 seconds.

## Editing existing pages

Each rendered page has an "Edit this page" pencil icon in the top right that
links straight to the GitHub editor for the source file.

## Stack

- [MkDocs](https://www.mkdocs.org/) — Markdown → static site generator
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) — theme + extra features
- Hosted on Cloudflare Pages
