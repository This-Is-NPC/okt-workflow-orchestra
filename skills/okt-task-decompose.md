---
name: okt-task-decompose
description: Break a coarse task into right-sized, independently shippable increments.
schema_version: 2
role_affinity:
  - Owner
  - Builder
command:
  name: okt-task-decompose
  next:
    - name: okt-task-estimate
      context: bare
      when: The breakdown is agreed
---
Split a coarse task into slices that ship on their own.

1. **Find the seams:** slices that are independently shippable, each with its own acceptance criteria.
2. **Order them:** name the dependencies between slices so they can become waves.
3. **Propose** the breakdown to the user; create subtasks (`okt task create --parent <id>`) only once it is agreed.

**Next:** `okt-task-estimate`.
