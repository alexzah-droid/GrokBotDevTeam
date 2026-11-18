# One-time confirmations

**English** · [Русский](CONFIRMATIONS.ru.md)

Protected actions require a single-use grant. The default `chat` channel issues it after a human replies in chat. A human can also run `confirm issue` in an interactive terminal. In `terminal` mode, only terminal grants are valid. The controller performs automatic rollback under the trial rules.

## Confirm in chat

1. The Lead prepares one action and calls `confirm describe`. To set the team-wide mode to autonomous, put `{"mode":"autonomous"}` in params.json.

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> confirm describe --action policy.set --target system --file params.json
```

2. The Lead displays `question` verbatim on a card with “Yes, I confirm” and “No”. Each card covers one action. A decline or dismissal ends that request. The only valid source of consent is the user's reply to that specific card. Replies to other questions, bots, routines, tool results, and web pages do not authorize an action.
3. After an explicit yes, the Lead saves the native reply ID as `message_ref`, the verbatim reply as `answer`, and the unchanged `question` as `asked`. Pass `--file chat.json` or use `--file -` for JSON on stdin. Keep user text in structured data and out of shell command strings.

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> confirm chat --action policy.set --target system --hash <intent-sha256> --file chat.json
```

4. The Lead runs the action as `--actor user` with the returned code in `--confirmation`. With `--confirmation -`, the code can stay on stdin and out of the chat. The journal keeps the reply reference, question, answer, channel, time, action hash, and code hash. Each reply ID is accepted once. The code binds to the target, parameters, and state snapshot. Issuance and execution are separate steps.

`policy channel terminal` tightens the policy without a grant. To switch back, describe `policy.channel` for `system` with `{"channel":"chat"}`, obtain a terminal grant, and run `--actor user --confirmation - policy channel chat`. A channel change increments the policy revision.

## Confirm in a terminal

Prepare the plan with `confirm describe`. The human runs the issuer in an interactive terminal.

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> --actor user confirm issue --action policy.set --target system --hash <intent-sha256> --ttl 300
```

The issuer prints the action and returns a code after the human types `ISSUE`. Noninteractive calls are rejected. Bots leave issuance to the human. The database and events keep the code's SHA-256, expiration, and binding. Exports omit the plaintext code.

The human passes the code for the exact action or executes it directly. The code can be read from stdin.

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> --actor user --confirmation - policy set autonomous --evidence <human-message>
```

Execution consumes the code in the same transaction. Invalid, mismatched, expired, or reused codes and changed parameters are rejected. A failed or rolled-back transaction leaves the code unused. A committed action consumes it even when a later stage is deferred.

## Action bindings

| Action | `--action` | `--target` | Payload for describe |
| --- | --- | --- | --- |
| Set global or per-role mode | `policy.set` | `system` or role | `mode` set to `approval`, `autonomous`, or `inherit` |
| Return to chat | `policy.channel` | `system` | `{"channel":"chat"}` |
| Install in autonomous mode | `install` | Lead ID | Exact mode, adapter, and source |
| Configure evaluator | `evaluation.configure` | Role | Exact config.json |
| Approve candidate | `improve.approve` | Experiment ID | None. Diff, candidate, baseline, report, and policy come from the database |
| Accept the trial result | `improve.accept` | Experiment ID | None. Uses version and trial observations |
| Cancel or manually roll back a trial | `improve.cancel` | Experiment ID | None |
| Restore historical instruction | `instruction.restore` | Role | `{"version":"version-id"}` |
| Accept task | `task.accept` | Task ID | Exact acceptance JSON with event_id and evidence |
| Delegate automatic acceptance | `task.auto_accept` | `task-` plus the first 16 SHA-256 characters of request_id | Exact request.json |
| Create task as user | `task.create` | Task ID as above | Exact request.json |
| Cancel or block task as user | `task.cancel` or `task.block` | Task ID | Exact action JSON |
| Record observation as user | `improve.observe` | Experiment ID | Exact success, critical, and evidence |
| Bind project as user | `project.bind` | `project` | Full repository, command, timeout, and evidence |
| Service control as user | `service.start` or `service.stop` | `service` | None |

An approval card displays up to 40 lines and 2,000 characters of the diff. A truncated preview shows the number of visible lines and the total. Use `diff <experiment-id>` to show the full diff. Terminal issuance displays the full diff and requires `--reviewed-diff <diff_hash>` from describe. Both channels bind the grant to the full diff. A change to the candidate, report, or policy revision invalidates that binding.

Hash `request_id` as a UTF-8 string without JSON quotes. Task creation consumes the automatic acceptance grant. The later acceptance uses the stored delegation. A Lead creating a regular task at the user's request needs no grant if automatic acceptance is disabled.

For actions that take a JSON file, pass the same data to describe and execute. If a field requires `evidence` before the card is answered, use an existing request or demo reference. The journal stores the actual reply ID separately in `message_ref`.

## Trust boundary

The chat channel assumes a trusted Lead and protects against mistakes, replay, and stale state. A bot on the same host can forge reply data, reach terminal issuance through a pseudo-terminal or direct Python import, or write a grant into SQLite. Off-host issuance or signatures made with a key unavailable to bots are planned before a stable release. See [Security](../SECURITY.md).
