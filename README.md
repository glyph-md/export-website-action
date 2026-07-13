# export-website-action

Export a folder of markdown files as a linked static website using [Glyph](https://github.com/hamidfzm/glyph), straight from GitHub Actions.

The export renders every markdown file with Glyph's viewer pipeline (GitHub Flavored Markdown, math, alerts, syntax highlighting, Mermaid diagrams as inline SVG), resolves `[[wikilinks]]` and relative links between pages, copies referenced images, and emits a shared stylesheet, cross-page navigation, and an `index.html` (your root `README.md`, or a generated page list).

Under the hood it runs `glyph <workspace> --export-website <output>` headless under `xvfb`, so it needs a Linux runner. Requires Glyph v0.16.0 or newer (the default installs the latest release).

## Usage

```yaml
- uses: glyph-md/export-website-action@v1
  with:
    workspace: docs
    output: site
```

### Inputs

| Input | Default | Description |
| --- | --- | --- |
| `workspace` | (required) | Path to the markdown workspace folder to export |
| `output` | `site` | Directory the generated site is written to |
| `version` | `latest` | Glyph release to install, e.g. `v0.16.0` |
| `timeout-seconds` | `600` | Fail the export if it has not finished in time |

### Outputs

| Output | Description |
| --- | --- |
| `path` | The output directory containing the generated site |

## Publish to GitHub Pages

A complete workflow that publishes your notes on every push to `main`:

```yaml
name: Publish notes

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  publish:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v5

      - uses: glyph-md/export-website-action@v1
        with:
          workspace: .
          output: site

      - uses: actions/upload-pages-artifact@v4
        with:
          path: site

      - id: deployment
        uses: actions/deploy-pages@v4
```

Enable Pages in the repo settings (Build and deployment: GitHub Actions) and every push renders your workspace to a browsable site.

## License

[MIT](LICENSE)
