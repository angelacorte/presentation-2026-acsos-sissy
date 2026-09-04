+++
title = "Toward Safe Aggregate Computing"
description = "A distributed control-theoretic safety filter for robot swarms."
outputs = ["Reveal"]
[params.reveal_hugo.katex]
enable = true
+++

# Toward **Safe Aggregate Computing**:
# A Distributed Control-Theoretic Safety Filter for Robot Swarms

[**<span class="deck-title-accent">Angela Cortecchia</span>**](mailto:angela.cortecchia@unibo.it)<small>1</small>,
[Alessandro Papadopoulos](mailto:alessandro.papadopoulos@mdu.se)<small>2</small>,
[Danilo Pianini](mailto:danilo.pianini@unibo.it)<small>1</small>

{{% spacer %}}

<span class="deck-affiliation"><small>*1</small> Department of Computer Science and Engineering (DISI)<br>
Alma Mater Studiorum -- University of Bologna - Cesena, Italy<br></span>
<br>
<span class="deck-affiliation"><small>*2</small>Department of Computer Science and Engineering<br>
Mälardalen University, Västerås, Sweden</span>

{{% spacer %}}

<div class="hero-logo">
  <img src="./images/dep-logo.pdf" width="45%">
  <img src="./images/MDU_logotyp.png" width="30%">
</div>

---

# Programming adaptive robot swarms safely
### Collective behavior and safety are both required

{{% multicol %}}
{{% col class="col-50" %}}

![Four drones approaching an obstacle](./images/drone_formation.svg)

{{% /col %}}
{{% col class="col-50" %}}

In applications such as search and rescue or environmental monitoring, groups of robots must:

- coordinate toward a common objective;
- adapt to changing conditions;
- remain safe while doing so.

A collective strategy may correctly describe <strong>where the swarm should go</strong>, while still producing unsafe motion on the way.

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">We need both an expressive way to specify collective behavior and a mechanism that enforces safety during execution.</p>

---

# Aggregate Computing
### Programming the collective, not each robot

<div class="ac-overview">
  <div class="ac-visual">
    <img src="./images/acDevices.svg">
  </div>
  <div class="ac-copy">
    <p>Aggregate Computing raises the programming abstraction from individual devices to <strong>collective behavior</strong>.</p>
    <p>Developers compose <strong>computational fields</strong>: distributed values that evolve through local interaction across the network.</p>
  </div>
</div>

<div class="pattern-line">
  <span>spreading information</span>
  <span>aggregating values</span>
  <span>electing leaders</span>
  <span>forming collective patterns</span>
</div>

<p class="takeaway">One global program is executed locally by all robots through repeated neighbor-to-neighbor interaction.</p>

---

# How AC executes
### Sense → compute → communicate / act

{{% multicol %}}
{{% col %}}

<div class="takeaway-editorial">
  <p>Each robot repeatedly:</p>

  <div class="takeaway-line">
    <span>01</span>
    <p><strong>Senses</strong> local inputs and the latest messages from neighbors.</p>
  </div>

  <div class="takeaway-line">
    <span>02</span>
    <p><strong>Computes</strong> the aggregate program using local state and neighbor information.</p>
  </div>

  <div class="takeaway-line is-critical">
    <span>03</span>
    <p><strong>Communicates / acts</strong> by sharing the updated state and applying the local output.</p>
  </div>
</div>

{{% /col %}}
{{% col %}}

### Round-based model

<div class="local-round-slide">
  <div class="local-round-points">
    <p><strong>Each round consists of three repeated phases:</strong></p>
    <ol>
      <li class="fragment" data-fragment-index="1">
        <strong class="local-round-sense-text">Sense</strong> collect sensor inputs and incoming neighbor messages.
      </li>
      <li class="fragment" data-fragment-index="2">
        <strong class="local-round-compute-text">Compute</strong> evaluate the aggregate program using local state and neighbor information.
      </li>
      <li class="fragment" data-fragment-index="3">
        <strong class="local-round-interact-text">Communicate / Act</strong> share the updated state and apply the local output.
      </li>
    </ol>
    <p class="fragment local-round-async" data-fragment-index="4">The model does not require global lock-step.</p>
  </div>

{{< local-round-loop >}}

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">Global self-organizing behavior emerges from repeated local interaction.</p>

---

# The limitation: self-stabilization is eventual

AC provides reusable coordination patterns with self-stabilizing behavior.

Under stable inputs and network topology, the system can recover from transient changes and **eventually** converge to a stable collective state.

But during that transient phase — or while the environment keeps changing — the aggregate program may continue adapting without guaranteeing that every physical constraint is respected.

<div class="pattern-line">
  <span>obstacle collisions</span>
  <span>inter-robot collisions</span>
  <span>broken communication links</span>
</div>

<p class="takeaway">For robot swarms, eventual convergence is not enough: safety must also hold before convergence.</p>

---

# The missing layer: safe actuation

{{% multicol %}}
{{% col class="col-50" %}}

### Collective level

