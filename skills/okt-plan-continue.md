---
name: okt-plan-continue
description: Preview a plan plus the next claimable task before committing to a claim.
schema_version: 2
role_affinity:
  - Owner
  - Concierge
command:
  name: okt-plan-continue
  next:
    - name: okt-plan-claim
      context: bare
      when: The previewed task should be taken
---
Preview a plan before claiming. Read-only.

1. **Preview:** `okt plan continue <slug>` — waves, done/total, the active wave and the task a claim would take next.
2. **Report** the goal, the wave layout and that candidate.

**Next:** `okt-plan-claim <slug>`.
