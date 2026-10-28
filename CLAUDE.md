# CLAUDE.md – conventions for this repository

This repository collects standalone, work-in-progress interactive excursions tied to ongoing
research projects, built by Theodore P. Pavlic. It holds experimental, research-facing work
rather than polished explainers, and follows the structural conventions below. The live site is at
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

**Confine setup edits to the file's outer edges (the `<head>` and the back-link footer); leave the
body interior untouched.** These excursions are often authored or edited in a separate tool (such as
Claude Desktop) and then re-imported, so the body between the head and the footer is owned by that
tool. When you register, add, or set up an excursion here, restrict your changes to the head metadata
(title, description, OG/Twitter/GA tags) and the back-link footer with its embed script, and
do not restructure or restyle anything in between, so a later re-import of the app body does not have
to re-apply your interior edits. This applies to the setup/import path; when the user explicitly asks
you to change the body (for example a footer or layout review pass), that is fine. Otherwise, if the
interior seems to need a change, flag it and ask rather than editing it silently.

When a user asks to add an existing excursion to the index/README/CLAUDE.md, **always also audit
the excursion's HTML file itself** before finishing:

1. Check that `<head>` has a `<meta name="description">`, the full OG block, the Twitter/X card
   block, and the Google Analytics gtag block. If any are missing, add them (use the preview
   image dimensions from the actual file; aspect ratio should be close to 2:1 for Twitter).
2. Check that the bottom of `<body>` has the standard back-link `<footer>` and the
   embed `<script>` (footer hiding plus the host-resize message). If missing, add them from
   the `excursion-setup` skill's templates.

Each excursion carries full `<head>` metadata (OG, Twitter/X card, and the GA gtag block), a
preview image, and the back-link footer; keep these intact when editing.

---

## Head, header, and footer changes

**Before any change that touches or relates to an excursion's `<head>` metadata, its header
copyright, its back-link footer, or its embed script, invoke the `excursion-setup` skill and follow
it.** This includes maintenance fixes that only seem nearby: a restyle of `footer`, `a`, or body
padding rules, a font-stack or link-color change, a re-import of the body, or any edit to the
footer's `<script>`. The skill holds the templates and the pitfalls (rules leaking onto
`#back-link-footer`, the arrow glyph, link contrast, the embed script's resize details) that keep
these from breaking. Interior changes that cannot reach the head or footer do not need it.

The full checklist for adding a new excursion is in the same skill. Canvas LMS iframe code and
resize testing are in the `canvas-embed` skill.

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

## Tabs and deep links

Every widget with page-section tabs should make them externally linkable: a reader should be able
to right-click a tab and copy a link straight to it, and a link ending in `#id` should open on that
tab rather than the default one. Widgets in this repository are not built from one shared scaffold,
so each one wires its own tabs differently (a `switchTab`/`showTab`/`gotoTab` function, buttons keyed
by a `data-tab` string, a DOM id, or a numeric index) — read the widget's own tab code before
applying this, rather than assuming any one shape.

- **A tab control is an `<a href="#id">`, never a `<button>`.** Keep the existing click handler's
  logic, adding `e.preventDefault()` (for a handler wired through `addEventListener`) or
  `return false;` (for an inline `onclick`) so the browser never actually navigates. This includes
  any pill/tile picker used in place of a plain tab row, and any hand-written prose link that jumps
  to another tab. It does not extend to a narrow-screen `<select>` fallback, which stays a `<select>`
  and drives the same handler through its `change` event. Confirm no CSS needs to change before
  converting: the existing tab class almost always already carries its own border, background, and
  cursor, and the nav row is usually a flex container, so a flex item is block-boxed the same way
  whether the tag is `<a>` or `<button>` — but check this per widget rather than assuming it.
- **The tab-switching function sets the hash itself, with `history.replaceState`, never
  `pushState`**, so switching tabs never grows the back/forward history: a reader tapping through
  several tabs should not have to fight the back button that many times to leave the page.
- **A validity guard checks the hash against the real tab set before acting on it**, reading the
  same markup or state object the tab code already has, rather than a second list that could drift
  out of step.
- **If a tab is identified by its position (a numeric index) rather than a string key, hash a fixed
  slug for that tab, never the raw index.** A link built from the index breaks the moment a tab is
  inserted anywhere but the end: every later tab's index shifts, so a link someone already shared
  silently opens the wrong content instead of failing loudly. Keep the index for whatever internal
  wiring already depends on it, and add one small array mapping each position to a permanent name
  (`const TAB_SLUGS = ['overview', 'perpproj', ...]`) used only for the hash and for translating an
  incoming hash back to an index.
- **A `hashchange` listener re-activates a tab when the hash changes from outside the page** (an
  external link opened while the page is already loaded, or a back/forward step across one), by
  calling the same function the tab controls' own click handlers use.
- **At boot, read the hash once and switch to that tab if it names one other than the default**,
  after the page's own startup sequence has finished priming its default tab, so a widget whose
  default tab does lazy setup work inside its tab-switching function (a canvas sized on first visit,
  an animation started or stopped) still gets that setup when the incoming link points elsewhere.
