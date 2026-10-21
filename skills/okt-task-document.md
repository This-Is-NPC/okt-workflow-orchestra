---
name: okt-task-document
description: Bring the user-facing documentation in line with what a task changed.
schema_version: 2
role_affinity:
  - Scribe
  - Reviewer
command:
  name: okt-task-document
  next:
    - name: okt-task-commit
      context: bare
      when: The docs are committed
---
Update the documentation for what the task changed. You write docs, not code.

1. **Read the change:** the task, its `#scope`, and `git diff main..HEAD` on the task branch.
2. **Update the pages a user reads** — `README.md`, `docs/` — for every behaviour, command, setting or error the change adds or alters. Facts come from the code or from running it; outputs shown are real outputs. No plan phases, okt ids, `.temp/` files or internal codenames.
3. **Commit** the docs on the task branch (`docs(<scope>): …`).
4. **Record** it with the filled `okt template show comment-documentation`: `okt comment add <id> -a agent -T documentation -b "<record>"`.

**Next:** `okt-task-commit`.
