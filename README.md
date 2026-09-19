# CameraControl3D

**CameraControl3D** is a playable 3D camera-authoring experiment for turning simple spatial gestures into cinematic camera motion.

The core idea is simple:

> **Do not make the user animate a camera. Let the user point, draw, and steer intent — then let the system turn that intent into a camera move.**

The project focuses on low-mental-load camera authoring for 3D scenes, digital twins, spatial demos, storyboards, and interactive video tooling.

---

## What it does

CameraControl3D currently supports:

- direct camera-path authoring inside a 3D scene
- semantic subject selection and live focus switching
- automatic rack-focus / depth-of-field transitions
- curvature-aware speed shaping
- fifth-order cinematic motion smoothing
- position + rotation smoothing with bounded high-order derivatives
- automatic acceleration / braking treatment
- predictive turn anticipation
- terminal settling without a hard final snap
- live GLB import
- top-view and side-view trajectory feedback
- an advanced six-axis control panel
- multi-shot playback and timeline scrubbing

The interaction target is not traditional keyframe animation.

The intended mental model is:

```text
show the camera where to move
        ↓
show it what to look at
        ↓
press play
        ↓
the system handles the cinematic motion
```

---

## Interaction model

### 1. Draw / steer camera intent

The main viewport is the control surface.

Current gesture semantics:

- near-vertical stroke → **Crane**
- near-horizontal stroke → **Truck**
- diagonal / free stroke → **depth-capable camera drive**
- click a semantic object during playback → **shift attention to that subject**

Axis-oriented gestures lock their interpretation once the intent is clear, so small hand wobble does not suddenly switch the gesture into a different camera move.

The current direction is to preserve the user's dominant stroke while recovering spatial depth and parallax automatically, rather than forcing the user to think in six camera parameters.

---

## Fifth-order cinematic smoothing

The stabilization pipeline is intentionally more than ordinary path interpolation.

The current motion state models:

```text
position
  ↓
velocity
  ↓
acceleration
  ↓
jerk
  ↓
snap
  ↓
crackle
```

The offline path and speed profiles use fifth finite-difference regularization, while runtime camera-body and camera-rotation motion use bounded fifth-order state control.

This is designed to reduce the subtle mechanical feel that can remain even after position, velocity, and acceleration already look numerically smooth.

The system also includes:

- curvature-aware speed reduction
- predictive look-ahead before turns
- fifth-order-safe subject transitions
- fifth-order-safe terminal settling
- micro banking
- lens breathing
- dynamic depth of field

The target is perceptual smoothness rather than simply minimizing visible jitter.

---

## Subject attention

Camera motion and camera attention are separated.

A shot can move through one trajectory while transferring attention between semantic scene objects such as:

- room
- sofa
- window
- coffee table
- TV / cabinet
- plant

During playback, clicking a subject schedules a smooth gaze transition rather than abruptly snapping the camera toward it.

This lets the user direct the camera with a much simpler interaction:

> **move here, now look at this**

---

## Demo scene

The repository currently ships with a procedural living-room scene so the camera system can be evaluated without requiring an external asset.

You can also import a `.glb` scene from the interface.

The default scene is intentionally arranged with foreground, mid-ground, and background geometry so that camera translation, parallax, subject transfer, and depth-of-field behavior can be felt directly.

---

## Advanced controls

The default workflow tries to hide camera-animation complexity.

For inspection and comparison, an **Advanced** drawer still exposes the original six paired controls:

| Translation / Lens | Orientation |
| --- | --- |
| Horizontal | Pan |
| Vertical | Tilt |
| Zoom | Rotate |

Speed, timing, acceleration, braking, shot points, and subject controls are also available there.

These controls are intentionally secondary. They are useful for debugging the compiler and comparing direct parameter editing with intent-driven authoring.

---

## Run locally

CameraControl3D is a static browser project.

A simple local HTTP server is enough:

```bash
python -m http.server 4173
```

Then open:

```text
http://127.0.0.1:4173/
```

You can also use any other static web server.

---

## Repository layout

```text
CameraControl3D/
├── index.html
├── styles.css
├── app.js
├── README.md
└── .github/
    └── workflows/
        └── pages.yml
```

The runtime is intentionally kept small and inspectable: the current prototype is mostly plain HTML, CSS, JavaScript, and Three.js.

---

## GitHub Pages status

This repository is currently **Private**.

GitHub is currently showing:

> **Upgrade or make this repository public to enable Pages**

So GitHub Pages cannot be enabled for this private repository under the current account / plan configuration.

That is why the included Pages workflow currently fails during `actions/configure-pages` with:

```text
Create Pages site failed.
Resource not accessible by integration.
```

This is a repository / plan visibility limitation, not a CameraControl3D build failure.

There are three practical options:

1. keep the repository private and run the project locally;
2. make the repository public, then enable GitHub Pages;
3. use a GitHub plan / organization configuration that supports private Pages.

Until one of those changes, the repository remains fully usable for development, but no public GitHub Pages URL will be available.

---

## Product direction

The project is evolving toward:

```text
Sparse camera intent
        ↓
3D path + subject intent
        ↓
cinematic motion compiler
        ↓
perceptually smooth camera state
        ↓
playable / exportable shot
```

The long-term goal is not another camera-animation editor.

It is a camera interface where a user can create a surprisingly good shot with the smallest possible amount of explicit control.

---

## Current design principle

> **用户不是在做 Camera Animation，而是在“指给镜头看”。**

Or, in English:

> **The user should not have to animate the camera. The user should only have to show the camera what they mean.**
