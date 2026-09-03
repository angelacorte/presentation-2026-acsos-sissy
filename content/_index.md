+++
title = "Toward Safe Aggregate Computing"
description = "A distributed control-theoretic safety filter for robot swarms."
outputs = ["Reveal"]
[params.reveal_hugo.katex]
enable = true
+++

# Toward **Safe Aggregate Computing**:
# A Distributed Control-Theoretic Safety Filter for Robot Swarms

### **Angela Cortecchia**, Alessandro Papadopoulos, Danilo Pianini

<span class="deck-affiliation"><small>*</small>*Department of Computer Science and Engineering (DISI)<br>
Alma Mater Studiorum -- University of Bologna - Cesena, Italy*</span>

<div class="hero-logo">
  <img src="./images/dep-logo.pdf" width="60%">
</div>

---

# Eventual convergence is not enough

{{% multicol %}}
{{% col class="col-50" %}}

![Four drones approaching an obstacle](./images/drone_formation.svg)

{{% /col %}}
{{% col class="col-50" %}}

Aggregate Computing can express resilient, self-organizing behavior.

But self-stabilization is an **eventual** guarantee.

During adaptation, robots may:

- collide with obstacles or each other;
- break communication links;
- keep adapting without ever reaching a stable regime.

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">We need constraints that hold during the transient.</p>

<aside class="notes">
About 55 seconds. Use the obstacle example to distinguish the software-level promise from the physical trajectory. The key transition is: the aggregate program says what should eventually happen, but does not constrain every intermediate command.

[Sources]
- Paper, Introduction, pp. 1-2.
- Drone illustration adapted from the original presentation assets.
</aside>

---

# Separate collective intent from safe actuation

<img class="paper-figure architecture" alt="Architecture of the aggregate safety filter" src="./images/architecture.svg" />

<div class="three-beats">
  <span><b>1.</b> Aggregate program proposes <em>u<sub>nom</sub></em></span>
  <span><b>2.</b> Safety filter computes <em>u</em></span>
  <span><b>3.</b> Robot acts and feeds back its state</span>
</div>

<p class="takeaway">The safety layer minimally changes the nominal command only when constraints require it.</p>

<aside class="notes">
About 70 seconds. Walk left to right through the architecture. Emphasize modularity: changing the aggregate strategy does not require re-encoding every safety rule, and the filter can observe neighbors through the same communication model.

[Sources]
- Paper, Fig. 1 and Section III, pp. 3-4.
- Architecture artwork: paper source file images/architecture.svg.
</aside>

---

# One filter combines progress and safety

<div class="formula">
\[
\min_{u,\,\delta \ge 0}\; \|u-u_{nom}\|^2 + \rho\delta^2
\]
\[
\begin{aligned}
\dot V &\le -cV + \delta && \text{progress: soft CLF}\\
\dot h_j &\ge -\gamma_j h_j && \text{safety: hard CBFs}
\end{aligned}
\]
</div>

{{% multicol %}}
{{% col class="col-50" %}}

### CLF: what should happen

Drive each robot toward its current target.

{{% /col %}}
{{% col class="col-50" %}}

### CBF: what must not happen

Keep every active safety function non-negative.

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">Safety has priority; progress may temporarily slow down.</p>

<aside class="notes">
About 75 seconds. Avoid deriving Lie derivatives. Explain the objective as staying close to the Aggregate Computing command. The slack is only on the CLF; the CBFs remain hard. Qualify the guarantee: active hard constraints must be jointly feasible.

[Sources]
- Paper, Safety Filter, Eq. 6, p. 3.
- Paper, Background on CLFs and CBFs, pp. 2-3.
</aside>

---

# Pairwise constraints make the filter distributed

<img class="paper-figure device-view" alt="Device-wise view of local and pairwise quadratic programs" src="./images/architecture2.svg" />

{{% multicol %}}
{{% col class="col-50" %}}

### Local QP

Target convergence, obstacle clearance, speed limits.

{{% /col %}}
{{% col class="col-50" %}}

### Pairwise QP

Collision avoidance and selected communication links.

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">ADMM decomposes the coupled problem into neighbor-to-neighbor subproblems.</p>

<aside class="notes">
About 70 seconds. Explain why some constraints are local and others couple two robots. Each device runs asynchronously with its latest neighbor information; pairwise variables are coordinated through ADMM rather than a central controller.

[Sources]
- Paper, Fig. 2 and Section III, pp. 3-4.
- Paper, Optimization problem and distributed solution, p. 5.
- Architecture artwork: paper source file images/architecture2.svg.
</aside>

---

# The specification covers five requirements

<div class="constraint-list">
  <div><b>Reach the target</b><span>CLF on squared target distance</span></div>
  <div><b>Avoid obstacles</b><span>CBF outside obstacle clearance</span></div>
  <div><b>Avoid collisions</b><span>CBF above minimum robot separation</span></div>
  <div><b>Preserve connectivity</b><span>CBF below the selected link range</span></div>
  <div><b>Respect speed limits</b><span>Bound on the control input</span></div>
