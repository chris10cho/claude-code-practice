# 2-Link Robot Arm

A 2D two-link arm simulator. One HTML file, no libraries. Open `index.html` in a browser.

- **Sliders:** joint 1 (-180° to 180°), joint 2 (-150° to 150°)
- **Inputs:** link lengths L1 and L2 (defaults 150 and 100)
- **Readout:** live end-point X and Y, with the base at (0, 0), X right, Y up
- **Trail:** faint line of where the end point has been (last 3000 points). "Clear trail" resets it.

## How the position is calculated (forward kinematics)

Joint 1 is measured from the +X axis. Joint 2 is **relative** to link 1, so link 2's absolute angle is θ1 + θ2.

```
elbow:  x1 = L1·cos(θ1)          y1 = L1·sin(θ1)
end:    x  = x1 + L2·cos(θ1+θ2)  y  = y1 + L2·sin(θ1+θ2)
```

Each link is a vector of its length in its absolute direction, and the end point is the sum of the two vectors.

Example: L1 = 150, L2 = 100, θ1 = 45°, θ2 = -30°
→ elbow (106.1, 106.1), link 2 angle 15°, end (202.7, 131.9).

## Notes

- Y is flipped when drawing, since canvas Y points down.
- If L1 + L2 is too long for the canvas, the drawing scales down. The X/Y readout stays in real units.
