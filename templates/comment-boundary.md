---
name: Comment Boundary
description: Input-domain analysis — fills the `#boundary` guard required to move review → done.
entity: comment
default: comment-boundary
laws:
  - template-fidelity
---
One row per input class at each boundary the change touches (equivalence partitioning + boundary values). Start at the outermost layer (transport, encoding) before the domain fields, and include the response sent when an input is rejected.

| Boundary | Input class | Handled by | Evidence |
| --- | --- | --- | --- |
| <entry point> | <valid / invalid / edge class> | type (unrepresentable) · parse (rejected at the boundary) · test | <type or parser name, or test name> |

**Unhandled** — <classes with no handling, as findings; "none">

Write `n/a: <reason>` when the change touches no external input.
