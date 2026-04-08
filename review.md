# **_Skepthical_** review: *Observability Thresholds for Damping and Stiffness Estimation in Stochastic Underdamped Oscillators*

## Summary

The manuscript aims to quantify practical limits for estimating stiffness (k) and damping (b) of an underdamped harmonic oscillator when only noisy position x(t) and velocity x˙(t) measurements are available (no direct acceleration). Using synthetic trajectories for 20 oscillators (Sec. 2.1), the authors vary both SNR and temporal resolution via downsampling, and compare (i) a Savitzky–Golay smoothing + numerical differentiation to obtain acceleration + linear least-squares regression pipeline (Sec. 2.2.1) against (ii) a Dual Kalman Filter (DKF) for simultaneous state/parameter estimation in a discrete-time state-space model (Sec. 2.2.2). An “observability threshold” is defined as the minimum SNR at which mean absolute percentage error (MAPE) remains below 5% (Sec. 2.3).

The topic and experimental framing are practically relevant: mapping estimator performance over an SNR–sampling grid can provide actionable guidance for experiment design and algorithm choice. However, the manuscript is currently not evaluable as a scientific contribution because the Results (Sec. 3) and Conclusions (Sec. 4) are essentially empty and no figures/tables are provided to support the specific threshold claims stated in the Abstract and Introduction (Sec. 1). In addition, key methodological details are missing or ambiguous: the data-generation and noise/SNR definitions (Sec. 2.1), the downsampling/aliasing implications, and complete specifications/tuning of both the DKF (Sec. 2.2.2) and the numerical differentiation pipeline (Sec. 2.2.1). Finally, the paper uses “observability” in a nonstandard way (empirical estimator-dependent threshold), and should either explicitly position this terminology relative to classical observability/identifiability theory or rename it to avoid confusion.

With a complete results section, reproducible method descriptions, clearer theoretical positioning, and a discussion of limitations and realism of assumptions (notably treating velocity as a directly measured channel), the work could become a useful reference for practitioners working with noisy oscillatory data.

## Strengths

- Studies a practically important system-identification question: how noise level (SNR) and sampling resolution constrain estimation of k and b from x and x˙ measurements (Sec. 1).
- Proposes a clear empirical evaluation framework: sweep SNR and temporal resolution and summarize performance via an explicit threshold criterion (Sec. 2.3).
- Compares two practically distinct approaches (smoothing/differentiation + regression vs DKF state/parameter estimation), which is valuable for applied readers (Sec. 2.2).
- Uses a controlled synthetic setup that, once fully specified, can isolate how measurement quality and sampling impact parameter estimation feasibility (Sec. 2.1).
- The governing equation and the rearrangement to a linear regression form are standard and (as stated) algebraically consistent (Eq. (1)–(2)).

## Major issues

1.  **The paper is incomplete: Sec.** 3 (Results) and Sec. 4 (Conclusions) are essentially empty, and no figures/tables/quantitative analyses are provided to substantiate the headline numerical claims in the Abstract and Introduction (Sec. 1) (e.g., ~12 dB for k and ~18 dB for b with DKF, >20 dB for the numerical method, and “k more observable than b”). As written, the central contribution cannot be verified or reviewed.
    
    *Recommendation:* Fully populate Sec. 3 with quantitative results across the full SNR × temporal-resolution grid for both methods and both parameters. At minimum include: (i) MAPE vs SNR curves for each sampling rate, or heatmaps/surfaces over (SNR, resolution); (ii) summary statistics across the 20 oscillators (mean/median and variability such as standard deviation or IQR); (iii) an explicit procedure for extracting thresholds (e.g., interpolation, averaging across oscillators, handling variability); and (iv) representative time-series/fit examples at “above threshold” and “below threshold” conditions. Then rewrite Sec. 4 to synthesize the major trends and translate them into practical guidance.

2.  **Terminology and theoretical positioning: the manuscript defines an “observability threshold” as an empirical estimator- and metric-dependent performance boundary (Sec.** 2.3), which differs from standard control-theoretic observability (structural property) and from identifiability/Fisher-information perspectives. Without clarification, readers may misinterpret the reported thresholds as fundamental system properties rather than contingent on estimator choices, priors, tuning, metrics, and parameter regimes.
    
    *Recommendation:* In Sec. 1 and Sec. 2.3, explicitly clarify that the paper studies an empirical performance/identifiability threshold under specific estimators and evaluation criteria. Either (a) rename to something like “empirical SNR requirement” / “practical identifiability threshold,” or (b) add a short formal bridge to classical notions (observability/identifiability, Fisher information/Cramér–Rao-type bounds) with appropriate citations, explaining what is and is not being claimed.

