# wp-updates

Self-hosted WordPress update server for VCNS Tech plugins, served
as a static site via GitHub Pages at
`https://vcns.github.io/wp-updates/`.

Each plugin's own repository builds a release ZIP and an `update.json`
manifest (see e.g. the `release.yml` workflow in the plugin's repo) and
publishes them here. The plugin itself polls its `update.json` on a timer
(via a `WPQ_Updater`-style class) to power WordPress's native update UI.

This repo does not contain any plugin source code — it is purely the static
hosting side.

## Structure

```
wp-updates/
  index.html            Landing page listing all published products
  products.json          Registry consumed by index.html
  <slug>/
    index.html            Per-product landing page
    update.json            Manifest WordPress polls for update info
    <slug>-latest.zip      Release artefact
  TEMPLATE/               Copy this to onboard a new product
    index.html              Edit placeholders in place
    update.json.example → update.json
```

Each product is fully self-contained in its own `<slug>/` folder, so any
number of products can be published side by side.

## Onboarding a new product

1. Copy `TEMPLATE/` to a new folder named after the plugin's slug, e.g.:
   ```
   cp -r TEMPLATE/ <slug>/
   mv <slug>/update.json.example <slug>/update.json
   ```
2. Fill in `<slug>/update.json` — `name`, `slug`, `version`, `download_url`,
   `last_updated`, and `sections.description`.
3. Fill in `<slug>/index.html` — replace the `REPLACE_WITH_*` placeholders.
4. Publish the release ZIP as `<slug>/<slug>-latest.zip`.
5. Add an entry to `products.json` at the repo root:
   ```json
   {
     "slug": "<slug>",
     "name": "Plugin Name",
     "description": "One-line description.",
     "homepage": "https://vcns.tech"
   }
   ```
6. Point the plugin's updater at
   `https://vcns.github.io/wp-updates/<slug>/update.json`.

## Updating an existing product

Overwrite `<slug>/update.json` and `<slug>/<slug>-latest.zip` with the new
release, bump `version` and `last_updated`, and update the version shown in
`<slug>/index.html`. `products.json` only needs a change if the name,
description, or homepage changed.
