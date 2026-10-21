---
name: Comment Scope
description: Task scope — fills the `#scope` guard required to move backlog → dev; the decomposition the task commits to.
entity: comment
default: comment-scope
laws:
  - template-fidelity
---
**Module** — <the one cohesive module this task delivers>

**Owns** — <files only this task writes; parallel tasks never touch them>

**Interfaces** — consumes: <signatures/contracts it relies on> · provides: <signatures/contracts it exposes>

**Data shape** — <the domain type or structure the logic is organised around>

**Parallelism** — blocking first: <…> · independent of: <tasks> · shared state: <none | what and how it is split or serialised>

**Done signal** — <observable result and the narrowest test that proves it>

**Principles applied** — <principle → the choice it changed>

Write `n/a: <reason>` for a field that does not apply; never drop it.