3.  **Synthetic data generation and parameter regime are under-specified (Sec.** 2.1). Key choices that strongly affect thresholds are missing: distributions/ranges for k and b across the 20 oscillators; initial conditions x(0), x˙(0); simulation duration relative to decay time; integrator and base time step; and confirmation/characterization of the underdamped regime (e.g., damping ratio ζ and proximity to critical damping). Without these, the reported thresholds are hard to interpret or generalize.
    
    *Recommendation:* Expand Sec. 2.1 with full reproducible specifications: (i) numeric ranges and sampling distributions for k and b and derived ranges for ωn and ζ; (ii) initial conditions and whether they vary; (iii) integration method (e.g., RK4) and base Δt; (iv) observation duration and how many oscillation cycles are present across the parameter set; and (v) explicitly verify underdamping (b^2 < 4mk) for all cases and report how close some cases are to critical damping. Consider stratifying Sec. 3 results by ζ and ωn to show how thresholds shift with regime.

4.  **SNR is central to the claims but is not mathematically defined, and it is unclear how SNR is enforced when noise is injected into both x and x˙ (Sec.** 2.1, Sec. 2.3). Different SNR conventions (power vs variance, per-channel vs joint, computed before/after downsampling) can materially change threshold values.
    
    *Recommendation:* Provide an explicit SNR definition in Sec. 2.1/Sec. 2.3, including the formula in dB and how signal power/variance is computed (over time window; detrending; per trajectory). State whether SNR is matched separately for x and x˙ or jointly, whether noise variances are constant over time, and whether x and x˙ noise are independent or correlated. Report the exact mapping from target SNR to injected Gaussian noise variance for each channel.

5.  **Downsampling and aliasing are not addressed (Sec.** 2.1), yet temporal resolution is a core experimental axis. If some oscillators’ frequencies approach/exceed Nyquist at low-resolution settings, estimation failures may be due to aliasing rather than noise or estimator limitations, confounding interpretation of “temporal-resolution observability thresholds.”
    
    *Recommendation:* In Sec. 2.1 and Sec. 3, report sampling rates for each downsampling condition and compare them to the oscillators’ frequency content (e.g., ωd/2π range). Specify whether an anti-aliasing low-pass filter is applied prior to decimation; if not, justify or add it. In Sec. 3, separate or annotate failure modes attributable to aliasing vs noise, and consider restricting the sweep (or the oscillator set) so the lowest sampling rate remains meaningfully above Nyquist for the studied ωd range.

6.  **The DKF (Sec.** 2.2.2) is described only qualitatively and is not reproducible or assessable for fairness. Missing are: the discrete-time state transition model for [x, x˙] as a function of (k, b, Δt) and whether discretization is exact or approximate; the parameter evolution model (constant vs random walk); the measurement model for observing x and x˙; full Q/R covariance choices and tuning; coupling between the dual filters; initialization (means/covariances); handling of parameter constraints (k>0, b>0); and sensitivity to priors/tuning.
    
    *Recommendation:* In Sec. 2.2.2, write down the full discrete-time model and DKF equations (or provide an appendix/pseudocode): (i) state vector, transition matrices (or nonlinear transition) and discretization method; (ii) parameter dynamics (e.g., random-walk variance for k and b); (iii) measurement equation for [x, x˙] with explicit H and R; (iv) Q and R numerical values (or rules) and how they were tuned; (v) initialization strategy (prior means/covariances relative to true parameter ranges); (vi) whether/ how you enforce positivity or prevent non-physical estimates; and (vii) a sensitivity analysis in Sec. 3 showing how thresholds change under reasonable variations of Q/R and prior covariance.

