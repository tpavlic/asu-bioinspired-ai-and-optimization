# CLAUDE.md — conventions for this repository

This repository hosts supplemental course visualizations for **CSE/IEE 598: Bio-Inspired AI &
Optimization** at Arizona State University, taught by Theodore P. Pavlic. The live site is at
<https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/>.

---

## Registering an existing visualization

When a user asks to add an existing demo to the index/README/CLAUDE.md, **always also audit
the demo's HTML file itself** before finishing:

1. Check that `<head>` has a `<meta name="description">`, the full OG block, and the Twitter/X
   card block. If any are missing, add them (use the preview image dimensions from the actual
   file; aspect ratio should be close to 2:1 for Twitter).
2. Check that the bottom of `<body>` has the standard back-link `<footer>` and the
   iframe-hiding `<script>`. If missing, add them.

Do this proactively — the user should not have to ask separately.

---

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

- Aspect ratio must be close to **2:1** (e.g. 1200×600, 2400×1200). Twitter silently drops
  images that deviate significantly, even if Facebook/Mastodon/Bluesky render them fine.
- File size must be **under 5 MB**.
- If the natural preview image is the wrong ratio, create a cropped/padded version for
  `twitter:image` while leaving `og:image` pointing at the full-resolution original.

**Use literal characters, not HTML entities, in `og:*` and `twitter:*` `content` attributes.**
Social-card scrapers read these values as plain text, not HTML, so they often do not decode
entities — a title like `Foo &amp; Bar` can surface verbatim as "Foo &amp; Bar". Worse, an
ampersand followed by a space (`Foo & Bar`) is not even a valid entity, so escaping is both
unnecessary and harmful here. Write the literal character instead: `og:title` and
`twitter:title` (and the matching `:description` tags) should contain `&`, not `&amp;`, and
likewise use literal `—`, `<`, `>`, `'`, etc. (This applies only to the social-card meta
`content` attributes; the human-visible `<title>` element and page body still follow normal
HTML escaping rules.)

### 2. Footer with back-link and iframe-hiding script

At the very bottom of `<body>`, before `</body>`, add:

```html
<footer id="course-nav-footer" style="margin-top:0;font-size:0.8rem;color:#78786A;">
  <div style="max-width:MAX_WIDTH;margin:0 auto;padding:0.75rem 0 0 1rem;">
    <a href="../" style="color:inherit;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'"><span style="font-family:sans-serif">&larr;</span> All course visualizations</a>
  </div>
</footer>
<script>
if (window.self !== window.top) { var f = document.getElementById('course-nav-footer'); if (f) { f.style.display = 'none'; } }
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

The `<script>` hides the footer when the page is embedded in a Canvas LMS iframe. Use
`getElementById('course-nav-footer')` rather than `querySelector('footer')` — some demos
have their own internal `<footer>` elements, and `querySelector` would match the first one
it finds instead of the back-link footer.

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

Current sections and their placeholder comments:

| Section heading | Placeholder comment |
| --- | --- |
| Genetic Algorithms | `<!-- Add more genetic algorithm demos here -->` |
| Evolution Strategies | `<!-- Add more evolution strategies demos here -->` |
| Evolutionary Programming &amp; Artificial Immune Systems | `<!-- Add more evolutionary programming and AIS demos here -->` |
| Physics-Inspired Methods | `<!-- Add more physics-inspired demos here -->` |
| Swarm Intelligence | `<!-- Add more swarm intelligence demos here -->` |
| Neural Networks | `<!-- Add more neural network demos here -->` |
| Cellular Automata | `<!-- Add more cellular automata demos here -->` |

To add a **new section**, copy the structure of an existing `<section class="demo-section">`
block and add a corresponding nav link in the `<nav>` at the top of the page.

### 4. Entry in `README.md`

Add a row to the appropriate table under `## Contents`:

```markdown
| [`my_demo/`](my_demo/) | Brief description matching the index entry |
```

---

## HiDPI `<canvas>` rendering

Any `<canvas>` drawing (plots, diagrams, scatter/loss charts, histograms) looks blurry on
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

When adding or reviewing a demo with canvas graphics, check that this dpr scaling is present.

## Site structure

- `index.html` — the root landing page; self-contained HTML (no Jekyll/build step)
- `README.md` — GitHub repo landing page; mirrors the index structure for repo visitors
- Each demo is a **self-contained, single-file HTML page** with all CSS and JS inlined
- Preview images live alongside their HTML file in the same subdirectory
- The site is deployed via **GitHub Pages** directly from the `main` branch (no build step)

## Current sections and demos

### Genetic Algorithms

- `shifting_balance_theory/sbt_four_peaks.html`
- `ideal_free_distribution/ifd_on_pond.html`
- `evolution_as_movement_in_drift_field/evolution_as_movement_in_drift_field.html` *(static figure)*
- `genetic_algorithms/ga_explorer.html`

### Evolution Strategies

- `evolution_strategies/es_explorer.html`
- `evolution_strategies/cmaes_explorer.html`

### Evolutionary Programming & Artificial Immune Systems

