# CLAUDE.md – conventions for this repository

This repository collects standalone, work-in-progress interactive excursions tied to ongoing
research projects, built by Theodore P. Pavlic. It is the experimental, research-facing sibling
of the more polished [Topic Visualizers](https://github.com/tpavlic/topic_visualizers)
repository, and it follows the same structural conventions. The live site is at
<https://tpavlic.github.io/research_sandbox/>.

The pieces collected here are called **excursions** (not "explorers" or "demos"): the word is
meant to signal intellectual experimentation rather than teaching. Use it consistently in prose.

---

## Prose conventions

This repository's human-readable prose (this file, `README.md`, and the visible text in
`index.html`) follows the author's house style in `~/.claude/CLAUDE.md`. The points that come up
most here:

- For an aside, prefer commas, parentheses, or a rephrase; where a dash genuinely reads best, use
  a **spaced en dash** ` – ` in plain text and Markdown, never a spaced em dash. In `index.html`,
  write it as the literal `–` character (or `&ndash;`), with a normal space on each side.
- Oxford comma; comma after an introductory adverb or clause; American spelling.
- "since" only for time; "while" only where it cannot be misread as temporal.

---

## Provenance and history

Each excursion began as its own standalone Git repository (hosted as a Gist). Those repositories
were rewritten with `git filter-repo --to-subdirectory-filter` so each one's single HTML file
lives in its own subdirectory, then merged into this repository with
`git merge --allow-unrelated-histories`. The trunk therefore fans back into one root commit per
excursion, with every original commit, author, and date preserved.

- `git log -- <excursion>/` shows a single excursion's full original history.
- `git log --graph --oneline` shows the four histories converging into the trunk.

When adding a brand-new excursion that has no prior history, just commit it directly on `main`.
When importing another existing repository, repeat the filter-repo + unrelated-history merge so
its history is preserved the same way.

---

## Registering an existing excursion

When a user asks to add an existing excursion to the index/README/CLAUDE.md, **always also audit
the excursion's HTML file itself** before finishing:

1. Check that `<head>` has a `<meta name="description">`, the full OG block, the Twitter/X card
   block, and the Google Analytics gtag block. If any are missing, add them (use the preview
   image dimensions from the actual file; aspect ratio should be close to 2:1 for Twitter).
2. Check that the bottom of `<body>` has the standard back-link `<footer>` and the
   iframe-hiding `<script>`. If missing, add them.

The four excursions imported at repository creation now carry full `<head>` metadata (OG, Twitter/X
card, and the GA gtag block), a preview image, and the back-link footer, alongside their status
pills and index/README entries. Keep these intact when editing those files.

---

## Adding a new excursion – full checklist

Each excursion lives in its own subdirectory:

```bash
my_excursion/
  my_excursion.html          # self-contained page (no build step)
  my_excursion-preview.png   # preview image for OG/Twitter cards
```

### 1. `<head>` metadata in `my_excursion.html`

