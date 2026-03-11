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

## Canonical file contract

`script.py` is the primary authored deliverable.

- Prefer exposing the final object at module scope as `result = ...`.
- `build()` remains an optional compatibility helper.
- Keep the primary geometry and assembly definition in `script.py`; do not split core authored state into scratch verifier files.

## Final assembly exposure

The runtime accepts either of these patterns:

1. Bind the final build123d object at module scope, for example `result = benchmark`.
2. Define `build()` and return the final build123d object.

`build()` is optional compatibility, not the preferred contract.

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

## Safety rules

1. Do not use `if __name__ == "__main__":` in authored scripts.
2. Use workspace-relative paths such as `script.py`, `todo.md`, and `renders/preview.png`.
3. Keep long operational guidance in skills like this one rather than copying it into planner/coder prompts.
