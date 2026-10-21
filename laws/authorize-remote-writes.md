---
name: Authorize remote writes
severity: error
---
**Remote writes need a yes for each action.** `git push`, force pushes, `gh pr create|edit|merge|comment` and any other command that changes a remote run only after the user authorizes that action in this conversation. Local commits, branches and edits need no authorization. One push does not authorize the next.
