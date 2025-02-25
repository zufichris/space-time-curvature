# Space-Time Curvature Simulation

This document provides a comprehensive breakdown of the code behind the **Enhanced Interactive Space-Time Curvature Simulation**. The simulation visualizes the curvature of space-time caused by massive objects using Three.js, a popular JavaScript library for 3D graphics. The code creates an interactive 3D scene where users can adjust parameters, add masses, and observe gravitational interactions in real time.

## Table of Contents

1. [HTML Structure](#html-structure)
2. [JavaScript Code Overview](#javascript-code-overview)
3. [Scene Initialization](#scene-initialization)
4. [Grid Creation](#grid-creation)
5. [Mass Creation and Interaction](#mass-creation-and-interaction)
6. [Particles and Visual Effects](#particles-and-visual-effects)
7. [Lighting](#lighting)
8. [User Controls and Interaction](#user-controls-and-interaction)
9. [Simulation Loop](#simulation-loop)
10. [Helper Functions](#helper-functions)
11. [Physics and Motion](#physics-and-motion)
12. [Conclusion](#conclusion)

---

## HTML Structure

The HTML provides the foundation for the simulation, including the canvas for rendering the 3D scene and the control panel for user interaction.

- **Canvas:** The Three.js renderer dynamically appends a `<canvas>` element to the `<body>`, serving as the surface where the 3D scene is drawn.
- **Overlays:**
  - `<div id="info">`: Displays the simulation title or brief description, positioned as an overlay on the canvas.
  - `<div id="instructions">`: Provides user interaction hints (e.g., "Hold Shift + Click to add a mass", "Scroll to zoom").
- **Control Panel (`<div id="controls">`):**
  - **Sliders:** Adjust mass values, sizes, grid detail, orbital speed, and particle count.
  - **Color Pickers:** Change the colors of the initial masses.
  - **Buttons:** Include "Pause/Resume" to toggle the simulation and "Reset" to restore the initial state.

---

## JavaScript Code Overview

The JavaScript code is executed within a `window.addEventListener('load', function() { ... })` block to ensure all resources (e.g., DOM elements, Three.js library) are loaded before the simulation starts. It initializes the 3D scene, sets up event listeners for user input, and defines functions to manage the grid, masses, particles, and simulation updates.

---

## Scene Initialization

The `init()` function establishes the core components of the Three.js environment:

- **Scene:** A container for all 3D objects (grid, masses, particles, lights). A fog effect is added with `scene.fog` to enhance depth perception by fading distant objects.
- **Camera:** A `PerspectiveCamera` is positioned above the grid (e.g., at `(0, 50, 50)`), angled downward to focus on the origin `(0, 0, 0)`. It uses a field of view (FOV) of 75 degrees and adjusts its aspect ratio based on the window size.
- **Renderer:** A `WebGLRenderer` is created with antialiasing enabled for smoother edges, and its size is set to match the window dimensions.
- **OrbitControls:** Imported from Three.js, this allows users to rotate the camera with the left mouse button and zoom with the scroll wheel.
- **Raycaster and Mouse:** A `Raycaster` and `Vector2` for mouse coordinates enable clicking interactions, such as adding new masses by detecting intersections with the grid plane.

---

## Grid Creation

The grid visualizes the fabric of space-time, deforming based on the gravitational influence of masses.

- **Function:** `createGrid()`
- **Process:**
  - **Geometry:** A `BufferGeometry` is used for efficient rendering of the grid. Points are generated in a square pattern (e.g., 50x50 units) centered at `(0, 0, 0)`.
  - **Vertices:** An array of `(x, y, z)` coordinates is computed, with `y` initially set to 0 (flat grid). These are stored in a `Float32Array` for performance.
  - **Indices:** Lines connecting adjacent points are defined to form a wireframe grid, stored as indices in another `Float32Array`.
  - **Material:** A `LineBasicMaterial` with a semi-transparent blue color (e.g., `color: 0x00aaff, opacity: 0.5`) is applied to the grid lines.
- **Result:** A `LineSegments` object is added to the scene, representing the space-time grid.
- **Performance Note:** For high-resolution grids, GPU-based shaders could replace CPU calculations to maintain smooth performance.

---

## Mass Creation and Interaction

Masses are depicted as colored spheres that distort the grid and interact gravitationally.

### Initial Masses

- **Function:** `createMasses()`
- **Details:**
  - Three initial masses are created with predefined properties:
    - **Mass 1:** Red, radius ~1, mass ~100, positioned at `(5, 0, 0)`.
    - **Mass 2:** Green, radius ~1.5, mass ~150, positioned at `(-5, 0, 5)`.
    - **Mass 3:** Blue, radius ~2, mass ~200, positioned at `(0, 0, -5)`.
  - Each mass is a `Mesh` with a `SphereGeometry` and `MeshPhongMaterial` for realistic shading.
  - Initial velocities are set to approximate circular orbits around the scene's center.
  - Stored in an array (e.g., `masses`) with additional properties like `velocity` and `massValue`.

### Adding New Masses

- **Function:** `createMass(data, index)`
- **Details:**
  - Triggered by a `Shift + Click` event, detected via raycasting to find the click position on the grid plane.
  - Creates a new `Mesh` with random radius (0.5–2), mass (50–300), and color.
  - Initial velocity is `(0, 0, 0)`, allowing gravitational forces from existing masses to influence its motion.
  - Added to the `masses` array and scene.

---

## Particles and Visual Effects

A particle system creates a starry background for visual enhancement.

- **Function:** `createParticles()`
- **Details:**
  - Generates `n` particles (default: 500) randomly within a spherical volume (radius ~100).
  - Uses a `BufferGeometry` with vertex positions stored in a `Float32Array`.
  - Applies a `PointsMaterial` with a small circular texture (e.g., a glowing dot) and additive blending for a bright, starry effect.
  - Resulting `Points` object is added to the scene.

- **Function:** `updateParticlesPosition()`
  - Rotates particles slowly around the Y-axis (e.g., 0.001 radians per frame).
  - Adds slight vertical oscillation (e.g., `sin(time) * 0.1`) for a dynamic, floating effect.

---

## Lighting

Lighting enhances the scene's depth and realism.

- **Function:** `addLights()`
- **Details:**
  - **Ambient Light:** A soft `AmbientLight` (e.g., `color: 0x404040`) illuminates all objects evenly.
  - **Directional Light:** A `DirectionalLight` (e.g., `color: 0xffffff, intensity: 1`) mimics sunlight, positioned at `(50, 50, 50)` and casting shadows.
  - **Point Lights:** Two `PointLights` (e.g., red and blue) at opposite corners add colored highlights and depth.
  - **Hemisphere Light:** A `HemisphereLight` (e.g., sky: 0xaaaaFF, ground: 0x555555) provides a subtle gradient glow.

---

## User Controls and Interaction

The control panel enables real-time parameter adjustments.

- **Mass Sliders:** Modify `massValue` for the initial three masses (range: 50–500), affecting grid distortion and gravitational forces.
- **Color Pickers:** Update the `material.color` of the initial masses.
- **Size Sliders:** Scale the `radius` of the initial masses (range: 0.5–3).
- **Grid Detail Slider:** Changes grid resolution (10–100), regenerating the grid with `createGrid()`.
- **Speed Slider:** Adjusts the simulation time step (e.g., 0.001–0.05), controlling physics update speed.
- **Particles Slider:** Sets the number of particles (100–2000), regenerating them with `createParticles()`.
- **Pause/Resume Button:** Toggles a `paused` flag to halt/resume the simulation loop.
- **Reset Button:** Calls `resetSimulation()` to restore initial conditions.

---

## Simulation Loop

The `animate()` function runs continuously using `requestAnimationFrame`:

- **Steps (if not paused):**
  - Updates mass positions and rotations via `updateMassPositions()`.
  - Updates particle positions via `updateParticlesPosition()`.
  - Recalculates grid vertex heights based on mass positions.
- **Rendering:**
  - Updates `OrbitControls` for camera movement.
  - Calls `renderer.render(scene, camera)` to draw the scene.

---

## Helper Functions

Utility functions support interactivity and maintenance:

- **onWindowResize:** Updates camera aspect ratio and renderer size when the window is resized.
- **onMouseMove:** Tracks mouse position in normalized coordinates for raycasting.
- **onMouseDown:** Detects `Shift + Click` to add a new mass at the raycasted grid position.
- **onKeyDown/onKeyUp:** Toggles a `shiftPressed` flag to enable mass-adding mode.
- **resetSimulation:** Clears extra masses, repositions initial masses, resets velocities, and regenerates the grid and particles.

---

## Physics and Motion

Simplified gravitational physics drives mass interactions.

- **Function:** `updateMassPositions()`
- **Details:**
  - **Gravity Calculation:** For each pair of masses, computes force using Newton’s law: `F = G * (m1 * m2) / r^2`, where `G` is a small constant (e.g., 0.1) and `r` is the distance between masses.
  - **Acceleration:** `a = F / m` updates each mass’s `velocity` vector.
  - **Position Update:** `position += velocity * dt`, where `dt` (e.g., 0.01) is the time step.
  - **Grid Alignment:** Sets each mass’s `y` position to `getGridHeightAt(x, z)` to sit on the distorted grid.
  - **Rotation:** Adds visual spin (e.g., `rotation.y += 0.02; rotation.x += 0.01`).

- **Function:** `getGridHeightAt(x, z)`
  - Sums gravitational influence of all masses: `height = -∑(m / (r + ε))`, where `ε` prevents division by zero, and `r` is the distance from `(x, z)` to each mass.

---

## Conclusion

This simulation leverages Three.js for 3D rendering and basic physics to visualize space-time curvature interactively. Users can manipulate masses, adjust settings, and observe gravitational effects on a deformable grid. The code is modular and extensible, offering opportunities for enhancements like:

- **Dynamic Controls:** Add sliders for newly created masses.
- **Motion Trails:** Draw paths of moving masses.
- **Textures:** Apply planetary textures to masses.
- **Optimization:** Implement shaders or spatial partitioning for scalability.
