+++
title = "Toward Safe Aggregate Computing"
description = "A distributed control-theoretic safety filter for robot swarms."
outputs = ["Reveal"]
[params.reveal_hugo.katex]
enable = true
+++

# Toward **Safe Aggregate Computing**: A Distributed Control-Theoretic Safety Filter for Robot Swarms

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
  <img src="./images/DIP INFORMATICA-SCIENZA E INGEGNERIA_DISI_EN.svg" width="60%">
  <img src="./images/MDU_logotyp.png" width="50%">
</div>

---

# Safe adaptation in robot swarms
### Collective strategies must respect physical constraints during execution

<p class="small-center">In search and rescue or environmental monitoring, a swarm must reach its goal &mdash; and stay safe on the way.</p>

<div class="motion-pair">
  <div class="motion-item">
    <img class="motion-figure" src="./images/drones_changing-formation.png" alt="The swarm splits to squeeze between the obstacles, its communication links break, and it reforms on the other side" />
    <p class="motion-caption is-problem">the group splits to get through: links break</p>
  </div>
  <div class="motion-item">
    <img class="motion-figure" src="./images/drones_avoiding_formation.png" alt="The swarm goes around the obstacle while staying connected" />
    <p class="motion-caption is-safe">what we want: around the obstacle, still connected</p>
  </div>
</div>

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
{{< local-round-loop >}}

{{% /col %}}
{{% /multicol %}}

<p class="takeaway">Global self-organizing behavior emerges from repeated local interaction.</p>

---

# The limitation: self-stabilization is eventual
### Convergence is guaranteed, but only in the limit

{{% multicol %}}
{{% col class="col-50" %}}

<video
    class="field-video"
    data-autoplay
    src="./images/eventual_consistency.mp4"
    type="video/mp4"
    loop
    muted
    playsinline>
</video>

{{% /col %}}
{{% col class="col-50 col-middle" %}}

<div class="takeaway-editorial">
  <div class="takeaway-line">
    <span>01</span>
    <p>AC building blocks are <strong>self-stabilizing</strong>: under stable inputs and topology, the swarm recovers from transient faults.</p>
  </div>
  <div class="takeaway-line">
    <span>02</span>
    <p>But the guarantee is <strong>eventual</strong> &mdash; a stable state is reached after an <em>indefinite</em> number of rounds.</p>
  </div>
  <div class="takeaway-line is-critical">
    <span>03</span>
    <p>Meanwhile the program keeps adapting, with <strong>nothing enforcing physical constraints</strong> at every instant.</p>
  </div>
</div>

{{% /col %}}
{{% /multicol %}}

<div class="pattern-line">
  <span>obstacle collisions</span>
  <span>inter-robot collisions</span>
  <span>broken communication links</span>
</div>

<p class="takeaway">For robot swarms, eventual convergence is not enough: safety must also hold before convergence.</p>

---

# The safety layer
### The aggregate command is filtered, not replaced

<img class="paper-figure architecture" alt="Architecture of the aggregate safety filter" src="./images/architecture.png" />

<p class="takeaway">Collective strategy and physical safety stay modular: AC commands are filtered, not replaced.</p>

---

# Control functions for convergence and safety
### One score for progress, one score for danger

