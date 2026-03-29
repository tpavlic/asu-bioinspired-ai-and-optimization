# Bio-Inspired AI & Optimization – Course Visualizations

Supplemental web visualizations for **CSE/IEE 598: Bio-Inspired AI & Optimization** at
[Arizona State University](https://www.asu.edu/), taught by
[Theodore P. Pavlic](https://search.asu.edu/profile/1995237)
(Associate Professor, [SCAI](https://scai.engineering.asu.edu/) &
[SOLS](https://sols.asu.edu/)).

**Live site:** <https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/>

The course surveys algorithms for optimization and multi-agent control inspired by biological
and physical systems – genetic algorithms, simulated annealing, swarm intelligence, neural
computation, and more. These visualizations are designed to build intuition for the concepts
behind those algorithms and to be usable both standalone and embedded in Canvas LMS pages
as iframes.

---

## Contents

### Genetic Algorithms

| Directory | Description |
| --- | --- |
| [`shifting_balance_theory/`](shifting_balance_theory/) | Interactive walkthrough of Sewall Wright's Shifting Balance Theory: genetic drift, within-deme selection, and interdemic migration |
| [`ideal_free_distribution/`](ideal_free_distribution/) | Interactive visualization of the Ideal Free Distribution, motivating fitness sharing in multi-modal GAs |
| [`evolution_as_movement_in_drift_field/`](evolution_as_movement_in_drift_field/) | Graphical explainer of selection, drift, and mutation as parallel forces pushing a population toward fixation |
| [`genetic_algorithms/ga_explorer.html`](genetic_algorithms/ga_explorer.html) | Interactive GA explorer: 1-D multimodal landscape with niching, island model / distributed GA, 2-D optimization zoo, and two-objective MOEA with Pareto ranking |

### Evolution Strategies

| Directory | Description |
| --- | --- |
| [`evolution_strategies/es_explorer.html`](evolution_strategies/es_explorer.html) | Interactive (μ,λ)- or (μ+λ)-ES explorer: per-individual step-size self-adaptation on a 1-D multimodal landscape, with optional correlated mutations |
| [`evolution_strategies/cmaes_explorer.html`](evolution_strategies/cmaes_explorer.html) | Interactive CMA-ES explorer: watch the algorithm adapt its mean, step size (CSA), and full covariance matrix (rank-1/rank-μ updates) on a 2-D landscape in real time |

### Evolutionary Programming & Artificial Immune Systems

| Directory | Description |
| --- | --- |
| [`evolutionary_programming/evolprog_representations.html`](evolutionary_programming/evolprog_representations.html) | Side-by-side explorer for three EP representation families — Abstract Syntax Trees, Linear Genetic Programs, and Finite-State Machines — with interactive variation operators and a cross-representation comparison |
| [`artificial_immune_systems/ais_explorer.html`](artificial_immune_systems/ais_explorer.html) | Interactive explorer for Negative Selection (NSA) and CLONALG: train detectors on self-data to flag anomalies, and watch clonal selection and affinity maturation build a pattern-recognition repertoire |

### Physics-Inspired Methods

| Directory | Description |
| --- | --- |
| [`simulated_annealing/`](simulated_annealing/) | Interactive explainer for Simulated Annealing: temperature schedules, neighbor selection, and probabilistic acceptance on an energy landscape |
| [`parallel_tempering/`](parallel_tempering/) | Interactive explainer for Parallel Tempering / REMC: fixed-temperature SA replicas at different temperatures with periodic configuration swaps |
| [`softmax/`](softmax/) | Interactive explorer for the softmax (Gibbs) distribution: how temperature interpolates between uniform exploration and greedy exploitation, connecting MaxEnt methods to SA and reinforcement learning |
| [`maxent/`](maxent/) | Interactive explainer for the Maximum Entropy principle: how MaxEnt selects the least-assumptive distribution consistent with known constraints, deriving the Gibbs/softmax distribution |
| [`boltzmann_maxent/`](boltzmann_maxent/) | Interactive simulation of N agents trading random amounts of a conserved quantity — any initial distribution relaxes to the Boltzmann (exponential) equilibrium |
| [`boltzmann_maxent/beta_spacings.html`](boltzmann_maxent/beta_spacings.html) | Interactive Beta(α,β) distribution explorer: adjust α and β, draw N samples, and watch how order statistics and spacings connect uniform random variables to the Beta distribution |

### Swarm Intelligence

| Directory | Description |
| --- | --- |
| [`bacterial_foraging_optimization/bfo_explorer.html`](bacterial_foraging_optimization/bfo_explorer.html) | Interactive explorer for the complete BFO algorithm (Passino 2002): cell-to-cell signaling and tumble-swim chemotaxis in 1-D and 2-D, plus the full nested loop with reproduction and elimination-dispersal |
| [`particle_swarm_optimization/pso_explorer.html`](particle_swarm_optimization/pso_explorer.html) | Interactive explorer for PSO: step through the velocity-update rule with inertia, cognitive, and social components, then run a live swarm on a 2-D landscape |

### Neural Networks

| Directory | Description |
| --- | --- |
| [`radial_basis_function_nn/`](radial_basis_function_nn/) | Interactive explorer for RBF neural networks: centers, widths, weights, and the kernel-trick connection to SVMs |

### Cellular Automata

| Directory | Description |
| --- | --- |
| [`cellular_automata/eca_explorer.html`](cellular_automata/eca_explorer.html) | Interactive explorer for all 256 of Wolfram's elementary cellular automaton rules — seed the first row and watch simple local rules generate complex emergent patterns |
| [`cellular_automata/voter_model.html`](cellular_automata/voter_model.html) | Interactive voter model simulation — cells adopt neighbors' opinions via local copying rules, illustrating consensus dynamics, coexistence, and fragmentation |

---

## Adding a new visualization

Each visualization lives in its own subdirectory and generally follows this pattern:

```bash
my_demo/
  my_demo.html          # self-contained page
  my_demo-preview.png   # preview image for OG/Twitter cards
```

For a new `my_demo.html`, the checklist is:

1. **`<head>` metadata:**

   * Include `<title>`, `<meta name="description">`, and the full Open Graph + Twitter
     card block (see any existing demo for the template)
   * Use the preview image as `og:image` and `twitter:image`; set width/height
     accurately
   * For Twitter/X, the image should be close to **2:1 aspect ratio**
     and under **5 MB**

2. **Back-link footer** – add at the bottom of `<body>`:

   ```html
   <footer style="margin-top:1.5rem;padding-top:0.75rem;border-top:1px solid #e0e0e0;font-size:0.8rem;color:#888;">
     <a href="../" style="color:#2e7d32;text-decoration:none;">&larr; All course visualizations</a>
   </footer>
   <script>if (window.self !== window.top) { var f = document.querySelector('footer'); if (f) f.style.display = 'none'; }</script>
   ```

   The script hides the footer when the page is embedded in a Canvas iframe.

3. **Index entry** – add a `<li>` to the appropriate `<section>` in [`index.html`](index.html),
   following the existing pattern (thumbnail + title + one-sentence description).

---

## License

Released under the [MIT License](LICENSE).
Copyright &copy; 2026 Theodore P. Pavlic.
