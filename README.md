# Vectors — Robotic Arm Control

> Used in **Boblio-max's C.O.R.I** — hand tracking, joystick mapping, and AI mode.

---

## Overview

This vector control system provides an intuitive, math-first interface for commanding robotic arms. Instead of manually calculating joint angles or writing low-level motor instructions, you simply describe **where you want the end-effector to go**.

---

## Core Concept

At the heart of arm control is a single idea: **a position in space is just a vector.**

Pass in a 3D target vector `(x, y, z)` and the inverse kinematics engine decomposes it into the individual joint vectors required to reach that position — automatically resolving the geometry across all degrees of freedom.

---

## Benefits

###  One Vector In, Full Motion Out
Provide a single target position vector and The system propagates motion across all joints — no manual angle math, no per-joint scripting. The IK solver computes the full joint-space trajectory automatically.

###  3-DOF Joint Visualization
The system decomposes your target into three interpretable segment vectors — one per joint — and exposes them in real time. Visualize, log, or override each independently for full transparency into how the arm reaches its target.

### Live Telemetry & Vector Feedback
As the arm moves, The system streams back the **current end-effector vector** so your control loop always knows the true position — not just the commanded one. This enables closed-loop correction without extra sensor code.

### Math-Native Interface
Vectors are first-class citizens in The system. Add, subtract, scale, and interpolate them using The system's built-in `Vector3` class — making smooth trajectories, relative offsets, and dynamic waypoints trivial to express.

###  Waypoint Chaining
Pass a list of vectors to define a multi-step path. The system interpolates between them and executes the sequence, making pick-and-place routines and patrol paths expressible in a few lines.

###  Hardware-Agnostic
The vector abstraction sits above the hardware layer — the same `move_to()` call works whether your arm runs on servo PWM signals, stepper drivers, or a ROS 2 action server. Swap the backend without changing your control logic.

###  Workspace Boundary Enforcement
Define a reachable workspace envelope as a bounding volume and The system will reject or clamp any target vector that falls outside it — protecting your hardware from self-collision and over-extension before a command is ever sent.

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

Each joint's contribution is represented as a rotational vector, making the pipeline fully inspectable and debuggable at every stage through The system's telemetry dashboard.
