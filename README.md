# Aggregate CLF-CBF Presentation

Presentation slides for the talk on **Integration of Control Lyapunov and Control Barrier Functions for Safety-Critical Guarantees in Aggregate Computing**.

## Author

[Angela Cortecchia](mailto:angela.cortecchia@unibo.it)

## View the Presentation

The presentation is available at: https://angelacorte.github.io/presentation-aggregate-clf-cbf/

## Overview

This presentation covers the integration of control theory concepts into Aggregate Computing to ensure safety-critical guarantees. Key topics include:

- **Control Lyapunov Functions (CLF)** for fast convergence and stability
- **Control Barrier Functions (CBF)** for safety in transient behavior
- CLF-CBF Quadratic Program optimization
- Use cases in distributed adaptive systems

## Building Locally

### Prerequisites

- [Hugo Extended](https://gohugo.io/) (version 0.152.2 or compatible)
- [Ruby](https://www.ruby-lang.org/) (for the preprocessor)

### Build Steps

1. Clone the repository with submodules:
   ```bash
   git clone --recurse-submodules https://github.com/angelacorte/presentation-aggregate-clf-cbf.git
   cd presentation-aggregate-clf-cbf
   ```

2. Build and serve the slides:
   ```bash
   hugo serve
   ```

3. The slides will be available at `http://localhost:1313/presentation-aggregate-clf-cbf/`.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