<div class="framework-grid is-two ctrl-grid">
  <div class="framework-card is-azure">
    <div class="framework-card-title">Control Lyapunov Functions (CLF) &mdash; what <em>should</em> happen</div>
    <div class="framework-card-body">
      <svg class="ctrl-sketch" viewBox="0 0 260 116" role="img" aria-label="A trajectory descending a bowl toward the target where V is zero">
        <path d="M 20 16 Q 130 150 240 16" fill="none" stroke="#1668b2" stroke-width="2.4" stroke-linecap="round"/>
        <line x1="16" y1="88" x2="244" y2="88" stroke="#1668b2" stroke-width="1.4" stroke-dasharray="5 5" opacity="0.5"/>
        <circle cx="64" cy="60" r="6.5" fill="#1668b2"/>
        <path d="M 84 72 q 20 11 44 15" fill="none" stroke="#0b1f33" stroke-width="2" stroke-linecap="round" marker-end="url(#clfArrow)"/>
        <defs>
          <marker id="clfArrow" markerWidth="7" markerHeight="7" refX="5.5" refY="3" orient="auto">
            <path d="M 0 0 L 6 3 L 0 6 z" fill="#0b1f33"/>
          </marker>
        </defs>
        <text x="130" y="104" text-anchor="middle" fill="#1668b2">V = 0 at the target</text>
      </svg>
      <p><strong>V</strong> scores how far the robot still is from its goal. The controller must keep pushing that score down, toward zero.</p>
    </div>
  </div>
  <div class="framework-card is-red">
    <div class="framework-card-title">Control Barrier Functions (CBF) &mdash; what must <em>not</em> happen</div>
    <div class="framework-card-body">
      <svg class="ctrl-sketch" viewBox="0 0 260 116" role="img" aria-label="A trajectory deflected along the boundary of the safe set">
        <path d="M 46 96 C 22 56 58 16 122 18 C 190 20 240 52 226 84 C 214 108 76 116 46 96 Z" fill="#d97706" fill-opacity="0.13" stroke="#d97706" stroke-width="2.4"/>
        <path d="M 74 86 C 120 90 178 84 200 66 C 214 50 186 36 154 41" fill="none" stroke="#0b1f33" stroke-width="2.2" stroke-linecap="round" marker-end="url(#cbfArrow)"/>
        <line x1="219" y1="52" x2="240" y2="34" stroke="#d97706" stroke-width="1.2" opacity="0.7"/>
        <defs>
          <marker id="cbfArrow" markerWidth="7" markerHeight="7" refX="5.5" refY="3" orient="auto">
            <path d="M 0 0 L 6 3 L 0 6 z" fill="#0b1f33"/>
          </marker>
        </defs>
        <text x="104" y="72" text-anchor="middle" fill="#d97706">h &#8805; 0</text>
        <text x="255" y="30" text-anchor="end" fill="#d97706" opacity="0.9">h = 0</text>
      </svg>
      <p><strong>h</strong> scores how much safety margin is left. The controller must never let that score reach zero.</p>
    </div>
  </div>
</div>

<p class="takeaway">CLFs encode what should happen; CBFs encode what must not.</p>

---

# Minimization Problem
### As close as possible to the nominal command, without entering the unsafe region

{{% multicol %}}
{{% col class="col-50 col-middle" %}}

<div class="formula">
\[
\min_{u,\,\delta \ge 0}\; \|u-u_{nom}\|^2 + \rho\delta^2
\]
<div class="qp-rows">
  <div class="qp-row is-clf">
    <span class="qp-math">\(\dot V \le -cV + \delta\)</span>
    <span class="qp-note">progress: soft CLF</span>
  </div>
  <div class="qp-row is-cbf">
    <span class="qp-math">\(\dot h_j \ge -\gamma_j h_j\)</span>
    <span class="qp-note">safety: hard CBFs</span>
  </div>
</div>
</div>

{{% /col %}}
{{% col class="col-50 col-middle" %}}

