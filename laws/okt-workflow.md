---
name: Okt workflow
severity: error
---
- **Record before work.** Multi-step work starts from a task (`okt task create`) or a claimed plan task; progress is recorded on it as it happens, never reconstructed at the end.
- **Satisfy guards; never route around them.** No wave reassignment, dropped dependency, placeholder comment or `--no-verify` to pass a guard. Satisfy it or stop and ask.
- **Plan start and end.** A plan starts only after its `#council` (comment-council) and `#decomposition` (comment-decomposition) plan comments and completes only after the plan auditor posts `#review-auditor` (comment-audit).
- **Workflow path.** backlog → dev: `#scope` (comment-scope) and `#self-branch`, and every blocker done. dev → review: `#resume` and `#tests-passing`. review → docs: `#boundary` (comment-boundary). docs → done: `#documentation`. Review sends a blocked task back to dev; each bucket's agents move the task on when their work is done. Post each tag when its work happens.
- **Check before moving.** `okt move <id> --to <bucket> --dry-run` lists every guard and what is missing; `okt task continue <id>` shows the same for the moves out of the current bucket. When a move fails, run the `fix` command from the error details — do not guess the syntax.
- **Exact syntax.** `okt move <id> --to <bucket>` · `okt depend add <id> --on <blocker>` · `okt comment add <id> -a agent -T <tag> -b "<body>"` · `okt plan assign <slug> <wave-id> <task-id>`. Removals need `--confirm`.
- **Done means integrated.** Before moving a task to done, confirm the reviewed commit is on the integration branch (`git branch --contains <commit>`); a fix that exists only in a worktree or an unpushed branch is not done.
- **Approved stays approved.** A review that finds no blocker ends the task's review: move it on. Non-blocking findings become follow-up tasks (`okt task create`, linked in a comment), never a reopened task.
- **Agent teams.** A move into a bucket that declares agents may open a team; the response carries `team` with its members and `follow` commands. Tell the user which agents opened and where to follow them, then do not duplicate their work: read results with `okt team show <team-id>` or `okt wait --task <id> --tag 'review-*'`. `--no-spawn` is only for a human who will do the next step by hand; an agent never passes it, so the next bucket's agents start.
- **No local references outside Omakiten.** Commits, pull requests and docs never mention plan phases, waves, okt ids, `.temp/` files or internal codenames.
