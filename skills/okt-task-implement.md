---
name: okt-task-implement
description: Execute approved implementation work with strict rigor and commit discipline.
schema_version: 2
role_affinity:
  - Builder
  - Committer
command:
  name: okt-task-implement
  next:
    - name: okt-task-self-review
      context: bare
      when: The increment is done and its evidence posted
---
Build the next increment of a claimed task.

1. **Load state** with `okt task continue <id>` unless you already have it. If the task has no `#self-branch` comment, post it and `okt move <id> --to dev`.
2. **Build** the increment. Prove it with the narrowest test that covers it; run the full check once at the end — or not at all when the user deferred testing.
3. **Record** material steps with `okt progress record --task-id <id> --comment "<step>"`. A defect outside the task becomes `okt task create`, linked in a comment — never a fix in this diff.
4. **Hand back.** Post `#resume` (fill `okt template show comment-resume`) and `#tests-passing` (fill `okt template show comment-tests-passing`), then `okt move <id> --to review`. With deferred testing, post `#resume` only and leave the task in dev.

**Stop when** the third fix attempt fails: report per the bounded-self-review law.

**Next:** `okt-task-self-review <id>`.
