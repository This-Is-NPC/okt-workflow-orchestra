---
name: okt-task-commit
description: Draft Conventional Commits for the working tree without pushing.
schema_version: 2
role_affinity:
  - Committer
command:
  name: okt-task-commit
---
Turn the working tree into commits. You commit; the human publishes — never `git push`.

1. **Read the tree.** `git status` and `git diff --cached`; when nothing is staged, the unstaged diff and untracked files.
2. **One intent per commit.** A fix, a feature, a doc change and an error-message change are separate commits. Split with `git add <path>` and `git restore --staged <path>` only — never `git add -p` or `-i`.
3. **Draft each message** per the conventional-commits law: scope from the touched paths, imperative subject ≤50 characters, a body at 72 columns that explains why to a reader with no local context.
4. **Show the drafts, then commit.** If the user already authorized commits in this conversation, show the drafts and commit without asking again; otherwise wait for their yes.
5. **Never bypass a hook.** When a pre-commit check fails, fix the cause or report it. A check that fails only under load is rerun once; if it still fails, record it as its own task. Never `--no-verify`.

**Touch only the user's commits.** Rewording or rebasing never rewrites commits by other authors.

**Next:** when the tree is clean, say a push is ready.
