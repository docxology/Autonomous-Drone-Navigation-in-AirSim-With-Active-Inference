# AGENTS.md — Autonomous Drone Navigation in AirSim with Active Inference

Guidance for agents working in this repository.

## What this repository is

A research project that flies a simulated quadrotor using active inference. The agent
minimizes expected free energy over a generative model of its own dynamics; AirSim provides
the physics and the camera, and the Julia package in `actinf/` provides belief updating,
planning and state-space estimation.

## Layout

| Path | Role |
|---|---|
| `actinf/` | The Julia package: state-space models, inference and planning |
| `airsim/` | AirSim-side scripts and settings for the simulator |
| `run_inference.jl` | Drives belief updating against a running simulation |
| `run_planning.jl` | Drives planning over the generative model |
| `rebuild.jl` | Rebuilds the Julia environment / manifest |
| `test.py` | **The simulator controller, not a test suite.** It drives the AirSim client; it does not assert anything and there is no test framework behind it |
| `Project.toml`, `Manifest.toml` | Julia environment. `actinf` is a path dependency; `RxInfer` and `StaticArrays` do the inference and numeric work |

## Running it

Julia for the inference and planning, Python for the AirSim client. `rebuild.jl` restores the
environment when `Manifest.toml` and the installed depot disagree.

```bash
julia --project=. -e 'using Pkg; Pkg.instantiate()'
julia --project=. run_inference.jl
julia --project=. run_planning.jl
python test.py          # starts/drives the simulator; not a test
```

The AirSim side needs a running simulator with the matching settings in `airsim/`. There is no
way to run this end to end without one.

## Working here

- **Do not treat `test.py` as verification.** It has no assertions and no runner. Nothing in
  this repository is covered by an automated check, so a change is verified only by what you
  actually ran and observed; report the command and what you saw.
- There is no CI workflow. Do not describe a green check as evidence for anything here.
- Changes to the generative model belong in `actinf/`, not in the driver scripts. Keep
  `run_inference.jl` and `run_planning.jl` as orchestrators over the package.
- `Manifest.toml` is committed on purpose: it pins the inference stack. Refresh it only with a
  reason, and say what moved.
