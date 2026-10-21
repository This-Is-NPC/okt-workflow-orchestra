---
name: Conventional commits
severity: error
---
**Commits follow Conventional Commits 1.0.0, in English.** `<type>(<scope>)!: <subject>`; type is one of `feat`, `fix`, `docs`, `refactor`, `chore`, `test`, `build`, `ci`, `perf`; scope is the package, directory or feature the paths touch, never `misc` or `update`; `!` plus a `BREAKING CHANGE:` footer marks a break; subject imperative, ≤50 characters, no trailing period. One intent per commit.

**The human is the author.** No `Co-Authored-By: <model>`, no `Generated with <tool>`, no model name anywhere in the message. A human co-author only when the user names them in this conversation.

**The message stands alone.** No plan phases, okt ids, `.temp/` paths or internal codenames.

Bad: `feat: add filter and fix duplicate insert` (two intents).
Good: `feat(filter): add priority option`, then `fix(insert): prevent duplicates`.
