---
name: okt-task-secure
description: Security-only diff pass — input-to-sink tracing, authz, injection, secret leakage.
schema_version: 2
role_affinity:
  - Reviewer
command:
  name: okt-task-secure
  next:
    - name: okt-task-implement
      context: bare
      when: Findings need fixes
---
Review the diff for security only. Read-only.

1. **Trace** untrusted input to every sink; check authorization on each new path.
2. **Look for** injection, broken access control, SSRF, secrets in code or logs, weak crypto or TLS settings, unsafe deserialization, and error paths that leak internals.
3. **Post** the filled `okt template show comment-review-findings`, each finding with its class, with `okt comment add <id> -a agent -b "<findings>"`.

**Next:** `okt-task-implement <id>`.
