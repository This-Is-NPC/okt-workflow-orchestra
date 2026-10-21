---
name: okt-task-prioritize
description: Rank the work against alternatives with an explicit scoring method and rationale.
schema_version: 2
role_affinity:
  - Owner
  - Ideator
command:
  name: okt-task-prioritize
  next:
    - name: okt-task-create
      context: bare
      when: The ranking is recorded
---
Rank candidates with a stated method. Read-only for code and task bodies.

1. **Score** with MoSCoW, RICE or value against effort.
2. **Persist** the filled `okt template show comment-moscow` (or `comment-rice-score`) with `okt comment add`: on the task when it exists, `--scope project` before tasks exist.

**Next:** `okt-task-create`.
