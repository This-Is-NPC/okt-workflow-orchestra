---
name: okt-task-validate
description: Pressure-test the problem framing — is it real, worth solving now, evidence-backed?
schema_version: 2
role_affinity:
  - Ideator
  - Reviewer
command:
  name: okt-task-validate
  next:
    - name: okt-task-requirements
      context: bare
      when: The framing survived
---
Try to break the problem framing before it becomes requirements. Scrutiny, not commitment.

1. **Challenge** it: is the problem real, worth solving now, and what evidence shows the demand?
2. **Name the falsifiers** — the observations that would prove the framing wrong — and the risks.
3. **Record** them with `okt comment add` (on the task, or `--scope project`).

**Next:** `okt-task-requirements`.