7.  **The numerical differentiation + least-squares pipeline (Sec.** 2.2.1) is under-specified and may not be a fair baseline without clear tuning. Savitzky–Golay window length/order, the rule for scaling with sampling resolution, the acceleration finite-difference scheme and boundary handling, and regression details (weighting/regularization/outlier handling, use of all points vs subsampling) are not given. These choices largely determine performance and thus the apparent gap vs DKF.
    
    *Recommendation:* Expand Sec. 2.2.1 with complete implementation details: (i) Savitzky–Golay polynomial order and window length in samples for each resolution (and rationale/selection method); (ii) explicit acceleration estimation formula (central difference, higher-order, etc.) and endpoint handling; (iii) regression formulation (OLS vs weighted/regularized; any constraints like k,b>0); and (iv) any preprocessing (discarding transients, normalization). In Sec. 3, include a tuning/fairness discussion: either tune both methods with a comparable protocol (e.g., cross-validated/held-out simulations) or show performance sensitivity to smoothing-window and filter-tuning choices.

8.  **Evaluation protocol is incomplete (Sec.** 2.3). It is unclear how many independent noise realizations are used per (SNR, resolution) condition, whether reported errors are averaged over Monte Carlo runs or based on a single draw, and whether baseline sanity checks are reported. Using only one realization can make thresholds unstable; using only MAPE can also hide bias/variance structure and can be dominated by small ground-truth parameter values (especially for b).
    
    *Recommendation:* In Sec. 2.3 and Sec. 3: (i) specify the number of Monte Carlo repeats per condition and how results are aggregated; (ii) report noise-free/high-resolution baselines to validate both implementations; (iii) complement MAPE with additional metrics (RMSE, median APE, bias ± variability across oscillators); and (iv) include a sensitivity analysis to the 5% cutoff (e.g., 2%, 10%) to show whether “~12 dB vs ~18 dB” conclusions are robust.

9.  **Assumptions and generalizability are not adequately discussed: the study appears to assume direct measurements of both x and x˙ with additive Gaussian noise (Sec.** 2.1), whereas in many real settings velocity is derived from position (introducing correlated/colored noise) and process noise/model mismatch exists. The Introduction (Sec. 1) gestures at broad applicability, but without a limitations discussion the reported thresholds risk being overgeneralized.
    
    *Recommendation:* Add a Limitations/Scope subsection (ideally in Sec. 4): clarify that simulations use a linear, unforced, single-DOF oscillator with known mass (m=1) and direct noisy access to both x and x˙. Discuss how thresholds might change if x˙ is numerically differentiated from x, if noise is colored/correlated, if forcing or process noise is present, if mass is unknown, or if the model is mismatched/nonlinear. If feasible, add one additional experiment contrasting “direct velocity measurement” vs “velocity derived from noisy position” to quantify the impact.

10.  **Literature context is currently too thin to assess novelty and to help readers interpret results (Sec.** 1). Related work on estimating damping/stiffness from free decay, Kalman-filter-based parameter estimation (dual vs augmented-state EKF/UKF), and identifiability/observability analyses is not cited or discussed.
    
    *Recommendation:* Add a Related Work subsection (e.g., Sec. 1.1): cover classical damping estimation (log decrement, frequency-domain fitting), state-space identification, dual/augmented Kalman filter approaches, and identifiability/observability/Fisher-information analyses for second-order systems. Then clarify the paper’s distinct contribution (e.g., systematic SNR–resolution maps and a practical comparison between a simple numerical pipeline and DKF under controlled conditions).

## Minor issues

1.  The abstract and Introduction (Sec. 1) state specific threshold values but (with Sec. 3 empty) do not point to any supporting figure/table, making these numbers appear ungrounded.
    
    *Recommendation:* After adding Sec. 3 results, update the Abstract/Sec. 1 to cite the exact figures/tables (e.g., “Fig. X”) from which thresholds are read, and ensure reported numbers match plotted values (use “~”/“around” unless precision is justified).

2.  The downsampling/noise/smoothing order of operations is unclear (Sec. 2.1, Sec. 2.2.1). It is ambiguous whether noise is added before/after downsampling, whether anti-aliasing filtering is used, and where Savitzky–Golay smoothing sits in the pipeline.
    
    *Recommendation:* Add an explicit step-by-step pipeline description (bullet list) in Sec. 2.1/Sec. 2.2.1: simulate at base Δt → (optional anti-alias) → downsample → inject noise (or vice versa, but justify) → apply smoothing → differentiate → regress; and mirror the same Δt handling in the DKF discretization (Sec. 2.2.2).

