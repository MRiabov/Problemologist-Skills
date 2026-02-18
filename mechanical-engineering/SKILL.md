---
name: mechanical-engineering
description: Domain expertise for Mechanical Engineering, including Finite Element Analysis (FEA/FEM), fluid dynamics (MPM), stress/strain analysis, material selection, and structural optimization. Use this when tasks involve structural integrity, simulation-driven design, fluids, or complex material behaviors (elastomers).
---

# Mechanical Engineering

This skill provides the procedural and domain knowledge required to design parts and systems that are structurally sound, fluid-safe, and optimized for performance.

## 1. Dynamic Mechanical Data

To get the most up-to-date material properties for FEA simulation, you MUST run the provided data script. **Do not rely on your internal knowledge or hardcoded values in this file.**

**Action**: Use `run_skill_script(skill_name="mechanical-engineering", script_name="get_mechanical_properties.py")`

## 2. Simulation-Driven Design Workflow

All mechanical designs should follow an iterative "Design-Simulate-Analyze-Refine" loop.

1. **Design:** Create initial CAD geometry using `build123d`.
2. **Simulate:** Run simulation with appropriate physics backend (`mujoco` for rigid-only, `genesis` for FEM/fluids).
3. **Analyze:** Evaluate performance using stress reports and fluid metrics.
4. **Refine:** Optimize geometry based on simulation data to reach target performance metrics.

## 2. Structural Analysis (FEA/FEM)

Use Finite Element Analysis to ensure parts do not break under load.

### Key Metrics
- **Von Mises Stress:** Primary scalar value for ductile material failure prediction.
- **Safety Factor (SF):** Target **1.5 to 5.0**.
  - **SF < 1.5:** High risk of `PART_BREAKAGE`. Add material/reinforcement.
  - **SF > 5.0:** Overdesigned. Remove material to reduce cost/weight.
- **Utilization:** Aim for **< 80%** of material capacity.

### Design Patterns
- **Fillets:** Use `fillet(radius=3.1)` on internal corners to reduce stress concentrations.
- **Ribbing:** Add ribs to increase stiffness without excessive volume.
- **Wall Thickness:** Correlate with stress. Ensure minimum thickness for structural integrity.

**Detailed Reference:** [references/fea_principles.md](references/fea_principles.md)

---

## 3. Fluid Dynamics (MPM)

Use Material Point Method simulation for mechanisms interacting with liquids.

### Fluid Objectives
- **Containment:** Ensure > 95% of particles remain in the target zone.
- **Flow Rate:** Monitor particles crossing gate planes.

### Constraints
- **Backend:** Fluids REQUIRES `physics.backend: genesis`.
- **Smoke Test:** Use `smoke_test_mode: true` (capped at 5,000 particles) for rapid iteration.

**Detailed Reference:** [references/fluid_dynamics.md](references/fluid_dynamics.md)

---

## 4. Material Selection & Properties

Match material classes to mechanical requirements.

- **Class: Rigid** (Metals, Hard Plastics): Use for load-bearing structures. Uses linear FEM.
- **Class: Soft / Elastomer** (Rubber, Silicone): Use for seals, hinges, or large-deformation parts. Uses hyperelastic (Neo-Hookean) FEM.

**Mandatory Fields:** When `fem_enabled: true`, every manufactured part must have `ultimate_stress_pa` defined in `manufacturing_config.yaml`.

---

## 5. Optimization & Integration

Mechanical requirements must be balanced with manufacturing and economic constraints.

- **Synergy with Manufacturing:** Use the `manufacturing-knowledge` skill to ensure mechanical optimizations (like ribs or fillets) remain manufacturable via CNC or IM.
- **Failure Modes:** Proactively design against `PART_BREAKAGE`, `PHYSICS_INSTABILITY`, and `FLUID_OBJECTIVE_FAILED`.

**Detailed Reference:** [references/optimization.md](references/optimization.md)
