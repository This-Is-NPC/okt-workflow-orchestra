---
name: okt-skill
description: System command — load a skill body via okt skill show (e.g. okt-skill commit), or list the catalog via okt skill list with no arg; pulls any skill, ungated by persona repertoire.
schema_version: 2
role_affinity:
  - Concierge
command:
  name: okt-skill
  next:
    - name: okt-task-commit
      context: bare
      when: The user asked for the commit skill
    - name: okt-help
      context: bare
      when: The user needs the command map
---
Read a skill on demand, or list the catalog. Read-only; any skill in the catalog, regardless of persona.

- **With a slug:** `okt skill show <slug>` and show its body verbatim. An unknown slug: relay the error and suggest a bare `okt-skill`.
- **Without:** `okt skill list` — slug, name and description, ordered by slug.
