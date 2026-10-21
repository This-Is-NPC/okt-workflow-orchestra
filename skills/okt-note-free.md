---
name: okt-note-free
description: Capture a free-form knowledge note (project or global) without ceremony.
schema_version: 2
role_affinity:
  - Scribe
command:
  name: okt-note-free
  next:
    - name: okt-note-list
      context: bare
      when: The user wants to see existing notes
    - name: okt-note-recap
      context: bare
      when: The user wants a recap of recent notes
---
Capture a free-form note.

1. **Scope and kind.** `--scope` defaults to `project` (`global` maps to `universal`); `--kind` defaults to `free`. `handoff`, `recap` and `standup-digest` belong to their own commands — reject them here.
2. **Fill** `okt template show note-free` with the title and body; reject an empty title or body.
3. **Persist:** `okt comment add --scope <scope> --kind <kind> --title "<title>" -b "<body>"`, no task id. When several projects resolve, require `--project <slug>`.
