# CLAUDE.md — conventions for this repository

This repository hosts supplemental course visualizations for **CSE/IEE 598: Bio-Inspired AI &
Optimization** at Arizona State University, taught by Theodore P. Pavlic. The live site is at
<https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/>.

---

## Widgets are course-neutral

**Never reference the course inside a widget.** Each widget is a general-purpose teaching tool
that any instructor anywhere should be able to find on the web and embed in their own class, so a
widget must not name this course, its catalog number ("CSE/IEE 598"), the university, the
instructor, or a semester — not in its visible text, its `<title>`, its `<head>` description or
OG/Twitter card text, or its preview image. Frame everything by topic ("particle swarm
optimization", "Hebbian learning"), never by course ("in this course", "for Bio-Inspired AI &
Optimization").

The course framing lives only in the site chrome around the widgets: `index.html`, `README.md`,
this file, and the shared back-link footer. The footer's "All course visualizations" label names
no particular course and stays as is; its embed script removes it when the widget is
embedded in an LMS page, so it appears only on direct visits, where a link back to the index is
intentional. URLs are exempt — `og:url`, `og:image`, and the GitHub Pages
base necessarily contain the repository name, and that is fine; the rule is about human-readable
text. When importing or reviewing a widget whose body already contains a course reference, treat
it like any other interior issue under the setup-edit rule below: flag it rather than silently
editing it.

---

## Registering an existing visualization

**Confine setup edits to the file's outer edges (the `<head>` and the back-link footer); leave the
body interior untouched.** These apps are often authored or edited in a separate tool (such as Claude
Desktop) and then re-imported, so the body between the head and the footer is owned by that tool.
When you register, add, or set up a demo here, restrict your changes to the head metadata (title,
description, OG/Twitter/GA tags) and the back-link footer with its embed script, and do not
restructure or restyle anything in between, so a later re-import of the app body does not have to
re-apply your interior edits. This applies to the setup/import path; when the user explicitly asks
you to change the body (for example a footer or layout review pass), that is fine. Otherwise, if the
interior seems to need a change, flag it and ask rather than editing it silently.

When a user asks to add an existing demo to the index/README, **always also audit
the demo's HTML file itself** before finishing:

1. Check that `<head>` has a `<meta name="description">`, the full OG block, and the Twitter/X
   card block. If any are missing, add them (use the preview image dimensions from the actual
   file; aspect ratio should be close to 2:1 for Twitter).
2. Check that the bottom of `<body>` has the standard back-link `<footer>` and the
   embed `<script>` (footer hiding plus the host-resize message). If missing, add them.

Do this proactively — the user should not have to ask separately.

---

## Widget head, footer, and embedding: use the `widget-setup` skill

Every widget carries a required `<head>` block (title, description, Open Graph, Twitter/X card, and
Google Analytics tags) and a required back-link footer with its embed script (footer hiding plus
the `lti.frameResize` height report). The full templates, the footer and copyright layout pitfalls,
the `index.html` and `README.md` entry formats, and the Canvas LMS embed code live in the project
skill at `.claude/skills/widget-setup/SKILL.md`.

**Before adding a new widget, registering an existing one, or changing any widget's `<head>`,
back-link footer, embed script, or copyright line, invoke the `widget-setup` skill and follow it.**
The same holds for writing Canvas iframe embed code or testing the resize. Edits confined to a
widget's interior (its body content, plots, and controls) do not need it.

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

## Site structure

- `index.html` — the root landing page; self-contained HTML (no Jekyll/build step)
- `README.md` — GitHub repo landing page; mirrors the index structure for repo visitors
- Each demo is a **self-contained, single-file HTML page** with all CSS and JS inlined
- Preview images live alongside their HTML file in the same subdirectory
- The site is deployed via **GitHub Pages** directly from the `main` branch (no build step)

## Commit messages

**A localized edit to one demo has to make that demo identifiable from the commit message, and
preferably from the subject line itself.** Normally that takes the form of a short prefix – the
demo's tag, a colon, and a space – so a narrow summary is not stranded in `git log --oneline`
with no sign of where it landed:

```text
boids: Rework the neighbor-radius control
eca: Fix the margins on the narrow-screen layout
```

Tags are not enumerated anywhere and are not permanent, because demos keep arriving. Several
directories already hold more than one – `cellular_automata/` carries the ECA explorer and the voter
model, and those are independent tools that happen to share a topic – so name the demo rather than
its directory wherever that is the case, and fall back to the directory name where it holds exactly
one (`softmax`, `memristors`, or a trimmed form such as `pso` for `particle_swarm_optimization/`).
Reuse whatever a demo has been tagged before, which `git log --oneline -- cellular_automata/` will
show, and keep the tag short: the whole subject line should stay at 72 characters or fewer. This is
not Conventional Commits, as there is no `feat:`/`fix:` type and the tag names a demo rather than a
kind of change.

**Omit the tag when the subject already says where the work is**, either because it names its
target ("Add mobile-friendliness rules to CLAUDE.md") or because it describes a sweep ("Standardize
the footer copyright across all demos"). Such a commit still owes the reader its scope, but carries
that scope in the summary, where a tag would understate it. A commit spanning exactly two demos can
carry both tags (`boids, vicsek: …`), though splitting it is usually better.

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
