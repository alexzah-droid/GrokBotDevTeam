# Architecture

## Three bots and one controller

The user's existing bot is the Lead. Setup adds a Developer and a Reviewer. Grok Bot runs the roles and delivers messages through its native tools. The Python controller stores task state, verifies transitions, runs the assigned project checks, and queues operations. A native tool receipt records each real delivery.

## Instructions and skills

Before each team task, the bootstrap runs `role read`. The result contains the active prompt, effective mode, and skill paths. Recorded version pointers show controller state. Check in your account that the bot reads and follows that prompt.

The prompt combines the role's contract with editable behavior. A candidate changes only the latter. Six file-backed skills ship with each release. Native private-skill registration is optional. Each edition uses the same controller and loads its own `grokteam/texts.json` for bootstrap, card, and role labels.

## Storage

Runtime holds `state.sqlite`, WAL, the lock, and the watchdog log. SQLite comes with Python and needs no separate service. The objects table stores tasks, operations, policy, roles, and experiments. Versions store immutable prompt text, parent, hash, and reason. Events form the audit journal.

Activating a version and recording the event happen in one transaction. Rollback records a new version containing the previous text. Use `history`, `version`, `diff`, `events`, and `export` to inspect the history.

## Project and acceptance

The controller fingerprints tracked or staged files in an external Git project. Unignored untracked files block verification. Internal symlinks are hashed, while external destinations and loops are rejected.

The Developer and Reviewer run the verification profile independently. Output, exit code, time, role, and snapshot are recorded. A check that modifies tracked source is invalid. Results bind to the snapshot and acceptance criteria. Changes after review invalidate demo or acceptance. The Reviewer can add checks without replacing the Lead's base profile. A profile change invalidates an older pass.

Criteria belong to the request. Changed requirements require a new request ID. Handoffs are asynchronous, while the workflow follows one verifiable result through review.

## Instruction improvement

Each role inherits the team mode unless it has its own setting. The role owner or Lead may propose changes. The controller selects an independent evaluator and uses the authorized held-out suite. It compares older and newer prompts against target, regression, and boundary cases within call and time budgets.

In approval mode, human grants bind to the candidate, baseline, report, diff, and policy revision. Applying the trial and accepting the result use separate grants. Autonomous trials apply and accept automatically after passing checks and sufficient observations. When either application or acceptance uses approval, final acceptance requires a grant.

Trials apply only when the role has no active work. A failed or expired trial rolls back. A local watchdog checks deadlines every five seconds and is required before new candidates can be applied in native setups. The [restart template](../routines/watchdog-restart.md) requires a supported routine or supervisor. While the host is off, the process cannot enforce deadlines. Its first sweep after restart handles expired trials.

## Trust boundaries

Role flags and references implement an auditable protocol. Bots on the same host can forge chat replies, reach the terminal issuer through a pseudo-terminal or direct Python import, and write grants into SQLite. Native receipts rely on the platform. See [Security](../SECURITY.md) and [Human confirmations](CONFIRMATIONS.md).

The controller can restore an instruction version. Completed external actions, personal memory, and project code remain separate from instruction history.
