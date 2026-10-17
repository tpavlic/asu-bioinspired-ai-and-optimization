---
name: widget-setup
description: "Required templates and checklist for a widget's head metadata (title, description, Open Graph, Twitter/X card, Google Analytics), its back-link footer and embed script, footer and copyright layout pitfalls, the index.html and README entries, and Canvas LMS iframe embedding. Use before adding or registering a demo, and before changing any widget's head, back-link footer, embed script, or copyright line, or writing Canvas embed code."
---

# Widget setup: head, footer, index entries, and Canvas embedding

This is the setup checklist that the repository's `CLAUDE.md` points to. The always-loaded rules
there (course-neutral widgets, confining setup edits to the head and footer, HiDPI canvas, tab deep
links, commit messages, course unit structure, and shared conventions) still apply alongside it.

## Adding a new visualization — full checklist

Each visualization lives in its own subdirectory:

```bash
my_demo/
  my_demo.html          # self-contained page (no build step)
  my_demo-preview.png   # preview image for OG/Twitter cards
```

### 1. `<head>` metadata in `my_demo.html`

Every demo page must have a proper HTML5 document structure (`<!DOCTYPE html>`, `<html lang="en">`,
`<head>`, `<body>`) — do not leave the file as a bare fragment.

Inside `<head>`, include all of the following, filling in the actual values:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Demo Title — interactive explainer</title>
<meta name="description" content="One or two sentences describing the demo.">

<!-- Open Graph (Facebook, LinkedIn, Slack, iMessage, etc.) -->
<meta property="og:type" content="website">
<meta property="og:title" content="Demo Title — interactive explainer">
<meta property="og:description" content="One or two sentences describing the demo.">
<meta property="og:image" content="https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/my_demo/my_demo-preview.png">
<meta property="og:image:width" content="ACTUAL_WIDTH">
<meta property="og:image:height" content="ACTUAL_HEIGHT">
<meta property="og:url" content="https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/my_demo/my_demo.html">
<meta property="fb:app_id" content="2385695445236853">

<!-- Twitter/X card -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Demo Title — interactive explainer">
<meta name="twitter:description" content="One or two sentences describing the demo.">
<meta name="twitter:image" content="https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/my_demo/my_demo-preview.png">

<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-LH9FR4R4WT"></script>
<script>window.dataLayer=window.dataLayer||[];function gtag(){dataLayer.push(arguments);}gtag('js',new Date());gtag('config','G-LH9FR4R4WT');</script>
</head>
```

**Twitter/X image requirements** (stricter than other platforms):

- Aim for an aspect ratio near **2:1** (e.g. 1200×600, 2400×1200) and keep the important
  content centered. Twitter's own card code enforces only a 280×150 minimum, so a wider ratio
  is not rejected, but the card is rendered toward 2:1 and the edges may not survive.
- File size must be **under 5 MB**.
- Only if a ratio actually causes a problem, add a cropped or padded version for `twitter:image`
  while leaving `og:image` on the full-resolution original. Do not do this pre-emptively.

**Use literal characters, not HTML entities, in `og:*` and `twitter:*` `content` attributes.**
Social-card scrapers read these values as plain text, not HTML, so they often do not decode
entities — a title like `Foo &amp; Bar` can surface verbatim as "Foo &amp; Bar". Worse, an
ampersand followed by a space (`Foo & Bar`) is not even a valid entity, so escaping is both
unnecessary and harmful here. Write the literal character instead: `og:title` and
`twitter:title` (and the matching `:description` tags) should contain `&`, not `&amp;`, and
likewise use literal `—`, `<`, `>`, `'`, etc. (This applies only to the social-card meta
`content` attributes; the human-visible `<title>` element and page body still follow normal
HTML escaping rules.)

### 2. Footer with back-link and embed script

At the very bottom of `<body>`, before `</body>`, add:

