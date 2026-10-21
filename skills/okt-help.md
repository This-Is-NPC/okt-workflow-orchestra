---
name: okt-help
description: "System command — tier-aware guide to how omakiten works: the orchestrator/system/granular tiers, the start→shape→run→audit→pause flow, and when to drop to granular commands."
schema_version: 2
role_affinity:
  - Concierge
command:
  name: okt-help
  next:
    - name: okt-start
      context: bare
      when: The user is ready to work
    - name: okt-config
      context: bare
      when: The question is about configuration
    - name: okt-skill
      context: bare
      when: The user wants to read a skill
---
Teach how Omakiten works. This is a tutorial, not a project action.

- **Orchestrators** — `okt-start`, `okt-shape`, `okt-run`, `okt-pause`; `okt-audit` audits a finished plan. They read state, propose the next move and delegate. Most sessions live here.
- **System** — `okt-help`, `okt-config`, `okt-skill`, `okt-reflect`. They explain or configure Omakiten itself.
- **Granular** — `okt-task-*`, `okt-plan-*`, `okt-project-*`, `okt-note-*`. One precise step each, for when you already know the step.

**The usual flow:** `okt-start` (orient) → `okt-shape` (idea to ready tasks and a plan) → `okt-run` (deliver by delegation) → `okt-audit` (deep review) → `okt-pause` (handoff).

**Next:** `okt-start`.
