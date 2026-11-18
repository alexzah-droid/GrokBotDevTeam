# Command and data formats

Use `<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> --actor <role>` as the command prefix. Keep runtime outside the release and project. The supported actors are `lead`, `developer`, `reviewer`, `user`, `installer`, and `controller`. Pass structured JSON via `--file <path>` or `--file -` for stdin. Use an argument array. A human evidence reference remains separate from the [single-use grant](CONFIRMATIONS.md).

## Installation and native bridge

Run `install --lead-id <actual-id>`, then handle `bridge pending` with `bridge begin <operation-id>` and `bridge ack <operation-id> --file receipt.json`. Follow the [installation guide](../INSTALL.md) for native receipt fields and probes. Run `service start`, `install-finish`, and `status` to finish. Each role calls `role read <role>` and performs its own `role probe <role>` or `role smoke <role>` when requested. `install --mock` is for tests.

## Project

Use `project bind --file project.json` with a repository, authorized command, timeout, and evidence reference.

```json
{
  "repository": "/absolute/path/to/project",
  "command": ["/absolute/venv/bin/python", "-B", "-m", "unittest", "discover", "-s", "tests"],
  "timeout": 60,
  "evidence": "actual-user-message-reference"
}
```

The Reviewer can add up to eight checks with `project add-check --file extra-argv.json`. The file contains one argument array. The base profile remains in effect.

## Task

Use `task create --file request.json` with a stable request ID, user reference, goal, criteria, and 1–10 allowed iterations. A repeated ID with the same request returns the existing task. A different request conflicts.

```json
{
  "request_id": "native-message-id",
  "user_ref": "native-message-reference",
  "goal": "Implement addition",
  "criteria": [{"id":"c1","text":"2 + 3 returns 5"}],
  "max_rounds": 3,
  "auto_accept": false
}
```

Automatic acceptance needs `auto_accept: true`, an explicit `auto_accept_ref`, and an action-bound grant. For all task transitions include a unique `event_id`. Repeating a transition with the same event ID is idempotent when its contents match.

- Lead runs `task plan <id> --file plan.json` with `steps`.
- Developer runs `project verify --task <id>` and `task submit <id> --file submission.json` with `summary` and a tracked `path` or a `not_needed` explanation for each of `architecture`, `user`, and `agent` in `docs`.
- Reviewer runs an independent `project verify --task <id>` and `task review <id> --file review.json`. For `pass`, provide each criterion exactly once with `id`, `passed: true`, and `evidence`. For `changes_requested` or `blocked`, supply `findings`.
- Lead runs `task demo <id> --file demo.json` with reproducible `steps` and `usage`. Manual `task accept <id> --file acceptance.json` uses the `user` actor, grant, and human `evidence`.
- Lead or user can run `task block` or `task cancel` with the task ID and a JSON file containing `event_id` and `reason`. For user actions, obtain the matching grant. Inspect `task list` and `task show <id>` after interruptions.

## Policy, versions, and watchdog

`policy show` returns effective modes. Use `policy set <mode> [--role <role>] --evidence <human-message>` with a grant. `inherit` removes a role override. `policy channel terminal` tightens the channel without a grant. `policy channel chat` requires a terminal grant.

`history <role>`, `version <version-id>`, `diff <before-id> <after-id>`, and `diff <experiment-id>` show versions and changes. Use `events --object <id>` or `export` for the audit trail. `restore <role> <version-id> --evidence <human-message>` requires a grant, a compatible contract, and no current experiment or task.

`service start` accepts installer, lead, controller, or user. `service stop` accepts installer or user after trials are closed. User service actions require grants. Run `watch --once` as controller to check deadlines.