- **A control that only changes a parameter — which system to load, which initial distribution to
  start from — is not a page tab**, even if it happens to carry a `tab`-sounding class name or
  visual style. Leave it as a plain button; this convention is for controls that switch between
  separate sections of the page.

The worked versions are in the sibling repository `asu-simulating-stochastic-systems`, whose
`THEME.md` documents the same convention for its own more uniform widget scaffold.

---

## Site structure

- Each excursion is a **self-contained, single-file HTML page** with all CSS and JS inlined
- The site is intended to deploy via **GitHub Pages** from the `main` branch (no build step)

## Commit messages

**A localized edit to one excursion has to make that excursion identifiable from the commit
message, and preferably from the subject line itself.** Normally that takes the form of a short
prefix – the excursion's tag, a colon, and a space – so a narrow summary is not stranded in
`git log --oneline` with no sign of where it landed:

```text
ytree: Rewrite the Scale range tooltip
v2v: Fix the margins on the narrow-screen layout
```

Tags are not enumerated anywhere and are not permanent, because excursions keep arriving. While a
directory holds a single excursion its name is the natural tag, trimmed where that reads better
(`rsf`, `v2v`, `ytree`, `social-cue`); once it holds several independent pieces, tag the excursion
instead, and let earlier commits keep the tags they were written with. Reuse whatever an excursion
has been tagged before, which `git log --oneline -- ytree_detrend/` will show, and keep the tag
short: the whole subject line should stay at 72 characters or fewer. This is not Conventional
Commits, as there is no `feat:`/`fix:` type and the tag names an excursion rather than a kind of
change.

The imported histories described under "Provenance and history" above already group each
excursion's original commits; the tag is what does the same for its later commits on the trunk.

**Omit the tag when the subject already says where the work is**, either because it names its
target ("Add mobile-friendliness rules to CLAUDE.md") or because it describes a sweep ("Bring every
back-link up to WCAG AA"). Such a commit still owes the reader its scope, but carries that scope in
the summary, where a tag would understate it. A commit spanning exactly two excursions can carry
both tags (`rsf, v2v: …`), though splitting it is usually better.

## Shared conventions

- **Accent color:** `#6b21a8` (deep purple) – used for the page title, the masthead rule, section-heading text and underlines, links, and the mobile nav button (contrast 8.7:1 on white, WCAG AAA), chosen to differentiate this site from the green Topic Visualizers. Color is concentrated at the page's structural anchors (title, dividers, headings, the callout edge); the **dense list chrome stays neutral gray** (borders `#e2e2e2` / `#ececec`) so the page does not read as monochromatic. Interactive backgrounds use faint purple tints `#f7f4fb` (hover) / `#eee3f8` (active). Hover-dark accent is `#581c87`.
- **Status pills and filter:** every excursion entry carries exactly one status, set in two places that must agree: the `<li data-status="wip|pub|arc">` and the pill next to the title. Three statuses: `.pill-wip` ("WIP", amber) = work in progress; `.pill-pub` ("Published", purple) = completed and carried through to publication, rendered as `<a class="pill pill-pub" href="<doi>">` linking the DOI; `.pill-arc` ("Archived", neutral gray) = set aside, no longer active, never published (a non-link `<span>`). Entry pills are indicators only. The **legend** inside the `.status-note` callout (a "Status:" label followed by the WIP, PUBLISHED, and ARCHIVED pills) doubles as the filter: hovering or focusing a legend pill shows a tooltip describing that status (`data-tip`), and clicking it toggles a `data-status` filter with deactivatable-radio behavior (click the active pill again, or the other, to switch or clear). The filter hides non-matching entries and any emptied section; untagged sections such as "More from Ted Pavlic" stay visible, and the `.filter-empty` note appears when a filter matches nothing (e.g. PUBLISHED while everything is WIP). The row title is a "stretched" link (`a.stretched`) covering the whole row; pills sit above it at `z-index:2`. Keep the three legend tooltips in sync with the categories.
- **"More from Ted Pavlic" heading:** rendered in neutral charcoal (`#1a1a1a`) via `#more h2`, not purple, to set the external-links section apart from the excursion sections.
- **Scope & attribution callout:** the `.status-note` box near the top of `index.html` carries the repeated copyright plus the standing statement that these excursions communicate the author's own ideas, do not disclose students' unpublished work, and are not released for reuse of the ideas without attribution, with the expectation that anyone building on an unpublished idea reach out first as a professional courtesy. Keep this statement current if the framing changes.
- **Copyright:** (c) Theodore P. Pavlic, MIT License (`LICENSE` file at repo root). The MIT License covers the page code, not the underlying research ideas.
- **fb:app_id:** `2385695445236853` – include in all OG blocks
- **GitHub Pages base URL:** `https://tpavlic.github.io/research_sandbox/`
- **Google Analytics:** GA4 measurement ID `G-1GJE3Z9182`. The landing page and every excursion carry the gtag block with this ID; include it in any new excursion's `<head>`
- **Git commits:** do **not** add a `Co-Authored-By: Claude` (or any AI co-author) trailer to commit messages
