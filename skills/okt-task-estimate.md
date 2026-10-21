---
name: okt-task-estimate
description: Size each increment with a relative estimate and a one-line basis-of-estimate.
schema_version: 2
role_affinity:
  - Owner
  - Builder
command:
  name: okt-task-estimate
  next:
    - name: okt-task-design
      context: bare
      when: Sizes are recorded
---
Size each slice. Read-only.

1. **Estimate** each slice in points or t-shirt sizes, with a one-line basis that can be questioned.
2. **Flag** the slices whose uncertainty dominates.
3. **Persist** with `okt comment add <id> -a agent -b "<estimates>"`.

**Next:** `okt-task-design`.