3.  DKF initialization is described only as using “reasonable priors” (Sec. 2.2.2), but practical performance can depend strongly on prior bias and covariance.
    
    *Recommendation:* Quantify prior means/covariances relative to the sampled k,b ranges in Sec. 2.2.2, and report at least one prior-misspecification sensitivity result in Sec. 3.

4.  The choice of 5% MAPE cutoff (Sec. 2.3) is not justified, and MAPE can be problematic when true parameters vary in magnitude (e.g., small b leading to large percentage errors).
    
    *Recommendation:* Add a brief justification for 5% in Sec. 2.3 (application-driven tolerance) and report robustness to alternative thresholds; also consider reporting absolute error metrics alongside MAPE.

5.  The role of temporal resolution is introduced (Sec. 2.1) but not yet integrated into an interpretive narrative (e.g., why sparse sampling affects numerical differentiation differently than DKF).
    
    *Recommendation:* In Sec. 3 and Sec. 4, add a targeted discussion comparing failure modes: derivative noise amplification and boundary effects for the numerical method vs model-based smoothing and prior dependence for DKF; connect observed trends to these mechanisms.

6.  State vector notation in Sec. 2.2.2 appears inconsistent/typoed (e.g., z = [x, x, k, b˙]^T) and another list appears malformed (e.g., (x, x,˙ x¨)).
    
    *Recommendation:* Correct to unambiguous notation throughout (e.g., z = [x, x˙, k, b]^T and (x, x˙, x¨)), and ensure dots indicate time derivatives only where intended.

7.  MAPE equation formatting (Eq. (3)) may be ambiguous regarding parentheses.
    
    *Recommendation:* Rewrite Eq. (3) with explicit parentheses: MAPE = (1/N) Σ_i |(θ_i − θ̂_i)/θ_i| × 100%.

8.  The Introduction uses a forced equation mx¨ + bx˙ + kx = F(t) but the Methods use the unforced case (Eq. (1)) without explicitly stating F(t)=0 in simulation.
    
    *Recommendation:* Add an explicit sentence in Sec. 2.1 that simulations set F(t)=0 (free decay), and briefly comment in Sec. 4 on whether/ how forcing would change the conclusions.

9.  Main contributions are not clearly enumerated (Sec. 1).
    
    *Recommendation:* At the end of Sec. 1, add a short bullet list of 3–4 concrete contributions (e.g., threshold definition, SNR–resolution mapping, method comparison, practical guidance).

## Very minor issues

1.  Section/heading and equation-referencing consistency is uneven (e.g., Sec. 2.3 heading style differs; equation labels/references are not always used uniformly; Sec. 3–4 placeholders remain).
    
    *Recommendation:* Standardize heading levels and ensure consistent equation numbering and references (e.g., always “Eq. (1)”). Remove placeholders by adding substantive Sec. 3–4 content.

2.  Minor wording repetition and informal phrasing appear in places (Abstract/Sec. 1 and parts of Sec. 2.2.2), which reduces polish.
    
    *Recommendation:* Perform a proofreading/copy-edit pass to reduce repetition, tighten phrasing, and use more precise technical language (e.g., “instead of initializing with true parameter values”).


## Mathematical consistency audit

This section audits **symbolic/analytic** mathematical consistency (algebra, derivations, dimensional/unit checks, definition consistency).

**Maths relevance:** light

The paper contains a small number of core equations (free oscillator ODE, its linear rearrangement for regression, and the MAPE definition). The central DKF method is described conceptually but without the explicit state-space/Kalman equations needed for a symbolic verification of internal mathematical consistency.

### Checked items

1.  ✔ **Free oscillator governing ODE** (Eq. (1), Sec. 2.1, p.2)
    
    - **Claim:** The simulated underdamped oscillator satisfies m x¨(t) + b x˙(t) + k x(t) = 0.
    - **Checks:** symbol/notation consistency, dimensional consistency
    - **Verdict:** PASS; confidence: high; impact: moderate
    - **Assumptions/inputs:** m is mass, b damping coefficient, k spring constant, Simulation uses free (unforced) dynamics
    - **Notes:** Units are consistent: m x¨ has force units; b x˙ requires b to have units of mass/time; k x requires k to have units of mass/time^2. The equation matches the described model in Methods.

