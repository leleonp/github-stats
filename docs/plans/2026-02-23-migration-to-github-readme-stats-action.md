# Plan: Migration to GitHub Readme Stats Action

Migrate the current custom Python-based stats generation to the community-maintained `readme-tools/github-readme-stats-action`. This will resolve hosting/maintenance issues and provide more reliable stats.

## Strategy

1.  **Update GitHub Workflow**: Replace the Python generation logic in `.github/workflows/main.yml` with the `readme-tools/github-readme-stats-action` to generate stats cards for both light and dark modes.
2.  **Generate New Files**: Use the action to create separate SVG files for dark and light themes (e.g., `generated/overview-dark.svg`, `generated/overview-light.svg`, etc.).
3.  **Update README.md**: Update the image display logic in `README.md` to point to the new files using the current fragment-based theme switching OR the modern `<picture>` tag.
4.  **Cleanup**: Delete the now redundant Python scripts, templates, and requirements.

## Detailed Steps

### 1. Update Workflow (`.github/workflows/main.yml`)

Modify the `build` job:
- Change `uses: actions/checkout@v3` to `@v4`.
- Remove `Set up Python`, `Install requirements`, and `Generate images`.
- Add steps for `readme-tools/github-readme-stats-action` for:
  - Stats (Dark) -> `generated/overview-dark.svg`
  - Stats (Light) -> `generated/overview-light.svg`
  - Top Languages (Dark) -> `generated/languages-dark.svg`
  - Top Languages (Light) -> `generated/languages-light.svg`
- Update the `Commit to the repo` step to add the new `generated/*.svg` files and remove old files if needed.

### 2. Update README.md

Modify the display logic:
```html
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/leleonp/github-stats/master/generated/overview-dark.svg">
  <img alt="Stats" src="https://raw.githubusercontent.com/leleonp/github-stats/master/generated/overview-light.svg">
</picture>
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/leleonp/github-stats/master/generated/languages-dark.svg">
  <img alt="Languages" src="https://raw.githubusercontent.com/leleonp/github-stats/master/generated/languages-light.svg">
</picture>
```
(This is the modern way, but I can keep the existing one with two files.)

### 3. Cleanup

Delete:
- `github_stats.py`
- `generate_images.py`
- `requirements.txt`
- `templates/` (directory)

## Verification

1.  Trigger the workflow via `workflow_dispatch` (if I could, but I'll assume it works).
2.  Check the generated SVGs in the repo.
3.  Verify the README displays correctly.
