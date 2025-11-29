# Physics Motion Pack — README

## Introduction
Physics Motion Pack brings ML-Agents powered, physics-based humanoid locomotion to Unity. The package provides inference-ready neural models, example agents and utilities for switching between motion primitives (Idle, Walk, Run, Sprint, Walk Backwards) and handling falls and get-ups.

This project adapts DeepMimic-style motion cloning to Unity using `ArticulationBody` for improved stability and many small specialized models rather than a single monolithic network. The package focuses on inference (pretrained models) rather than training workflows.

The model has a humanoid Rig, so the movement can be easily retargeted to another humanoid model.

## Technical requirements
- Unity: `6000.2.10.f1` or newer.
- ML-Agents: `2.0.2` installed via the Package Manager.
- Platform: Desktop (Windows / macOS / Linux) — standard Unity build targets supported.

Note: Models and controllers use `ArticulationBody`. Ensure your physics settings (Fixed Timestep) are compatible with the sample scenes.

## Installation
1. Copy or import the `Assets/PhysicsMotionPack1` folder into your Unity project.
2. Install ML-Agents `2.0.2` via the __Package Manager__ (`Window > Package Manager`) if it is not already present.
3. Open one of the example scenes (see Folder structure) and press Play.

## Quick Start
1. Open the provided example scene or drag the example agent prefab into your scene.
2. In the inspector assign the ragdoll `Animator` to the agent, set up the `models` list and optionally assign `textState` UI `Text`.
3. Press Play. Use keyboard controls (see Controls section) to switch behaviours and test get-up/reset.

## Models
The package provides pretrained `ModelAsset` inference models for five locomotion behaviours:
- Idle
- Walking
- Run
- Sprint
- Walking Backwards

Each behavior has its own duration (Length) and movement speed. This data must be specified in the LocomotionAgent, SimpleLocomotionAgent and SimpleAgent script parameters.
| behaviour      | Length (s) |  Speed  |
| :------------- | ---------: | ------: |
| Backwards      |     1.200  | -1.071  |
| Idle           |     2.000  |  0.000  |
| Walk           |     1.233  |  1.014  |
| Run            |     0.833  |  2.370  |
| Sprint         |     0.533  |  5.519  |

Each behaviour includes four model variants:
- `Base` — high-fidelity imitation of reference animation, minimal jitter.
- `Transition` — supports two behaviours and enables transitions between them.
- `Turn` — specialized for turning maneuvers.
- `Strong` — robust model trained to withstand disturbances; useful for recovery and extended sequences but less natural for transitions/turns.

Additionally the package contains two get-up models: `GetupFromBack` and `GetupFromBelly`.

## Examples
- CharacterController — implemented by `LocomotionAgent`. Can sequentially switch motions, perform turns, handle falls and get-up behaviours. This is the most feature-complete setup and functions like a CharacterController.
- SimpleCharacterController — implemented by `SimpleLocomotionAgent`. Uses only `Strong` models per behaviour. Easier to configure; motions are less natural.
- SimpleAgent — implemented by `SimpleAgent`. Minimal agent controlling a single `ModelAsset` for quick tests or custom usage.

The CharacterController and SimpleCharacterController scenes have a Retarget object. If activated, the movement will be retargeted to another model.

## Scripts (overview)
- `LocomotionAgent` — High-level agent managing multiple models, transitions, turning, strong/fail/get-up logic and updating `BehaviorParameters.Model` at runtime.
  - Inspector highlights: `ragdoll` (`Animator`), `models` (list of `ModelSettings`), `getupFromBack`, `getupFromBelly`, `textState` (optional UI).
  - Runtime testing: Up/Down to request model switches, Left/Right to rotate, G to force get-up, R to reset.

- `SimpleLocomotionAgent` — Simpler controller using only `Strong` models. Provides CharacterController-like behaviour with simpler configuration.

- `SimpleAgent` — Minimal agent controlling a single model.

- `BoneController` — Internal utility used to collect observations and apply actions to the ragdoll joints. IMPORTANT: `BoneController` was used during training to form observations. It is an internal component that maps the agent's action/observation spaces to the physical ragdoll; users normally do not need to edit it.

## Inspector reference (`LocomotionAgent` highlights)
- `ragdoll` (Animator) — root animator for the ragdoll.
- `models` (List<ModelSettings>) — each entry contains:
  - `model` — base inference `ModelAsset`.
  - `transitionModel` — model used for transitions.
  - `strongModel` — robust model for recovery.
  - `turnModel` — model specialized for turning.
  - `length` — nominal phase length of the motion.
  - `speed` — target speed multiplier used by the controller.
- `getupFromBack`, `getupFromBelly` — model settings used when the agent gets up from lying on back or belly.
- `textState` (optional) — UI `Text` to display current agent state.

## Controls (runtime testing)
- Up / W — request next (faster) motion.
- Down / S — request previous (slower) motion.
- Left / A — rotate left.
- Right / D — rotate right.
- G — force get-up behaviour (selects back/belly get-up automatically).
- R — reset agent to initial pose.

## Troubleshooting & Tips
- Agent falls immediately: Verify all `ModelAsset` fields are assigned and the GameObject has a `BehaviorParameters` component.
- No outputs / freezes: Confirm ML-Agents package `2.0.2` is installed and models are compatible with that runtime.
- Get-up not triggering: Check the hips orientation threshold logic (used to choose back vs belly get-up) and ensure the ragdoll is not constrained by colliders.
- Physics stability: If you experience jitter, try adjusting `Time.fixedDeltaTime` and `ArticulationBody` solver settings.

## Folder structure
The package contents are organized under `Assets/PhysicsMotionPack1/`.
Important paths:
- `Assets/PhysicsMotionPack1/Scenes/` — example scenes (if included)
- `Assets/PhysicsMotionPack1/Prefabs/` — agent prefabs
- `Assets/PhysicsMotionPack1/Models/` — pretrained `ModelAsset` files
- `Assets/PhysicsMotionPack1/Scripts/` — agent and controller scripts (`LocomotionAgent`, `SimpleLocomotionAgent`, `SimpleAgent`, `BoneController`)

Note: In this release the `PhysicsMotionPack1` folder contains inference-ready agents only. Training utilities are not included.

## License & Support
Include your license file in the package (e.g., `LICENSE`). Provide an email or support URL for bug reports and questions.

---
