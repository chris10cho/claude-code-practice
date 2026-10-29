# 2-Link Robot Arm

A 2D two-link arm simulator. One HTML file, no libraries. Open `index.html` in a browser.

- **Sliders:** joint 1 (-180° to 180°), joint 2 (-150° to 150°)
- **Inputs:** link lengths L1 and L2 (defaults 150 and 100)
- **Readout:** live end-point X and Y, with the base at (0, 0), X right, Y up
- **Reach shading:** the blue ring is every point the arm can reach (dashed edges). Outer radius = L1 + L2 (arm straight). Inner radius = √(L1² + L2² + 2·L1·L2·cos 150°), where joint 2 is bent to its limit. Joint 1 spins a full 360°, so the reach is a complete ring with a hole in the middle.
- **Warning:** shows when joint 2 is 0°, i.e. the arm is fully stretched and on the outer edge of the ring.
- **Click to move:** click the canvas and the end point moves there (about half a second). If the point is outside the ring, the arm stays put and a red message says how far away it is and what the arm can reach.
- **Play:** your last two reachable clicks become points A (older) and B (newer), drawn as labelled markers. Press **Play** and the arm glides to A, then the end point travels A → B → A → B… until you press **Stop**. Clicking the canvas or touching a slider also stops it.
  - The end point moves in a straight line, solving the inverse kinematics at every frame with the same elbow bend throughout.
  - If the straight line would cut through the hole around the base, the arm can't follow it, so it moves both joints together instead and says so.
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

## Click to move (inverse kinematics)

Going the other way, from a target (x, y) to angles. With d² = x² + y²:

```
cos θ2 = (d² − L1² − L2²) / (2·L1·L2)
θ1     = atan2(y, x) − atan2(L2·sin θ2, L1 + L2·cos θ2)
```

`cos θ2` outside the range -1 to cos 150° means the point is out of reach. Otherwise there are two answers, θ2 = +acos and θ2 = −acos (elbow one way or the other). The app picks the one that needs the least joint motion.

## Notes

- Y is flipped when drawing, since canvas Y points down.
- If L1 + L2 is too long for the canvas, the drawing scales down. The X/Y readout stays in real units.
