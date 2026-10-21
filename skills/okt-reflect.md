---
name: okt-reflect
description: Turn recorded corrections and guard friction into proposed rule changes for the preset or the project's AGENTS file.
schema_version: 2
command:
  name: okt-reflect
  parameters:
    - name: period
      type: string
      required: false
  next:
    - name: okt-task-create
      context: bare
      when: The user approved a change to implement
---
Propose rule changes from what went wrong. Read-only: you propose; the user decides.

1. **Collect corrections.** `okt error list --tag agent-correction` lists the mistakes the user had to correct; `okt search "<description>" --entity-type solution` finds the expected behaviour recorded for each, and `okt solution list-top` ranks solutions by confirmations.
2. **Collect friction.** `okt insights summary` (guard hotspots, stuck tasks), `okt logs --category guard --since <period>` (default `7d`), `okt metrics summary --period <period>` per model.
3. **Group by cause.** One cause per group: a missing rule, an ambiguous rule, a rule that contradicts another, or friction no rule can fix (a CLI or workflow gap).
4. **Propose the smallest change per cause**, and where it belongs: a preset law, command or guard (global, every project), the project's AGENTS file (this project only), or an Omakiten issue (product gap). Prefer structure — a guard, a template, a check — over more text.
5. **Present** each proposal with its evidence (record ids, counts) and the exact text or YAML to change. Apply nothing.

**Next:** for each approved proposal, `okt-task-create`.