2.  ✔ **Regression form derived from ODE** (Eq. (2), Sec. 2.2.1, p.3)
    
    - **Claim:** Rearranging Eq. (1) yields x¨(t) = −(b/m) x˙(t) − (k/m) x(t).
    - **Checks:** algebraic derivation, dimensional consistency
    - **Verdict:** PASS; confidence: high; impact: critical
    - **Assumptions/inputs:** m ≠ 0, Eq. (1) holds
    - **Notes:** Direct division of Eq. (1) by m gives Eq. (2). Dimensions also check: (b/m) has units 1/time and (k/m) has units 1/time^2.

3.  ✔ **Consistency of 'm assumed unity' with equations** (Sec. 2.1 (text) and Eq. (2), p.2–3)
    
    - **Claim:** Mass is assumed to be unity while equations still include m explicitly.
    - **Checks:** definition consistency, symbol usage
    - **Verdict:** PASS; confidence: medium; impact: minor
    - **Assumptions/inputs:** m=1 in simulations
    - **Notes:** Keeping m explicit in Eq. (2) is not inconsistent with setting m=1 in experiments; it is a presentation choice. However, explicitly stating whether regression solves for b and k directly (given m=1) versus for ratios b/m and k/m would improve clarity.

4.  ✖ **DKF augmented state vector definition** (Sec. 2.2.2, p.3)
    
    - **Claim:** The system is modeled with state vector z = [x, x, k, b˙]^T and DKF estimates kinematic state (x, x˙) and parameters (k, b).
    - **Checks:** notation consistency, symbol-definition consistency
    - **Verdict:** FAIL; confidence: high; impact: moderate
    - **Assumptions/inputs:** State should include position and velocity, plus parameters k and b
    - **Notes:** The written vector repeats x (second entry should presumably be x˙) and includes a dot on b (suggesting b˙), contradicting the surrounding text that parameters are (k, b). This is an internal inconsistency in notation that should be corrected.

5.  ⚠ **MAPE definition and parentheses** (Eq. (3), Sec. 2.3, p.4)
    
    - **Claim:** MAPE = (1/N) Σ_i |(θi − θ̂i)/θi| × 100%.
    - **Checks:** algebraic/formula clarity, definition consistency
    - **Verdict:** UNCERTAIN; confidence: medium; impact: minor
    - **Assumptions/inputs:** θi is ground-truth parameter for oscillator i, θ̂i is the corresponding estimate, θi ≠ 0 for all i
    - **Notes:** The intended MAPE is clear from the prose, but the displayed fraction/absolute-value grouping is potentially ambiguous without explicit parentheses. If interpreted as |θi − (θ̂i/θi)| it would be nonstandard and dimensionally inconsistent; if interpreted as |(θi − θ̂i)/θi| it is dimensionless and consistent.

6.  ✔ **Observability threshold definition** (Sec. 2.3 (text), p.4)
    
    - **Claim:** An 'observability threshold' is defined as the SNR level below which MAPE exceeds 5%.
    - **Checks:** definition consistency
    - **Verdict:** PASS; confidence: high; impact: minor
    - **Assumptions/inputs:** MAPE computed as defined, SNR is a scalar level associated to each experimental condition
    - **Notes:** As a definition, it is internally consistent. However, it depends on an explicit SNR definition, which is not provided elsewhere in the provided pages.

7.  ✔ **Forced vs unforced dynamics consistency** (Introduction (p.2) vs Eq. (1) (p.2))
    
    - **Claim:** The paper references mx¨+bx˙+kx=F(t) in the introduction but simulates mx¨+bx˙+kx=0 in methods.
    - **Checks:** assumption consistency
    - **Verdict:** PASS; confidence: medium; impact: minor
    - **Assumptions/inputs:** Simulations set external forcing to zero
    - **Notes:** Not a mathematical contradiction if the simulation assumes F(t)=0, but the assumption should be stated explicitly to avoid confusion.

### Limitations

- Only the provided 4-page extracted PDF text was available; no additional equations/appendices/state-space matrices were present to audit.
- The DKF is described conceptually without the explicit update/prediction equations, so its mathematical correctness and internal consistency cannot be verified from the document.
- No explicit SNR definition is given, preventing an analytic check of SNR-related claims and experimental condition definitions.


## Numerical results audit

This section audits **numerical/empirical** consistency: reported metrics, experimental design, baseline comparisons, statistical evidence, leakage risks, and reproducibility.

