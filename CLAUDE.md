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
   embed `<script>` (footer hiding plus the host-resize message). If missing, add them.

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
the actual values. Use any existing page in this repository as the template.

**Ampersands in the document head only:** use a literal `&`, not the `&amp;` entity, in the
`<title>` element and in the OG/Twitter/X metadata strings (the `og:title`, `twitter:title`, and
description `content` attributes); several card scrapers (Slack notably) read these as plain text
and display the literal `&amp;` rather than expanding it. A bare `&` followed by a space stays valid
HTML, so the entity is never needed there. This rule is scoped to the head: in the visible body
(headings, prose, citations) leave the `&amp;` entity in place, where it is conventional and in
some cases required (a bare `&` is only valid when it cannot be read as the start of an entity).

**Twitter/X image guidance:** aim for an aspect ratio near **2:1** with the important content
centered, and keep the file under **5 MB**. Twitter's own card code enforces only a 280×150
minimum, so a wider ratio is not rejected, but the card is rendered toward 2:1.

### 2. Footer with back-link and embed script

At the very bottom of `<body>`, before `</body>`, add:

```html
<footer id="back-link-footer" style="max-width:CONTENT_MAX_WIDTH;margin:0 auto;padding:0 CONTENT_HPAD 1.5rem;">
  <div style="padding-top:0.75rem;border-top:1px solid #e0e0e0;font-size:0.8rem;color:#888;">
    <a href="../" style="color:#6b21a8;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'"><span style="font-family:sans-serif">&larr;</span> All excursions</a>
  </div>
</footer>
<script>
if (window.self !== window.top) {
  var f = document.getElementById('back-link-footer'); if (f) { f.style.display = 'none'; }
  // A min-height tied to the viewport (the "fill the window" idiom) would track the frame's
  // own height and keep the frame from ever shrinking, so neutralize such rules here, where
  // the frame is sized to the content. Cross-origin sheets (web fonts) throw and are skipped.
  (function () {
    function drop(rules) {
      for (var i = 0; i < rules.length; i++) {
        var r = rules[i];
        if (r.style && /vh\b/.test(r.style.minHeight)) { r.style.minHeight = '0'; }
        if (r.cssRules) { drop(r.cssRules); }
      }
    }
    for (var s = 0; s < document.styleSheets.length; s++) { try { drop(document.styleSheets[s].cssRules); } catch (e) {} }
  })();
  // Embedded in another page: report the content height to the host so the iframe can
  // grow and shrink with the active tab instead of scrolling inside itself. Canvas LMS
  // listens for this message on every page and resizes whichever iframe sent it. It is
  // never sent on a direct visit, and it never leaves the browser.
  (function () {
    var last = 0;
    function report(force) {
      var h = Math.ceil(document.documentElement.getBoundingClientRect().height);
      if (h > 0 && (force || h !== last)) { last = h; window.parent.postMessage({ subject: 'lti.frameResize', height: h }, '*'); }
    }
    if (window.ResizeObserver) { new ResizeObserver(function () { report(false); }).observe(document.documentElement); }
    window.addEventListener('load', function () { report(true); });
    // The host's listener may attach after this page has loaded (Canvas boots a large
    // bundle after its HTML arrives), so repeat the current height a few times.
    [500, 1500, 3000, 6000, 12000].forEach(function (ms) { setTimeout(function () { report(true); }, ms); });
    report(true);
  })();
}
</script>
```

Replace `CONTENT_MAX_WIDTH` and `CONTENT_HPAD` with the `max-width` and horizontal `padding` of
the page's main centered content container so the back-link aligns with the page content.

The `<script>` does two things when the page is embedded in an iframe, and nothing at all on a
direct visit, because everything sits behind the `window.self !== window.top` guard. First, it hides
the back-link footer. Use `getElementById('back-link-footer')` rather than `querySelector('footer')`,
because some demos have their own internal `<footer>` elements and `querySelector` would match the
first one it finds. Second, it reports the page's content height to the host with the
`lti.frameResize` message, which Canvas LMS listens for on every page (the listener matches the
sender against every iframe on the page, not only LTI launches, and applies no maximum), so the
iframe grows and shrinks with the active tab instead of scrolling inside itself. Three details keep
that working:

- **Measure the `<html>` element's box, not `scrollHeight`.** `scrollHeight` is floored at the
  viewport height, so once the host has grown the iframe for a tall tab it would never report a
  shorter one, and the iframe could never shrink back. The bounding rect of
  `document.documentElement` is content-driven and includes the body margins.
- **Viewport-tied `min-height` rules are neutralized when embedded.** The `min-height: 100vh`
  idiom that fills a window would track the frame's own height and keep it from ever shrinking, so
  the script walks the page's stylesheets and zeroes any `min-height` given in `vh` units, whether
  on `body` or on an inner wrapper. It touches only `min-height`, so a fixed-position overlay sized
  with `height: 100vh` keeps working, and fixed-position elements are out of flow anyway.
