# Orchestra preset benchmarks

What changed in the preset and in Omakiten during the benchmark sessions (2026-10-15 to 2026-10-17), what every round measured and found, and a self-contained harness to run the next round.

## Summary

- **Best workflow: F2, the developer at the terminal plus the autonomous pipeline.** You create tasks, review them and move them to dev. From there, agents implement, review (back to dev when blocked), document, commit and move the task to done. F2 matched the quality of an external Owner driving the plan (23.5 vs 23.25 /30) at about half the tokens (15.1M vs 28.6M per delivery).
- **Best model mix: a strong model plans and reviews, a cheaper model implements** (`gpt-5.6-terra` on planning, review and audit; `gpt-6-luna` implementing).
- **Defects no workflow fixed:** 500 on non-encodable input (`NaN`, `Infinity`, a raw `"\ud800"`), and ISBN uniqueness without normalization. These are treated as a model limit, not something the preset encodes.

## What changed

### Preset (omakase branch `feat/agent-guidance`, 29 commits ahead of `master`)

**Prompts**

- Laws, skills and templates rewritten in a lean numbered-step format, following the AGENTS structure of the `forma` project.
- Laws are stated as software engineering concepts and not tied to an implementation. They cover:
  - parse, don't validate (the boundary includes the error path);
  - equivalence partitioning and boundary value analysis;
  - proving a state change with an independent read on the real artifact;
  - information hiding and cohesion;
  - contract first;
  - a single source of truth;
  - attacking the premise;
  - recording decisions.
  
  The first three were inspired by cursor/pstack.

**Agent conduct**

- Autonomous runs decide instead of asking.
- An approved task stays approved; its warnings become follow-up work.
- Every repeated review needs a fresh verdict for the commit under review.
- "Done" means the work is integrated.
- Corrections are recorded with `okt error record` and solutions.

**Guards**

- `#scope` and `#self-branch` on backlog → dev.
- `#resume` and `#tests-passing` on dev → review.
- `#boundary` on review → docs.
- `#documentation` on docs → done.

**Plan gates**

