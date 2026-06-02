# chaos-rs

Chaos theory and nonlinear dynamics in Rust — Lyapunov exponents, strange attractors, bifurcation analysis, fractal dimensions, and more.

## Features

- **Lyapunov exponents** — 1-D maps, n-D maps, continuous flows (QR method)
- **Strange attractors** — Lorenz, Rössler, Hénon-Heiles, Chua's circuit, Hénon map
- **Bifurcation detection** — saddle-node, transcritical, pitchfork, Hopf, period-doubling
- **Fractal dimensions** — box-counting, correlation (Grassberger-Procaccia), information dimension
- **Poincaré sections** — directional/bidirectional crossings, return maps
- **Symbolic dynamics** — partition encoding, shift spaces, topological entropy
- **Period doubling** — Feigenbaum constants, logistic-map cascade analysis
- **Fractal generation** — Mandelbrot, Julia, Burning Ship (point tests and grids)
- **Unpredictability analysis** — time-series Lyapunov estimation, behavioral regime classification

59 tests. Pure functions, no global state.

## Install

```toml
[dependencies]
chaos-rs = "0.1.0"
```

Requires **Rust 2021 edition**.

## Examples

### Lorenz attractor

```rust
use chaos_rs::{lorenz_integrate, LorenzParams};

let params = LorenzParams::default(); // σ=10, ρ=28, β=8/3
let trajectory = lorenz_integrate(&params, [1.0, 1.0, 1.0], 0.01, 10_000);
println!("Final state: {:?}", trajectory.last().unwrap());
```

### Lyapunov exponent of the logistic map

```rust
use chaos_rs::lyapunov_exponent_map;

let f  = |x: f64| 4.0 * x * (1.0 - x);   // logistic map, r = 4
let df = |x: f64| 4.0 * (1.0 - 2.0 * x); // derivative

let le = lyapunov_exponent_map(f, df, 0.1, 50_000);
println!("Lyapunov exponent = {:.4}  (theoretical: ln 2 ≈ 0.693)", le);
```

### Detect bifurcations

```rust
use chaos_rs::detect_bifurcations_1d;

let map   = |r: f64, x: f64| r * x * (1.0 - x);
let deriv = |r: f64, x: f64| r * (1.0 - 2.0 * x);

let bifs = detect_bifurcations_1d(map, deriv, (2.5, 4.0), 200, 5000, 0.1);
for b in &bifs {
    println!("{:?} at r = {:.4}: {}", b.bif_type, b.parameter, b.description);
}
```

### Fractal generation

```rust
use chaos_rs::mandelbrot_grid;

let grid = mandelbrot_grid((-2.0, 1.0), (-1.5, 1.5), 800, 800, 256);
// grid[row][col] = Some(escape_iter) if escaped, None if in set
```

### Unpredictability analysis

```rust
use chaos_rs::{analyze_scalar_unpredictability, build_behavior_profile};

let series: Vec<f64> = vec![/* your time series */];
let report = analyze_scalar_unpredictability(&series, vec![series_mean]);
println!("Chaotic? {}  Score: {:.3}", report.is_chaotic, report.unpredictability_score);

let profile = build_behavior_profile(&series);
println!("Regime: {:?}", profile.behavioral_regime);
```

## API Overview

| Module | Key exports |
|---|---|
| `sensitivity` | `lyapunov_exponent_map`, `lyapunov_exponents_map`, `lyapunov_exponents_flow`, `kaplan_yorke_dimension` |
| `attractors` | `lorenz_integrate`, `rossler_integrate`, `henon_heiles_integrate`, `chua_integrate`, `henon_map` |
| `bifurcation` | `detect_bifurcations_1d`, `classify_bifurcation`, `detect_hopf_bifurcation` |
| `fractal_dimension` | `box_counting_dimension`, `correlation_dimension`, `information_dimension` |
| `poincare` | `poincare_section`, `poincare_section_directed`, `return_map` |
| `symbolic` | `Partition`, `encode_itinerary`, `topological_entropy`, `shannon_entropy_rate` |
| `period_doubling` | `FEIGENBAUM_DELTA`, `FEIGENBAUM_ALPHA`, `logistic_bifurcation_points` |
| `fractal_gen` | `mandelbrot`, `julia`, `burning_ship`, `mandelbrot_grid`, `julia_grid` |
| `unpredictability` | `analyze_scalar_unpredictability`, `build_behavior_profile` |

## License

MIT OR Apache-2.0
