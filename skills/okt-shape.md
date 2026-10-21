---
name: okt-shape
description: Owner orchestrator — convene the persona council, shape a raw idea into ready tasks + an execution plan; deliberate before persist.
schema_version: 2
role_affinity:
  - Owner
  - Ideator
command:
  name: okt-shape
  next:
    - name: okt-task-research
      context: bare
      when: An open question blocks the brief
    - name: okt-task-validate
      context: bare
      when: The problem itself is unproven
    - name: okt-task-requirements
      context: bare
      when: Acceptance criteria are missing
    - name: okt-task-prioritize
      context: bare
      when: Several candidates compete
    - name: okt-task-decompose
      context: bare
      when: A candidate is too coarse
    - name: okt-task-estimate
      context: bare
      when: Slices need sizes
    - name: okt-task-create
      context: bare
      when: A candidate is ready
    - name: okt-plan-create
      context: bare
      when: Ready tasks need ordering
    - name: okt-run
      context: bare
      when: The plan is assembled
    - name: okt-pause
      context: bare
      when: When work must stop
    - name: okt-task-continue
      context: bare
      when: The brief maps to an existing task
---
Shape a raw idea or a loose backlog into ready tasks and a plan. You orchestrate; you do not implement. Omakiten returns this prompt — you spawn subagents with your own Agent tool.

1. **Read the board.** `okt project overview` and `okt list`, so you shape against what exists.
2. **Draft a brief** in chat: topic, scope, candidate tasks, waves, dependencies, risks. Nothing is persisted yet.
3. **Convene the council.** `okt persona list`; spawn one subagent for every listed slug. Each runs `okt persona show <slug>` in its own context and returns to you, in eight bullets at most: what breaks if we proceed, what is missing to endorse it, what it would order differently.
4. **Decide on every point.** You are the planner: for each point the council raised, decide — adopt it into the goal as a quality attribute, make it a task, leave it out of scope, or discard it as irrelevant — with a one-line reason. Revise the brief with what you adopted.
5. **Refine where evidence is missing,** by command name: `okt-task-research`, `okt-task-validate`, `okt-task-requirements`, `okt-task-prioritize`; `okt-task-decompose` and `okt-task-estimate` for coarse work. Skip discovery when the problem is already understood.
6. **Write the goal** from your decisions with `okt template show plan-goal`: outcome, functional scope, the quality attributes you adopted (each with how it will be verified on the real artifact), and what is out of scope.
7. **Decompose** by modules, not by steps: one cohesive module and owner per task, disjoint files for parallel tasks, interfaces fixed in an earlier wave, shared knowledge in one place. Write the module map (`comment-decomposition`) before authoring; then **author** each ready task with `okt-task-create`, then group them with `okt-plan-create` (waves, assignments, `okt depend add` edges).
8. **Record the council decisions** on the plan: fill `okt template show comment-council` and `okt comment add --scope plan --plan <slug> -a agent -T council -b "<table>"`. The plan cannot start without it.

**Stop when** a candidate's value or feasibility is unproven — loop back to validation instead of authoring it. Persist nothing before the council synthesis, unless the user accepts the named gaps.

**Before handing off,** list what still blocks building: unanswered requirements, unranked or unestimated candidates, missing acceptance criteria, unresolved dependencies, council dissent.

**Next:** `okt-run <slug>`.
