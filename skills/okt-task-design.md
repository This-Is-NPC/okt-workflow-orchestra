---
name: okt-task-design
description: Shape the solution — approach, seams, interfaces — and weigh an alternative before coding.
schema_version: 2
role_affinity:
  - Builder
  - Reviewer
command:
  name: okt-task-design
  next:
    - name: okt-task-implement
      context: bare
      when: The design decision is recorded
---
Settle the approach before writing code. No production edits here.

1. **Sketch** the data flow, the seams you touch and the interfaces you add; weigh one real alternative.
2. **Record** the decision and why it beat the alternative with `okt comment add <id> -a agent -T decision -b "<design>"`.

**Next:** `okt-task-implement <id>`.
