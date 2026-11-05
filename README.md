# QFF 2026 Phase 2 — PS-S1 | Chicken Leg Piece

**Team:**Shorya Pratap Singh Chandrawat<br>
**Problem:** PS-S1, the open-chain transverse-field Ising ground-state simulation.<br>
**Phase 1 repository:** [qff2026-phase1-s1-chicken-leg-piece](https://github.com/ShoryaPratapSinghChandrawat/qff2026-phase1-s1-chicken-leg-piece)<br>
**Phase 1 submission files:** Complete Phase 1 code, data, and artifacts are preserved in [`phase1/`](phase1/).

This release contains the Phase 2 PS-S1 implementation, saved evidence, reproducibility checks, and research extensions. The official Phase 2 inputs and simulator remain preserved in `handoff_payload/OFFICIAL_KIT/`; `qff_sim.py` and `starter/qff_sim.py` match the official SHA-256 `dd0305834f88164f01f9b2a17f7333eb48a966cb81951b1953eed0e218de4cfc`.

## 60-second reviewer path

- **1. Open Notebook:** Run `jupyter notebook main.ipynb` (opens in < 5 seconds; defaults to `RECOMPUTE=False` to inspect saved tables, convergence curves, and figures).
- **2. Review Primary Results:** Check `results/tables/s1_results.csv` ($N=4$ HVA minimal layer convergence across all 7 fields) and read [report.md](report.md).
- **3. Validate Processor D:** Run `python tools/check_processor_d.py processors/processor_D.json 6` (passes all official connectivity and capacity rules).
- **4. Run Automated Test & Verifier:** Execute `python verify_submission.py && python -m pytest -q` (validates all official hashes, schemas, and 23 tests in < 15 seconds).
- **5. Explore Extensions:** Inspect optional $N=6$ convergence in `results/additional/s1_n6_results.csv`, finite-size scaling in `results/figures/additional_finite_size_scaling.png`, and seed consistency in [docs/SEED_STABILITY.md](docs/SEED_STABILITY.md).

## Hackathon Objectives 1–5 Crosswalk

| Objective | Focus | Key Deliverable & Evidence Files | Verified Status |
|:---|:---|:---|:---:|
| **Objective 1** | Problem Formulation & Exact Reference | `data/s1/` (locked JSON instances), `results/tables/s1_exact_reference.csv`, `main.ipynb` (Sec 1) | **100% Match** ($|\Delta E| < 10^{-9}$) |
| **Objective 2** | VQE Implementation & Layer Study | `src/run_s1.py`, `results/tables/s1_results.csv`, `results/tables/layer_study.csv`, `results/figures/layer_study_convergence.png` | **100% Converged** ($L_{\min}=3,4; |\Delta E| < 6 \times 10^{-13}$) |
| **Objective 3** | Processor D Architecture & Resource Analysis | `processors/processor_D.json`, `tools/check_processor_d.py`, `results/tables/resources.csv`, `results/figures/processor_D_topology.png` | **50% 2Q Gate Reduction** (18 $\to$ 9 gates) |
| **Objective 4** | Noise Simulation & Common Bell Benchmark | `results/tables/bell.csv`, `results/tables/s1_noisy.csv`, `results/figures/bell_comparison.png`, `results/figures/noisy_vs_ideal_energy.png` | **Audited Error Bars** ($0.9598 \pm 0.0022$ direct) |
| **Objective 5** | Extended Research & Methodological Honesty | `results/additional/` ($N=6$, finite-size $N \le 12$, ZNE mitigation, $p_2$ sensitivity), `verify_submission.py` | **Fully Preserved** (All failures flagged) |

## Results at a glance

- **Exact and variational solution:** all seven locked `g` values (`0.25` through `2.00`), layers `L=1..4`, and seeds `0,1,2` are represented. L=4 reaches absolute energy error below `6e-13` at every `g`. The strict `<1e-3` best-of-three threshold is first met at L=3 for `g=1.00, 1.50, 2.00`, and at L=4 for the other four values.
- **Processor D:** a connected six-qubit chorded ring with seven edges and native RZZ. The official checker passes for 4 and 6 qubits. On the selected N=4, L=3 HVA, the native RZZ implementation uses 9 generic two-qubit gates on D versus 18 on A/B, with no routing SWAPs. In the explicit CX representation, A/B/D use 18 gates; D's RZZ hardware basis does not reduce that CX-form count.
- **Measured bottleneck:** A and B already compile this open-chain HVA with zero SWAPs. The measured improvement is the RZZ interaction encoding: the CX-RZ-CX construction takes 12 versus 6 two-qubit gates at L=2, and 18 versus 9 at the selected L=3. The companion CX/RZZ resource rows make the comparison visible for every processor.
- **Bell experiment:** four ideal/noisy rows, 4,096 shots per basis. Noisy fidelity is `0.959839 ± 0.002184` for adjacent pair 0–1 and `0.938354 ± 0.002685` for distance-3 pair 1–4.
- **Noise research:** the required `s1_noisy.csv` results use D. A separate 36-row controlled comparison reuses the same saved logical HVA parameters across A/B/D for `g={0.5,1.0,2.0}` and `p2` multipliers `{1,1.25,1.5,2}`. D has lower noisy energy error and higher gate-noisy density-matrix fidelity than A/B at all 12 tested `g × multiplier` settings. This is a synthetic sensitivity result, not hardware evidence.
- **Additional research (not part of the official required results):** the optional N=6 sweep has 204 saved starts across all seven fields (L=1..8 for g=0.25, 1.25, 1.5, 2.0; L=1..12 for g=0.5, 0.75, 1.0), the finite-size study has 180 exact sparse-spectrum rows for N=4, 6, 8, 10, and 12, and the D mitigation study has 15 rows with 400 bootstrap replicates each. These are supplementary results.

See [report.md](report.md) for methods, tables, figures, results, and limitations; [docs/FINAL_RUBRIC_MATRIX.md](docs/FINAL_RUBRIC_MATRIX.md) for the evidence map; and [docs/NUMBER_AUDIT.md](docs/NUMBER_AUDIT.md) for numeric provenance.

## Reproduce and verify (Windows PowerShell)

Run from the repository root. The original saved simulation package records Python 3.13.16. The final notebook and verifier were also rerun on Python 3.11.9 with the same core package versions; both environments use the package pins in `requirements-local-lock.txt`.

```powershell
py -3.13 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-local-lock.txt
.\.venv\Scripts\python.exe -m pip check
.\.venv\Scripts\python.exe -m pytest -q
.\.venv\Scripts\python.exe tools/check_processor_d.py processors/processor_D.json 4
.\.venv\Scripts\python.exe tools/check_processor_d.py processors/processor_D.json 6
.\.venv\Scripts\python.exe verify_submission.py
.\.venv\Scripts\python.exe -m jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=-1 main.ipynb
```

The verifier checks the embedded official manifest and hashes, official schemas, all required N=4 outputs, the 204 N=6 starts and derived summaries/resources, all 180 finite-size rows and reference/crossing checks, all 15 mitigation rows and their target/error/cost consistency, the 36-row A/B/D p2 comparison, figures, the executed notebook, and Processor D at 4 and 6 qubits. It recomputes outputs from saved parameters and sparse spectra; it does not launch the optimizer sweep or Bell experiment. The notebook defaults to `RECOMPUTE=False` and displays saved results and figures.

The single command for the complete Stage B noisy/resource regeneration is `make regenerate-stage-b`. It runs `src/finish_stage_b.py`, then `src/compare_processor_p2.py` to restore both HVA encodings and Bell rows in the nine-row resources table, then runs the verifier; the command returns nonzero if the final table or any other saved output is incomplete. The optimizer and Bell experiment are separate: `src/run_s1.py` launches the 84 required starts and `src/run_bell.py` reruns Bell measurements. Leave `RECOMPUTE=False` for a saved-output review.

For convenience, activate `.venv` and run `make test`, `make verify`, `make notebook`, or `make regenerate-stage-b`. The CI workflow at `.github/workflows/ci.yml` checks dependency consistency, the test suite, and the submission verifier on Python 3.11 and 3.13.

## Additional research (not part of the official required results)

- `results/additional/p2_sensitivity.csv` and `results/figures/p2_sensitivity.png`: D-only hypothetical p2 multiplier sweep, with `p1=0.001` and `p_readout=0.01` held fixed.
- `results/additional/p2_processor_comparison.csv` and `results/figures/p2_processor_comparison.png`: same-parameter A/B/D comparison across the same multipliers, with `p1=0.001` and `p_readout=0.01` fixed. Density-matrix state fidelity includes gate noise only; readout is not folded into it.
- `results/additional/finite_size_scaling.csv`, `finite_size_crossings.csv`, `finite_size_scaling_interpretation.md`, and `results/figures/additional_finite_size_scaling.png`: exact sparse results for N=4, 6, 8, 10, 12 over 36 fields from 0.25 to 2.00. Crossings are linear interpolations of adjacent-size `N × gap` curves on the 0.05 grid; with `g_c=1` as the known infinite-chain value, the finite N≤12 data show trends near 1 but do not establish a thermodynamic critical point.
- `results/additional/mitigation_d_results.csv`, `mitigation_d_costs.csv`, and `results/figures/additional_mitigation_d.png`: readout correction and zero-noise extrapolation for D at `g={0.5,1,2}`. The synthetic study scales `p2` only, uses 400 bootstrap replicates per observable row, and compares with the noiseless expectation of the same selected HVA circuit.
- `results/additional/s1_n6_*` and `results/figures/additional_n6_layer_study.png`: 204 N=6 starts across all seven `g` (L=1..8 for g=0.25, 1.25, 1.5, 2.0; L=1..12 for g=0.5, 0.75, 1.0) and seeds 0,1,2; 88 optimizer non-success flags remain preserved. The strict threshold is reached across all fields: L=4 for `g=2.0`, L=5 for `g=1.5`, L=8 for `g=0.25,1.25`, L=9 for `g=0.5,0.75`, and L=10 for `g=1.0`. For g=0.5, 0.75, 1.0, and 1.25, the selected minimal-threshold result has `success=False` (with optimizer convergence confirmed at higher depth for g=0.5, 0.75). Processor A capacity and B/D resource rows are in `s1_n6_resources.csv`; this remains an optional supplemental tier.

## Limitations

All noise and mitigation results use a synthetic model; Processor D is not calibrated hardware. Fewer couplers means less to calibrate; crosstalk is not represented in the official noise model, so no benefit is claimed. The measured trade-off between 7 and 9 couplers is reflected in the far Bell pair fidelity (0.938 on 7 couplers vs 0.946 on 9 couplers). The mitigation study applies p2-only scales and shared calibration counts, not hardware noise scaling. Best-of-three is a limited optimizer budget, and all non-converged N=4/N=6 starts remain flagged in raw data. Finite-size crossings are diagnostic interpolations on a discrete field grid, not a thermodynamic extrapolation.

## AI disclosure

Arena.ai Agent Mode, Codex, and Antigravity were used to inspect the supplied challenge materials, implement reproducibility and analysis code, execute simulations and validation checks, inspect generated outputs, and assist in drafting this documentation and report. The team (Swarup, Biswajit, Arhaan Sethi, and Shorya Pratap Singh Chandrawat) has reviewed the complete repository, validated all numerical results against primary evidence, and takes full responsibility for the contents of this submission.
