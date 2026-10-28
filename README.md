# Hierarchical neural-mass model of δ–β–γ phase–amplitude coupling in Parkinson's disease

A minimal, phenomenological neural-mass model of nested δ→β→γ phase–amplitude coupling (PAC) in scalp EEG. It
asks one question: **can weakening the δ→β link alone explain a medication-related drop in *both* δ–β and β–γ PAC
while β power stays the same?**

In the model, it cannot. With β power held at its Off level, weakening δ→β coupling removes δ–β PAC but leaves
β–γ PAC unchanged. Reproducing a drop in both couplings needs a second, independent reduction of β→γ coupling of
roughly 40–80 %, and the two effects add without interacting. The model therefore favours **parallel effects over
a delta-driven cascade**.

> **Scope.** The model illustrates a mechanism; it is not fitted to patient data and contains no dopaminergic or
> basal-ganglia mechanism. "Medication" is a change in coupling gains. Magnitudes (z-scores, MI values) are not
> comparable with EEG; directions and dissociations are the outputs. The patient pattern the model is compared
> against is taken from [Hasib et al., 2026](#citation); an independent reanalysis of that dataset is in progress.

![Main figure: spectra, comodulograms and PAC along the two K_δβ sweeps](figures/figure_pac_model.png)

---

## Contents

| File | What it is |
|---|---|
| `pd_pac_neural_mass_final.ipynb` | The full model, analysis and figures (sections 1–9) |
| `pd_pac_model_info.md` | Model description: architecture, state indexing, every deviation from canonical parameters |
| `requirements.txt` | Python dependencies |
| `figures/` | Figures from the reference run |
| `LICENSE` | MIT |

## Quick start

```bash
git clone https://github.com/tanvir-hasib/Hierarchical-neural-mass-model-of-PAC-in-Parkinson-s-disease.git
cd Hierarchical-neural-mass-model-of-PAC-in-Parkinson-s-disease
pip install -r requirements.txt
jupyter notebook pd_pac_neural_mass_final.ipynb      # then Run All
```

Only `numpy`, `scipy` and `matplotlib` are needed. Tested with Python 3.13, NumPy 2.5, SciPy 1.18 and
Matplotlib 3.11.

**Run time** (2-core laptop CPU): about 6 min for sections 1–8, plus about 4 min for the gallery (section 9).
Switches at the top of the notebook:

| Switch | Default | Effect |
|---|---|---|
| `QUICK_MODE` | `False` | `True` gives a ~2 min smoke test (fewer seeds, shorter runs) |
| `RUN_B_SWEEP` | `True` | Secondary manipulation: β-column inhibitory gain |
| `RUN_SENSITIVITY` | `True` | Section 7: ±10 % one-factor sensitivity analysis |
| `RUN_TWO_HIT` | `True` | Section 8: two-hit factorial |
| `RUN_GALLERY` | `True` | Section 9: alternative visualisations |

All parameters live in the `CONFIG` dictionary. Seeds are fixed, so a run reproduces the reference numbers below
to the second decimal.

## The model

Three neural-mass columns coupled in series; the scalp signal is their weighted sum plus 1/f noise.

| Column | Model | Kinetic scale *k* | Peak | Role |
|---|---|---|---|---|
| δ | Jansen–Rit | 0.2 | ≈ 2.2 Hz | Irregular, noise-driven δ driver |
| β | Jansen–Rit | 2.0 | ≈ 21.5 Hz | Just past its Hopf point; δ troughs gate β bursts |
| γ | Wendling (2002) | 2.6 | ≈ 85 Hz | Just past its Hopf point; β input gates γ |

- **δ→β coupling:** `K_δβ · [S(v_δ) − S̄_δ]`, zero-mean so that K_δβ sets modulation depth, not the operating point.
- **β→γ coupling:** `K_βγ · HP[S(v_β)]`, a 2nd-order high-pass (τ = 20 ms) that stops slow δ-following drive
  reaching the γ column.
- **Integration:** Euler–Maruyama, Δt = 0.1 ms, all conditions and seeds vectorised, common random numbers.
- **PAC:** mean-vector-length MI z-scored against 200 circular-shift surrogates (one FFT for all shifts), Tort MI
  as a check, comodulograms with a max-statistic family-wise threshold.

Equations, parameter tables and the reasons for each choice are in [`pd_pac_model_info.md`](pd_pac_model_info.md).

## Experiments and reference results

20 seeds × 60 s per condition unless stated. Δz = Off − On (positive = coupling falls on medication), mean ± 95 % CI.

| Experiment | Result |
|---|---|
| Off state | δ→β z = 9.99 ± 0.86, β→γ z = 5.33 ± 0.71; peaks at 2.2, 21.5 and 85 Hz |
| Weaken δ→β, β power free (falls 6.5 dB) | Δz δ→β = 6.79 ± 0.87, Δz β→γ = 3.71 ± 0.60 |
| Weaken δ→β, **β power matched** | Δz δ→β = 7.88 ± 0.91, **Δz β→γ = −0.95 ± 0.80** (no fall) |
| Covariate analysis (n = 400) | β→γ PAC tracks β power (r = 0.81), not K_δβ once β power is controlled |
| Sensitivity, 6 parameters ± 10 % | Every sign holds in 13/13 configurations; matched β→γ never falls |
| β-column inhibitory gain ↑ | *Raises* β power by up to 21 dB: not a valid medication proxy |
| Two-hit (δ→β + β→γ reduced) | Reproduces both drops with β power unchanged once K_βγ ≤ 0.6 × baseline; interaction ≈ 0 |

Simulation p-values only reflect the number of seeds, so the notebook reports effect sizes and confidence
intervals instead.

## Limitations

- **Phenomenological.** No dopamine, basal ganglia or thalamus; "medication" is a change in coupling gains.
- **Assumed architecture.** The unidirectional δ→β→γ wiring is the hypothesis under test, not a finding.
  Common-input or bidirectional architectures were not tested.
- **Hand-tuned operating points** near bifurcations. Signs survive ±10 % perturbations; larger ranges are untested.
- **Δt not converged for the γ column.** Results are internally consistent at Δt = 0.1 ms, but μ_γ values do not
  transfer to another step size or integrator.
- **Power matching changes β's character** (bursty → sustained), so the matched condition is "different β, same
  power".
- **One electrode, no forward model.** Mixing weights (1, 3, 1.5) keep about 81 % of scalp β power from the β
  column; `source_check` reports this split on every run.


The empirical study the model accompanies:

> Hasib, T., Shunmugam, P., Muthusamy, K. A., Ramakrishnan, K., & Vengadasalam, V. (2026). The effect of
> medication on cross frequency coupling in Parkinson's disease. *Biomedical Signal Processing and Control*,
> 123, 110468. https://doi.org/10.1016/j.bspc.2026.110468

Key model references: Jansen & Rit (1995), *Biol. Cybern.* 73:357–366 · Wendling et al. (2002), *Eur. J. Neurosci.*
15:1499–1508 · Canolty et al. (2006), *Science* 313:1626–1628 · Tort et al. (2010), *J. Neurophysiol.* 104:1195–1210 ·
Aru et al. (2015), *Curr. Opin. Neurobiol.* 31:51–61.

## License

MIT — see [`LICENSE`](LICENSE).

## Contact

Tanvir Hasib · Multimedia University, Malaysia · open an [issue](../../issues) for questions or bug reports.
