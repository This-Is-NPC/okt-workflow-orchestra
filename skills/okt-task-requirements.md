---
name: okt-task-requirements
description: Capture functional + non-functional requirements and explicit acceptance criteria.
schema_version: 2
role_affinity:
  - Ideator
  - Tester
command:
  name: okt-task-requirements
  next:
    - name: okt-task-prioritize
      context: bare
      when: The baseline is recorded
---
Record what the solution must satisfy. You record the baseline; you do not author the task.

1. **Elicit** functional and non-functional requirements, edge cases and testable acceptance criteria; separate must-have from nice-to-have.
2. **Persist** with `okt comment add`: on the task when it exists, `--scope project` before tasks exist.

**Next:** `okt-task-prioritize`.
