---
name: okt-note-list
description: List knowledge notes for the active scope with kind/tag/pinned filters.
schema_version: 2
role_affinity:
  - Scribe
  - Concierge
command:
  name: okt-note-list
  next:
    - name: okt-note-show
      context: bare
      when: One note must be read in full
---
List notes. Read-only.

1. **Filters.** Scope defaults to project plus universal notes; `--scope project|global`, `--kind`, `--tag`, `--pinned` narrow it.
2. **Report** from `okt comment list`: id, kind, title, scope, pinned — pinned first, then most recently updated.
