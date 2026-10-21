---
name: Comment Decomposition
description: Plan module map — fills the `#decomposition` plan gate required before any task of the plan starts.
entity: comment
default: comment-decomposition
laws:
  - template-fidelity
---
| Module | Task | Owns (files) | Provides | Consumes | Wave |
| --- | --- | --- | --- | --- | --- |
| <cohesive module> | <task id> | <paths only this task writes> | <interfaces it exposes> | <interfaces it relies on> | <n> |

**Shared knowledge** — <values or rules used by several modules, and the one module that owns each>

**Why this split** — <the smallest safe decomposition; if one task is best, say why>
