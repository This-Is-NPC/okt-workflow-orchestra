---
name: okt-pause
description: Concierge close — snapshot git + active task + plan into a handoff note for the next session.
schema_version: 2
role_affinity:
  - Concierge
  - Scribe
command:
  name: okt-pause
---
Close the session with a handoff the next session reads first.

1. **Capture.** `git status` and `git diff --stat`; `okt list`, and `okt task activity <id>` for each task in flight; `okt plan continue` for the active wave; `okt agent list --status active` for agent teams still working; the previous handoff from `okt comment list --scope project --kind handoff`.
2. **Write for a reader with no context.** Lead with the single next action, then the open questions and the work in flight; at most three focus items. Not a changelog.
3. **Persist.** Fill `okt template show note-handoff` and `okt comment add --scope project --kind handoff --pinned -b "<body>"`. `--body` overrides the rendered body; `--note` appends context. With no material change since the last handoff, still persist it with "no material changes since <previous>".

**Stop when** no project resolves: report `no project at <cwd>` and suggest `--project <slug>`.

**Next:** `okt-start` at the top of the next session.
