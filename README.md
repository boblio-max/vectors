# Vectors — Robotic Arm Control
- Used in Boblio-max's C.O.R.I
  - Hand tracking
  - Joystick mapping
  - AI mode simplicity
## Overview

Sentinel's vector control system provides an intuitive, math-first interface for commanding robotic arms. Instead of manually calculating joint angles or writing low-level motor instructions, you simply describe **where you want the end-effector to go** — Sentinel handles the rest.

---

## Core Concept

At the heart of Sentinel's arm control is a single idea: **a position in space is just a vector.**

You pass in a 3D target vector `(x, y, z)`, and Sentinel's inverse kinematics engine decomposes that into the individual joint vectors required to reach it — automatically resolving the geometry across all degrees of freedom.

```python
arm = sentinel.RoboticArm(dof=3)
arm.move_to(Vector3(x=12.4, y=0.0, z=8.7))  # That's it.
```

---

## Benefits

### 🎯 One Vector In, Full Motion Out
Provide a single target position vector and Sentinel propagates the motion across all joints — no manual angle math, no per-joint scripting. The IK solver computes the full joint-space trajectory for you.

### 📐 3 DOF Joint Visualization
Sentinel decomposes your target into three interpretable segment vectors — one per joint — and exposes them in real time. You can visualize, log, or override each one independently, giving you full transparency into how the arm is reaching its target.

### 🔁 Live Telemetry & Vector Feedback
As the arm moves, Sentinel streams back the **current end-effector vector** so your control loop always knows the true position — not just the commanded one. This enables closed-loop correction without extra sensor code.

### 🧮 Math-Native Interface
Vectors are first-class citizens in Sentinel. Add, subtract, scale, and interpolate them using Sentinel's built-in `Vector3` class — making smooth trajectories, relative offsets, and dynamic waypoints trivial to express.

```python
# Move 5 units forward from current position
arm.move_to(arm.position + Vector3(x=5, y=0, z=0))
```

### 🗺️ Waypoint Chaining
Pass a list of vectors to define a multi-step path. Sentinel interpolates between them and executes the sequence, making pick-and-place routines and patrol paths expressible in a few lines.

```python
arm.follow_path([
    Vector3(10, 0, 5),
    Vector3(10, 5, 5),
    Vector3(0,  5, 0),
])
```

### ⚙️ Hardware-Agnostic
The vector abstraction sits above the hardware layer — the same `move_to()` call works whether your arm runs on servo PWM signals, stepper drivers, or a ROS 2 action server. Swap the backend without changing your control logic.

### 🛡️ Workspace Boundary Enforcement
Define a reachable workspace envelope as a bounding volume and Sentinel will reject or clamp any target vector that falls outside it — protecting your hardware from self-collision and over-extension before a command is ever sent.

---

## How It Works Internally

```
Target Vector (x, y, z)
        │
        ▼
  IK Solver (Jacobian / geometric)
        │
        ▼
Joint Vectors [θ₁, θ₂, θ₃]
        │
        ▼
  Motor Driver Layer
        │
        ▼
  Physical Arm Motion
```

Each joint's contribution is represented as a rotational vector, making the pipeline fully inspectable and debuggable at every stage through Sentinel's telemetry dashboard.
