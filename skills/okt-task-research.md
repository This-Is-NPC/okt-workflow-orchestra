---
name: okt-task-research
description: Investigate the problem space and map the unknowns before any solution is committed.
schema_version: 2
role_affinity:
  - Ideator
  - Reviewer
command:
  name: okt-task-research
  next:
    - name: okt-task-validate
      context: bare
      when: The decision is recorded
---
Answer one question before building. Read-only for code.

1. **State the question** and the observation that would answer it.
2. **Read the primary source** — the code, the docs, `okt search <query>`, `okt list` — so you do not re-solve a solved problem.
3. **Run one decisive experiment** if reading cannot answer it; measure once and reuse the number.
4. **Record the decision** in three to five lines — the answer, the evidence and what is still open — with `okt comment add` (on the task, or `--scope project`). No catalogue of rejected options.

**Next:** `okt-task-validate`.
