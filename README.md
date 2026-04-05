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
| [`monte_carlo/`](monte_carlo/) | Interactive explorer for Monte Carlo methods: rejection sampling, Monte Carlo integration, and MCMC |
| [`simulated_annealing/`](simulated_annealing/) | Interactive explainer for Simulated Annealing: temperature schedules, neighbor selection, and probabilistic acceptance on an energy landscape |
| [`parallel_tempering/`](parallel_tempering/) | Interactive explainer for Parallel Tempering / REMC: fixed-temperature SA replicas at different temperatures with periodic configuration swaps |
| [`softmax/`](softmax/) | Interactive explorer for the softmax (Gibbs) distribution: how temperature interpolates between uniform exploration and greedy exploitation, connecting MaxEnt methods to SA and reinforcement learning |
| [`maxent/`](maxent/) | Interactive explainer for the Maximum Entropy principle: how MaxEnt selects the least-assumptive distribution consistent with known constraints, deriving the Gibbs/softmax distribution |
| [`boltzmann_maxent/`](boltzmann_maxent/) | Interactive simulation of N agents trading random amounts of a conserved quantity — any initial distribution relaxes to the Boltzmann (exponential) equilibrium |
| [`boltzmann_maxent/beta_spacings.html`](boltzmann_maxent/beta_spacings.html) | Interactive Beta(α,β) distribution explorer: adjust α and β, draw N samples, and watch how order statistics and spacings connect uniform random variables to the Beta distribution |

### Swarm Intelligence

| Directory | Description |
| --- | --- |
| [`collective_motion/boids_explorer.html`](collective_motion/boids_explorer.html) | Interactive Boids explorer: tune separation, alignment, and cohesion weights to watch emergent flocking arise from Reynolds' three local rules — a classic inspiration for PSO |
| [`collective_motion/vicsek_explorer.html`](collective_motion/vicsek_explorer.html) | Interactive Vicsek model explorer: adjust noise and interaction radius to watch self-propelled particles transition between disordered and coherent collective motion |
| [`particle_swarm_optimization/pso_explorer.html`](particle_swarm_optimization/pso_explorer.html) | Interactive explorer for PSO: step through the velocity-update rule with inertia, cognitive, and social components, then run a live swarm on a 2-D landscape |
| [`bacterial_foraging_optimization/bfo_explorer.html`](bacterial_foraging_optimization/bfo_explorer.html) | Interactive explorer for the complete BFO algorithm (Passino 2002): cell-to-cell signaling and tumble-swim chemotaxis in 1-D and 2-D, plus the full nested loop with reproduction and elimination-dispersal |
| [`ant_colony_optimization/aco_explorer.html`](ant_colony_optimization/aco_explorer.html) | Interactive ACO / Ant System explorer: pheromone-guided ants solve a layered combinatorial problem and a TSP — adjust evaporation rate and exploitation bias to see stigmergic reinforcement in action |
| [`collective_behavior/ant_foraging_explorer.html`](collective_behavior/ant_foraging_explorer.html) | Interactive ant foraging dynamics explorer: experiment with trail noise and Y-maze decision-making to see how pheromone stigmergy and recruitment linearity drive collective path selection |

### Neural Networks

| Directory | Description |
| --- | --- |
| [`single_layer_perceptron/slp_explainer.html`](single_layer_perceptron/slp_explainer.html) | Animated explainer linking the biological neuron to the linear classifier: adjust synaptic weights and inputs to watch the decision boundary shift in real time |
| [`radial_basis_function_nn/`](radial_basis_function_nn/) | Interactive explorer for RBF neural networks: centers, widths, weights, and the kernel-trick connection to SVMs |
| [`optimal_foraging_theory/mvt_explorer.html`](optimal_foraging_theory/mvt_explorer.html) | Interactive MVT explorer: drag the tangent line to find optimal patch residence times under Charnov's Marginal Value Theorem — R* as opportunity cost mirrors the temporal discount rate in the reinforcement learning Bellman equation |

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
    <footer id="course-nav-footer" style="margin-top:0;padding-top:0.75rem;border-top:1px solid #e0e0e0;font-size:0.8rem;color:#888;text-align:left;">
      <a href="../" style="color:#2e7d32;text-decoration:none;" onmouseover="this.style.textDecoration='underline'" onmouseout="this.style.textDecoration='none'">&larr; All course visualizations</a>
    </footer>
    <script>
    if (window.self !== window.top) { var f = document.getElementById('course-nav-footer'); if (f) { f.style.display = 'none'; } }
    </script>
   ```

   The script hides the footer when the page is embedded in a Canvas iframe.

3. **Index entry** – add a `<li>` to the appropriate `<section>` in [`index.html`](index.html),
   following the existing pattern (thumbnail + title + one-sentence description).

---

## License

Released under the [MIT License](LICENSE).
Copyright &copy; 2026 Theodore P. Pavlic.
