---
name: electronics-engineering
description: Comprehensive guidance for designing, validating, and routing electromechanical systems. Use when (1) designing circuits for motors, actuators, or switches; (2) performing 3D physical wire routing with waypoints and splines; (3) validating electrical connectivity (shorts, overcurrent) and physical clearances (wire-mesh intersection).
---

# Electronics Engineering Skill

## Overview

This skill enables the integrated design of electronics and mechanics. It bridges the gap between mechanical mechanisms (motors) and the electrical systems (PSU, circuits, wiring) required to drive them.

## Core Workflows

### 1. Circuit Design (Logical Netlist)
Design circuits using the `ElectronicsSection` model. Support components include:
- **Motors**: (Rated Voltage, Stall Current). Map terminals as `+`, `-`.
- **PSUs**: (DC Voltage, Max Current). Standard nodes: `supply_v+`, `0`.
- **Switches/Relays**: (Gated control). Map terminals as `in`, `out`.

**See [references/circuit_patterns.md](references/circuit_patterns.md) for implementation examples.**

### 2. Physical 3D Wire Routing
Wires are modeled as physical 3D splines/arcs defined by waypoints.
- **Tools**: Use `shared.wire_utils.route_wire`.
- **Waypoints**: Must avoid moving parts and maintain static clearance (>2mm).
- **Physicality**: Simulation detects wire tears (high tension) and clearance violations (collisions).

**See [references/routing_best_practices.md](references/routing_best_practices.md) for routing strategies.**

### 3. Verification & Validation
- **Electrical**: PySpice is used to detect short circuits (I > 100x rating) and overcurrent.
- **Physical**: `check_wire_clearance` detects path-geometry intersections.
- **Gating**: Motors operate ONLY if the circuit state `is_powered(motor_id, t)` is active.

## Implementation Details

### Boilerplate Setup
Use the following imports and structure when creating new electromechanical scripts:
**See [assets/electronics_boilerplate.py](assets/electronics_boilerplate.py)**

### Key Rules
1. **Backward Compatibility**: Mechanisms without defined electronics are "powered by default."
2. **Terminal Mapping**: Ensure `from_terminal` and `to_terminal` correctly resolve to the intended component and node.
3. **Safety First**: Always check `max_current_a` of the PSU against the sum of all load stall currents.

## Summary of Tools
- `shared.circuit_builder.build_circuit_from_section`: Generates PySpice Netlist.
- `shared.wire_utils.route_wire`: Defines physical and logical wire properties.
- `shared.wire_utils.check_wire_clearance`: Validates path against assembly geometry.
