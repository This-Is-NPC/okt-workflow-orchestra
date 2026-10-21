---
name: okt-task-continue
description: Read a task's checkpoint before resuming work.
schema_version: 2
role_affinity:
  - Builder
command:
  name: okt-task-continue
  next:
    - name: okt-task-implement
      context: bare
      when: The next increment is clear
---
Read where a task stopped. Orientation only — do not start coding.

1. **Read:** `okt task continue <id>`.
2. **Report** the last decision, the open questions and the next increment.

**Next:** `okt-task-implement <id>`.
