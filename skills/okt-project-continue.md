---
name: okt-project-continue
description: Warm-resume the project from the last session — pick up the open thread.
schema_version: 2
role_affinity:
  - Concierge
command:
  name: okt-project-continue
  next:
    - name: okt-task-continue
      context: bare
      when: The handoff names an open task
---
Resume the project from the last session — a known thread, not a cold scan.

1. **Read** the latest handoff (`okt comment list --scope project --kind handoff`), `okt project overview` and `okt list`.
2. **Report** what changed since that handoff and the immediate next move.

**Next:** `okt-task-continue <id>`.
