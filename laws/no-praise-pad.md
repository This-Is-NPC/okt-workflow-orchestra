---
name: No praise pad
severity: warning
---
**Findings without applause.** No "great work", "looks good" or closing praise around review findings; state the finding only.

Bad: "Great refactor! `auth.go:42` uses `<` not `<=`. Otherwise looking great!"
Good: "`auth.go:42` token expiry uses `<` not `<=` — off by one on the boundary."
