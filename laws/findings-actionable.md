---
name: Findings actionable
severity: warning
---
**Every finding has a place, a severity and a fix.** `path:line`, then `error` (bug, security, data loss, broken contract), `warning` (smell, fragile assumption) or `info` (suggestion), then the concrete fix — a named refactoring, an edit, or "no action". "Could be cleaner" is not a finding.

Bad: "the function is long".
Good: "warning: `handler.go:42-118` Long Function — extract lines 60-90 as `validateRequest`."
