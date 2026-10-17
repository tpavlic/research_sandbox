---
name: canvas-embed
description: Use when embedding an excursion in a Canvas LMS page, writing its iframe code, choosing the fallback height, or testing the lti.frameResize iframe resizing that each excursion's footer script performs.
---

# Embedding in Canvas LMS

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
