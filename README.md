# ParticleSourceEvolutionInHyperboloidalCompactCoord

Time-domain Kerr point-particle waveform calculation in hyperboloidal compactified coordinates. This distribution brings together the existing theoretical derivation, Mathematica checks, Python evolution solver, shared waveform tools, and a compact reference dataset.

The scientific material originates from tasks T006--T007 (W02) of the parent research repository. This is a file-organization release: equations, algorithms, parameters, tests, and saved results are preserved unchanged. The existing owner acceptance and open qualifications are described in the [scientific overview](findings/kerr_point_particle_evolution/README.md).

## Reading Guide

| Content | Entry point |
| --- | --- |
| Scientific overview and evidence boundaries | [Overview](findings/kerr_point_particle_evolution/README.md) |
| Derivation and complete source-term appendices | [Derivation](findings/kerr_point_particle_evolution/theory/DERIVATION.md) |
| Fields, coordinates, normalization, and conventions | [Conventions](findings/kerr_point_particle_evolution/theory/CONVENTIONS.md) |
| Formula-to-code correspondence | [Formula map](code/kerr_point_particle_solver/docs/formula_code_map.md) |
| Discretization and evolution contracts | [Numerical contract](code/kerr_point_particle_solver/docs/numerical_contract.md) |
| Mathematica checks and saved residuals | [Verification](code/kerr_point_particle_verification/README.md) |
| Python evolution and restart workflow | [Solver](code/kerr_point_particle_solver/README.md) |
| Shared geometry, trajectories, extraction, and strain | [Waveform tools](code/kerr_waveform_tools/README.md) |
| Accepted representative waveforms and input files | [SXS0305 medium dataset](data/kerr_point_particle_evolution/sxs0305_medium/README.md) |
| Original numerical design rationale | [Archived W02 plan](workspace/plan/W02_point_particle_evolution/PLAN.md) |
| Literature index, including original evidence status | [References](REFERENCE.md) |

The implementation evolves a fixed azimuthal mode, with the source and trajectory interfaces detailed in the linked derivation and numerical contract. The example data include the (2,2) and (4,4) modes. The historical plans explain design decisions; their proposed validation gates should be read together with the later acceptance record.

## Installation

Use Python 3.11 or newer. Run all shell examples from this directory, which can also be the root of a standalone checkout.

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -e ./code/kerr_waveform_tools -e ./code/kerr_point_particle_solver
python -m pip install pytest
```

Dependencies are declared in the two existing `pyproject.toml` files: NumPy, SciPy, Matplotlib, and tqdm. Editable installation preserves the existing layout used to locate default output and initial-data directories. A relocated wheel installation has not been validated.

## Run the Supplied Example

```bash
python code/kerr_point_particle_solver/scripts/run_case.py \
  code/kerr_point_particle_solver/configs/smoke/evolution.json \
  code/kerr_point_particle_solver/configs/smoke/output.json \
  code/kerr_point_particle_solver/tests/fixtures/chi0p8_l6_flux.json
```

The smoke configuration and flux fixture are supplied unchanged; they are distinct from the SXS0305 medium reference calculation. The CLI provides `--t-end` for a shortened run, `--restart`, and explicit output/cache options; inspect them with `--help`. A shortened run is not a completed production bundle.

Without an explicit output directory, runs use `data/kerr_point_particle_evolution/runs/.staging/` and completed production bundles are finalized under `runs/`. Initial data use `data/kerr_point_particle_evolution/initial_data/`. New computation does not imply owner acceptance.

For other backgrounds, the existing [flux provider](code/kerr_waveform_tools/scripts/compute_isco_flux.wl) requires Wolfram and the Black Hole Perturbation Toolkit packages `KerrGeodesics` and `Teukolsky`. It accepts input/output JSON paths as its last two script arguments and fixes `lmax=6`. See [initial-data and trajectory inputs](workspace/plan/W02_point_particle_evolution/numerical/appendix/initial_data_and_trajectory.md) before supplying a different flux record. No Wolfram dependency is needed to run the Python example with its supplied flux JSON.

## Verify the Distribution

```bash
(cd code/kerr_point_particle_solver && python -m pytest tests)
(cd code/kerr_waveform_tools && python -m pytest tests)
python code/kerr_waveform_tools/scripts/validate_file_manifest.py \
  data/kerr_point_particle_evolution/sxs0305_medium/manifest.json
```

Without installation, prepend `PYTHONPATH=code/kerr_point_particle_solver/src:code/kerr_waveform_tools/src` to root-level Python commands on macOS/Linux. For tests, run from each package directory as shown: existing subprocess tests expect that working directory. Its `pyproject.toml` supplies the test import paths.

The Mathematica evidence was recorded with Wolfram 14.2.1 on macOS ARM64. From a Wolfram session whose current directory is this distribution root:

```wolfram
Get[FileNameJoin[{Directory[], "code", "kerr_point_particle_verification",
  "checks", "a6", "a6_targeted_summary.wl"}]]
```

Summary scripts read the bundled JSON evidence. They do not recompute the heavy symbolic stages. Follow the [verification README](code/kerr_point_particle_verification/README.md) for the distinction between individual checks, helpers, compatibility entries, and summaries.

## Scientific and Archival Status

The accepted numerical basis includes the recorded lowest-resolution cross-code comparison. Other originally planned numerical gates were cancelled, not passed. The A1 strain sign/normalization bridge remains `conditional-open`; A6 targeted symbolic checks do not establish a full numerical endpoint, stability, or convergence claim. These qualifications are retained in the original documents and data metadata.

The directory layout preserves existing relative links and script interfaces. Historical documents and result provenance are byte-preserved, including local absolute paths, historical review language, task identifiers, and references to material outside this distribution. Such paths describe the original environment and are not portable runtime dependencies. The original standalone spin-batch driver and subsequent near-horizon/filtering/model-comparison studies are outside this W02 distribution.

See [distribution notes](README_distribution.md) for organization checks and known archival links. This preparation does not publish to GitHub or assign a software license. No project license was supplied; the owner should select applicable licensing and citation information before public release. Third-party scientific packages retain their own licenses.
