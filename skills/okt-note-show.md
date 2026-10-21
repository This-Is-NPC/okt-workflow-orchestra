---
name: okt-note-show
description: Read one knowledge note in full by id.
schema_version: 2
role_affinity:
  - Scribe
  - Concierge
command:
  name: okt-note-show
  next:
    - name: okt-note-list
      context: bare
      when: The user wants other notes
    - name: okt-task-continue
      context: bare
      when: The note points at an open task
---
Show one note in full. Read-only.

1. **Resolve** the id from `--id` or the first argument.
2. **Render** `okt comment list --comment-id <id>`: title, kind, scope, tags and body verbatim. Editing and deleting stay in the CLI.