Every page must have a proper HTML5 document structure (`<!DOCTYPE html>`, `<html lang="en">`,
`<head>`, `<body>`). Inside `<head>`, include the `<title>`, `<meta name="description">`, the
full Open Graph block, the Twitter/X card block, and the Google Analytics gtag block, filling in
the actual values. Use any page in [Topic Visualizers](https://github.com/tpavlic/topic_visualizers)
as the template.

**Ampersands in the page title and OG/Twitter strings:** use a literal `&`, not the `&amp;`
entity, in the `<title>` element and in the `og:title`, `twitter:title`, and description `content`
attributes; several card scrapers (Slack notably) read these as plain text and display the literal
`&amp;` rather than expanding it. A bare `&` followed by a space stays valid HTML, so the entity is
never needed here. This applies only to the title and metadata strings: in visible body content
(headings, prose, citations) the `&amp;` entity is conventional and fine, since the browser decodes
it and scrapers do not read it.

**Twitter/X image requirements:** aspect ratio close to **2:1**, file size under **5 MB**.

### 2. Footer with back-link and iframe-hiding script

At the very bottom of `<body>`, before `</body>`, add:

```html
<footer id="back-link-footer" style="max-width:CONTENT_MAX_WIDTH;margin:0 auto;padding:0 CONTENT_HPAD 1.5rem;">
  <div style="padding-top:0.75rem;border-top:1px solid #e0e0e0;font-size:0.8rem;color:#888;">
    <a href="../" style="color:#6b21a8;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'">&larr; All excursions</a>
  </div>
</footer>
<script>
if (window.self !== window.top) { var f = document.getElementById('back-link-footer'); if (f) f.style.display = 'none'; }
</script>
```

Replace `CONTENT_MAX_WIDTH` and `CONTENT_HPAD` with the `max-width` and horizontal `padding` of
the page's main centered content container so the back-link aligns with the page content. The
script hides the footer when the page is embedded in a Canvas LMS iframe.

### 3. Entry in `index.html`

Add a `<li data-status="...">` inside the appropriate `<section class="demo-section">`. Follow
the template comment above the excursion list for the exact markup: a stretched title link, a
status pill, and (once a preview image exists) the `<img class="demo-thumb">` as the first child
of `<div class="demo-row">`. Set both the `<li>`'s `data-status` and the matching pill.

### 4. Entry in `README.md`

Add a row to the appropriate table under `## Contents`.

### 5. Entry in `CLAUDE.md`

Update the **Current excursions** list below.

---

## Site structure

- `index.html` – the root landing page; self-contained HTML (no Jekyll/build step)
- `README.md` – GitHub repo landing page; mirrors the index structure
- Each excursion is a **self-contained, single-file HTML page** with all CSS and JS inlined
- Preview images live alongside their HTML file in the same subdirectory
- The site is intended to deploy via **GitHub Pages** from the `main` branch (no build step)

## Current excursions

### Collective Behavior

- **Y-Tree Trajectory Detrending & Spectral Analysis** – `ytree_detrend/ytree_detrend.html`
  Detrending and spectral analysis of collective-transport (weaver ant) trajectories moving loads vertically up trees, including forked trees.

- **RSF Explorer – Subnest Destination Choice** – `rsf_explorer/rsf_explorer.html`
  A resource selection function (RSF) framing of how distance interacts with other nest properties in the navigational choices of collective-transport groups and individual ants in polydomous colonies.

### Multi-Robot Systems

- **V2V Energy-Safe Swarm Explorer** – `v2v_swarm_explorer/v2v_swarm_explorer.html`
  Strategies for battery-safe vehicle-to-vehicle (V2V) energy sharing that extend the endurance and capabilities of multi-robot teams, with an exportable related-work review.

### Reinforcement Learning and Decision Models

- **Q-Learning Social Cue Model** – `social-cue-ql/social-cue-ql.html`
  A connection between conventional reinforcement learning (Q-learning) and a psychological social-cue assay, as a candidate model for the phenomena.

---

## Shared conventions

- **Accent color:** `#6b21a8` (deep purple) – used for the page title, the masthead rule, section-heading text and underlines, links, and the mobile nav button (contrast 8.7:1 on white, WCAG AAA), chosen to differentiate this site from the green Topic Visualizers. Color is concentrated at the page's structural anchors (title, dividers, headings, the callout edge); the **dense list chrome stays neutral gray** (borders `#e2e2e2` / `#ececec`) so the page does not read as monochromatic. Interactive backgrounds use faint purple tints `#f7f4fb` (hover) / `#eee3f8` (active). Hover-dark accent is `#581c87`.
- **Status pills and filter:** every excursion entry carries exactly one status, set in two places that must agree: the `<li data-status="wip|pub|arc">` and the pill next to the title. Three statuses: `.pill-wip` ("WIP", amber) = work in progress; `.pill-pub` ("Published", purple) = completed and carried through to publication, rendered as `<a class="pill pill-pub" href="<doi>">` linking the DOI; `.pill-arc` ("Archived", neutral gray) = set aside, no longer active, never published (a non-link `<span>`). Entry pills are indicators only. The **legend** inside the `.status-note` callout (a "Status:" label followed by the WIP, PUBLISHED, and ARCHIVED pills) doubles as the filter: hovering or focusing a legend pill shows a tooltip describing that status (`data-tip`), and clicking it toggles a `data-status` filter with deactivatable-radio behavior (click the active pill again, or the other, to switch or clear). The filter hides non-matching entries and any emptied section; untagged sections such as "More from Ted Pavlic" stay visible, and the `.filter-empty` note appears when a filter matches nothing (e.g. PUBLISHED while everything is WIP). The row title is a "stretched" link (`a.stretched`) covering the whole row; pills sit above it at `z-index:2`. Keep the three legend tooltips in sync with the categories.
- **"More from Ted Pavlic" heading:** rendered in neutral charcoal (`#1a1a1a`) via `#more h2`, not purple, to set the external-links section apart from the excursion sections.
- **Scope & attribution callout:** the `.status-note` box near the top of `index.html` carries the repeated copyright plus the standing statement that these excursions communicate the author's own ideas, do not disclose students' unpublished work, and are not released for reuse of the ideas without attribution, with the expectation that anyone building on an unpublished idea reach out first as a professional courtesy. Keep this statement current if the framing changes.
- **Copyright:** (c) Theodore P. Pavlic, MIT License (`LICENSE` file at repo root). The MIT License covers the page code, not the underlying research ideas.
- **fb:app_id:** `2385695445236853` – include in all OG blocks
- **GitHub Pages base URL:** `https://tpavlic.github.io/research_sandbox/`
- **Google Analytics:** GA4 measurement ID `G-1GJE3Z9182`. The landing page and all four excursions carry the gtag block with this ID; include it in any new excursion's `<head>`
- **Git commits:** do **not** add a `Co-Authored-By: Claude` (or any AI co-author) trailer to commit messages