```html
<footer id="course-nav-footer" style="margin-top:0;font-size:0.8rem;color:#78786A;">
  <div style="max-width:MAX_WIDTH;margin:0 auto;padding:0.75rem 0 0 1rem;">
    <a href="../" style="color:inherit;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'"><span style="font-family:sans-serif">&larr;</span> All course visualizations</a>
  </div>
</footer>
<script>
if (window.self !== window.top) {
  var f = document.getElementById('course-nav-footer'); if (f) { f.style.display = 'none'; }
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

Replace `MAX_WIDTH` with the page's primary content `max-width` (e.g. `860px`). The inner
`<div>` constrains the link to the same width as the page body so it aligns on wide screens.

**Back-link color: match the page's own link color — but mind how `color:inherit` works.**
Each demo has its own palette, so a fixed course-green clashes. The anchor keeps
`color:inherit`, but note that `inherit` takes the **footer element's** computed color, NOT
the page's `a { color: ... }` rule (an inline `color:inherit` on the anchor outranks the `a`
selector). So to make the back-link match the page's other links, set the **`#course-nav-footer`
element's** `color` to the page's link/accent color (often a CSS var such as `var(--accent)`)
and let the anchor inherit it. Check contrast against the footer's background: on dark-themed
pages whose links are white/light, use the nearest readable accent instead. Where the page has
no distinct link color, the muted default (`#78786A`) is fine. Keep `text-decoration:none`
plus the hover-underline. Never hardcode `#2e7d32`.

The `<script>` does two things when the page is embedded in an iframe, and nothing at all on a
direct visit, because everything sits behind the `window.self !== window.top` guard. First, it hides
the back-link footer. Use `getElementById('course-nav-footer')` rather than `querySelector('footer')`,
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

**Watch for body padding:** if the demo's `body` CSS has no `padding-bottom`, the footer will
sit flush against the viewport edge. Add `padding-bottom` to the body or `margin-bottom` to
the footer if needed.

**Footer/copyright layout — conventions and pitfalls** (from a full pass over every demo):

- **Footer copyright centered; back-link left-aligned** to the content's left edge, with only a
  small gap between them. Put a subtle footer copyright line (`© 2026 Theodore P. Pavlic ·
  MIT License`, MIT linked, `MIT&nbsp;License` non-breaking) just above the back-link.
- **A generic `footer { … }` rule leaks into `#course-nav-footer`.** Many demos style their own
  copyright `<footer>` with `font-family:var(--mono)`, `text-align:center|right`, and padding;
  since `#course-nav-footer` is also a `<footer>`, those cascade in and make the back-link look
  monospace / centered / oddly padded. Fix by overriding on the back-link's inline style
  (`font-family:inherit; text-align:left; padding:0`) or scoping the demo's rule to
  `footer:not(#course-nav-footer)`.
- **The back-link footer sits OUTSIDE the page's main content wrapper** (it is a direct
  `<body>` child placed after the wrapper). If the demo sets its `font-family` (or text color)
  on that wrapper — e.g. `.wrap { font-family: sans-serif }` — rather than on `body`, the
  back-link does not inherit it and falls back to the browser default (serif). Set
  `font-family` explicitly on `#course-nav-footer` to match the page, and align its left edge
  to the wrapper's content, not with extra padding.
- **Header copyright vs title baseline.** A header flex row with `align-items:flex-start` makes
  a small top-right copyright sit visibly *above* the large title's glyphs (different
  half-leading). Use `align-items:baseline`.
- **`html, body { padding: … }` applies the padding twice** (once to each element), doubling the
  top/side/bottom space. Put layout padding on `body` only.
- **Don't try to center the footer copyright on the organic tab-row width.** CSS can't reference
  another element's rendered width, hardcoded pixel guesses land off-center, and JS measurement
  is fragile (web fonts load late; tabs may collapse to a dropdown). Left-align it instead, or
  center it under a fixed content-column `max-width`.