8 candidate checks were processed: 5 PASS, 0 FAIL, and 3 UNCERTAIN. Passes covered internal ordering of SNR thresholds (12/18/20 dB), derived timestep calculations from a 20 s window and step counts (500–25), algebraic consistency between Eq. (1) and Eq. (2), the m=1 simplification, and consistent use of a 5% MAPE threshold definition. Uncertain items were primarily cross-reference/enumeration checks requiring additional occurrences/lists beyond the provided text.

### Checked items

1.  ✔ **C1** (Page 1, Abstract)
    
    - **Claim:** “the observability threshold for the DKF was found to be approximately 12 dB for the spring constant and a higher 18 dB for the more sensitive damping coefficient, while the numerical method required an SNR above 20 dB.”
    - **Checks:** inequality / ordering consistency
    - **Verdict:** PASS
    - **Notes:** Checked strict ordering: 18 dB > 12 dB and 20 dB > 18 dB (and > 12 dB).

2.  ⚠ **C2** (Page 2, Section 2.1)
    
    - **Claim:** “The dataset comprised 20 distinct oscillators… For each oscillator… generated over a 20-second interval.”
    - **Checks:** cross-reference consistency
    - **Verdict:** UNCERTAIN
    - **Notes:** Cross-reference check requires additional document text; provided inputs include only one mention of each constant.

3.  ⚠ **C3** (Page 4, Eq. (3) MAPE definition + Page 2, Section 2.1)
    
    - **Claim:** MAPE formula uses N as the total number of oscillators; earlier the dataset comprised 20 distinct oscillators.
    - **Checks:** symbol-to-number substitution consistency
    - **Verdict:** UNCERTAIN
    - **Notes:** Can instantiate N=20 from the dataset description, but cannot verify there is no conflicting explicit N elsewhere using the provided inputs alone.

4.  ⚠ **C4** (Page 3, Section 2.1)
    
    - **Claim:** “six distinct Signal-to-Noise Ratio (SNR) levels, ranging from 5 dB to 40 dB.”
    - **Checks:** range/step sanity check (internal completeness)
    - **Verdict:** UNCERTAIN
    - **Notes:** Enumeration consistency requires an explicit list of SNR values elsewhere; not available in the provided inputs.

5.  ✔ **C5** (Page 3, Section 2.1)
    
    - **Claim:** “downsampling the original trajectories to 500, 250, 100, 50, and 25 time steps, while preserving the total 20-second observation window.”
    - **Checks:** derived sampling interval computation
    - **Verdict:** PASS
    - **Notes:** Computed Δt = 20/n for n in {500,250,100,50,25} yielding {0.04,0.08,0.2,0.4,0.8} s; matched expected values and strictly increased as n decreased.

6.  ✔ **C6** (Page 3, Eq. (2) and Page 2, Eq. (1))
    
    - **Claim:** Equation (2) rearranges equation (1) into x¨(t) = −(b/m) x˙(t) − (k/m) x(t).
    - **Checks:** algebraic consistency (symbolic sign/term check)
    - **Verdict:** PASS
    - **Notes:** Structural/algebraic consistency check: dividing mx¨+bx˙+kx=0 by m and solving for x¨ gives negative (b/m)x˙ and (k/m)x terms.

7.  ✔ **C7** (Page 2, Section 2.1)
    
    - **Claim:** “m is the mass (assumed to be unity)” in Eq. (1).
    - **Checks:** parameter substitution impact on Eq. (2)
    - **Verdict:** PASS
    - **Notes:** With m=1, coefficients b/m and k/m simplify exactly to b and k.

8.  ✔ **C8** (Page 4, Section 2.3)
    
    - **Claim:** “observability threshold” defined as the SNR level below which the MAPE for a given parameter exceeded 5%.
    - **Checks:** threshold definition internal consistency with abstract wording
    - **Verdict:** PASS
    - **Notes:** The 5% criterion is consistent between Methods (MAPE exceedance definition) and Abstract phrasing.

### Limitations

- Only 4 pages of parsed text were provided; no tables/figures/results data are available to numerically reproduce reported thresholds or error rates.
- No explicit list of the six SNR levels (beyond the 5–40 dB range) is given, preventing verification of the exact tested SNR set.
- No raw simulated time-series, ground-truth (b,k) values, noise standard deviations, or generated estimates are included, so performance metrics (MAPE) cannot be recomputed.
- Some checks are limited to internal logical/algebraic consistency rather than validating empirical correctness.
