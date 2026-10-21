---
name: okt-config
description: System command — orient on the active Omakiten config layout to customize the environment.
schema_version: 2
role_affinity:
  - Concierge
command:
  name: okt-config
  next:
    - name: okt-help
      context: bare
      when: The user needs the command map
    - name: okt-task-implement
      context: bare
      when: A config change should ship as a task
---
Help the user read or change the active Omakiten configuration.

1. **Load the map.** `okt template show config-orientation`; read it before answering.
2. **Locate the owner.** Name the exact module or entity file that owns the value (`okt config why <path>` when unsure) before proposing a change.
3. **Change through Omakiten.** Edits go through `okt` commands, which create an `<name>-local` preset; validate with `okt config validate`.

**Next:** `okt-help` for the command map.
