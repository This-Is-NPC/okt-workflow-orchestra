---
name: okt-project-resume
description: Scan likely-next work across the active project.
schema_version: 2
role_affinity:
  - Concierge
command:
  name: okt-project-resume
  next:
    - name: okt-task-continue
      context: bare
      when: The user picked a candidate
---
Find the next work to pick up — a cold scan of the project.

1. **Scan:** `okt project resume`.
2. **Report** the top candidates, each with one line on why.

**Next:** `okt-task-continue <id>`.
