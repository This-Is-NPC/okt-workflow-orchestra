---
name: okt-task-debrief
description: Capture learnings from completed work — decisions that held, assumptions that broke.
schema_version: 2
role_affinity:
  - Scribe
  - Reviewer
command:
  name: okt-task-debrief
  next:
    - name: okt-task-document
      context: bare
      when: The learnings change documented behaviour
---
Record what finished work taught — learnings, not a list of what was done. Read-only for code.

1. **Distill** the decisions that held, the assumptions that broke and the follow-ups worth carrying.
2. **Persist** with `okt comment add <id> -a agent -b "<debrief>"` on the task or project that owns the work.

**Next:** `okt-task-document`.