- Start: `#decomposition` (module map) and `#council` (the planner's decision for each council point).
- Complete: `#review-auditor` (the plan auditor's verdict).

**Plans**

- The plan goal comes from the `plan-goal` template: outcome, scope, quality attributes (each with how it is verified on the real artifact) and out-of-scope items.
- The plan auditor (`okt-audit`, Kakashi) judges the plan against that goal and the council decisions, on the running system:
  - blocking findings → tasks in a new wave, and an `#audit-blocked` verdict;
  - non-blocking findings → one `#follow-up` plan comment.

**Agent seats**

- Seats are assigned to personas through configuration, with no role concept.
- Omakiten never picks a harness or a model: you set them in preferences.

**Workflow:** an autonomous pipeline with five buckets.

```
backlog → dev ─────────────── implementer (naruto) → move to review
          review ──────────── security (kakashi) + tests (sakura) in parallel
                              → lead on team.finished: blocked → dev, else → docs
          docs ────────────── documenter (shikamaru) → committer (iruka) on team.finished
                              → commit, merge to main, move to done
          done
```

**Surfaces:** `agent.list`, `agent.show`, `agent.spawn`, `team.close`, `team.show`, `wait`, `error.list` and `project.relocate`.

### Omakiten (branch `feature/agent-guidance`, no push)

**Agents and seats**

- Seats run headless and one-shot under a hidden supervisor, `okt agent run`:
  - the transcript goes to `<state>/agents/<session>.jsonl`;
  - the exit code becomes the session status;
  - the supervisor emits `agent.session_finished` and `team.finished`;
  - orphaned sessions are reconciled.
- Bucket rosters: `trigger: auto|manual`, a `start` list, a `lead`, and members with `persona`, `command`, `model`, `brief` and `timeout`.
- Hooks start seats with `do: spawn` (`member` or `plan_member`). Spawning is idempotent per seat.
- Plan seats (`plans.agents`); `guard.violated` names the plan gate, which is what triggers the auditor.
- CLI: `okt agent spawn|list|show`, `okt team show|close`, `okt wait`, `okt logs --session`, `--no-spawn`, `--dry-run`, and a `fix` field on guards.

**Plans and guards**

- Plan gates (`plans.start|progress|complete`) with `plan_comments_tagged`.
- Plan-scoped comments.

**Fixes**

- A seat that moves its own task is no longer killed before the next team spawns.
- `max_sessions` holds across concurrent spawns.
- `okt` resolves the project inside git worktrees.
- Events are attributed to their agent session.
- `bundle.imported` is recorded only for new content.

**Removed:** built-in harness, model and launcher defaults, and the tmux/herdr wake mechanism.

## Benchmark rounds

Every score is a blind review on the rubric below. Probes were added to the rubric in rounds 4–5, so scores are only comparable inside a round. Reviewer variance is about ±2.

### Round 1 — cheap Owner vs. Claude as planner (DeepSeek V4.1 Flash free, then Claude Haiku 5.5)

| Bench | Planning | Score | Tests | Tasks | Review rejections | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Cheap model as Owner via `okt-shape` | 27/30 | 60 | 4 | 9 | Reopened approved tasks; gold-plating |
| 2 | Claude as Owner, fine-grained waves | 26/30 | 87 | 13 | 5 | Parallel waves; 0 guard violations |

Fine-grained planning improved the process, not the quality. The quality came from the review teams.

### Round 2 — OpenAI only, dedicated planner vs. Omakiten alone (workers `gpt-6-luna`)

| Scenario | Planning | Score | Tests | Tasks | Rejections |
| --- | --- | --- | --- | --- | --- |
| S1 | `gpt-6.1-sol` (medium) as planner | 25/30 | 229 | 13 | 2 |
| S2 | Omakiten alone (`okt-shape`, Owner `gpt-6-luna`) | 21/30 | 56 | 2 | 4 |

### Round 3 — `#scope` and `#boundary` guards

| Scenario | Score | Tests | Tasks | Rejections |
| --- | --- | --- | --- | --- |
| S1 planner `gpt-6.1-sol` | 23/30 | 172 | 11 | 0 |
| S2 Omakiten alone (`gpt-6-luna`) | 22/30 | 66 | 7 | 4 |

### Round 4 — `#decomposition` plan gate, four model mixes

| Scenario | Owner | Implementers | Reviewers | Score |
| --- | --- | --- | --- | --- |
| S1 | `gpt-6.1-sol` (planner prompt) | luna | luna | 21/30 |
| S2 | luna (Omakiten flow) | luna | luna | 21/30 |
| S3 | terra | terra | terra | did not finish (Owner ended its turn) |
| S4 | terra | luna | terra | **24/30** |

### Round 5 — headless seats, persona seats, plan auditor

| Scenario | Score | Notes |
| --- | --- | --- |
| S1 sol planner | 24/30 | 11/11 mutants caught |
| S2 all luna | **13/30** | SQLite connection shared across threads; almost everything 500 under parallel requests; the luna auditor approved (in-process TestClient only) |
| S3 all terra | 21/30 | Finished, but the Owner posted the result before the auditor ran |
| S4 terra + luna | 24/30 | |

The auditor checked the plan against its own criteria, which never mentioned concurrency. This led to the `plan-goal` template, auditing on the real artifact, and the council decisions.

### Round 6 — council decisions, `plan-goal`, auditor against the goal

| Scenario | Score | Previous round |
| --- | --- | --- |
| S1 sol planner | 24/30 (9 × 500 under concurrent PUT/DELETE) | 24 |
| S2 all luna | **24/30** (0 × 5xx under 64 threads) | 13 |
| S3 all terra | 25/30 | 21 |
| S4 terra + luna | did not finish: the auditor opened a fix wave and the terra Owner ended its turn without driving it | 24 |

All four runs recorded a `#council`. The structure worked, but the content still depends on the model: one planner read "concurrency" as a duplicate ISBN race. The repeated Owner stall led to the autonomous pipeline (F2).

### Round 7 — workflow comparison: F1 vs. F2 (2 tasks × 2 repetitions × 2 blind reviews)

Models fixed: Owner/developer, review and audit on `gpt-5.6-terra`; implementer, documenter and committer on `gpt-6-luna`.

- **F1:** an external Owner shapes and drives the plan (`okt-shape`, `okt-run`). The pipeline still runs the buckets.
- **F2:** the developer at the terminal only creates tasks (scope, branch, dependencies), reviews them and moves them to dev. Then it stops.
- **Tasks:**
  - greenfield: the books CRUD from scratch;
  - brownfield: add search and pagination to an existing books API.

| Run | Flow | Task | Reviews | Avg | Min | Sessions | review→dev | Done | Tokens |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| f1g1 | F1 | green | 24, 25 | 24.5 | 115 | 37 | 4 | 3/5 | 32.6M |
| f1g2 | F1 | green | 24, 22 | 23.0 | 45 | 30 | 3 | 3/4 | 37.0M |
| f1b1 | F1 | brown | 23, 23 | 23.0 | 32 | 31 | 3 | 2/2 | 25.6M |
| f1b2 | F1 | brown | 23, 22 | 22.5 | 27 | 18 | 3 | 1/2 | 19.2M |
| f2g1 | F2 | green | 25, 25 | 25.0 | 40 | 24 | 3 | 2/2 | 21.1M |
| f2g2 | F2 | green | 23, 23 | 23.0 | 30* | 18 | 3 | 1/2 | 15.7M |
| f2b1 | F2 | brown | 23, 22 | 22.5 | 90* | 26 | 5 | 1/2 | 14.3M |
| f2b2 | F2 | brown | 24, 23 | 23.5 | 20* | 16 | 1 | 2/3 | 9.2M |

\* Active minutes: the 15 idle minutes before the stall stop are excluded.

| | F1 | F2 |
| --- | --- | --- |
| Quality (avg /30) | 23.25 | 23.5 |
| Tokens per delivery | 28.6M | 15.1M (−47%) |
| Agent sessions | 29 | 21 |
| Active minutes | 55 | 45 |
| review → dev | 3.25 | 3.0 |
| 5xx under ≥50 threads | 0 in all | 0 in all |

**How to read it**

- Quality is a tie: the spread between reviewers is larger than the gap between the flows.
- F2 costs half as much, because nobody re-reads the board and drives each step. The F1 Owner alone spent 12–25M tokens per run.
- **F2's weak point:** in 3 of 4 runs the simulated developer ended its turn with a dependent task still in backlog. Two of those were "Document …" tasks, which duplicate what the docs bucket already does. In real use, moving that task is your touch, not a pipeline failure.
- F1 also left follow-up tasks in backlog in 3 of 4 runs.
- **Owner token counts:** they come from the interactive codex session (`source: cli`). An earlier version of `metrics.py` matched agent seats instead.

## Findings

### Defects that kept appearing

| Defect | Status |
| --- | --- |
| 500 on non-encodable input (`NaN`, `Infinity`, `1e400`, raw `"\ud800"`): the default validation error cannot serialize the value it echoes | Every delivery in rounds 4–7; treated as a model limit |
| 500 on ids beyond 64-bit in PUT/DELETE | Inherited from the brownfield base; no flow fixed it unasked |
| ISBN uniqueness bypassed by whitespace, case or hyphens; whitespace-only and NUL strings accepted | Most deliveries; domain-specific, not in the preset |
| Update without commit passes the suite | Fixed by the independent-read law (round 4 on), still survives in some suites |
| SQLite connection shared across threads | Round 5 (all-luna); gone after `plan-goal` and the real-artifact audit |

### Model behaviour

- A cheap Owner reopened approved tasks to fold in warnings.
- Haiku asked for confirmations nobody could give.
- Leads reused stale verdicts.
- When the environment broke, a luna Owner wrote the feature itself.
- An Owner told to "stop on environment failure" halted on a usage error.
- A terra Owner ends its turn while work waits on another agent (three times). An event-driven pipeline removes the need for a live Owner.
- A review lead passed `--no-spawn` and skipped the next bucket. The law now says only a human passes it.
- An auditor approves against the plan's own criteria unless the goal names quality attributes and how to verify them.

### Environment pitfalls

- Codex `workspace-write` makes `.git` read-only. The benches run codex with `--dangerously-bypass-approvals-and-sandbox`, in disposable directories only.
- Codex does not pass the parent environment to its shells. Force variables with `-c shell_environment_policy.set.<VAR>=...`.
- Folder-trust dialogs appear once per new folder. `run-batch.sh` accepts them and logs an intervention.
- Project slugs are lowercased.
- `pkill -f` matches your own shell; kill by PID.
- `okt preset add` without `--scope` writes `.omakiten` in the current directory.
- Free model quotas run out mid-run. opencode-go has region errors, and in opencode permissions the last matching rule wins.
- Claude Code `--allowedTools` is variadic: end the options with `--`.

## Next steps

1. **Use F2 as the default flow.** Two preset changes, not applied yet:
   - the task-creation skill says not to create documentation tasks, because the docs bucket covers them;
   - tasks are sliced vertically, so they can move to dev in parallel without dependencies.
2. **Optional:** a cap on dev↔review rounds, in preferences.
3. **F3 (deferred):** the Owner as an event-driven plan seat. It needs a `rerun: when_finished` option on `do: spawn` in Omakiten.
4. **Release:** the push, PR and Omakiten 0.37.0 release for both branches are pending your decision. The preset requires okt 0.37.0.

## Harness (self-contained)

Everything ran from scratch directories under `/tmp`, which were deleted after round 7. To rerun, recreate the files below:

- `$BENCH` (for example `/tmp/okt-bench`) holds the scripts;
- `$BENCH/base/brownfield` holds the brownfield starting project;
- the Omakiten build is `$OMAKITEN_REPO/.tmp/build/okt` (`mise run build`).

Requirements: `uv`, `tmux`, `git`, `sqlite3` and codex. Codex uses the `--dangerously-bypass-approvals-and-sandbox` flag inside disposable directories only.

```sh
export BENCH=/tmp/okt-bench OMAKITEN_REPO=~/Projects/omacon/omakiten PRESET_REPO=~/Projects/omacon/okt-workflow-orchestra
```

### Run a batch

```sh
$BENCH/run-batch.sh 1                      # sets up f1g1 f2g1 f1b1 f2b1 and launches the Owners
for N in f1g1 f2g1 f1b1; do ($BENCH/mon.sh $N &); done; $BENCH/mon.sh f2b1
python3 -I $BENCH/metrics.py f1g1 F1 greenfield          # one per run
$BENCH/review-prompt.sh f1g1 greenfield K3 > K3.prompt    # blind label; give it to two reviewers per run
```

Watch a run with `OMAKITEN_HOME=/tmp/okt-N/home $OMAKITEN_REPO/.tmp/build/okt -p N tui`, or `okt agent list`.

**Clean up:** `tmux kill-session -t okt-N; rm -rf /tmp/okt-N /tmp/review-*`.

### `setup.sh NAME [MODEL] [PRESET_DIR] [greenfield|brownfield]`

```bash
#!/bin/bash
set -e
N=$1; MODEL=${2:-gpt-6-luna}; PRESET=${3:-$PRESET_REPO}; TASK=${4:-greenfield}
B=/tmp/okt-$N; H=$B/home; BUILD=$OMAKITEN_REPO/.tmp/build; O=$BUILD/okt
tmux kill-session -t okt-$N 2>/dev/null || true; rm -rf $B; mkdir -p $H $B/proj
cd $B/proj && git init -q -b main && git config user.email bench@local && git config user.name bench
if [ "$TASK" = brownfield ]; then cp -a $BENCH/base/brownfield/. .; else
printf '.venv/\n__pycache__/\n*.db\n.pytest_cache/\n' > .gitignore
printf '# Bench project\n\nPython 3 + FastAPI + SQLite. Use `.venv/bin/python` and `.venv/bin/pytest`.\n' > README.md; fi
uv venv -q .venv && uv pip install -q --python .venv/bin/python fastapi uvicorn pytest httpx
git add . && git commit -qm "chore: bootstrap project"
cat > $H/preferences.yaml <<YAML
agents:
  defaults: {harness: codex, model: $MODEL, timeout: 45m}
  harnesses:
    codex:
      argv: [codex, exec, --json, --skip-git-repo-check, --dangerously-bypass-approvals-and-sandbox, --cd, "{cwd}", [--model, "{model}"], --, "{prompt}"]
YAML
export OMAKITEN_HOME=$H; cd $B
$O preset add $PRESET --scope global >/dev/null
$O preset use $(grep "^name:" $PRESET/preset.yaml | cut -d" " -f2) --scope global --confirm >/dev/null
cd $B/proj && $O init --name "Bench $N" --slug $N --root "$PWD" >/dev/null
tmux new-session -d -s okt-$N -n base -c $B/proj
echo "ready $B (slug $(echo $N | tr A-Z a-z))"
```

### `goals.sh`

```bash
GOAL_greenfield='Deliver a complete CRUD REST API for a `books` resource (id, title, author, year, isbn unique) in Python 3 + FastAPI + SQLite, with pytest tests, on branch main.'
GOAL_brownfield='The repository already has a working books CRUD API (app.py) with tests. Add search and pagination to GET /books: query `q` (case-insensitive substring match on title or author), `limit` (1..100, default 20) and `offset` (>= 0, default 0); results ordered by id; the response stays a JSON list and carries an `X-Total-Count` header with the number of matches before pagination. Invalid parameters return 422. Keep every existing behaviour and test passing; update the README. Work on branch main.'
```

### `make-prompt.sh F1|F2 greenfield|brownfield NAME`

```bash
#!/bin/bash
FLOW=$1; TASK=$2; N=$3; s=$(echo $N | tr A-Z a-z); B=/tmp/okt-$N; OKT=$OMAKITEN_REPO/.tmp/build/okt
source $BENCH/goals.sh; eval GOAL=\$GOAL_$TASK
COMMON="Use \`.venv/bin/python\` and \`.venv/bin/pytest\` (fastapi, uvicorn, httpx, pytest installed; no network). Code, commits and docs in English. Delivery quality is what is measured. The user is not available: decide everything yourself and never stop to ask.

Environment: \`okt\` is $OKT and OMAKITEN_HOME is already set; never use any other okt binary or database. Environment failure means only that the okt binary or the OMAKITEN_HOME database is unreachable — then record it with a project comment and stop; a command error from usage is not — fix the usage and continue."
{
echo "Project: Omakiten slug \`$s\`, repository $B/proj."; echo; echo "$GOAL"; echo; echo "$COMMON"; echo
if [ "$FLOW" = F1 ]; then cat <<'EOT'
You are the Owner. Worker agents run on their own once a task is in development: implementer → review (back to dev when blocked) → docs → done, and a plan auditor runs when the plan's last move is held by its completion gate.
1. Run `okt command list`, then `okt command resolve okt-shape` and follow it to shape the work into tasks and a plan.
2. Then `okt command resolve okt-run` and follow it to drive the plan to done. Follow agents with `okt agent list`, `okt team show <team-id>` and `okt wait`; retry a move held by a gate once its evidence is posted.
3. Satisfy every guard with real evidence; never route around one.
4. When the plan is done, run the full suite once and post the result: `okt comment add --scope project -a agent -T bench-result -b "..."`.
EOT
else cat <<'EOT'
You are the developer at the terminal. You only create and review tasks; worker agents do everything after development starts: implementer → review (back to dev when blocked) → docs → done.
1. Read the repository. Split the goal into the tasks you judge right (`okt task create`), each with a precise description, acceptance criteria and owned files.
2. For each task post its scope (`okt template show comment-scope`, then `okt comment add <id> -a agent -T scope -b "..."`) and its branch (`okt comment add <id> -a agent -T self-branch -b "Branch <name> from main"`), and add `okt depend add` edges where one task needs another.
3. Re-read every task once as its reviewer; fix unclear descriptions.
4. Move each task whose blockers are done to development (`okt move <id> --to dev`). Move a dependent task once its blockers reach done (`okt wait --task <blocker> --tag review-lead` can help you notice).
5. Do not implement, review, move tasks past development, or intervene in the agents' work. When every task has been moved to development, stop.
EOT
fi
} > $B/owner-prompt.md
```

### `run-batch.sh REP`

Builds a copy of the preset with `gpt-5.6-terra` on the review seats and the auditor (the luna default comes from preferences). It then sets up four runs and launches an interactive codex Owner, on terra, in each tmux session.

```bash
#!/bin/bash
set -e
REP=$1; MP=$BENCH/preset-mixed; BP=$OMAKITEN_REPO/.tmp/build
rm -rf $MP; mkdir -p $MP; (cd $PRESET_REPO && git ls-files -z | xargs -0 -I{} cp --parents {} $MP/)
MP=$MP python3 -I - <<'PY'
import os, pathlib
mp=pathlib.Path(os.environ['MP']); w=mp/'config/workflows.yaml'; t=w.read_text()
for old in ['lead: {persona: kakashi-hatake, command: okt-task-review,','- {key: security, persona: kakashi-hatake, command: okt-task-secure,','- {key: tests, persona: sakura-haruno, command: okt-task-check,','- {key: auditor, persona: kakashi-hatake, command: okt-audit,']:
    assert old in t, old; t=t.replace(old, old+' model: gpt-5.6-terra,',1)
w.write_text(t)
p=mp/'preset.yaml'; p.write_text(p.read_text().replace('name: orchestra','name: orchestra-mixed'))
PY
for r in f1g:F1:greenfield f2g:F2:greenfield f1b:F1:brownfield f2b:F2:brownfield; do IFS=: read n F T <<<"$r"; N=$n$REP
  $BENCH/setup.sh $N gpt-6-luna $MP $T >/dev/null; $BENCH/make-prompt.sh $F $T $N
  B=/tmp/okt-$N; : > $B/interventions.log; date +%s > $B/start; M=gpt-5.6-terra
  tmux new-window -d -t okt-$N -n owner -c $B/proj "env OMAKITEN_HOME=$B/home OMAKITEN_AGENT_MODEL=$M OMAKITEN_AGENT_SESSION_ID=owner PATH=$BP:\$PATH codex -m $M --dangerously-bypass-approvals-and-sandbox -C $B/proj -c 'shell_environment_policy.set.OMAKITEN_HOME=\"$B/home\"' -c 'shell_environment_policy.set.PATH=\"$BP:$PATH\"' -c 'shell_environment_policy.set.OMAKITEN_AGENT_SESSION_ID=\"owner\"' -c 'shell_environment_policy.set.OMAKITEN_AGENT_MODEL=\"$M\"' -- \"\$(cat $B/owner-prompt.md)\""
done
sleep 12
for n in f1g f2g f1b f2b; do N=$n$REP; if tmux capture-pane -t okt-$N:owner -p | grep -q "Trust this folder"; then tmux send-keys -t okt-$N:owner Enter; echo "$(date +%H:%M) operator: accepted codex folder-trust dialog for owner" >> /tmp/okt-$N/interventions.log; echo "$N trust"; fi; done
```

### `mon.sh NAME`

Stops when every task is done and nothing is running, on a `bench-result` comment, after a 15-minute stall, or after 120 minutes.

```bash
#!/bin/bash
N=$1; s=$(echo $N | tr A-Z a-z); B=/tmp/okt-$N; export OMAKITEN_HOME=$B/home; O=$OMAKITEN_REPO/.tmp/build/okt; L=$B/monitor.log; DB=$B/home/data/omakiten.db
cd $B/proj; start=$(cat $B/start); last_ev=0; last_change=$(date +%s)
while true; do
  now=$(date +%s); el=$(( (now-start)/60 ))
  read total done <<<$($O -p $s list 2>/dev/null | python3 -I -c 'import json,sys; d=json.load(sys.stdin)["data"]; ts=d.get("tasks",d); print(len(ts), sum(1 for t in ts if t.get("bucket_key")=="done"))' 2>/dev/null)
  board=$($O -p $s list 2>/dev/null | python3 -I -c 'import json,sys,collections; d=json.load(sys.stdin)["data"]; print(dict(collections.Counter(t.get("bucket_key") for t in d.get("tasks",d))))' 2>/dev/null)
  running=$($O -p $s agent list --status active 2>/dev/null | python3 -I -c 'import json,sys; print(sum(1 for t in json.load(sys.stdin)["data"]["teams"] for x in t["sessions"] if x["status"]=="running"))' 2>/dev/null)
  ev=$(sqlite3 $DB "select max(id) from events" 2>/dev/null)
  [ "$ev" != "$last_ev" ] && { last_ev=$ev; last_change=$now; }
  idle=$(( (now-last_change)/60 ))
  echo "t+${el}m board=$board running=$running idle=${idle}m" >> $L
  res=$($O -p $s comment list --scope project --tag bench-result 2>/dev/null | python3 -I -c 'import json,sys; d=json.load(sys.stdin)["data"]; print(len(d.get("comments",d)))' 2>/dev/null)
  if [ "${total:-0}" -gt 0 ] && [ "$total" = "$done" ] && [ "${running:-0}" = 0 ]; then echo "STOP done t+${el}m" >> $L; echo $now > $B/end; exit 0; fi
  if [ "${res:-0}" -gt 0 ] && [ "${running:-0}" = 0 ]; then echo "STOP bench-result t+${el}m" >> $L; echo $now > $B/end; exit 0; fi
  if [ "${running:-0}" = 0 ] && [ $idle -ge 15 ] && [ $el -ge 10 ]; then echo "STOP stalled t+${el}m" >> $L; echo $now > $B/end; exit 0; fi
  if [ $el -ge 120 ]; then echo "STOP timeout t+${el}m" >> $L; echo $now > $B/end; exit 0; fi
  sleep 60
done
```

### `metrics.py NAME FLOW TASK`

Prints one JSON line of process and cost metrics:

- agent tokens, summed from the seat transcripts;
- Owner tokens, from the interactive codex rollout (`source == cli`).

```python
import json, sys, glob, os, sqlite3
N, FLOW, TASK = sys.argv[1:4]; B=f'/tmp/okt-{N}'; db=sqlite3.connect(f'{B}/home/data/omakiten.db')
q=lambda s: db.execute(s).fetchone()[0]
m={'run':N,'flow':FLOW,'task':TASK}
m['tasks']=q("select count(*) from tasks")
m['tasks_done']=q("select count(*) from tasks t where t.bucket_id=4")
m['review_to_dev']=q("""select count(*) from events where event_type='task.moved' and payload like '%"from":"review"%' and payload like '%"to":"dev"%'""")
m['done_reopened']=q("""select count(*) from events where event_type='task.moved' and payload like '%"from":"done"%'""")
m['guard_violations']=q("select count(*) from events where event_type='guard.violated'")
m['agent_sessions']=q("select count(*) from agent_sessions")
m['failed_sessions']=q("select count(*) from agent_sessions where status='failed'")
start=int(open(f'{B}/start').read()); end=int(open(f'{B}/end').read()) if os.path.exists(f'{B}/end') else start
m['wall_min']=round((end-start)/60,1)
m['interventions']=sum(1 for l in open(f'{B}/interventions.log') if l.strip()) if os.path.exists(f'{B}/interventions.log') else 0
m['outcome']=open(f'{B}/monitor.log').read().strip().splitlines()[-1].split()[1] if os.path.exists(f'{B}/monitor.log') else '?'
def usage_agents():
    i=c=o=0
    for f in glob.glob(f'{B}/home/state/agents/*.jsonl'):
        for line in open(f,errors='ignore'):
            try: d=json.loads(line)
            except: continue
            u=d.get('usage') if d.get('type')=='turn.completed' else None
            if u: i+=u.get('input_tokens',0); c+=u.get('cached_input_tokens',0); o+=u.get('output_tokens',0)
    return i,c,o
def usage_owner():
    best=None
    for f in glob.glob(os.path.expanduser('~/.codex/sessions/*/*/*/rollout-*.jsonl')):
        if os.path.getmtime(f) < start-60: continue
        cwd=None; tot=None; cli=False
        for line in open(f,errors='ignore'):
            try: d=json.loads(line)
            except: continue
            p=d.get('payload',{})
            if d.get('type')=='session_meta': cwd=p.get('cwd'); cli=p.get('source')=='cli'
            if p.get('type')=='token_count' and p.get('info'): tot=p['info'].get('total_token_usage')
        if cli and cwd==f'{B}/proj' and tot and (not best or tot.get('input_tokens',0)>best.get('input_tokens',0)): best=tot
    return best or {}
ai,ac,ao=usage_agents(); ow=usage_owner()
m['agent_tokens_in']=ai; m['agent_tokens_cached']=ac; m['agent_tokens_out']=ao
m['owner_tokens_in']=ow.get('input_tokens',0); m['owner_tokens_out']=ow.get('output_tokens',0)
m['total_tokens']=ai+ao+m['owner_tokens_in']+m['owner_tokens_out']
print(json.dumps(m))
```

`wall_min` of a stalled run includes the 15 idle minutes before the stop; subtract them for active time. The F2 Owner stops by design once its tasks are in dev. Count a dependent task left in backlog as one human touch.

### `review-prompt.sh RUN TASK LABEL` and the frozen rubric

Give each delivery a random label and two independent reviewers. Do not tell a reviewer which flow produced the delivery.

```bash
#!/bin/bash
N=$1; T=$2; L=$3
if [ "$T" = greenfield ]; then SPEC='a complete CRUD REST API for `books` (id, title, author, year, isbn unique) in Python 3 + FastAPI + SQLite, with pytest tests.'
else SPEC='an existing books CRUD API (FastAPI + SQLite) extended with search and pagination on GET /books: `q` (case-insensitive substring on title or author), `limit` (1..100, default 20), `offset` (>= 0, default 0), results ordered by id, a JSON list response with an `X-Total-Count` header holding the matches before pagination, 422 on invalid parameters; every existing behaviour and test kept; README updated.'; fi
cat <<EOT
Review a software delivery ("Delivery $L") for quality. Do NOT modify the original. Copy it: \`rm -rf /tmp/review-$L && cp -a /tmp/okt-$N/proj /tmp/review-$L\`; work only there (recreate venv: \`cd /tmp/review-$L && rm -rf .venv && uv venv -q .venv && uv pip install -q --python .venv/bin/python fastapi uvicorn pytest httpx\`). Review branch \`main\` only; ignore \`.omakiten\`, worktrees and build history.
Spec: $SPEC
$(cat $BENCH/rubric.md)
If you start a server, use a free port unique to you (pick one between 18000 and 18999).
EOT
```

`rubric.md`:

```text
Score each 1–5 with evidence (file:line, command output):
1. Functional correctness — probe every route the spec requires, its status codes (201/200/204/404/409/422) and, for the search spec, `q` (case-insensitive, title or author), `limit` 1..100 default 20, `offset` >= 0, ordering by id, `X-Total-Count` before pagination, invalid parameters → 422; missing/invalid/overflow ids; invalid bodies (empty/whitespace, wrong types, bool year, string year, huge strings, NUL, raw-JSON "\ud800", NaN, Infinity).
2. Data integrity & SQLite — parameterized SQL (including the search), DB-level uniqueness incl. normalization, transactions, connection lifecycle; concurrency: run uvicorn and hammer with ≥50 parallel threads (mixed create/read/update/delete/search) and report the 5xx count.
3. Validation & error handling — try hard to provoke a 500; consistent error bodies.
4. Test quality — run the suite (count/time); mutate: remove uniqueness, change a status code, remove a 404, drop the commit of create and of update, and (search spec) break the case-insensitive match and the total count; report survivors; revert.
5. Structure & maintainability — layout, duplication, dead code, scope.
6. Docs — run what the README says.
Output ≤350 words, Portuguese: score table, total /30, 3 most serious defects with reproduction, 3 strongest points. Mark unverified items "lido, não executado". Delete your copy at the end.
```

### Brownfield base (`$BENCH/base/brownfield`)

The base deliberately leaves PUT and DELETE without the 64-bit id bound that GET has, to see whether a flow fixes an inherited defect. None did.

`.gitignore`:

```text
.venv/
__pycache__/
*.db
.pytest_cache/
```

`pytest.ini`:

```ini
[pytest]
pythonpath = .
```

`README.md`:

````markdown
# Books API

FastAPI + SQLite CRUD for books.

## Run

    .venv/bin/uvicorn app:app

## Test

    .venv/bin/pytest

## Endpoints

| Method | Path | Result |
| --- | --- | --- |
| POST | /books | 201 created, 409 duplicate isbn, 422 invalid |
| GET | /books | 200 list of books |
| GET | /books/{id} | 200 or 404 |
| PUT | /books/{id} | 200, 404, 409 or 422 |
| DELETE | /books/{id} | 204 or 404 |
````

`app.py`:

```python
import os
import sqlite3

from fastapi import Depends, FastAPI, HTTPException, Path, Response
from pydantic import BaseModel, ConfigDict, Field, StrictInt, StrictStr

DB_PATH = os.environ.get("BOOKS_DB_PATH", "books.db")

SCHEMA = """
CREATE TABLE IF NOT EXISTS books (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    author TEXT NOT NULL,
    year INTEGER NOT NULL,
    isbn TEXT NOT NULL UNIQUE
)
"""


class BookIn(BaseModel):
    model_config = ConfigDict(extra="forbid")
    title: StrictStr = Field(min_length=1, max_length=200)
    author: StrictStr = Field(min_length=1, max_length=200)
    year: StrictInt = Field(ge=1, le=9999)
    isbn: StrictStr = Field(min_length=1, max_length=32)


class Book(BookIn):
    id: int


def create_app(db_path: str = DB_PATH) -> FastAPI:
    app = FastAPI(title="Books API")

    def connection():
        conn = sqlite3.connect(db_path, check_same_thread=False)
        conn.row_factory = sqlite3.Row
        try:
            yield conn
        finally:
            conn.close()

    with sqlite3.connect(db_path) as conn:
        conn.execute(SCHEMA)

    def fetch(conn, book_id: int) -> Book:
        row = conn.execute("SELECT * FROM books WHERE id = ?", (book_id,)).fetchone()
        if row is None:
            raise HTTPException(status_code=404, detail="Book not found")
        return Book(**dict(row))

    @app.post("/books", status_code=201, response_model=Book)
    def create_book(book: BookIn, conn=Depends(connection)):
        try:
            with conn:
                cur = conn.execute(
                    "INSERT INTO books (title, author, year, isbn) VALUES (?, ?, ?, ?)",
                    (book.title, book.author, book.year, book.isbn),
                )
        except sqlite3.IntegrityError:
            raise HTTPException(status_code=409, detail="ISBN already exists")
        return fetch(conn, cur.lastrowid)

    @app.get("/books", response_model=list[Book])
    def list_books(conn=Depends(connection)):
        rows = conn.execute("SELECT * FROM books ORDER BY id").fetchall()
        return [Book(**dict(r)) for r in rows]

    @app.get("/books/{book_id}", response_model=Book)
    def get_book(book_id: int = Path(gt=0, le=2**63 - 1), conn=Depends(connection)):
        return fetch(conn, book_id)

    @app.put("/books/{book_id}", response_model=Book)
    def update_book(book: BookIn, book_id: int, conn=Depends(connection)):
        fetch(conn, book_id)
        try:
            with conn:
                conn.execute(
                    "UPDATE books SET title = ?, author = ?, year = ?, isbn = ? WHERE id = ?",
                    (book.title, book.author, book.year, book.isbn, book_id),
                )
        except sqlite3.IntegrityError:
            raise HTTPException(status_code=409, detail="ISBN already exists")
        return fetch(conn, book_id)

    @app.delete("/books/{book_id}", status_code=204)
    def delete_book(book_id: int, conn=Depends(connection)):
        with conn:
            cur = conn.execute("DELETE FROM books WHERE id = ?", (book_id,))
        if cur.rowcount == 0:
            raise HTTPException(status_code=404, detail="Book not found")
        return Response(status_code=204)

    return app


app = create_app()
```

`tests/test_books.py`:

```python
import pytest
from fastapi.testclient import TestClient

from app import create_app


@pytest.fixture
def client(tmp_path):
    return TestClient(create_app(str(tmp_path / "books.db")))


def book(**kw):
    data = {"title": "Dune", "author": "Frank Herbert", "year": 1965, "isbn": "9780441013593"}
    data.update(kw)
    return data


def test_create_and_get(client):
    created = client.post("/books", json=book())
    assert created.status_code == 201
    got = client.get(f"/books/{created.json()['id']}")
    assert got.json()["title"] == "Dune"


def test_duplicate_isbn_conflicts(client):
    client.post("/books", json=book())
    assert client.post("/books", json=book(title="Other")).status_code == 409


def test_list_returns_all(client):
    client.post("/books", json=book())
    client.post("/books", json=book(isbn="2"))
    assert len(client.get("/books").json()) == 2


def test_update_persists(client):
    book_id = client.post("/books", json=book()).json()["id"]
    assert client.put(f"/books/{book_id}", json=book(title="Dune Messiah")).status_code == 200
    assert client.get(f"/books/{book_id}").json()["title"] == "Dune Messiah"


def test_delete_then_missing(client):
    book_id = client.post("/books", json=book()).json()["id"]
    assert client.delete(f"/books/{book_id}").status_code == 204
    assert client.get(f"/books/{book_id}").status_code == 404
    assert client.delete(f"/books/{book_id}").status_code == 404
```
