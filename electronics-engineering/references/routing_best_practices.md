# 3D Wire Routing Best Practices

## 1. Waypoint Selection
- **Attachment Points**: Ensure the first and last waypoints are physically on or near the component terminals.
- **Support**: Place waypoints along structural frames to simulate "clipping" or "ziptieing" wires.
- **Clearance**: Maintain at least 2mm distance from static parts and 10mm from moving parts.

## 2. Avoiding Moving Mechanisms
- **Pathing**: Identify the bounding box of moving parts (belts, gears, shears) and route wires outside this volume.
- **Service Loops**: If a wire must connect to a moving part, provide "slack" waypoints to prevent `FAILED_WIRE_TORN` events.

## 3. Gauge Selection (AWG)
- **18-20 AWG**: Standard for small DC motors (up to 15A).
- **14-16 AWG**: For high-power actuators or long runs.
- **22-26 AWG**: Signal level or low-current switches.

## 4. Verification
- Use `check_wire_clearance` to detect intersections with assembly geometry.
- If a wire path is too tight, the simulation will detect high tension and fail.
