---
name: okt-plan-create
description: Author a WBS-style plan grouping child tasks into ordered waves with a goal body.
schema_version: 2
role_affinity:
  - Owner
command:
  name: okt-plan-create
  next:
    - name: okt-plan-show
      context: bare
      when: Verify the assembled plan
    - name: okt-run
      context: bare
      when: The plan is verified
---
Create a plan that orders tasks into waves. The plan is the execution skeleton: it is complete only when its waves, assignments and dependencies are persisted.

1. **Identity.** A kebab-case slug unique in the project, a name, and the goal body filled from `okt template show plan-goal` (outcome, scope, quality attributes with their verification, out of scope): `okt plan create`.
2. **Waves.** `okt plan wave-add <slug> "<name>"` for each wave, in order.
3. **Assign.** `okt plan assign <slug> <wave-id> <task-id>` for every task; read wave ids from `okt plan show <slug>`.
4. **Dependencies.** `okt depend add <task> --on <blocker>` for every ordering edge. Waves order execution; they do not replace the dependency graph.
5. **Verification.** When the user wants tests at the end, add a final verification task that depends on every implementation task.
6. **Module map.** Fill `okt template show comment-decomposition` and post it on the plan: `okt comment add --scope plan --plan <slug> -a agent -T decomposition -b "<map>"`. No task of the plan can start before it.
7. **Check.** `okt plan show <slug>` and `okt depend list <id>`: no wave empty, no task unassigned.

**Decompose by modules, not by steps.** Each task maps to one cohesive module with a single owner (information hiding); tasks that run in parallel touch disjoint files. Interfaces between modules are fixed in an earlier wave than the tasks that rely on them (contract first). Knowledge used by several modules lives in one place, owned by its own task (single source of truth).

**Adding a task to an active plan.** Assign it to the wave where its blockers are already done, or append a new wave (`okt plan wave-add`). Never move a task into an earlier wave to get past the wave gate.

**Next:** `okt-run <slug>`.