</div>

<p class="takeaway">Each experiment activates the subset of constraints required by its collective task.</p>

<aside class="notes">
About 55 seconds. This replaces the long theory section. Point out the symmetry: collision avoidance imposes a lower distance bound, while connectivity preservation imposes an upper distance bound on selected links.

[Sources]
- Paper, Section IV-B, Eqs. 8-12, pp. 4-5.
</aside>

---

# Proof of concept: four robots

<div class="metrics">
  <div><strong>4</strong><span>robots in 2D</span></div>
  <div><strong>2 m</strong><span>minimum separation</span></div>
  <div><strong>10 m</strong><span>communication range</span></div>
  <div><strong>2 m/s</strong><span>maximum speed</span></div>
</div>

<p class="experiment-stack"><b>Collektive</b> specifies the aggregate behavior &nbsp;→&nbsp; <b>Alchemist</b> simulates the swarm &nbsp;→&nbsp; <b>Gurobi</b> solves the QPs</p>

<p class="small-center">Static obstacles use radius 2 m plus a 1.5 m safety margin. Robots sense targets and obstacles locally, and exchange information only with neighbors.</p>

<aside class="notes">
About 55 seconds. Clarify that this is a proof of concept, not yet a scalability benchmark. The robot model is a 2D single integrator: the control input directly specifies velocity.

[Sources]
- Paper, Section IV-A, p. 4.
- Paper, implementation details, p. 4.
</aside>

---

# Different goals, shared safety constraints

<img class="simulation" alt="Animated simulation of robots reaching two different targets" src="./images/different-targets.gif" />

<p class="takeaway">Both groups reach their targets while preserving robot separation and obstacle clearance.</p>

<aside class="notes">
About 75 seconds. Let the animation run. Robots 0 and 1 go to one target; robots 2 and 3 go to the other. The nominal commands differ, but all robots share the same collision and obstacle constraints.

[Sources]
- Paper, Section IV-C.1 and Fig. 3, p. 5.
- Reproducible experiment asset: experiments-2026-acsos-ws-carol/charts/gifs/different-targets/different-targets.gif.
</aside>

---

# The collective strategy adapts while links stay safe

<img class="simulation" alt="Animated leader-election and connectivity-preservation simulation" src="./images/follow-leader.gif" />

<p class="takeaway">Two clusters merge, elect a common leader, then navigate around the obstacle without breaking preserved links.</p>

<aside class="notes">
About 85 seconds. The yellow ring marks the current leader. Before the clusters meet they may follow different leaders. When they merge, the aggregate program elects the highest-ID leader. The safety filter first pulls robots inside the communication margin, then allows progress toward the target.

[Sources]
- Paper, Section IV-C.2 and Fig. 4, p. 5.
- Reproducible experiment asset: experiments-2026-acsos-ws-carol/charts/gifs/follow-leader/follow-leader.gif.
</aside>

---

# Safety can expose a strategy-level dead end

<img class="simulation" alt="Animated simulation with multiple obstacles and a local minimum" src="./images/multiple-obstacles.gif" />

<p class="takeaway">The filter correctly blocks unsafe motion, but a direct target policy can become trapped in a local minimum.</p>

<p class="next-step">The Aggregate Computing layer can detect slow progress and switch target, waypoint, or exploration policy.</p>

<aside class="notes">
About 70 seconds. This is the honest limitation and the reason for the monitoring loop in the architecture. The safety filter is doing its job: it should not invent a global path. Strategy adaptation is future work, not an implemented result in this paper.

[Sources]
- Paper, Section IV-C.3 and Fig. 5, pp. 5-6.
- Reproducible experiment asset: experiments-2026-acsos-ws-carol/charts/gifs/multiple-obstacles/multiple-obstacles.gif.
</aside>

---

# What this work establishes

<div class="closing">
  <p><b>Aggregate Computing</b> remains the high-level language for adaptive collective behavior.</p>
  <p><b>CLF/CBF filtering</b> makes convergence and safety requirements explicit before actuation.</p>
  <p><b>Distributed optimization</b> handles local and pairwise constraints through neighbor exchanges.</p>
</div>

<p class="takeaway final">A programmable swarm can adapt without treating transient safety as an afterthought.</p>

<p class="future">Next: quantitative scalability and overhead, richer swarm behaviors, and runtime strategy changes.</p>

<aside class="notes">
About 60 seconds, leaving roughly one minute of margin in a 12-minute slot. Close by returning to the opening tension: eventual convergence and instantaneous safety are different properties, and the architecture gives them distinct but composable mechanisms. Invite questions on feasibility under delay/asynchrony, ADMM convergence, and strategy adaptation.

[Sources]
- Paper, Conclusions and Future Work, p. 6.
</aside>
