# Delta-driven nested PAC in a hierarchical neural-mass model of Parkinson's disease

**Companion simulation for:** *The effect of medication on cross-frequency coupling in Parkinson's disease.*

The model is a **minimal, phenomenological** illustration, not a fit to patient data. It shows that a
delta-driven hierarchy can generate nested delta→beta and beta→gamma phase–amplitude coupling (PAC).
It then tests whether weakening **only** the delta→beta link also attenuates beta→gamma PAC, which is
the "cascade" interpretation in the paper.

**Architecture: three unidirectionally coupled columns.**

| Column | Model | Kinetic scaling *k* | Role |
|---|---|---|---|
| δ | Jansen–Rit (6 states) | 0.2 → peak ≈ 2 Hz | noise-driven, irregular delta driver |
| β | Jansen–Rit (6 states) | 2.0 → peak ≈ 21 Hz | sits just past its Hopf point; delta input gates beta bursts |
| γ | Wendling 2002 (10 states, fast somatic inhibition) | 2.6 → peak ≈ 85 Hz | sits just past its Hopf point; beta input gates gamma |

The **"medication" manipulation is a phenomenological proxy** for dopaminergic restoration of
beta-generating circuits. The model contains no dopaminergic mechanism.

**State indexing is 0-based throughout.** For a JR column, `y0` is the PSP that pyramidal output
drives in the interneurons, `y1` is the excitatory PSP on pyramidal cells, and `y2` is the inhibitory
PSP on pyramidal cells. The pyramidal mean membrane potential, used as the model "LFP", is `v = y1 − y2`.
For the Wendling column it is `v = y1 − y2 − y3`.

**Deviations from the canonical parameter sets.** Each is flagged again in `CONFIG`.
1. All rate constants (a, b, g) and synaptic gains (A, B, G) of a column are multiplied by its *k*.
   This preserves the static gains A/a, B/b and G/g, so each column is the canonical model run
   *k* times faster.
2. Each column's mean input μ is set past its numerical Hopf point (μ_δ = 420, μ_β = 420, μ_γ = 200).
   The canonical JR value is 220.
3. The Wendling column uses B = 5 mV and G = 60 mV, its "fast activity" regime.
   The canonical values are B = 50 mV and G = 20 mV.
4. δ→β coupling is mean-compensated: `K_δβ·(S(v_δ) − S̄_δ)`. Changing K_δβ therefore changes the
   *depth* of delta modulation, not the beta column's operating point.
5. β→γ coupling passes the beta column's output through a 2nd-order high-pass (τ = 20 ms, ≈ 8 Hz).
   Without it, the slow, delta-following shift of the beta column's membrane potential drives the
   gamma column directly. The hierarchy then collapses into direct δ→γ coupling. This is a modelling
   choice and is reported as such.

6. Scalp mixing weights (δ, β, γ) = (1, 3, 1.5). The gamma column also emits a beta-locked response
   to its beta input. With a heavy gamma weight, that echo dominated the scalp β band, so "β power"
   and "β phase" no longer referred to the beta generator. `source_check` prints the per-column
   share of each band at the start of every run and warns if the beta column falls below 70 % of
   scalp β power.

**Controls (version 2).**
* **Power-matched On condition.** Reducing K_δβ alone lowers β power by about 8 dB, but in the
  patient EEG β power changed by < 1 dB from Off to On. A second sweep therefore re-calibrates μ_β at
  every K_δβ step so that β power stays at the Off level. Any PAC change in that sweep cannot be
  explained by β power.
* **β-power covariate.** Partial correlations of PAC with K_δβ, controlling log β power (and log γ
  power), across both sweeps.
* **Sensitivity analysis** (section 7). Six parameters are each moved ±10 % to test whether the sign
  of each effect survives.

Simulation p-values only reflect the number of seeds, so effects are reported as paired differences
with 95 % CIs and Cohen's d_z.