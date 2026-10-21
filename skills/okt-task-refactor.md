---
name: okt-task-refactor
description: Apply one behavior-preserving structural improvement with the suite green throughout.
schema_version: 2
role_affinity:
  - Builder
  - Reviewer
command:
  name: okt-task-refactor
  next:
    - name: okt-task-check
      context: bare
      when: The transformation is applied
---
Improve structure without changing behaviour. One transformation per pass.

1. **Name** one smell and the refactoring that removes it (Extract Function, Move Method…).
2. **Apply** it with the narrowest tests green before and after; no feature work rides along.
3. **Record** it with the filled `okt template show comment-refactor-opportunities`, or `okt progress record --task-id <id> --comment "<change>"`.

**Next:** `okt-task-check`.
