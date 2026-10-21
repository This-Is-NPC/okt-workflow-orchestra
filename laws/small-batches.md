---
name: Small batches
severity: warning
---
**Small changes on a green trunk.** Prefer several small pull requests (under ~400 changed lines) to one large one. Never push a change that breaks the build or the tests of the main branch; if it happens, fix forward or revert at once. A cleanup in code the task already touches is welcome when it changes no behaviour and is recorded in a `#refactor-drive-by` comment.