The aggregate program computes the intended motion:

\[
u_{nom}
\]

It says where the robot would like to go.

{{% /col %}}
{{% col class="col-50" %}}

### Control level

A safety filter checks whether that command is admissible.

It outputs the command actually applied:

\[
u
\]

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">The idea is not to replace AC, but to filter its commands before actuation.</p>

---

# Two control intuitions

{{% multicol %}}
{{% col class="col-50" %}}

### CLF: progress

A Control Lyapunov Function measures distance from a goal.

\[
V = 0 \quad \text{at the target}
\]

The controller should make \(V\) decrease.

{{% /col %}}
{{% col class="col-50" %}}

### CBF: safety

A Control Barrier Function defines a safe set.

\[
h \ge 0 \quad \text{means safe}
\]

The controller should prevent \(h\) from becoming negative.

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">CLFs encode what should happen; CBFs encode what must not happen.</p>

---

# The safety filter

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

<p class="takeaway">Stay as close as possible to the aggregate command, but never violate active hard safety constraints.</p>

---

# Proposed architecture

<img class="paper-figure architecture" alt="Architecture of the aggregate safety filter" src="./images/architecture.svg" />

<div class="three-beats">
  <span><b>1.</b> AC computes <em>u<sub>nom</sub></em></span>
  <span><b>2.</b> CLF/CBF filter computes <em>u</em></span>
  <span><b>3.</b> Robot acts and feeds back its state</span>
</div>

<p class="takeaway">Collective strategy and physical safety are kept modular.</p>

---

# Distributed safety constraints

<img class="paper-figure device-view" alt="Device-wise view of local and pairwise quadratic programs" src="./images/architecture2.svg" />

{{% multicol %}}
{{% col class="col-50" %}}

### Local constraints

- target convergence;
- obstacle clearance;
- speed limits.

{{% /col %}}
{{% col class="col-50" %}}

### Pairwise constraints

- inter-robot collision avoidance;
- selected communication-link preservation.

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">Pairwise constraints expose the distributed structure of the problem.</p>

---

# What is enforced?

<div class="constraint-list">
  <div><b>Reach the target</b><span>CLF on squared target distance</span></div>
  <div><b>Avoid obstacles</b><span>CBF outside obstacle clearance</span></div>
  <div><b>Avoid collisions</b><span>CBF above minimum robot separation</span></div>
  <div><b>Preserve connectivity</b><span>CBF below selected communication range</span></div>
  <div><b>Respect speed limits</b><span>Bound on the control input</span></div>
</div>

<p class="takeaway">The active constraints depend on the collective task being executed.</p>

---

# Proof of concept

<div class="metrics">
  <div><strong>4</strong><span>robots in 2D</span></div>
  <div><strong>2 m</strong><span>minimum separation</span></div>
  <div><strong>10 m</strong><span>communication range</span></div>
  <div><strong>2 m/s</strong><span>maximum speed</span></div>
</div>

<p class="experiment-stack"><b>Collektive</b> specifies the aggregate behavior &nbsp;→&nbsp; <b>Alchemist</b> simulates the swarm &nbsp;→&nbsp; <b>Gurobi</b> solves the QPs</p>

<p class="small-center">This is a proof of concept: the goal is to validate the architecture, not yet to provide a scalability benchmark.</p>

---

# Scenario 1: different targets

<img class="simulation" alt="Animated simulation of robots reaching two different targets" src="./images/different-targets.gif" />

<p class="takeaway">Different nominal goals, shared safety constraints: robots reach their targets while avoiding obstacles and collisions.</p>

---

# Scenario 2: leader election and connectivity

<img class="simulation" alt="Animated leader-election and connectivity-preservation simulation" src="./images/follow-leader.gif" />

<p class="takeaway">The aggregate strategy adapts when clusters merge, while the safety filter preserves selected communication links.</p>

---

# Scenario 3: when safety reveals a strategy limit

<img class="simulation" alt="Animated simulation with multiple obstacles and a local minimum" src="./images/multiple-obstacles.gif" />

<p class="takeaway">The filter correctly blocks unsafe motion, but a direct target policy can get trapped in a local minimum.</p>

<p class="next-step">This motivates runtime strategy adaptation in the AC layer: switch target, waypoint, or exploration policy.</p>

---

# Takeaways

<div class="closing">
  <p><b>Aggregate Computing</b> gives a high-level language for adaptive collective behavior.</p>
  <p><b>CLF/CBF filtering</b> turns progress and safety requirements into constraints before actuation.</p>
  <p><b>Distributed optimization</b> exploits local and pairwise structure through neighbor exchanges.</p>
</div>

<p class="takeaway final">Safe Aggregate Computing means keeping self-organization programmable without treating transient safety as an afterthought.</p>

<p class="future">Next: scalability, communication overhead, richer swarm behaviors, and runtime strategy changes.</p>

---

# Thank you for the attention!

Reproducible experiments here:

![qr.png](images/qr.png)

angelacorte/experiments-2026-acsos-ws-carol