<svg class="filter-sketch" viewBox="0 0 520 230" role="img" aria-label="The robot heads straight for its target, but the direct route crosses an unsafe region, so the filter bends the command around the boundary and back toward the target">
  <defs>
    <marker id="nomArrow" markerUnits="userSpaceOnUse" markerWidth="17" markerHeight="15" refX="0" refY="7.5" orient="auto"><path d="M 0 0 L 17 7.5 L 0 15 z" fill="#1668b2"/></marker>
    <marker id="appArrow" markerUnits="userSpaceOnUse" markerWidth="17" markerHeight="15" refX="0" refY="7.5" orient="auto"><path d="M 0 0 L 17 7.5 L 0 15 z" fill="#063f72"/></marker>
  </defs>
  <circle cx="260" cy="140" r="64" fill="#d97706" fill-opacity="0.16" stroke="#d97706" stroke-width="2" stroke-dasharray="6 5"/>
  <circle cx="260" cy="140" r="42" fill="#878480" fill-opacity="0.5"/>
  <circle cx="60" cy="140" r="10" fill="#1f77b4" stroke="#14496e" stroke-width="2"/>
  <polygon points="462,125 465.8,134.7 476.3,135.4 468.2,142 470.8,152.1 462,146.5 453.2,152.1 455.8,142 447.7,135.4 458.2,134.7" fill="#2ca02c"/>
  <text x="60" y="178" text-anchor="middle" fill="#14496e">robot</text>
  <text x="462" y="180" text-anchor="middle" fill="#2ca02c">target</text>
  <text x="260" y="146" text-anchor="middle" fill="#5c5854">unsafe</text>
  <g class="fragment" data-fragment-index="1">
    <line x1="76" y1="140" x2="436" y2="140" stroke="#0b1f33" stroke-opacity="0.2" stroke-width="2" stroke-dasharray="7 7"/>
    <path d="M 78 140 L 148 140" fill="none" stroke="#1668b2" stroke-width="4.5" stroke-dasharray="10 7" marker-end="url(#nomArrow)"/>
    <text x="130" y="166" text-anchor="middle" fill="#1668b2">u<tspan font-size="9" dy="3">nom</tspan></text>
  </g>
  <g class="fragment" data-fragment-index="2">
    <g stroke="#d97706" stroke-width="3.6" stroke-linecap="round"><line x1="192" y1="131" x2="209" y2="149"/><line x1="209" y1="131" x2="192" y2="149"/></g>
  </g>
  <g class="fragment" data-fragment-index="3">
    <path d="M 80 133 C 140 130 175 122 197.6 104 A 72 72 0 0 1 322.4 104 C 350 128 392 132 426 133" fill="none" stroke="#063f72" stroke-width="4.5" stroke-linecap="round" marker-end="url(#appArrow)"/>
    <text x="260" y="56" text-anchor="middle" fill="#063f72">u</text>
  </g>
</svg>

<div class="sim-legend">
  <span><span class="lg lg-nom"></span><em>u<sub>nom</sub></em> &mdash; what the aggregate program asks for</span>
  <span><span class="lg lg-app"></span><em>u</em> &mdash; what the safety filter applies</span>
  <span><span class="lg lg-unsafe"></span>region the robot must stay out of</span>
</div>

{{% /col %}}
{{% /multicol %}}

<p class="takeaway fragment" data-fragment-index="4">Stay as close as possible to the aggregate command, but never violate active hard safety constraints.</p>

---

# What can be enforced?

<div class="constraint-list">
  <div class="is-clf"><b>Reach the target</b><span>CLF on squared target distance</span></div>
  <div class="is-cbf"><b>Avoid obstacles</b><span>CBF outside obstacle clearance</span></div>
  <div class="is-cbf"><b>Avoid collisions</b><span>CBF above minimum robot separation</span></div>
  <div class="is-cbf"><b>Preserve connectivity</b><span>CBF below selected communication range</span></div>
  <div class="is-cbf"><b>Respect speed limits</b><span>Bound on the control input</span></div>
</div>

<p class="takeaway">The active constraints depend on the collective task being executed.</p>

---

