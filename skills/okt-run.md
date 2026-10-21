---
name: okt-run
description: Owner orchestrator — drive a plan or task to completion by spawning a Builder subagent per task and reviewing each compact return; conditional parallelism.
schema_version: 2
role_affinity:
  - Owner
command:
  name: okt-run
  next:
    - name: okt-task-resume
      context: bare
      when: A Builder starts a claimed task with no context
    - name: okt-task-continue
      context: bare
      when: A Builder resumes a task it already knows
    - name: okt-task-implement
      context: bare
      when: A Builder builds the claimed piece
    - name: okt-task-self-review
      context: bare
      when: A Builder finished its increment
    - name: okt-task-refactor
      context: bare
      when: A Builder needs a behaviour-preserving cleanup
    - name: okt-task-check
      context: bare
      when: The integration or a Tester run is due
    - name: okt-audit
      context: bare
      when: Every plan task is done
    - name: okt-pause
      context: bare
      when: Work must stop
---
Drive a plan, or one task, to done by delegation. You are the Owner: you split, delegate and accept; you never write the code. Omakiten returns this prompt — you spawn the Builders with your own Agent tool.

**Target.** A task id runs that task; a plan slug runs that plan; no argument resolves the current plan with `okt plan continue`. With no runnable target, stop and ask for one — do not summarize the board instead.

1. **Read the plan.** `okt plan show <slug>`, and `okt depend list <id>` for each candidate. Direct by command name only; never load `okt-task-*` bodies here.
2. **Claim.** A task is runnable only when every blocker is done and its `#scope` is posted. `okt plan claim <slug>` (needs `OMAKITEN_AGENT_MODEL`), then post its `#self-branch` comment and `okt move <id> --to dev`.
3. **Split before delegating.** Each Builder brief names the claimed task id, the files in scope, what is out of scope, the done signal and the narrowest test. A task too large for one reviewable diff is split first (`okt task create --parent <id>`).
4. **Spawn one Builder per piece**, with the model and agent count the user chose. The Builder runs `okt-task-resume`, `okt-task-implement` and `okt-task-self-review` itself, in its own context. Run pieces in parallel only when their files are disjoint and their blockers are done.
5. **Accept or reject the return** — a diff summary plus the narrowest test output. Accept, reject with the reason, or respawn. Deep review belongs to `okt-audit`. An approved review moves the task on; its non-blocking findings become follow-up tasks, not another round of the same task.
6. **Keep the board current per task.** `#resume` and `#tests-passing` → `okt move <id> --to review`; `#documentation` → `okt move <id> --to done`; then `okt plan show <slug>` to confirm progress.
7. **Close the plan.** When every task is ready, the plan auditor runs and posts `#review-auditor`. If it posted `#audit-blocked`, drive the new wave, then rerun the auditor (`okt agent spawn --plan <slug> --member auditor`); repeat until it posts `#review-auditor`, then complete the plan (`okt plan edit <slug> --status done`).

**When the user defers testing,** Builders return the diff only. A verification task that depends on every implementation task, modelled when the plan was created, runs the integration once; its output is posted as `#tests-passing` on each task it covers. If the plan lacks that task, add it in a new last wave (`okt plan wave-add`).

**When the bucket declares an agent team,** the move opens it: do not spawn your own Builders for that step. Follow the team with `okt team show <team-id>` and `okt wait --task <id> --tag 'review-*'`; reopen a stuck member with `okt agent spawn --team <team-id> --member <key>`.

**Stop when** a Builder returns failing or blocked: spawn nothing more, record the halt with `okt progress record --task-id <id> --comment "<why>"`, and report what was accepted, what halted and why, and what remains. Keep finished work; revert nothing the user did not name.

**Next:** `okt-audit` for a deep review, `okt-pause` to hand off.
