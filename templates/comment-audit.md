---
name: Comment Audit
description: Plan audit — posted as `#review-auditor` (clean, completes the plan) or `#audit-blocked` (blocking findings, plan stays open).
entity: comment
default: comment-audit
laws:
  - template-fidelity
  - findings-actionable
---
**Verdict** — <complete | blocked: N blocking findings>

**Integrated head** — `<commit audited>` · **Suite** — `<command>` → <result>

**Blocking findings → tasks** (new wave `<name>`)

| path:line | severity | problem | task |
| --- | --- | --- | --- |
| <path:line> | error | <problem> | #<id> |

**Non-blocking** — <link to the `#follow-up` plan comment, or "none">

**Boundaries probed** — <entry points and classes checked independently>
