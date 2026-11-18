# One-time confirmations

**English** · [Русский](CONFIRMATIONS.ru.md)

Use a single-use grant for protected actions. The default channel is `chat`. In `terminal`, use only terminal grants. Trial rollback is automatic.

## Confirm in chat

1. Describe one action. For team-wide autonomy, put `{"mode":"autonomous"}` in params.json.

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> confirm describe --action policy.set --target system --file params.json
```

2. Show `question` and the supplied buttons verbatim. Accept only the user's reply to that card. End the request after decline or dismissal.
3. On consent, send the native reply ID, verbatim reply, and unchanged question as `message_ref`, `answer`, and `asked`. Use `--file chat.json` or `--file -` with JSON stdin. Never build shell strings from user text.

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> confirm chat --action policy.set --target system --hash <intent-sha256> --file chat.json
```

4. Run the authorized action as `--actor user` with the code via stdin (`--confirmation -`). The journal records the question, answer, channel, times, and hashes. Reply IDs are single-use.

If the owner answers a card in another chat, `message_ref` may contain the actual relay reference for that reply.

`policy channel terminal` tightens the policy without a grant. To switch back, describe `policy.channel` for `system` with `{"channel":"chat"}`, obtain a terminal grant, and run `--actor user --confirmation - policy channel chat`. A channel change increments the policy revision.

The card lists key parameters separately and shows an eight-character action code. Pass the full `hash` from `confirm describe --json` to the issuer. The grant binds the full hash, payload, target, and revision.

For the second installation card, describe `install` with `{"mode":"autonomous","adapter":"native","source":"https://github.com/alexzah-droid/GrokBotDevTeam"}` plus `section_id` when selected. Record the reply to that card. Pass its grant to `--actor installer --confirmation - install --mode autonomous`.

For a new or changed binding, the Lead runs `project bind` without a card in team mode `autonomous`. In `approval`, the Lead needs a single-use `project.bind` grant. Describe it for target `project` before applying the same project file. When acting as `user`, a new or changed binding requires a grant in either mode. The card shows the path and full command with shell quoting for display only. A repeated binding with the same path, command, and timeout returns existing state.

## Confirm in a terminal

Prepare the plan with `confirm describe`. The human runs the issuer in an interactive terminal.

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime> --actor user confirm issue --action policy.set --target system --hash <intent-sha256> --ttl 300
```

The issuer prints the action and returns a code after the human types `ISSUE`. Noninteractive calls are rejected. Bots leave issuance to the human. The database and events keep the code's SHA-256, expiration, and binding. Exports omit the plaintext code.

Pass the resulting code via `--confirmation -` for the exact action.

Execution consumes the code in the same transaction. Invalid, mismatched, expired, or reused codes and changed parameters are rejected. A failed or rolled-back transaction leaves the code unused. A committed action consumes it even when a later stage is deferred.

## Action bindings

| Action | `--action` | `--target` | Payload for describe |
| --- | --- | --- | --- |
| Set global or per-role mode | `policy.set` | `system` or role | `mode` set to `approval`, `autonomous`, or `inherit` |
| Return to chat | `policy.channel` | `system` | `{"channel":"chat"}` |
| Install in autonomous mode | `install` | Lead ID | `mode`, `adapter`, `source`, and `section_id` if selected |
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
| Bind project with a grant | `project.bind` | `project` | Full repository, command, timeout, and evidence |
| Service control as user | `service.start` or `service.stop` | `service` | None |

An approval card displays up to 40 lines and 2,000 characters of the diff. A truncated preview shows the number of visible lines and the total. Use `diff <experiment-id>` to show the full diff. Terminal issuance displays the full diff and requires `--reviewed-diff <diff_hash>` from describe. Both channels bind the grant to the full diff. A change to the candidate, report, or policy revision invalidates that binding.

Hash `request_id` as a UTF-8 string without JSON quotes. Task creation consumes the automatic acceptance grant. The later acceptance uses the stored delegation. A Lead creating a regular task at the user's request needs no grant if automatic acceptance is disabled.

For actions that take a JSON file, pass the same data to describe and execute. If a field requires `evidence` before the card is answered, use an existing request or demo reference. The journal stores the reply or relay reference separately in `message_ref`.

## Trust boundary

The chat channel assumes a trusted Lead and protects against mistakes, replay, and stale state. A bot on the same host can forge reply data, reach terminal issuance through a pseudo-terminal or direct Python import, or write a grant into SQLite. See [Security](../SECURITY.md).
