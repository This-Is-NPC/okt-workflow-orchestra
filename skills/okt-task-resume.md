---
name: okt-task-resume
description: Cold-start a task from scratch — rebuild full context when none is loaded in this session.
schema_version: 2
role_affinity:
  - Builder
command:
  name: okt-task-resume
  next:
    - name: okt-task-implement
      context: bare
      when: The next increment is clear
---
Rebuild a task's context from nothing.

1. **Read:** `okt task continue <id>` and `okt comment list <id>` — the description, every comment, the dependencies, the latest `#resume` and `#tests-passing`.
2. **Report** the current state, the open questions and the next increment, derived from those artifacts only.

**Next:** `okt-task-implement <id>`.
