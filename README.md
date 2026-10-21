# Orchestra workflow for Omakiten

Orchestra is the Omakase workflow plus autonomous agent teams. It keeps
Omakase's evidence-led development practices, laws, skills, templates, and
guards, then adds coordinated execution for projects that benefit from a
dedicated development, review, and documentation pipeline.

## What Orchestra adds

- **Bucket agent teams.** Development, review, and documentation buckets can
  start specialist agents. Review members report to a lead, which decides
  whether work returns to development or advances.
- **Autonomous dev → review → docs pipeline.** Completed implementation moves
  through security and test review, documentation, integration, and completion
  without manual handoffs when all required evidence is present.
- **Plan auditor.** A plan-completion gate starts an auditor that checks the
  completed plan against its goal before it can be marked complete.

## Install and activate

From the target project, install this checkout. A published repository can also
be installed by its catalog name or Git URL:

```sh
okt preset catalog
okt preset add /path/to/okt-workflow-orchestra
okt preset use orchestra --confirm
okt config validate
okt init --name "My project" --slug my-project
```

Orchestra runs agent harnesses through its workflow hooks, so selecting it
requires the explicit `--confirm` flag. Add `--scope global` to `add` and `use`
for a user-wide installation. Installation captures a complete snapshot; the
checkout is not needed afterward.

## Customize

`preset.yaml` identifies this package. `config/preset.yaml` links its modules:

| File in `config/` | Contents |
| --- | --- |
| `settings.yaml` | Output, runtime options, and agent next-step prompts |
| `views.yaml` | Columns and visible fields |
| `events.yaml` | Event definitions and delivery |
| `hooks.yaml` | Notifications and agent-team hook bindings |
| `workflows.yaml` | Buckets, teams, transitions, plan guards, and operations |
| `surfaces.yaml` | CLI, TUI, and HTTP API operation availability |
| `catalog.yaml` | Active skill and law references |
| `personas.yaml` | Persona roles and entity bindings |
| `bindings.yaml` | CLI command bindings |

Simple records use compact YAML rows; nested rules use indented blocks. Entity
bodies and the color theme live in sibling directories. Omakiten edits the
module that owns each changed value and preserves the imports.

Each workflow command is a skill in `skills/<command-name>.md`. Its `command`
frontmatter names the command, declares optional parameters, and lists related
commands. `config/bindings.yaml` selects its persona and supporting context;
`okt command list` and `okt command resolve <name>` read this package.

Editing the active package through Omakiten creates an independent
`orchestra-local` preset on the first change. Further edits retain that name.
`okt preset list` reports active and modified snapshots; use a full id when a
name has several revisions.

## Share

```sh
okt preset export --output orchestra.md
okt preset import --file orchestra.md
okt preset use orchestra --confirm
```

The Markdown contains the whole package. Imports install without activation.
Use `okt preset --help` and each subcommand's help for scope and output flags.

## Validation

Omakiten validates the manifest, configuration schema, required references,
relative file paths, content identity, and workflow contracts. Hook scripts are
user-provided code; installation does not inspect their implementation. Install
this checkout and validate the selected configuration:

```sh
okt preset add . --scope global
okt preset use orchestra --scope global --confirm
okt config validate
```
