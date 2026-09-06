# Static GitHub Activity Graph Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the broken live Vercel activity-graph image in the GitHub profile README with a Dracula-themed static SVG generated daily by GitHub Actions and served from the repository's `output` branch.

**Architecture:** A GitHub Actions workflow on `main` generates `dist/activity-graph.svg` with `maurodesouza/github-readme-activity-graph-action@v1`, then publishes `dist` to an `output` branch using `crazy-max/ghaction-github-pages@v3.1.0`. The README is switched to the raw `output/activity-graph.svg` URL only after the first successful workflow run creates the asset, preventing a second broken-image window.

**Tech Stack:** GitHub Actions, `actions/checkout@v6`, `maurodesouza/github-readme-activity-graph-action@v1`, `crazy-max/ghaction-github-pages@v3.1.0`, Markdown

**Spec:** `docs/superpowers/specs/2026-09-05-static-activity-graph-design.md`

## Global Constraints

- Keep the activity graph theme `dracula`.
- Generate the file as `activity-graph.svg`.
- Publish generated output to branch `output`.
- Use only the repository-provided `GITHUB_TOKEN`; no PAT or custom secret.
- Workflow permission is limited to `contents: write`.
- Support both a daily schedule and `workflow_dispatch`.
- Do not change any README content except the existing activity-graph image/link line.
- Do not switch README to the static URL until `output/activity-graph.svg` exists.

---

### Task 1: Add the activity-graph workflow

**Files:**
- Create: `.github/workflows/activity-graph.yml`

**Interfaces:**
- Consumes: GitHub repository owner (`github.repository_owner`) and repository `GITHUB_TOKEN`.
- Produces: `dist/activity-graph.svg` during the job and publishes it as `activity-graph.svg` at the root of branch `output`.

- [ ] **Step 1: Create the workflow file**

Create `.github/workflows/activity-graph.yml` with exactly:

```yaml
name: Update Activity Graph

on:
  schedule:
    - cron: "15 6 * * *"
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    permissions:
      contents: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v6

      - name: Generate activity graph
        uses: maurodesouza/github-readme-activity-graph-action@v1
        with:
          username: ${{ github.repository_owner }}
          options: theme=dracula
          output_path: dist/activity-graph.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Publish activity graph to output branch
        uses: crazy-max/ghaction-github-pages@v3.1.0
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

The cron expression runs daily at 06:15 UTC (13:15 Thailand time).

- [ ] **Step 2: Validate the committed workflow text**

Read `.github/workflows/activity-graph.yml` back from `main` and verify all of these literal values are present:

```text
workflow_dispatch
cron: "15 6 * * *"
contents: write
maurodesouza/github-readme-activity-graph-action@v1
theme=dracula
output_path: dist/activity-graph.svg
target_branch: output
build_dir: dist
```

Expected: every value is present exactly once in the intended workflow section.

- [ ] **Step 3: Commit**

Commit message:

```text
feat: add static activity graph workflow
```

---

### Task 2: Verify the first generated SVG before changing README

**Files:**
- Read: `output:activity-graph.svg`
- Read: GitHub Actions run/job metadata if the first run fails

**Interfaces:**
- Consumes: workflow created in Task 1.
- Produces: confirmed readable `https://raw.githubusercontent.com/dui-w-moonrin/dui-w-moonrin/output/activity-graph.svg`.

- [ ] **Step 1: Start or await the first workflow run**

Preferred: use the repository Actions UI and run `Update Activity Graph` with **Run workflow** once. If no manual dispatch mechanism is available to the executor, await the next daily schedule at 06:15 UTC.

Expected: workflow run starts on `main`.

- [ ] **Step 2: Verify the output branch asset exists**

Read:

```text
https://raw.githubusercontent.com/dui-w-moonrin/dui-w-moonrin/output/activity-graph.svg
```

Expected: response is SVG text beginning with an `<svg` element rather than an HTTP error or HTML error page.

- [ ] **Step 3: Diagnose before proceeding if generation failed**

If the output asset does not exist, inspect the workflow run jobs and failing step logs. Do not modify README until the workflow has succeeded and the raw SVG is readable.

Expected: either the SVG is confirmed or the task stops with the concrete failing workflow step and error message.

---

### Task 3: Switch the profile README to the static asset

**Files:**
- Modify: `README.md` — only the existing activity-graph Markdown line near the top of the file

**Interfaces:**
- Consumes: verified `output/activity-graph.svg` from Task 2.
- Produces: profile README that renders the repository-hosted static activity graph.

- [ ] **Step 1: Fetch the current README immediately before editing**

Confirm the current graph line is still:

```md
[![Ashutosh's github activity graph](https://github-readme-activity-graph.vercel.app/graph?username=dui-w-moonrin&theme=dracula)](https://github.com/ashutosh00710/github-readme-activity-graph)
```

Expected: exact line found. If it has changed since the design was approved, stop and review the new line rather than overwriting unrelated edits.

- [ ] **Step 2: Replace only the image source URL**

Replace the line above with:

```md
[![Ashutosh's github activity graph](https://raw.githubusercontent.com/dui-w-moonrin/dui-w-moonrin/output/activity-graph.svg)](https://github.com/ashutosh00710/github-readme-activity-graph)
```

Do not alter the alt text, click-through attribution link, or any other README line.

- [ ] **Step 3: Verify the README diff is one-line-only**

Compare the README before and after the edit.

Expected diff:

```diff
-[![Ashutosh's github activity graph](https://github-readme-activity-graph.vercel.app/graph?username=dui-w-moonrin&theme=dracula)](https://github.com/ashutosh00710/github-readme-activity-graph)
+[![Ashutosh's github activity graph](https://raw.githubusercontent.com/dui-w-moonrin/dui-w-moonrin/output/activity-graph.svg)](https://github.com/ashutosh00710/github-readme-activity-graph)
```

- [ ] **Step 4: Commit**

Commit message:

```text
fix: serve activity graph from generated SVG
```

---

### Task 4: Final verification

**Files:**
- Read: `.github/workflows/activity-graph.yml`
- Read: `README.md`
- Read: branch `output` file `activity-graph.svg`

**Interfaces:**
- Consumes: completed workflow and README changes.
- Produces: evidence that the profile no longer depends on the broken Vercel deployment at page-view time.

- [ ] **Step 1: Re-read all three deployed artifacts**

Verify:

```text
main:.github/workflows/activity-graph.yml
main:README.md
output:activity-graph.svg
```

Expected: workflow exists, README references the raw output URL, and output SVG is readable.

- [ ] **Step 2: Verify the old runtime dependency is gone from README**

Search `README.md` for:

```text
github-readme-activity-graph.vercel.app
```

Expected: zero matches.

- [ ] **Step 3: Verify scope discipline**

Compare the final `README.md` against the version from before Task 3.

Expected: exactly one README line changed; all portfolio sections, project links, other widgets, images, and text remain unchanged.
