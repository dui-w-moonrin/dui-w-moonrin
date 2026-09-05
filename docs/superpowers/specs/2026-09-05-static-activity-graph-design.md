# Static GitHub Activity Graph Design

## Goal
Replace the broken runtime dependency on `github-readme-activity-graph.vercel.app` with a GitHub Actions workflow that generates and publishes a static activity graph SVG for the profile README.

## Current State
The profile README embeds the activity graph directly from:

`https://github-readme-activity-graph.vercel.app/graph?username=dui-w-moonrin&theme=dracula`

The upstream deployment is currently unavailable, so GitHub renders only the image alt text.

## Chosen Approach
Use `maurodesouza/github-readme-activity-graph-action` in GitHub Actions to generate the graph on a schedule and publish the generated SVG to an `output` branch. The profile README will render the SVG from that branch instead of requesting the Vercel service at page-view time.

## Workflow
Create `.github/workflows/activity-graph.yml` with:

- `workflow_dispatch` for manual runs.
- A daily `schedule` trigger.
- `contents: write` permission so the workflow can publish generated output.
- Checkout of the profile repository.
- Activity graph generation for GitHub user `dui-w-moonrin` using the `dracula` theme.
- Publication of the generated asset to an `output` branch.

The generated graph filename will be `activity-graph.svg`.

## README Change
Replace the current Vercel-backed Markdown image with an image sourced from the repository's generated `output` branch:

`https://raw.githubusercontent.com/dui-w-moonrin/dui-w-moonrin/output/activity-graph.svg`

The image remains clickable and links to the upstream activity-graph project for attribution/reference.

## Data Flow
1. GitHub Actions starts from either the daily schedule or manual dispatch.
2. The activity graph action reads public GitHub contribution data for `dui-w-moonrin`.
3. The action generates `activity-graph.svg` using the Dracula theme.
4. The workflow publishes the SVG to the `output` branch.
5. The profile README displays the SVG through `raw.githubusercontent.com`.

## Reliability
The profile page will no longer depend on the upstream Vercel deployment during each page load. If a scheduled run fails, the last successfully generated SVG remains available on the `output` branch, so the README continues to display the previous graph rather than a broken image.

## Security and Permissions
Use the repository-provided `GITHUB_TOKEN`; no personal access token or custom secret is required. Workflow permissions are limited to `contents: write`, which is needed to publish the generated SVG branch.

## Scope
Only these profile-repository concerns are in scope:

- Add the activity graph workflow.
- Generate and publish `activity-graph.svg`.
- Replace the broken graph URL in `README.md`.

No other README widgets, portfolio sections, project links, or repository content will be changed.

## Verification
Implementation is complete when all of the following are true:

1. The workflow file is valid GitHub Actions YAML.
2. The workflow can be started manually.
3. A successful run creates or updates the `output` branch with `activity-graph.svg`.
4. The raw SVG URL returns a valid graph image.
5. The profile README displays the graph instead of the current broken alt-text fallback.
6. Existing README content outside the activity graph line remains unchanged.
