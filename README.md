# Spacetime Curvature Simulation

This project is a Three.js simulation that demonstrates the curvature of spacetime around a moving mass. The simulation features:
- A dynamically deformed grid representing spacetime.
- A moving sphere (mass) whose gravitational influence warps the grid.
- Particles that react to the sphere’s gravity.
- A background starfield with a parallax effect.

Below is a detailed, line-by-line explanation of every part of the JavaScript code (inside the `<script>` tag) so that you can understand and recreate the simulation exactly.

---

## Prerequisites

- Basic knowledge of HTML, CSS, and JavaScript.
- [Three.js](https://threejs.org/) library (loaded via CDN).
- OrbitControls (from Three.js examples) for interactive camera control.

---

## Code Breakdown

### 1. Basic Setup

```js
// --- Basic Setup ---
const container = document.getElementById("container");
```
- **Purpose:** Retrieves the HTML element with the ID `container` where the WebGL canvas will be inserted.

```js
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x000011);
```
- **Purpose:** 
  - Creates a new Three.js scene to hold all 3D objects.
  - Sets the scene’s background color to a deep blue (`#000011`).

```js
const camera = new THREE.PerspectiveCamera(
  75, 
  window.innerWidth / window.innerHeight, 
  0.1, 
  1000
);
camera.position.set(0, -20, 15);
```
- **Purpose:** 
  - Creates a perspective camera with a 75° field of view.
  - Sets the aspect ratio to match the window’s dimensions.
  - Defines the near (0.1) and far (1000) clipping planes.
  - Positions the camera at `(0, -20, 15)` to provide an angled view of the scene.

```js
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
container.appendChild(renderer.domElement);
```
- **Purpose:** 
  - Instantiates a WebGL renderer with antialiasing enabled for smoother edges.
  - Sets the renderer to fill the entire browser window.
  - Appends the renderer’s canvas element to the `container` in the HTML.

---

### 2. Lighting

```js
// --- Lighting ---
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
directionalLight.position.set(1, 1, 1).normalize();
scene.add(directionalLight);
```
- **Purpose:** 
  - Creates a directional light with white color (`0xffffff`) and an intensity of `0.8`.
  - Positions and normalizes the light’s vector so that it shines from the `(1, 1, 1)` direction.
  - Adds the light to the scene.

```js
const ambientLight = new THREE.AmbientLight(0x111111);
scene.add(ambientLight);
```
- **Purpose:** 
  - Creates an ambient light with a very low intensity (`0x111111`) to softly illuminate the entire scene.
  - Adds the ambient light to the scene.

---

### 3. Orbit Controls

```js
// --- Orbit Controls ---
const controls = new THREE.OrbitControls(camera, renderer.domElement);
controls.target.set(0, 0, 0);
controls.update();
```
- **Purpose:** 
  - Instantiates OrbitControls to allow interactive camera movement (orbiting, panning, and zooming).
  - Sets the focal point (target) of the controls to the center of the scene `(0, 0, 0)`.
  - Updates the controls to apply the new target.

---

### 4. Stars

```js
// --- Stars ---
const stars = [];
const numStars = 500;
const starGeometry = new THREE.SphereGeometry(0.02, 8, 8);
const starMaterial = new THREE.MeshBasicMaterial({ color: 0xffffff });
```
- **Purpose:** 
  - Initializes an empty array `stars` to hold star objects.
  - Sets the total number of stars to `500`.
  - Defines a small sphere geometry for each star (radius `0.02`, with low detail).
  - Uses a basic white material (`MeshBasicMaterial`), ensuring stars aren’t affected by scene lighting.

```js
for (let i = 0; i < numStars; i++) {
  const star = new THREE.Mesh(starGeometry, starMaterial);
  star.position.set(
    (Math.random() - 0.5) * 100,
    (Math.random() - 0.5) * 100,
    (Math.random() - 0.5) * 100
  );
  scene.add(star);
  stars.push(star);
}
```
- **Purpose:** 
  - Loops 500 times to create individual star meshes.
  - Positions each star randomly within a cube of 100 units centered at the origin.
  - Adds each star to the scene and stores it in the `stars` array for later animation.

---

### 5. Spacetime Grid

```js
// --- Spacetime Grid ---
const gridSize = 20;
const gridSegments = 150;
const gridGeometry = new THREE.PlaneGeometry(gridSize, gridSize, gridSegments, gridSegments);
```
- **Purpose:** 
  - Defines a grid with a total size of `20` units and `150` subdivisions in both directions for high detail.
  - Uses `PlaneGeometry` to create a flat grid.

```js
const gridMaterial = new THREE.MeshPhongMaterial({
  color: 0x333333,
  wireframe: true,
  transparent: true,
  opacity: 0.8
});
```
- **Purpose:** 
  - Creates a Phong material with a dark gray color (`0x333333`).
  - Enables wireframe mode to show the grid lines.
  - Sets the material to be transparent with an opacity of `0.8`.

```js
const gridMesh = new THREE.Mesh(gridGeometry, gridMaterial);
scene.add(gridMesh);
```
- **Purpose:** 
  - Combines the grid geometry and material into a mesh.
  - Adds the grid mesh to the scene.

---

### 6. Sphere (Mass)

```js
// --- Sphere (Mass) ---
const sphereRadius = 1;
const sphereGeometry = new THREE.SphereGeometry(sphereRadius, 64, 64);
const sphereMaterial = new THREE.MeshPhongMaterial({ color: 0xffa500 });
```
- **Purpose:** 
  - Defines a sphere with a radius of `1` and high detail (`64` segments).
  - Uses an orange Phong material (`0xffa500`) for a realistic, lit appearance.

```js
const sphereMesh = new THREE.Mesh(sphereGeometry, sphereMaterial);
sphereMesh.position.set(0, 0, -4);
scene.add(sphereMesh);
```
- **Purpose:** 
  - Creates a mesh from the sphere geometry and material.
  - Positions the sphere at `(0, 0, -4)` within the scene.
  - Adds the sphere mesh to the scene to represent the mass causing gravitational effects.

---

### 7. Particles

```js
// --- Particles ---
const particles = [];
const numParticles = 200;
const particleGeometry = new THREE.SphereGeometry(0.05, 8, 8);
const particleMaterial = new THREE.MeshPhongMaterial({ color: 0xffffff });
```
- **Purpose:** 
  - Initializes an empty array `particles` to hold particle objects.
  - Sets the total number of particles to `200`.
  - Defines a small sphere geometry for each particle (radius `0.05`).
  - Uses a white Phong material so particles interact with light.

```js
for (let i = 0; i < numParticles; i++) {
  const particle = new THREE.Mesh(particleGeometry, particleMaterial);
  particle.position.set(
    (Math.random() - 0.5) * gridSize,
    (Math.random() - 0.5) * gridSize,
    0
  );
  // Store velocity in userData for clarity
  particle.userData = {
    vx: (Math.random() - 0.5) * 0.2,
    vy: (Math.random() - 0.5) * 0.2
  };
  scene.add(particle);
  particles.push(particle);
}
```
- **Purpose:** 
  - Loops 200 times to create individual particles.
  - Positions each particle randomly within the grid on the x-y plane (with z fixed at 0).
  - Assigns each particle a random velocity (both `vx` and `vy`) stored in the `userData` property.
  - Adds each particle to the scene and the `particles` array for later updates.

---

### 8. Animation Loop

```js
// --- Animation ---
const clock = new THREE.Clock();
```
- **Purpose:** 
  - Creates a clock to measure elapsed time and the time between frames (`deltaTime`), ensuring smooth and time-based animations.

```js
function animate() {
  requestAnimationFrame(animate);
  const deltaTime = clock.getDelta();
  const elapsedTime = clock.getElapsedTime();
```
- **Purpose:** 
  - Defines the `animate` function that updates and renders the scene on every frame.
  - Uses `requestAnimationFrame` to create a loop.
  - Retrieves `deltaTime` (time since the last frame) and `elapsedTime` (total elapsed time) from the clock.

#### 8.1 Sphere Orbit Motion

```js
  // Sphere Orbit Motion
  const orbitRadius = 7;
  sphereMesh.position.x = orbitRadius * Math.cos(elapsedTime * 0.7);
  sphereMesh.position.y = orbitRadius * Math.sin(elapsedTime * 0.7);
  sphereMesh.rotation.y += 0.05;
```
- **Purpose:** 
  - Causes the sphere (mass) to orbit in a circular path with a radius of `7` units.
  - Updates the x and y coordinates using cosine and sine functions of the elapsed time (multiplied by `0.7` to control the speed).
  - Rotates the sphere slightly on its y-axis (`rotation.y`) on each frame for a dynamic effect.

#### 8.2 Grid Deformation (Gravitational Curvature)

```js
  // Grid Deformation (simulate gravitational curvature)
  const positions = gridGeometry.attributes.position;
  const sphereX = sphereMesh.position.x;
  const sphereY = sphereMesh.position.y;
  for (let i = 0; i < positions.count; i++) {
    const x = positions.getX(i);
    const y = positions.getY(i);
    const dx = x - sphereX;
    const dy = y - sphereY;
    const r2 = dx * dx + dy * dy + 1;
    // Gravitational deformation effect: deeper well near the sphere
    const z = -7 / Math.sqrt(r2);
    positions.setZ(i, z);
  }
  positions.needsUpdate = true;
```
- **Purpose:** 
  - Accesses the position attribute of the grid’s vertices.
  - Retrieves the current x and y position of the moving sphere.
  - Iterates over each vertex of the grid:
    - Calculates the differences (`dx`, `dy`) between the vertex and the sphere.
    - Computes `r2` (the squared distance plus one, to avoid division by zero).
    - Calculates a new z-value using the formula `-7 / Math.sqrt(r2)`, which creates a depression in the grid near the sphere (simulating gravitational curvature).
    - Updates the vertex’s z-coordinate.
  - Flags the position attribute with `needsUpdate = true` to re-render the updated geometry.

#### 8.3 Particle Movement and Sphere Influence

```js
  // Particle Movement and Sphere Influence
  particles.forEach((particle) => {
    particle.position.x += particle.userData.vx;
    particle.position.y += particle.userData.vy;
```
- **Purpose:** 
  - Iterates over each particle and updates its x and y positions based on its current velocity.

```js
    // Calculate distance to the sphere
    const dx = particle.position.x - sphereX;
    const dy = particle.position.y - sphereY;
    const distance = Math.sqrt(dx * dx + dy * dy);
```
- **Purpose:** 
  - Computes the distance between each particle and the sphere using the Pythagorean theorem.

```js
    // If within influence radius, adjust velocity to simulate gravitational pull
    if (distance < 3) {
      const force = 0.5 / (distance + 0.1);
      particle.userData.vx -= dx * force * deltaTime;
      particle.userData.vy -= dy * force * deltaTime;
    } else {
      // Apply slight deceleration when not near the sphere
      particle.userData.vx *= 0.99;
      particle.userData.vy *= 0.99;
    }
```
- **Purpose:** 
  - Checks if the particle is within a 3-unit radius of the sphere.
    - If yes, computes a gravitational-like force (scaled inversely with distance) and adjusts the particle’s velocity accordingly.
    - If not, applies a slight deceleration (multiplies velocity by `0.99`) to simulate friction or energy loss.

```js
    // Simple boundary collision for particles
    if (particle.position.x > gridSize / 2 || particle.position.x < -gridSize / 2) {
      particle.userData.vx *= -1;
    }
    if (particle.position.y > gridSize / 2 || particle.position.y < -gridSize / 2) {
      particle.userData.vy *= -1;
    }
  });
```
- **Purpose:** 
  - Checks if a particle has moved beyond the grid boundaries.
  - If a particle exceeds the grid size along the x or y axis, its velocity in that direction is reversed, simulating a bounce.

#### 8.4 Moving Stars (Parallax Effect)

```js
  // Move Stars to Create a Parallax Effect
  stars.forEach((star) => {
    star.position.z += 0.1;
    if (star.position.z > 50) {
      star.position.z = -50;
      star.position.x = (Math.random() - 0.5) * 100;
      star.position.y = (Math.random() - 0.5) * 100;
    }
  });
```
- **Purpose:** 
  - Iterates over each star and gradually moves it along the z-axis.
  - Once a star’s z-coordinate exceeds `50`, it is reset to `-50` with a new random x and y position, creating a continuous parallax scrolling effect.

#### 8.5 Rendering and Control Update

```js
  controls.update();
  renderer.render(scene, camera);
}
```
- **Purpose:** 
  - Updates the orbit controls to reflect any user interactions.
  - Renders the current state of the scene from the perspective of the camera.

```js
animate();
```
- **Purpose:** 
  - Initiates the animation loop by calling the `animate` function.

---

### 9. Responsive Resize

```js
// --- Responsive Resize ---
window.addEventListener("resize", () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});
```
- **Purpose:** 
  - Adds an event listener to handle browser window resizing.
  - When the window is resized:
  - Updates the camera’s aspect ratio.
  - Recalculates the camera’s projection matrix.
  - Resizes the renderer to ensure the scene always fills the window.

---

## Conclusion

Happy coding!