- **Let the `ResizeObserver` do the tracking.** It fires on tab switches, on results panels that
  appear after a run, on late-loading web fonts, and on reflow after the host's column changes
  width, so no per-widget hook into the tab code is needed. The `last` check keeps it from
  re-sending an unchanged height. The timed re-sends exist because Canvas attaches its listener
  only once its own bundle has booted, which on a wiki page can be well after a small static
  widget has finished loading; a height posted before that point is lost, and the observer will
  not repeat it unless the content moves. Test the resize against a host page whose listener
  attaches a few seconds late, not only against one that is listening from the start.

The message carries only a subject string and one integer, is delivered in-browser to the parent
window only, and makes no network request, so the `'*'` target origin is fine: the embedding site is
not known in advance, and nothing in the payload needs protecting. See "Embedding in Canvas LMS"
below for the matching iframe code and how to test the resize.

The back-link color is the site accent `#6b21a8` so it reads as one of the page's own links; keep
a literal hex (not a CSS variable) so the footer stays self-contained when the widget source is
re-pasted. If an excursion uses a different link color, match that instead.

**The literal hex is per-page, not one shared color.** Each excursion keeps its own link color; the
rule only governs how that color is *written*. Substitute the hex the page's own variable already
resolves to, so the rendered color is unchanged – today the four are `#2f6b4a` (`ytree_detrend`),
`#2d5a27` (`rsf_explorer`), `#2d5016` (`social-cue-ql`), and `#d97e3b` (`v2v_swarm_explorer`). The
reason for the literal is that the `<style>` block travels with the body from the authoring tool: a
re-import that renames the variable, rescopes it, or drops it turns `var(--green)` into the default
blue silently, with nothing to flag it. An inline literal cannot be broken by whatever the body
ships. This applies to the back-link alone; the license link and body links are page content and
should take their color from the page's own `a{}` rule.

**Watch for body padding:** if the page's `body` CSS has no `padding-bottom`, the footer sits
flush against the viewport edge. Add `padding-bottom` to the body, bump the footer's bottom
padding, or add `margin-bottom` if needed.

**Back-link arrow (`&larr;`) glyph varies by font fallback.** The page webfonts usually lack a
`←` glyph, so it falls back down the stack, and how far it falls decides the glyph. Measured at
`font-size:100px`, the same character renders 100.0px wide in generic `sans-serif`
(Helvetica/Arial), 90.6px in `system-ui`/`-apple-system` (the stubby San Francisco arrow on
macOS), and 60.2px in `IBM Plex Mono` – so a **mono** footer stack, which is common here, cramps
the arrow considerably more than `system-ui` does. For a consistent long arrow, wrap just the
arrow in `<span style="font-family:sans-serif">&larr;</span>` (as in the template above).

Add the span on every page, including ones whose footer stack already ends in `sans-serif` and so
looks correct without it. There it is a no-op today, and it is what keeps the arrow correct after a
re-import swaps the body's font stack, which is the same reasoning as the literal hex above. When
auditing, measure the glyph rather than assuming the `system-ui` case applies: of the four
excursions, only `ytree_detrend` actually had a `system-ui` footer stack.

**A generic `footer { … }` rule can leak onto `#back-link-footer`.** If a page styles its own
copyright `footer{}` (font-family, `text-align`, `border-top`, `margin-top`, padding), those
properties also land on the back-link footer, since both are `<footer>` elements — giving an
unwanted second horizontal rule, a mono font, or a large gap. Reset the offending properties on
`#back-link-footer` inline, or scope the rule to `footer:not(#back-link-footer)`.

**A `footer a { … }` rule is the same trap one level down, and is easier to miss.** Where the
generic `footer{}` rule restyles the back-link's *container*, a `footer a{}` rule lands on the
back-link anchor itself, so it collides with the very properties the template sets inline. The
damaging case is a resting `text-decoration:underline`, which contradicts the hover-underline
default below: the template's inline `text-decoration:none` does suppress it, but only because
inline styles outrank any selector, and neither rule read on its own reveals that the inline
declaration is the only thing preventing the underline. Scope it to `footer:not(#back-link-footer)
a` so the back-link sits outside the rule by construction rather than by specificity.
`rsf_explorer` had exactly this.

Checking this is a distinct audit step from reading the footer markup, because nothing in the
markup shows it. Confirm from the rendered page which rules actually match the back-link anchor:

```js
const a = document.querySelector('#back-link-footer a');
Array.from(document.styleSheets)
  .flatMap(s => { try { return [...s.cssRules]; } catch (e) { return []; } })
  .filter(r => r.selectorText && a.matches(r.selectorText))
  .map(r => r.selectorText + ' => ' + r.style.cssText);
```

The `try`/`catch` is required, not defensive padding: every page pulls webfonts from
`fonts.googleapis.com`, and reading `cssRules` on a cross-origin stylesheet throws `SecurityError`,
which without the guard aborts the whole sweep and reports nothing rather than reporting a clean
page. Returning `[]` skips only the font sheet, whose rules are never relevant here anyway.

Treat any footer-scoped hit as something to scope away. A page-wide `a{}` rule matching is expected
and fine, as the back-link is meant to override it with its own literal; on `ytree_detrend` the
only matches are `*` and `a`, which is what a clean page looks like.

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

