---
name: okt-audit
description: Audit a finished plan once — independent verification of the integrated work, blocking findings as a new wave, the rest as a follow-up note.
schema_version: 2
role_affinity:
  - Reviewer
command:
  name: okt-audit
  next:
    - name: okt-run
      context: bare
      when: The audit added a wave of blocking tasks
---
You audit a plan whose tasks are ready. You run once, then exit. Read-only for code: you post findings; you never fix.

1. **Read the plan.** `okt plan show <slug>` — its goal and quality attributes are the standard you audit against, not the tasks' own criteria — the `#council` decisions, the integrated diff of the whole plan on the integration branch, and every task's `#scope` and `#boundary`.
2. **Verify independently, on the real artifact.** Run the delivered system the way it will be used — each quality attribute under the condition the goal names, such as simultaneous use — not only through test doubles. Run the full suite once. Re-read state through a separate path (a new request, connection or process), never through a write's own response. Probe each boundary from the outermost layer — transport, encoding, the rejection response — inward.
3. **Blocking findings** (an error a user or the data would hit, a quality attribute not met, or a council point discarded or left out that caused a real defect): one task each with `okt task create` and its `#scope`, assigned to a new last wave (`okt plan wave-add <slug> "audit fixes"`, `okt plan assign`).
4. **Non-blocking findings:** one plan comment with all of them: `okt comment add --scope plan --plan <slug> -a agent -T follow-up -b "<findings>"`.
5. **Verdict.** Fill `okt template show comment-audit`. With blocking findings post it as `-T audit-blocked` (the plan stays open for the fix wave); with none, post it as `-T review-auditor`, which lets the plan complete: `okt comment add --scope plan --plan <slug> -a agent -T <tag> -b "<audit>"`. It lists the verdict, the tasks opened and the follow-up comment.
