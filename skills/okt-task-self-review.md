---
name: okt-task-self-review
description: Author's own pre-handoff diff pass — distinct from the third-party review.
schema_version: 2
role_affinity:
  - Builder
  - Reviewer
command:
  name: okt-task-self-review
  next:
    - name: okt-task-review
      context: bare
      when: Your own findings are fixed or posted
---
Review your own diff before anyone else does.

1. **Read every hunk** of `git diff <base>..HEAD`: dead code, debug output, missing tests, unhandled edges, and the gap between what you meant and what you changed.
2. **Fix the trivial** at once and record it with `okt progress record --task-id <id> --comment "<fix>"`.
3. **Post the rest** with `okt comment add <id> -a agent -b "<findings>"`; do not bury them.

**Next:** `okt-task-review <id>`.
