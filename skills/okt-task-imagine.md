---
name: okt-task-imagine
description: PLAN phase — interrogate the user via 5W2H and frame success in SMART terms before any task exists.
schema_version: 2
role_affinity:
  - Ideator
  - Concierge
command:
  name: okt-task-imagine
  next:
    - name: okt-task-create
      context: bare
      when: The 5W2H answers are persisted
---
Discover the work before any task exists. Exploration is free here; template fidelity is off until the notes are written.

1. **Read the board:** `okt project overview` and `okt list`.
2. **Ask the 5W2H questions** one at a time and refuse vague answers.
3. **Persist** when the user commits to answers: fill `okt template show comment-5w2h` and `okt template show comment-smart-success`, then `okt comment add --scope project -a agent -b "<notes>"`.

**Next:** `okt-task-create`.
