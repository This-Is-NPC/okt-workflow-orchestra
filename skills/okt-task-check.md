---
name: okt-task-check
description: Run discovered test/lint targets and report pass/fail in a tabular comment.
schema_version: 2
role_affinity:
  - Tester
command:
  name: okt-task-check
  next:
    - name: okt-task-implement
      context: bare
      when: A target failed
    - name: okt-task-review
      context: bare
      when: Every target passed
---
Run the project's checks and report. You run and report; you never fix.

1. **Find the targets.** `mise tasks` first; then `npm run`, `make -qp`, `package.json` scripts or `CONTRIBUTING.md`. Stop at the first hit; do not guess.
2. **Run each target once** and capture stdout, stderr and the exit code.
3. **Report.** Fill `okt template show comment-check-report`: one row per target (`pass`, `fail`, `skip`, `yellow`), and the last ≤10 lines of each failure verbatim. Post it with `okt comment add <id> -a agent -b "<report>"`.

**Flaky targets.** A target that fails under load or timing is rerun once. If the result differs, record it as its own task; never bypass it.

**Next:** `okt-task-implement` for failures, `okt-task-review` when green.
