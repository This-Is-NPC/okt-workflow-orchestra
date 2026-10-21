---
name: okt-note-recap
description: Recap timeline of recent notes for the active or explicitly selected project.
schema_version: 2
role_affinity:
  - Scribe
  - Concierge
command:
  name: okt-note-recap
  next:
    - name: okt-task-continue
      context: bare
      when: The recap points at an open task
---
Render a recap of recent activity for one project. Read-only: the recap is the artifact; never persist it.

1. **Window and kinds.** `--since` defaults to `7d`; `--kinds` defaults to all. Stay on the active project unless a slug is given.
2. **Read** `okt comment list --scope project` within the window, then fill `okt template show note-recap`, grouped by kind, oldest first, each bullet prefixed with its timestamp.

**Empty window:** say "nothing in window".
