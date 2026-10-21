---
name: okt-plan-claim
description: Atomically reserve the next claimable task in the plan's active wave.
schema_version: 2
role_affinity:
  - Owner
  - Builder
command:
  name: okt-plan-claim
  next:
    - name: okt-task-continue
      context: bare
      when: A task was claimed
---
Claim the next task in a plan's active wave.

1. **Claim:** `okt plan claim <slug>` (needs `OMAKITEN_AGENT_MODEL`). It assigns the task; it does not move it.
2. **Report** the claimed id, or `claimed=false` when the active wave has nothing left to claim.

**Next:** post `#self-branch`, `okt move <id> --to dev`, then `okt-task-continue <id>`.
