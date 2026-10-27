# A comparison of Langevin Sampling Algorithms

This repository contains Python code that compares three Langevin sampling algorithms
(ULA, MALA and kinetic langevin using the BAOAB splitting scheme). The code runs the algorithms on eight target distributions, in
dimensions from 1 to 1000. It also computes diagnostic metrics and creates corresponding figures.

## Algorithms

| Algorithm | Type | Step | Bias |
|---|---|---|---|
| ULA (Unadjusted Langevin Algorithm) | Overdamped | A gradient step plus Gaussian noise. | Yes. The bias is proportional to η. |
| MALA (Metropolis-Adjusted Langevin Algorithm) | Overdamped | The ULA step is a proposal. A Metropolis–Hastings test accepts or rejects it. | No, for each η. |
| Kinetic Langevin (BAOAB splitting) | Underdamped | The state has a position and a velocity (kinetic Langevin). The step has five parts: B, A, O, A, B. | Yes, but smaller. The bias is proportional to η². On a Gaussian target, the position has no bias. |

Each algorithm uses one new gradient per step.

## Target distributions

| Target | Difficulty |
|---|---|
| Gaussian | None. It is the reference. |
| Student-t | Tails that decrease slowly |
| Cauchy | Very heavy tails. No mean and no variance. |
| Anisotropic Gaussian | The variances go from 1 to κ. |
| Banana | Curved dependence in each pair of coordinates |
| Gaussian mixture | Three peaks at −a, 0, a on the first coordinate, with low density between them |
| Funnel | The scale of the coordinates changes with the first coordinate v |
| Double well | Two wells in each coordinate, with a barrier between them |

For each target, the code also gives the true mean, variance, quantiles and CDF of each
coordinate. The metrics compare the samples with these values.

## Metrics

| Metric | What it measures | Best value |
|---|---|---|
| KS (Kolmogorov–Smirnov) | The largest distance between the CDF of the samples and the true CDF of a coordinate | 0 |
| q-MAE | The error of the sample quantiles at q = 0.025, 0.5 and 0.975 | 0 |
| Tail coverage | The fraction of samples beyond the true 1 %, 5 %, 95 % and 99 % quantiles, divided by the expected fraction | 1 |
| b² | The squared error of a sample mean (of x_i² and, for the mixture and the double well, of x_i), divided by its true variance. A value below 0.01 is a success. | 0 |
| ESS (effective sample size) | The number of independent samples that give the same information as the chain | High |
| ESS per second | The ESS divided by the run time | High |
| Acceptance rate | The fraction of accepted proposals (MALA only) | — |
| Divergence rate | The fraction of samples that are not finite or larger than 10⁶ | 0 |
| Peak metrics | The rate of jumps between peaks and the error of the mass in each peak (mixture and double well only) | — |

The main metric is KS for the Gaussian, the Student-t and the Cauchy. It is b² for the
other targets, because for some of their coordinates the true CDF has no formula.
