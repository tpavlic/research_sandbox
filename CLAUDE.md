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

**Ampersands in the document head only:** use a literal `&`, not the `&amp;` entity, in the
`<title>` element and in the OG/Twitter/X metadata strings (the `og:title`, `twitter:title`, and
description `content` attributes); several card scrapers (Slack notably) read these as plain text
and display the literal `&amp;` rather than expanding it. A bare `&` followed by a space stays valid
HTML, so the entity is never needed there. This rule is scoped to the head: in the visible body
(headings, prose, citations) leave the `&amp;` entity in place, where it is conventional and in
some cases required (a bare `&` is only valid when it cannot be read as the start of an entity).

**Twitter/X image requirements:** aspect ratio close to **2:1**, file size under **5 MB**.

### 2. Footer with back-link and iframe-hiding script

At the very bottom of `<body>`, before `</body>`, add:

```html
<footer id="back-link-footer" style="max-width:CONTENT_MAX_WIDTH;margin:0 auto;padding:0 CONTENT_HPAD 1.5rem;">
  <div style="padding-top:0.75rem;border-top:1px solid #e0e0e0;font-size:0.8rem;color:#888;">
    <a href="../" style="color:#6b21a8;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'"><span style="font-family:sans-serif">&larr;</span> All excursions</a>
  </div>
</footer>
<script>
if (window.self !== window.top) { var f = document.getElementById('back-link-footer'); if (f) f.style.display = 'none'; }
</script>
```

Replace `CONTENT_MAX_WIDTH` and `CONTENT_HPAD` with the `max-width` and horizontal `padding` of
the page's main centered content container so the back-link aligns with the page content. The
script hides the footer when the page is embedded in a Canvas LMS iframe.

The back-link color is the site accent `#6b21a8` so it reads as one of the page's own links; keep
a literal hex (not a CSS variable) so the footer stays self-contained when the widget source is
re-pasted. If an excursion uses a different link color, match that instead.

**Watch for body padding:** if the page's `body` CSS has no `padding-bottom`, the footer sits
flush against the viewport edge. Add `padding-bottom` to the body, bump the footer's bottom
padding, or add `margin-bottom` if needed.

**Back-link arrow (`&larr;`) glyph varies by font fallback.** The page webfonts usually lack a
`←` glyph, so it falls back down the stack. A stack containing `system-ui`/`-apple-system`
renders a short, stubby `←` (San Francisco on macOS), whereas falling through to the generic
`sans-serif` gives a longer, nicer `←` (Helvetica/Arial). For a consistent long arrow, wrap just
the arrow in `<span style="font-family:sans-serif">&larr;</span>` (as in the template above) so
it never picks up `system-ui`.

**A generic `footer { … }` rule can leak onto `#back-link-footer`.** If a page styles its own
copyright `footer{}` (font-family, `text-align`, `border-top`, `margin-top`, padding), those
properties also land on the back-link footer, since both are `<footer>` elements — giving an
unwanted second horizontal rule, a mono font, or a large gap. Reset the offending properties on
`#back-link-footer` inline, or scope the rule to `footer:not(#back-link-footer)`.

**Link decoration (underline) consistency.** Within each page, the copyright/license "MIT
License" link (in the `.status-note` callout) and the back-link should share ONE underline
behavior; the default is **hover-underline** (no resting underline, no hover-bold, no hover
color-shift — the underline appears only on hover). Use a resting (always-on) underline only when
a link is the *same color* as its surrounding text so nothing else signals it is a link; better
still, give such links a distinct accent color and keep hover-underline. Choose each link's color
to be readable **and** distinct from adjacent text *in its own context* (an accent that reads on a
light background is often unreadable on a dark banner). Bring body/reference links into the same
behavior via the page's global `a{}` rule (`a{…;text-decoration:none} a:hover{text-decoration:underline}`)
so the whole page is consistent.

**At most one hard rule in the footer area.** The copyright block and the `#back-link-footer` can
each carry a `border-top`, and having both stacks two rules bracketing the copyright, which reads as
too much. Keep at most one. If the page body is built from panels with hard edges, no footer rule is
needed. If the body is borderless, a single rule above the copyright can help, mirroring the rule
under the lede at the top of the page, but then do not also put one on the back-link footer.

**When you remove a footer rule, drop the `padding-top` that paired with it.** The `#back-link-footer`
template pairs `border-top:1px solid #e0e0e0` with `padding-top:0.75rem` on its inner `<div>` (the
padding seats the link below the rule). Once you remove the border, reduce that padding (about
`0.35rem`) or the back-link floats with a phantom gap.

**Tighten the copyright-to-back-link gap from the content side, not with a negative margin on the
back-link footer.** The copyright is usually the last child inside the main content wrapper, so the
gap below it is the wrapper's `padding-bottom`, not the copyright's own margin. Reduce that wrapper
bottom padding (a positive value) rather than pulling the back-link up with a negative `margin-top`.

**Space under the back-link: do not stack `body` padding-bottom and the footer's own `padding-bottom`.**
If `body` has all-sides padding (e.g. `padding:18px`), a `#back-link-footer` that also sets a bottom
padding doubles the space, so that page's back-link sits visibly lower than sibling pages whose
footers have none. Pick one source (usually the body padding) and keep it consistent across pages.

**Header copyright in a colored banner.** For a right-aligned copyright/license in a colored header,
make it a flex child pushed right with `margin-left:auto`, styled like the muted subtitle. On a dark
or colored banner keep the license link `color:inherit` (the banner's light text) rather than the
page accent, which would be unreadable there, but keep hover-underline so its behavior matches the
footer link. To balance a two-line title, stack it on two lines by default (copyright on top, license
below) and collapse to one line as the header narrows, using a toggled `<br>` and a `·` separator
(two-line: the `<br>` shows and the separator is hidden; one-line: the `<br>` is hidden, the separator
shows, and `width:100%` drops the block onto its own line under the subtitle). Split it back to two
lines at a much narrower breakpoint. Match the header separator's spacing to the footer separator's
(e.g. `margin:0 .3em`) so both dots look the same.

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

## HiDPI `<canvas>` rendering

Any `<canvas>` drawing (plots, trajectories, phase portraits, spectra) looks blurry on
retina/HiDPI unless the backing store is scaled by `devicePixelRatio`. Draw in **logical** units
but size the backing store at `logical × dpr` and scale the context once:

```js
const dpr = window.devicePixelRatio || 1, W = 600, H = 175;   // logical size
cv.style.width = W + 'px';                                     // display size (height:auto keeps ratio)
cv.width = Math.round(W * dpr); cv.height = Math.round(H * dpr);
const ctx = cv.getContext('2d');
ctx.setTransform(dpr, 0, 0, dpr, 0, 0);                        // all drawing below uses logical W,H
```

- Draw with the logical `W`/`H`, **not** `cv.width`/`cv.height` (those are now the larger backing
  store — using them would double-scale).
- For a canvas redrawn every frame, guard the resize (`if (cv.width !== Math.round(W*dpr)) { … }`)
  so an incremental (non-clearing) draw loop is not wiped each frame.
- Mouse/click mapping that uses `getBoundingClientRect()` normalized to `[0,1]` is unaffected by
  the backing-store change, so interaction keeps working.

When adding or reviewing an excursion with canvas graphics, check that this dpr scaling is present.

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
