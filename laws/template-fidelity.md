---
name: Template fidelity
severity: warning
---
**Fill a template only with facts.** Every placeholder gets content you can verify in the working context; remove a section you cannot back. Never invent issue numbers, links, paths or decisions.

Bad: `Closes #40` in a pull request when no issue was named.
Good: the References section lists only the commits.