[//]: # (# Solving it in a distributed way)

[//]: # (### Alternating Direction Method of Multipliers &#40;ADMM&#41;)

[//]: # ()
[//]: # (<div class="flow">)

[//]: # (  <div class="flow-stage">)

[//]: # (    <span class="flow-kicker">Step 1</span>)

[//]: # (    <strong>Split</strong>)

[//]: # (    <p>The global problem is decomposed into subproblems: one per robot, one per relevant pair.</p>)

[//]: # (  </div>)

[//]: # (  <div class="flow-link"><span class="flow-arrow">&#10230;</span></div>)

[//]: # (  <div class="flow-stage">)

[//]: # (    <span class="flow-kicker">Step 2</span>)

[//]: # (    <strong>Exchange</strong>)

[//]: # (    <p>Neighbors exchange the variables their shared constraints depend on.</p>)

[//]: # (  </div>)

[//]: # (  <div class="flow-link"><span class="flow-arrow">&#10230;</span></div>)

[//]: # (  <div class="flow-stage">)

[//]: # (    <span class="flow-kicker">Step 3</span>)

[//]: # (    <strong>Agree</strong>)

[//]: # (    <p>Each robot updates its own command, until neighbors agree on a common safe solution.</p>)

[//]: # (  </div>)

[//]: # (</div>)

[//]: # ()
[//]: # (<div class="flow-feedback"><span>repeat until convergence</span></div>)

[//]: # ()
[//]: # (<p class="takeaway">The safety filter is itself an aggregate program: no global synchronization, only neighbor-to-neighbor exchange.</p>)

[//]: # ()
[//]: # (---)

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

<img class="simulation" alt="Animated simulation of robots reaching two different targets" src="./images/different-targets-amber-crop.gif" />

<div class="sim-legend">
  <span><span class="lg lg-robot"></span>Robots</span>
  <span><span class="lg lg-safety"></span>Robot safety radius</span>
  <span><span class="lg lg-comm"></span>Communication radius</span>
  <span><span class="lg lg-link"></span>Links within communication distance</span>
  <span><span class="lg-star">&#9733;</span>Targets</span>
  <span><span class="lg-cross">&#10006;</span>Obstacles</span>
  <span><span class="lg lg-margin"></span>Obstacle safety margin</span>
</div>

<p class="takeaway">Different nominal goals, shared safety constraints: robots reach their targets while avoiding obstacles and collisions.</p>

---

# Scenario 2: leader election and connectivity

<img class="simulation" alt="Animated leader-election and connectivity-preservation simulation" src="./images/follow-leader-amber-crop.gif" />

<div class="sim-legend">
  <span><span class="lg lg-robot"></span>Robots</span>
  <span><span class="lg lg-safety"></span>Robot safety radius</span>
  <span><span class="lg lg-comm"></span>Communication radius</span>
  <span><span class="lg lg-link"></span>Links within communication distance</span>
  <span><span class="lg-star">&#9733;</span>Targets</span>
  <span><span class="lg-cross">&#10006;</span>Obstacles</span>
  <span><span class="lg lg-margin"></span>Obstacle safety margin</span>
</div>

<p class="takeaway">The aggregate strategy adapts when clusters merge, while the safety filter preserves selected communication links.</p>

---

# Scenario 3: when safety reveals a strategy limit

<img class="simulation" alt="Animated simulation with multiple obstacles and a local minimum" src="./images/multiple-obstacles-amber-crop.gif" />

<div class="sim-legend">
  <span><span class="lg lg-robot"></span>Robots</span>
  <span><span class="lg lg-safety"></span>Robot safety radius</span>
  <span><span class="lg lg-comm"></span>Communication radius</span>
  <span><span class="lg lg-link"></span>Links within communication distance</span>
  <span><span class="lg-star">&#9733;</span>Targets</span>
  <span><span class="lg-cross">&#10006;</span>Obstacles</span>
  <span><span class="lg lg-margin"></span>Obstacle safety margin</span>
</div>

<p class="takeaway">The filter correctly blocks unsafe motion, but a direct target policy can get trapped in a local minimum.</p>

<p class="next-step">This motivates runtime strategy adaptation in the AC layer: switch target, waypoint, or exploration policy.</p>

---

# Conclusions and future work

<div class="closing-layout">
  <div class="takeaway-editorial">
    <h2>Takeaways</h2>
    <div class="takeaway-line">
      <span>01</span>
      <p><strong>Aggregate Computing</strong> specifies adaptive collective behavior at a high level.</p>
    </div>
    <div class="takeaway-line">
      <span>02</span>
      <p><strong>CLF/CBF filtering</strong> makes convergence and safety requirements explicit before actuation.</p>
    </div>
    <div class="takeaway-line">
      <span>03</span>
      <p><strong>Distributed optimization</strong> exploits local and pairwise structure through neighbor exchanges.</p>
    </div>
  </div>

  <div class="future-panel">
    <h2>Future work</h2>
    <div class="future-group">
      <h3>Quantitative evaluation</h3>
      <p>Measure convergence time, scalability, and communication overhead.</p>
    </div>
    <div class="future-group">
      <h3>Complex collective behaviors</h3>
      <p>Explore formation control, flocking, and coverage.</p>
    </div>
    <div class="future-group">
      <h3>Dynamic policies for target convergence</h3>
      <p>Handle local minima and improve adaptability in complex environments.</p>
    </div>
  </div>
</div>

<p class="takeaway final closing-statement">Safe Aggregate Computing keeps self-organization programmable while enforcing transient safety.</p>

---

# Thank you for the attention!

Reproducible experiments here:

![qr.png](images/qr.png)

angelacorte/experiments-2026-acsos-ws-carol
