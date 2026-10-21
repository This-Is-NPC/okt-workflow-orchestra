---
name: okt-task-create
description: PLAN → DO handoff — author the task with an INVEST-checked story; record prioritization when alternatives exist.
schema_version: 2
role_affinity:
  - Ideator
  - Owner
command:
  name: okt-task-create
  next:
    - name: okt-task-implement
      context: bare
      when: The task is in dev
---
Author a task. Creation only — building it is `okt-task-implement`.

1. **Feasibility first.** If the work cannot be built in the current architecture, report why and stop (feasibility-gate law).
2. **Fill the scaffold:** `okt template show user-story` (`task-bugfix` for a defect), INVEST-checked, with a SMART success definition.
3. **Create:** `okt task create -t "<title>" --file -` with the filled body.
4. **Duplicates.** When the response carries `similar_tasks`, show them to the user and let them choose. Pass `--confirm` only after they confirm it is new.

**Next:** post `#scope` (fill `okt template show comment-scope`), `#self-branch`, `okt move <id> --to dev`, then `okt-task-implement <id>`.
