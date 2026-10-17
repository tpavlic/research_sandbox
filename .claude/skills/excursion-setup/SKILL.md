---
name: excursion-setup
description: Use when adding or registering an excursion, and before ANY change that touches or relates to an excursion's <head> metadata (title, description, OG/Twitter/X, GA gtag) or its header copyright, back-link footer, or embed script, including small maintenance fixes and restyles whose CSS could reach the header or footer. Holds the head and footer templates, the embed script, the known footer pitfalls, and the link-contrast audits.
---

# Adding or maintaining an excursion's head and footer

The root `CLAUDE.md` rule still applies: confine setup edits to the `<head>` and the back-link
footer, and leave the body interior alone unless asked.


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
not known in advance, and nothing in the payload needs protecting. See the `canvas-embed` skill
for the matching iframe code and how to test the resize.

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

### 5. A short tag for the excursion's commits

The commits that introduce an excursion usually name it in their summaries and so need no prefix,
but the narrow follow-up edits ("Fix the margins", "Reword the lede") do. Settle on a short tag for
the new excursion now (see "Commit messages" in `CLAUDE.md`) so those later commits have one to
reach for.
If the excursion joins a directory that already holds one, tag at the excursion level from here on:
the two are independent pieces sharing a topic, and the directory name no longer picks out either.
