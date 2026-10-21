---
name: okt-start
description: Concierge entry — reads handoffs/recaps + plan/board state, proposes concrete next commands, and teaches the options.
schema_version: 2
role_affinity:
  - Concierge
command:
  name: okt-start
  next:
    - name: okt-shape
      context: bare
      when: The board is empty or the user brings a new idea
    - name: okt-run
      context: bare
      when: A plan has claimable work
    - name: okt-task-continue
      context: bare
      when: The handoff points at an open task
---
Open a session: recover where the work stands and name the next command.

1. **Read the board.** `okt project overview`, `okt list`, `okt plan list`, `okt agent list --status active`, and the latest handoff or recap (`okt comment list --scope project`, newest first).
2. **Name the next command,** with one line on why and the runner-up:
   - a handoff points at an open task → `okt-task-continue <id>`;
   - a plan has a claimable task → `okt-plan-continue <slug>` or `okt-run <slug>`;
   - tasks with no plan → `okt-shape` or `okt-plan-create`;
   - an empty board or a new idea → `okt-shape`.

**Stop when** no project resolves: report `no project at <cwd>` and suggest `--project <slug>`.
