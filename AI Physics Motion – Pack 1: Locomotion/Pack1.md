# Physics Motion Pack

## Introduction

In Unity, a "Ragdoll" usually means a humanoid model driven by physics. A ragdoll is passive — springs act as its muscles. As someone once put it, a ragdoll only really “comes to life” when it dies. Actively controlling a physics-driven humanoid has long attracted researchers and developers; the first satisfactory solution was presented by the authors of the DeepMimic paper.

The DeepMimic project used the Bullet physics engine. I have been trying for a long time to port DeepMimic techniques to Unity using ML-Agents, and finally I got some promising results. The first obstacle I overcame was replacing Rigidbodies with ArticulationBodies — the latter are much more stable. The next challenge was training a single neural network to learn many different skills. I chose a different path: many small models, each trained for one simple skill. This approach makes it possible to scale the number of skills indefinitely.

This package includes models for five locomotion behaviors: Idle, Walking, Run, Sprint, and Walking Backwards. For each behavior there are four neural-network models:
- **Base** — faithfully reproduces the animation without jitter.
- **Transition** — can perform two behaviors and transition between them.
- **Turn** — specialized for turning.
- **Strong** — a very robust model, trained to withstand disturbances. Because of its stability it can also turn and transition to other behaviors, but it performs those tasks less naturally than the specialized models.

The package also contains two get-up models: `GetupFromBack` and `GetupFromBelly`.

## Technical requirements
- Unity: `6000.2.10.f1` or newer.
- ML-Agents: `2.0.2` installed via the Package Manager.
- Platform: Desktop (Windows / macOS / Linux), Mobile (Android / iOS) — standard Unity build targets supported.

## Installation
1. Copy or import the `Assets/PhysicsMotionPack1` folder into your Unity project.
2. Install ML-Agents `2.0.2` via the __Package Manager__ (`Window > Package Manager`) if it is not already present.
3. Open one of the example scenes and press Play.

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
  - `length` — duration of the motion.
  - `speed` — target speed used by the `BoneController`.
- `getupFromBack`, `getupFromBelly` — model settings used when the agent gets up from lying on back or belly.
- `textState` (optional) — UI `Text` to display current agent state.

## Controls (runtime testing)
- Up / W — request next (faster) motion.
- Down / S — request previous (slower) motion.
- Left / A — rotate left.
- Right / D — rotate right.
- G — force get-up behaviour (selects back/belly get-up automatically).
- R — reset agent to initial pose.

---
