---
name: okt-plan-show
description: Inspect one plan — wave layout, done/total counts, percent, and the active wave.
schema_version: 2
role_affinity:
  - Owner
  - Concierge
command:
  name: okt-plan-show
  next:
    - name: okt-plan-continue
      context: bare
      when: The plan has pending work
    - name: okt-plan-claim
      context: bare
      when: The active wave has a claimable task
---
Show one plan's structure. Read-only.

1. **Read:** `okt plan show <slug>`.
2. **Report** the waves, done/total per wave and overall, the percent complete and the active wave (the first with pending work).
