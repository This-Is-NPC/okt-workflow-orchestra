---
name: okt-task-review
description: Walk the diff through Fowler/Beck/Martin/Feathers lens and surface findings + refactor opportunities.
schema_version: 2
role_affinity:
  - Reviewer
command:
  name: okt-task-review
  next:
    - name: okt-task-implement
      context: bare
      when: Findings need fixes
---
Review the diff. Read-only: you post findings; you never edit or commit.

1. **Read every hunk:** `git diff <base>..HEAD` (base `main` unless named).
2. **Pass in order:** correctness, security, smells, refactoring opportunities, performance. Name the method when one applies (Extract Function — Fowler).
3. **Probe the boundaries.** For each external input the change touches, derive its valid and invalid equivalence classes and test the edges of each (boundary value analysis). An internal error caused by external input is a blocking finding. Record the analysis in `#boundary` (`okt template show comment-boundary`): each class is unrepresentable by type, rejected at parse, or tested — in a functional codebase most classes should be the first.
4. **Post** the filled `okt template show comment-review-findings` and, when there are any, `comment-refactor-opportunities`, with `okt comment add <id> -a agent -b "<findings>"`.

**Next:** `okt-task-implement <id>` to apply the fixes.
