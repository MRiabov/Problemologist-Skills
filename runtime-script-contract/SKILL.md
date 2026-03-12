---
name: runtime-script-contract
description: Canonical import and execution contract for authored benchmark and engineer scripts. Use when writing `script.py` or solution scripts that call validation, simulation, or submission helpers.
---

# Runtime Script Contract

Use this skill when authoring build123d scripts that the runtime will execute or inspect.

## Canonical imports

Use top-level `utils` imports in authored scripts:

```python
from utils.submission import validate, simulate, submit_for_review
from utils.metadata import PartMetadata, CompoundMetadata
```

`shared.*` paths are implementation internals. Do not use them in authored agent scripts.

For ordinary mechanical engineer scripts, keep imports minimal:

- default to `build123d` plus `utils.metadata`
- add other `utils.*` imports only when the final geometry or task contract actually uses them
- do not leave unused helper imports in the final file

## Canonical file contract

`script.py` is the primary authored deliverable.

- Prefer exposing the final object at module scope as `result = ...`.
- `build()` remains an optional compatibility helper.
- Keep the primary geometry and assembly definition in `script.py`; do not split core authored state into scratch verifier files.
- For planner/coder handoffs, `script.py` should implement the full required assembly for that turn, not just an initial subset of parts.
- For routine passive/freestanding mechanical handoffs, draft `script.py` from the planner files first; do not pause to reread general CAD references unless you hit a concrete modeling blocker.

## Final assembly exposure

The runtime accepts either of these patterns:

1. Bind the final build123d object at module scope, for example `result = benchmark`.
2. Define `build()` and return the final build123d object.

`build()` is optional compatibility, not the preferred contract.

## Placement contract

Place top-level authored parts with `Location(...)` through one of these patterns:

- `part.move(Location(...))`
- `part.moved(Location(...))`
- `part.location = Location(...)`

Do not rely on `part.position = (...)` as the runtime placement contract for authored assemblies.

## Execution modes

Choose the narrowest mode that matches the task contract.

### Import-safe mode

Use when the workspace, validator, or seeded handoff expects the module to be imported safely.

- expose `result` or `build()`
- avoid validation/simulation/submission side effects on import
- keep the file reusable by external validators

### Script-execution mode

Use only when the task explicitly allows direct execution side effects from the authored script.

- helper calls such as `validate(...)`, `simulate(...)`, and `submit_for_review(...)` may live in the module body only when that execution model is explicitly part of the task
- do not add wrapper files just to trigger those helpers

## Contract boundary

- This skill defines the authored-script contract, not a preferred shell tactic.
- Prompts should not hardcode `python -c`-style workflows here.
- Validation should operate on the real `script.py` artifact rather than alternate reconstructed geometry.
- For engineer or benchmark self-checks, prefer the runtime helper tools that operate on `script.py` (`validate(...)`, `validate_and_price(...)`, `preview_design(...)`, `simulate(...)`, `submit_for_review(...)`) instead of raw shell import probes such as `python -c "from script import result ..."`.
- `execute_command(...)` already runs from the seeded workspace root. Do not prepend `cd /workspace` or other host-specific workspace paths before those checks.

## Safety rules

1. Do not use `if __name__ == "__main__":` in authored scripts.
2. Use workspace-relative paths such as `script.py`, `todo.md`, and `renders/preview.png`.
3. Do not add a leading slash to workspace artifacts. Use `script.py`, `journal.md`, `todo.md`, `renders/...`, and `simulation_result.json`, not `/script.py`, `/journal.md`, `/todo.md`, `/renders/...`, or `/simulation_result.json`.
4. Keep long operational guidance in skills like this one rather than copying it into planner/coder prompts.
5. Keep authored scripts terse. Avoid long module docstrings, banner comment blocks, and other decorative prose in files that will be emitted through `write_file(...)`.
6. If a tool call would require a very large or quote-heavy `content` string, simplify the script content first so the `write_file(...)` arguments remain valid JSON.
7. If `write_file("script.py", ...)` is rejected for invalid JSON arguments, do not retry the same large payload. Write a compact import-safe stub first, then expand the file with smaller `edit_file(...)` replacements.
8. Do not import visualization-only helpers such as `ocp_vscode`, and do not call `show(...)` from authored workspace scripts.
9. Updating `todo.md` is not a reason to stop early. Finish the required assembly unless a concrete blocker forces a partial handoff or refusal.
10. For simple freestanding/passive mechanical seeds, start with the fewest primitives and booleans that satisfy the planner handoff. Avoid decorative fillets, chamfers, and optional detailing unless functionally required.
11. For simple passive seeds, the normal read set is the planner handoff plus `objectives.yaml`. Do not treat general CAD skills or large helper references as mandatory pre-reads before the first draft.