**"Readable" means at least 4.5:1 against the background the link actually sits on.** Footer links
are normal-size text, not large text, so WCAG AA asks 4.5:1; measure it rather than trusting that a
color which looks fine is fine. Current back-link contrasts are 7.49:1 (`rsf_explorer`), 7.51:1
(`social-cue-ql`), 6.32:1 (`ytree_detrend`), and 4.55:1 (`v2v_swarm_explorer`).

A page whose accent is tuned for fills and headings is often far too light for text: `v2v`'s
`--accent` `#d97e3b` reached only 2.58:1, and its `--accent-dark` `#b86426` only 3.70:1. When that
happens, do not retune the accent itself, which would change every button and heading using it, and
do not darken only the back-link, which would leave it mismatched against the page's other links.
Add a **link-only token** beside the accent, derived by holding hue and saturation and dropping
lightness until it clears 4.5:1 – `--accent-link: #a35820` keeps `--accent`'s 25.6 deg and 67%
saturation at 38% lightness – then point every link-colored rule at it. Non-link uses of the accent
stay untouched, and the links stay recognizably the page's own color.

A link color change has two knock-on effects. A `:hover` rule that shifts to the *old* accent will
now **lighten** the link and undo the fix, so drop the color shift and keep the hover-underline.
The affected links also usually outnumber the footer's by a wide margin, since one citation or
reference rule can feed dozens. Enumerate them from the rendered page rather than by grepping – on
`v2v` this was 89 anchors, 87 of them from a single `.cite a` rule:

```js
const lum = (r, g, b) => { [r, g, b] = [r, g, b].map(c => { c /= 255;
  return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4); });
  return 0.2126 * r + 0.7152 * g + 0.0722 * b; };
const p = s => s.match(/\d+(\.\d+)?/g).map(Number);
const bgOf = e => { for (let n = e; n && n !== document.documentElement; n = n.parentElement) {
  const c = getComputedStyle(n).backgroundColor, q = p(c);
  if (!(q.length > 3 && q[3] === 0)) return c; } return 'rgb(255,255,255)'; };
const cr = (f, b) => { const F = p(f), B = p(b);
  const [hi, lo] = [lum(...F.slice(0, 3)), lum(...B.slice(0, 3))].sort((x, y) => y - x);
  return +((hi + 0.05) / (lo + 0.05)).toFixed(2); };
[...document.querySelectorAll('a')].map(a => {
  const c = getComputedStyle(a); return { color: c.color, size: c.fontSize,
    contrast: cr(c.color, bgOf(a)) }; });
```

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

### 6. A short tag for the excursion's commits

The commits that introduce an excursion usually name it in their summaries and so need no prefix,
but the narrow follow-up edits ("Fix the margins", "Reword the lede") do. Settle on a short tag for
the new excursion now (see "Commit messages" below) so those later commits have one to reach for.
If the excursion joins a directory that already holds one, tag at the excursion level from here on:
the two are independent pieces sharing a topic, and the directory name no longer picks out either.

---

## Embedding in Canvas LMS

Widgets are embedded in Canvas pages as plain iframes pasted through the Rich Content Editor's HTML
view. Canvas's sanitizer keeps `src`, `width`, `height`, `loading`, `allowfullscreen`, `frameborder`,
`scrolling`, `allow`, and `sandbox` on an iframe, plus the global `style`, `title`, `class`, and
`id`, and the `src` must be http or https. Do not add `sandbox` (it blocks the widget's scripts) or
`scrolling="no"` (it clips content wherever the resize message is not honored).

- **Embed code**, with the URL, title, and fallback height changed per widget:

  ```html
  <iframe src="https://tpavlic.github.io/research_sandbox/rsf_explorer/rsf_explorer.html"
          title="RSF Explorer"
          width="100%" height="FALLBACK_HEIGHT"
          style="width:100%;border:0;display:block;"
          loading="lazy" allowfullscreen></iframe>
  ```

- **The `height` attribute is the fallback** for any host that ignores the resize message, and it
  is also what shows for the moment before the first message lands, so a close guess avoids a
  visible jump at load. Set it to the tallest tab, measured with the footer hidden at about 780px
  (the narrow end of Canvas's desktop content column) and rounded up a little to absorb results
  that appear after a run.
- **Test the resize with a local host page**, not by eye: serve the repository over HTTP (Playwright
  refuses `file:` URLs), embed the widget in a page whose `message` listener applies
  `lti.frameResize` to the sending iframe, and check on every tab that the iframe's height equals
  the widget's content height and that the widget's `scrollHeight` does not exceed the iframe's
  `clientHeight`. Walk the tabs in both directions so a failure to shrink shows up, and repeat with
  a listener that attaches a few seconds late.

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

- `index.html` – the root landing page; self-contained HTML (no Jekyll/build step)
- `README.md` – GitHub repo landing page; mirrors the index structure
- Each excursion is a **self-contained, single-file HTML page** with all CSS and JS inlined
- Preview images live alongside their HTML file in the same subdirectory
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