- `evolutionary_programming/evolprog_representations.html`
- `artificial_immune_systems/ais_explorer.html`

### Physics-Inspired Methods

- `monte_carlo/mc_explorer.html`
- `simulated_annealing/simulated_annealing_demo.html`
- `parallel_tempering/parallel_tempering.html`
- `softmax/softmax_temperature_explorer.html`
- `maxent/maxent_demo.html`
- `boltzmann_maxent/boltzmann_maxent_random_exchange.html`
- `boltzmann_maxent/beta_spacings.html`

### Swarm Intelligence

- `collective_motion/boids_explorer.html`
- `collective_motion/vicsek_explorer.html`
- `particle_swarm_optimization/pso_explorer.html`
- `bacterial_foraging_optimization/bfo_explorer.html` *(full BFO: 1-D and 2-D chemotaxis with cell-to-cell signaling, plus reproduction and elimination-dispersal)*
- `ant_colony_optimization/aco_explorer.html`
- `collective_behavior/ant_foraging_explorer.html`

### Neural Networks

- `single_layer_perceptron/slp_explainer.html`
- `radial_basis_function_nn/rbfnn_explorer.html`
- `multi_layer_perceptron/mlp_explorer.html`
- `cross_entropy/cross_entropy_explainer.html`
- `optimal_foraging_theory/mvt_explorer.html`
- `unsupervised_learning/autoencoder_explorer.html`
- `spiking_neural_networks/snn_explorer.html`
- `memristors/memristor_stdp_array.html`
- `hebbian_learning/hebbian_competitive_clustering.html`
- `recurrent_neural_networks/rnn_explorer.html`
- `reservoir_computing/esn_explorer.html`
- `root_mean_square/rms_explorer.html`
- `transformers/transformer_explorer.html`
- `transformers/toward_multimodal_AI.html`

### Cellular Automata

- `cellular_automata/eca_explorer.html`
- `cellular_automata/voter_model.html`

---

## Course unit structure

Use this to decide which section a new demo belongs in. Section titles deliberately match the
course unit vocabulary so students can orient themselves.

| Course unit | Topics | Index section |
| --- | --- | --- |
| Unit 1 | Genetic algorithms, evolutionary algorithms, genetic drift | Genetic Algorithms |
| Unit 2 | Evolution Strategies, CMA-ES, genetic/evolutionary programming, artificial immune systems | Evolution Strategies; Evolutionary Programming &amp; Artificial Immune Systems |
| Unit 3 | Multi-objective optimization, MOEA, Pareto ranking, niching | Genetic Algorithms |
| Unit 4 | Multi-modal optimization, diversity/niching methods, distributed/parallel GAs | Genetic Algorithms |
| Unit 5 | Simulated Annealing, MaxEnt, Gibbs/softmax, Metropolis–Hastings, MCMC | Physics-Inspired Methods |
| Unit 6 | Ant Colony Optimization, Bacterial Foraging, Particle Swarm Optimization | Swarm Intelligence |
| Unit 7 | Perceptrons, RNNs, Hebbian learning, STDP, spiking neural networks | Neural Networks |
| Unit 8 *(if time)* | Cellular automata | Cellular Automata |
| Unit 9 *(if time)* | Stochastic policies, multi-scale robotics | *(no section yet)* |

**Section title rationale:**

- *"Genetic Algorithms"* — covers Units 1, 3, 4: canonical GA, multi-objective (MOEA/Pareto),
  multi-modal optimization, niching, island model, and supporting conceptual foundations (fitness
  landscape, drift, diversity, Ideal Free Distribution).
- *"Evolution Strategies"* — covers the ES/CMA-ES strand of Unit 2: self-adaptive step sizes,
  correlated mutations, and covariance matrix adaptation.
- *"Evolutionary Programming & Artificial Immune Systems"* — covers the EP/AIS strand of Unit 2:
  non-standard representations (ASTs, linear GP, FSMs) and immune-inspired algorithms (NSA, CLONALG).
- *"Physics-Inspired Methods"* — matches Unit 5 phrasing exactly; covers both the metaheuristic
  side (SA, PT) and the probabilistic/information-theoretic side (MaxEnt, Gibbs/softmax), all of
  which share roots in statistical mechanics and the Boltzmann distribution.
- *"Swarm Intelligence"* — covers Unit 6: ACO, BFO (chemotaxis, communication, reproduction, elimination-dispersal), and PSO.
- *"Neural Networks"* — straightforward; covers Unit 7 content.

## Shared conventions

- **Accent color:** `#2e7d32` (dark green) — used in links and section headings
- **Copyright:** © Theodore P. Pavlic, MIT License (`LICENSE` file at repo root)
- **fb:app_id:** `2385695445236853` — include in all OG blocks
- **Google Analytics ID:** `G-LH9FR4R4WT` — include the two-line GA4 snippet in every `<head>`, after the Twitter/X card block and before `</head>`
- **GitHub Pages base URL:** `https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/`
- **YouTube channel:** <https://www.youtube.com/@TedPavlic> — linked from the index header
