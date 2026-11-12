# Regression Discontinuity: Bandwidths, Manipulation and Close Elections

**Repository:** `econ5200-lab13-regression-discontinuity`

## Objective

I wanted to see how the bandwidth choice drives a regression discontinuity (RD) estimate, how to test the design's assumptions, and what RD shows about how close US House elections change the way a district is represented.

## Methodology

- I simulated 4,000 students with a grant at a need index of 50 and a true effect on enrolment of 8 percentage points. The outcome curves differently on each side of the cutoff.
- I fit one global quadratic to every student, with a single curvature term for both sides, and compared it with a local linear fit from `rdrobust` at a bandwidth the package chose from the data.
- I estimated the jump at seven bandwidths (3 to 50) with equal weights in the window, then repeated the simulation 300 times to measure bias, spread and root mean squared error (RMSE) at each bandwidth.
- I ran a density test (`rddensity`) on the running variable, once on clean data and once on data where 40% of the units just above the cutoff moved just below it.
- I ran a covariate continuity test on a prior test score fixed before the grant, and on a copy with a 5-point jump planted at the cutoff.
- I applied RD to the Lee, Moretti and Butler (2004) US House data (1948–1990, 13,577 district-congress observations), using the 50% Democratic vote share as the cutoff and the adjusted ADA score as the outcome.
- I wrote my own local linear RD estimator with a triangular kernel, using weighted least squares in `statsmodels`, and checked it against `rdrobust`.
- I saved the tools as a module, `src/rd_tools.py`, that reports every estimate with its bandwidth, a sensitivity table and the design checks.

## Key Findings

- **The global fit was badly off.** The global quadratic estimated 0.0357, less than half the true 0.08. Because it uses one curvature term for both sides, the shape of the data far from the cutoff pulls the estimate down. It gave almost the same answer as a straight line on each side fitted to every student (0.0358). The local linear fit at a bandwidth of 16.1 estimated 0.0789.
- **Bandwidth is a tradeoff between bias and variance.** Across the 300 simulations, RMSE was lowest at a bandwidth of 20. Narrow windows were nearly unbiased but noisy. Wide windows were precise but biased: at a bandwidth of 50 the bias was about 48% of the true effect.
- **The density test caught sorting.** On clean data the p-value was 0.965. When units moved across the cutoff it was 0.0022.
- **The covariate test behaved as it should.** The prior score showed no jump (estimate 0.243, p = 0.7694). The planted 5-point jump was detected (estimate 5.243, p < 0.0001).
- **Close elections:** when a Democrat barely wins rather than barely loses, the district is represented 46.491 ADA points more liberally. That is more than the median score of 38, which supports partisan divergence over median-voter convergence. The density test at 50% found no sign of sorting (p = 0.717). The estimate applies only to close races.
- **My own estimator matched the package.** It reproduced `rdrobust` on the grant data (0.0789 at a bandwidth of 16.1) and on the House data (46.491).

## Takeaway

An RD estimate without the bandwidth behind it isn't a complete result, so I report the estimate together with its bandwidth, a sensitivity table and the design checks.
