# CLAUDE.md — conventions for this repository

This repository collects standalone, work-in-progress interactive explorers tied to ongoing
research projects, built by Theodore P. Pavlic. It is the experimental, research-facing sibling
of the more polished [Topic Visualizers](https://github.com/tpavlic/topic_visualizers)
repository, and it follows the same structural conventions. The live site is at
<https://tpavlic.github.io/research_sandbox/>.

---

## Provenance and history

Each explorer began as its own standalone Git repository (hosted as a Gist). Those repositories
were rewritten with `git filter-repo --to-subdirectory-filter` so each one's single HTML file
lives in its own subdirectory, then merged into this repository with
`git merge --allow-unrelated-histories`. The trunk therefore fans back into one root commit per
explorer, with every original commit, author, and date preserved.

- `git log -- <explorer>/` shows a single explorer's full original history.
- `git log --graph --oneline` shows the four histories converging into the trunk.

When adding a brand-new explorer that has no prior history, just commit it directly on `main`.
When importing another existing repository, repeat the filter-repo + unrelated-history merge so
its history is preserved the same way.

---

## Registering an existing explorer

When a user asks to add an existing explorer to the index/README/CLAUDE.md, **always also audit
the explorer's HTML file itself** before finishing:

1. Check that `<head>` has a `<meta name="description">`, the full OG block, the Twitter/X card
   block, and the Google Analytics gtag block. If any are missing, add them (use the preview
   image dimensions from the actual file; aspect ratio should be close to 2:1 for Twitter).
2. Check that the bottom of `<body>` has the standard back-link `<footer>` and the
   iframe-hiding `<script>`. If missing, add them.

The four explorers imported at repository creation do **not** yet carry the OG/Twitter/GA
metadata, preview images, or the back-link footer — these are the main outstanding polish items.

---

## Adding a new explorer — full checklist

Each explorer lives in its own subdirectory:

```bash
my_explorer/
  my_explorer.html          # self-contained page (no build step)
  my_explorer-preview.png   # preview image for OG/Twitter cards
```

### 1. `<head>` metadata in `my_explorer.html`

Every page must have a proper HTML5 document structure (`<!DOCTYPE html>`, `<html lang="en">`,
`<head>`, `<body>`). Inside `<head>`, include the `<title>`, `<meta name="description">`, the
full Open Graph block, the Twitter/X card block, and the Google Analytics gtag block, filling in
the actual values. Use any page in [Topic Visualizers](https://github.com/tpavlic/topic_visualizers)
as the template.

**Ampersands in OG/Twitter `content` titles:** use a literal `&`, not the `&amp;` entity, in the
`og:title`, `twitter:title`, and description `content` attributes — several card scrapers (Slack
notably) display the literal `&amp;`. A bare `&` followed by a space stays valid HTML.

**Twitter/X image requirements:** aspect ratio close to **2:1**, file size under **5 MB**.

### 2. Footer with back-link and iframe-hiding script

At the very bottom of `<body>`, before `</body>`, add:

```html
<footer id="back-link-footer" style="max-width:CONTENT_MAX_WIDTH;margin:0 auto;padding:0 CONTENT_HPAD 1.5rem;">
  <div style="padding-top:0.75rem;border-top:1px solid #e0e0e0;font-size:0.8rem;color:#888;">
    <a href="../" style="color:#2e7d32;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'">&larr; All explorers</a>
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

Add a `<li>` inside the appropriate `<section class="demo-section">` in `index.html`. If no
suitable section exists, copy the structure of an existing section block. Once a preview image
exists, add the `<img class="demo-thumb">` as the first child of `<a class="demo-row">` (see the
commented template near the top of the explorer list).

### 4. Entry in `README.md`

Add a row to the appropriate table under `## Contents`.

### 5. Entry in `CLAUDE.md`

Update the **Current explorers** list below.

---

## Site structure

- `index.html` — the root landing page; self-contained HTML (no Jekyll/build step)
- `README.md` — GitHub repo landing page; mirrors the index structure
- Each explorer is a **self-contained, single-file HTML page** with all CSS and JS inlined
- Preview images live alongside their HTML file in the same subdirectory
- The site is intended to deploy via **GitHub Pages** from the `main` branch (no build step)

## Current explorers

### Collective Behavior

- **Y-Tree Trajectory Detrending & Spectral Analysis** — `ytree_detrend/ytree_detrend.html`
  Detrending and spectral analysis of collective-transport (weaver ant) trajectories moving loads vertically up trees, including forked trees.

- **RSF Explorer — Subnest Destination Choice** — `rsf_explorer/rsf_explorer.html`
  Field-hypothesis explorer for how distance interacts with other nest properties in the navigational choices of collective-transport groups and individual ants in polydomous colonies.

### Multi-Robot Systems

- **V2V Energy-Safe Swarm Explorer** — `v2v_swarm_explorer/v2v_swarm_explorer.html`
  Strategies for battery-safe vehicle-to-vehicle (V2V) energy sharing that extend the endurance and capabilities of multi-robot teams, with an exportable related-work review.

### Reinforcement Learning and Decision Models

- **Q-Learning Social Cue Model** — `social-cue-ql/social-cue-ql.html`
  A connection between conventional reinforcement learning (Q-learning) and a psychological social-cue assay, as a candidate model for the phenomena.

---

## Shared conventions

- **Accent color:** `#2e7d32` (dark green) — used in links and section headings
- **Copyright:** (c) Theodore P. Pavlic, MIT License (`LICENSE` file at repo root)
- **fb:app_id:** `2385695445236853` — include in all OG blocks
- **GitHub Pages base URL:** `https://tpavlic.github.io/research_sandbox/`
- **Google Analytics:** the landing page carries a placeholder gtag ID (`G-XXXXXXXXXX`); replace it with a real GA4 property ID before deploy, then add the same block to each explorer's `<head>`
- **Git commits:** do **not** add a `Co-Authored-By: Claude` (or any AI co-author) trailer to commit messages