- **Per-tab pages sharing one `<footer>`:** a bottom copyright shows a top rule only on the tab
  whose last element happens to have a border. Give the copyright `<footer>` its own
  `border-top` so the rule is consistent across tabs.
- **`#body { flex:1 }` under `body { min-height:100vh; display:flex; flex-direction:column }`**
  stretches the widget and strands the back-link at the very bottom on tall windows. Drop the
  `flex:1` so the footer sits directly under the content.
- **In-plot copyright** baked into `<canvas>`/`<svg>` `<text>` can still be linked by wrapping
  the `<text>` in an SVG `<a href="…" target="_blank" rel="noopener">` (keeps the same look).
- **Back-link arrow (`&larr;`) glyph varies by font fallback.** The page webfonts (Outfit,
  Inter, etc.) usually lack a `←` glyph, so it falls back down the stack. A stack containing
  `system-ui`/`-apple-system` renders a short, stubby `←` (San Francisco on macOS), whereas
  falling through to the generic `sans-serif` gives a longer, nicer `←` (Helvetica/Arial).
  For a consistent long arrow, wrap just the arrow in `<span style="font-family:sans-serif">&larr;</span>`
  (as in the template above) so it never picks up `system-ui`.
- **A generic `footer { … }` rule also leaks `border-top` and `margin-top` onto
  `#course-nav-footer`.** Beyond font/align, a demo's copyright `footer{}` styling can put a hard
  rule (`border-top`) and a large top margin on the back-link footer too (both are `<footer>`),
  giving an unwanted second horizontal rule and a big gap. Reset `border-top`/`margin-top`/`padding`
  on `#course-nav-footer` inline, or scope the rule to `footer:not(#course-nav-footer)`.
- **Reused `cr-br`/`cr-sep` wrap classes can carry the wrong default.** Some headers put the
  copyright in a narrow column and set `cr-br` to show (two-line) by default; a footer copyright
  that reuses those classes inherits the two-line default. Give the footer copyright its own
  scoped wrap rules (`.foo .cr-sep{display:inline}.foo .cr-br{display:none}` + a narrow media
  query) so it is one line with the dot by default and only reflows to two lines on narrow screens.
- **At most one hard rule in the footer area.** The copyright footer and the back-link footer can
  each carry a `border-top`, and having both stacks two rules bracketing the copyright, which reads
  as too much. Keep at most one. If the body is built from panels with hard edges, no footer rule is
  needed. If the body is borderless, a single rule above the copyright can help, mirroring the rule
  under the lede at the top of the page, but then do not also put one on the back-link footer.
- **When you remove a footer rule, drop the `padding-top` that paired with it.** A `border-top` is
  usually paired with a `padding-top` that seats the text below the rule (for example on the
  back-link footer's inner `<div>`). Once the rule is gone, reduce that padding (say `0.75rem` to
  `0.35rem`), or the element floats with a phantom gap.
- **Tighten the copyright-to-back-link gap from the content side, not with a negative margin on the
  back-link footer.** The copyright is usually the last child inside the main content wrapper, so the
  gap below it is the wrapper's `padding-bottom`, not the copyright's own margin. Reduce that wrapper
  bottom padding (a positive value) rather than pulling the back-link up with a negative `margin-top`.
- **Space under the back-link: do not stack `body` padding-bottom and the footer's own
  `padding-bottom`.** If `body` has all-sides padding (e.g. `padding:18px`), a back-link footer that
  also sets a bottom padding doubles the space, so that page's back-link sits visibly lower than
  sibling pages whose footers have none. Pick one source (usually the body padding) and keep it
  consistent across pages.
