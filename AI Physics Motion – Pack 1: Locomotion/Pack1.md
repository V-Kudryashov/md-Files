# Introduction

In Unity, a "Ragdoll" usually means a humanoid model driven by physics. A ragdoll is passive — springs act as its muscles. As someone once put it, a ragdoll only really “comes to life” when it dies. Actively controlling a physics-driven humanoid has long attracted researchers and developers; the first satisfactory solution was presented by the authors of the DeepMimic paper.

The DeepMimic project used the Bullet physics engine. I have been trying for a long time to port DeepMimic techniques to Unity using ML-Agents, and finally I got some promising results. The first obstacle I overcame was replacing Rigidbodies with ArticulationBodies — the latter are much more stable. The next challenge was training a single neural network to learn many different skills. I chose a different path: many small models, each trained for one simple skill. This approach makes it possible to scale the number of skills indefinitely.

This package includes models for five locomotion behaviors: Idle, Walking, Run, Sprint, and Walking Backwards. For each behavior there are four neural-network models:
- **Base** — faithfully reproduces the animation without jitter.
- **Transition** — can perform two behaviors and transition between them.
- **Turn** — specialized for turning.
- **Strong** — a very robust model, trained to withstand disturbances. Because of its stability it can also turn and transition to other behaviors, but it performs those tasks less naturally than the specialized models.

The package also contains two get-up models: `GetupFromBack` and `GetupFromBelly`.

Three agent scripts are provided:
- `LocomotionAgent` — can switch behaviors sequentially and perform turns. In other words, it solves the locomotion task and acts like a CharacterController.
- `SimpleLocomotionAgent` — uses a single model per behavior (the Strong model). It provides the same high-level functionality as a CharacterController, but the motions look less natural.
- `SimpleAgent` — controls one model only. A very simple script that can be useful if you want to implement your own custom agent.
