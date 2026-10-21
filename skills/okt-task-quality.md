---
name: okt-task-quality
description: Qualitative human-lens quality read — smells, coverage, design — distinct from the mechanical gate.
schema_version: 2
role_affinity:
  - Reviewer
  - Tester
command:
  name: okt-task-quality
  next:
    - name: okt-task-refactor
      context: bare
      when: A finding is structural
    - name: okt-task-implement
      context: bare
      when: A finding changes behaviour
---
Judge quality where tools cannot: design coherence, naming, tests for the branches that matter, smells that pass the checks. Read-only.

1. **Read the diff** for those concerns only; `okt-task-check` covers pass/fail.
2. **Post** the filled `okt template show comment-review-findings` with `okt comment add <id> -a agent -b "<findings>"`.

**Next:** `okt-task-refactor` or `okt-task-implement`.