- **Header copyright in a colored banner.** For a right-aligned copyright/license in a colored
  header, make it a flex child pushed right with `margin-left:auto`, styled like the muted subtitle.
  On a dark or colored banner keep the license link `color:inherit` (the banner's light text) rather
  than the page accent, which would be unreadable there, but keep hover-underline so its behavior
  matches the footer link. To balance a two-line title, stack it on two lines by default (copyright
  on top, license below) and collapse to one line as the header narrows, using a toggled `<br>` and
  a `·` separator (two-line: the `<br>` shows and the separator is hidden; one-line: the `<br>` is
  hidden, the separator shows, and `width:100%` drops the block onto its own line under the
  subtitle). Split it back to two lines at a much narrower breakpoint. Match the header separator's
  spacing to the footer separator's (e.g. `margin:0 .3em`) so both dots look the same.

**Link decoration (underline) consistency.** Within each page, the copyright/license "MIT License"
links and the back-link should share ONE underline behavior; the default is **hover-underline**
(no resting underline, no hover-bold, no hover color-shift — the underline appears only on hover).
Use a resting (always-on) underline only when a link is the *same color* as its surrounding text
so nothing else signals it is a link; better still, give such links a distinct accent color and
keep hover-underline. Colors may differ by context and need not match across header/footer:

- Choose each link's color to be readable **and** distinct from adjacent text *in its own
  context*. An accent that reads on a light footer (orange, maroon, green) is often unreadable on
  a dark header banner — there, let the header "MIT License" link keep the banner's own text color
  (it is fine if it does not obviously look like a link).
- The back-link should match the page's link color: set the `#course-nav-footer` element's `color`
  to that accent (the anchor keeps `color:inherit`).
- Bring body/reference links into the same behavior (e.g. via the page's global `a{}` rule:
  `a{…;text-decoration:none} a:hover{text-decoration:underline}`) so the whole page is consistent.

### 3. Entry in `index.html`

Add a `<li>` inside the correct `<section class="demo-section">` in `index.html`. Sections
are delimited by HTML comments; each has a placeholder comment marking where to insert:

```html
<li>
  <a class="demo-row" href="my_demo/my_demo.html">
    <img class="demo-thumb"
         src="my_demo/my_demo-preview.png"
         alt="My Demo preview"
         width="120" height="90">
    <div class="demo-text">
      <h3>Demo Title — interactive explainer</h3>
      <p>One sentence description that conveys what the demo shows and why it matters for the course.</p>
    </div>
  </a>
</li>
```

To add a **new section**, copy the structure of an existing `<section class="demo-section">`
block and add a corresponding nav link in the `<nav>` at the top of the page.

### 4. Entry in `README.md`

Add a row to the appropriate table under `## Contents`:

```markdown
| [`my_demo/`](my_demo/) | Brief description matching the index entry |
```

### 5. A short tag for the demo's commits

The commits that introduce a demo usually name it in their summaries and so need no prefix, but the
narrow follow-up edits ("Fix the margins", "Reword the lede") do. Settle on a short tag for the new
demo now (see "Commit messages" in `CLAUDE.md`) so those later commits have one to reach for. Tag the demo
itself rather than its directory wherever the directory holds more than one.

## Embedding in Canvas LMS

Widgets are embedded in Canvas pages as plain iframes pasted through the Rich Content Editor's HTML
view. Canvas's sanitizer keeps `src`, `width`, `height`, `loading`, `allowfullscreen`, `frameborder`,
`scrolling`, `allow`, and `sandbox` on an iframe, plus the global `style`, `title`, `class`, and
`id`, and the `src` must be http or https. Do not add `sandbox` (it blocks the widget's scripts) or
`scrolling="no"` (it clips content wherever the resize message is not honored).

- **Titles stay the widget's own.** The `<title>` element and the iframe's `title` attribute both
  carry the widget's name exactly as it appears on the page. The Canvas page that hosts the
  iframe is titled in Canvas, and whatever prefix that page uses is a Canvas-side choice that
  never enters the widget or the embed code.
- **Embed code**, with the URL, title, and fallback height changed per widget:

  ```html
  <iframe src="https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/genetic_algorithms/ga_explorer.html"
          title="Genetic Algorithm Explorer"
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
