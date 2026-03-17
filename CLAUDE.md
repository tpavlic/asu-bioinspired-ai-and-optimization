# CLAUDE.md — conventions for this repository

This repository hosts supplemental course visualizations for **CSE/IEE 598: Bio-Inspired AI &
Optimization** at Arizona State University, taught by Theodore P. Pavlic. The live site is at
<https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/>.

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
</head>
```

**Twitter/X image requirements** (stricter than other platforms):

- Aspect ratio must be close to **2:1** (e.g. 1200×600, 2400×1200). Twitter silently drops
  images that deviate significantly, even if Facebook/Mastodon/Bluesky render them fine.
- File size must be **under 5 MB**.
- If the natural preview image is the wrong ratio, create a cropped/padded version for
  `twitter:image` while leaving `og:image` pointing at the full-resolution original.

### 2. Footer with back-link and iframe-hiding script

At the very bottom of `<body>`, before `</body>`, add:

```html
<footer style="margin-top:1.5rem;padding-top:0.75rem;border-top:1px solid #e0e0e0;font-size:0.8rem;color:#888;">
  <a href="../" style="color:#2e7d32;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'">&larr; All course visualizations</a>
</footer>
<script>if (window.self !== window.top) { var f = document.querySelector('footer'); if (f) f.style.display = 'none'; }</script>
```

The `<script>` hides the footer when the page is embedded in a Canvas LMS iframe.

**Watch for body padding:** if the demo's `body` CSS has no `padding-bottom`, the footer will
sit flush against the viewport edge. Add `padding-bottom` to the body or `margin-bottom` to
the footer if needed.

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
| Intuition Pumps for Genetic Algorithms | `<!-- Add more GA intuition demos here -->` |
| Physics-Inspired Metaheuristics | `<!-- Add more physics-inspired demos here -->` |
| Neural Networks | `<!-- Add more neural network demos here -->` |

To add a **new section**, copy the structure of an existing `<section class="demo-section">`
block and add a corresponding nav link in the `<nav>` at the top of the page.

### 4. Entry in `README.md`

Add a row to the appropriate table under `## Contents`:

```markdown
| [`my_demo/`](my_demo/) | Brief description matching the index entry |
```

---

## Site structure

- `index.html` — the root landing page; self-contained HTML (no Jekyll/build step)
- `README.md` — GitHub repo landing page; mirrors the index structure for repo visitors
- Each demo is a **self-contained, single-file HTML page** with all CSS and JS inlined
- Preview images live alongside their HTML file in the same subdirectory
- The site is deployed via **GitHub Pages** directly from the `main` branch (no build step)

## Current sections and demos

### Intuition Pumps for Genetic Algorithms

- `shifting_balance_theory/sbt_four_peaks.html`
- `ideal_free_distribution/ifd_on_pond.html`
- `evolution_as_movement_in_drift_field/evolution_as_movement_in_drift_field.html` *(static figure)*

### Physics-Inspired Metaheuristics

- `simulated_annealing/simulated_annealing_demo.html`
- `parallel_tempering/parallel_tempering.html`
- `softmax/softmax_temperature_explorer.html`
- `maxent/maxent_demo.html`

### Neural Networks

- `radial_basis_function_nn/rbfnn_explorer.html`

## Shared conventions

- **Accent color:** `#2e7d32` (dark green) — used in links and section headings
- **Copyright:** © Theodore P. Pavlic, MIT License (`LICENSE` file at repo root)
- **fb:app_id:** `2385695445236853` — include in all OG blocks
- **GitHub Pages base URL:** `https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/`
- **YouTube channel:** <https://www.youtube.com/@TedPavlic> — linked from the index header
