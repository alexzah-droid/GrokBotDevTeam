# Operations and recovery

**English** · [Русский](OPERATIONS.ru.md)

Talk to your Lead to operate the team. The Lead runs the commands below on your behalf, including database maintenance.

## State and history

`status` shows the team, roles, effective modes, project, queue, watchdog, and experiments. `task show` returns the current task result. Use `history`, `version`, `diff`, and `events` to inspect instructions. `export` produces a local JSON archive of versions and the event log. The controller sends no export to the project maintainer.

Keep passwords, API keys, and tokens out of instructions. The core controller needs none of them. Configure the optional runner through a protected environment. Keep runtime state, `.env`, and keys out of GitHub releases.

## Watchdog

A [routine template](../routines/watchdog-restart.md) can restart the watchdog through a supported platform routine. Enable it explicitly. An exception during a sweep is logged and the loop continues. Restarting a terminated process requires a routine or supervisor.

The Lead runs `service start` on session recovery and checks the heartbeat in `status`. Starting an already running service leaves one process. Logs go to `<runtime>/watchdog.log`.

Deadlines cannot be enforced while the host is off or the service has stopped. A native setup needs a fresh heartbeat to apply new candidates. The first sweep after restart rolls back expired trials. For diagnostics, use `--actor controller watch --once`. It checks the active policy.

Finish or cancel active trials before running `service stop` at the user's request. The stop request uses the process token and does not signal an arbitrary PID. The watchdog stops on its next sweep.

## Recover a pending native action

1. Read `bridge pending`.
2. For a dispatched operation, find its result in the platform's tool or message history.
3. If a bot was created, record its actual `agent_id` and `external_ref`. If you cannot identify the outcome, stop to avoid creating a duplicate.
4. A repeated ack with the same receipt is safe. A different receipt conflicts.

An error or missing reply leaves the operation pending reconciliation. The queue records delivery after it receives the receipt. Use the mock adapter for tests.

## Recover an interrupted evaluation

A candidate in `testing` is still unresolved. Leave interrupted external calls alone, since they may already have incurred charges. The user can cancel the experiment and start a new one. An `inconclusive` report also prevents application. If active work holds up a passing candidate, check its state and run `improve progress` once the work finishes. The watchdog can also advance an eligible candidate.

## Rollback

Manual cancellation, restore, and observations made as `user` require a single-use grant. Trial conditions can trigger rollback automatically. An evidence reference alone grants no authority. See [Human confirmations](CONFIRMATIONS.md).

A negative independent observation or an expired trial makes the controller record the previous instruction text as a new version. `history <role>` shows the candidate and rollback. The next `role read` should return the restored hash. The bootstrap requires a fresh read on each task.

If the current version differs from the expected candidate, the controller marks the role blocked and preserves the unexpected version. Inspect `version`, events, and compatibility. Once the active experiment is closed, `restore <role> <version-id> --evidence <human-ref>` creates a new version and clears the block with human confirmation.

Rolling back the instruction leaves completed publications, messages, expenses, and code changes in place.

## Backup

Redirect `export` to a file outside the release for a readable archive. To restore runtime state, take a consistent SQLite backup rather than copying one file while WAL is active. Use SQLite's backup API in a trusted shell session or stop the processes and copy the full runtime directory. The platform stores bot memories and native conversations separately.

## Update and disconnect

Each team pins a release. Editing release files in place causes an integrity error at `role read`. Moving to a different release requires a separate migration and contract check. Automatic migration across future versions is outside this pre-release.

To disconnect, finish work, cancel active trials with rollback, stop the watchdog, and remove the team bootstrap from each participant's memory at the user's request. The bots, conversation history, and user data remain on the platform. The Lead resumes its usual work.

## Emergency stop

1. Tell the Lead “stop”. It should block or cancel team tasks and wait for your decision. A contract is an instruction to the bot, not an operating-system process kill.
2. Disable any restart routine or supervisor. Cancel active trials with the required grants. Run `service stop` as `installer` or as `user` with a grant. Confirm the watchdog has stopped.
3. Pause or remove only the two added bots in Grok Bot. Remove the team bootstrap from the participants' memory and retain the Lead's previous instructions.
4. Close the controller and any remaining runtime processes. Export or back up records you need, then delete the runtime if you want to erase local state. Runtime deletion leaves platform conversations and past actions in place.

## Installation smoke test

The smoke test creates a temporary Git project. The Reviewer runs the same profile on a working and deliberately broken implementation. The broken variant must fail. This checks command execution and artifact access. Native delivery still needs verification in a live pilot.

By default, the watchdog sweeps every five seconds. `install-finish` needs a live heartbeat. After restart, use the virtual environment recorded in the bootstrap. Rebind the bootstrap if the paths move.
