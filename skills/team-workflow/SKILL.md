---
name: team-workflow
description: Plan, hand off, and accept tasks across three roles.
---

# Team tasks

Take the prefix before `role read` from the startup rule. Keep your `actor`. Use `--actor user` only with a valid grant for that action. Pass JSON by file or stdin without shell interpolation. [Formats](../../docs/FORMATS.md).

1. **Lead.** Check `status` at task start. If `watchdog_alive=false`, run `service start` and wait for a heartbeat. Native installs need it to activate instructions.
2. Bind the authorized Git project and command via `project bind --file project.json`. Record goal, criteria, references, and iteration budget via `task create --file request.json`. Automatic acceptance needs a separate `task.auto_accept` grant for the whole request with `auto_accept_ref`.
3. Plan via `task plan <id> --file plan.json`. In `bridge pending`, mark `bridge begin <op>`, send `data`, and record the actual receipt via `bridge ack <op> --file receipt.json`. Reconcile `dispatched` with history. Check delivery by receipt and execution by task state.
4. **Developer and Reviewer.** Start with `task show <id>`. Check project, revision, and state. After a change, tell the Lead about `pending`. The Lead handles delivery. Explain blockers.
5. **Lead.** Return findings within budget. Open a new task for new requirements. To stop a task, give a reason with `task block|cancel <id> --file reason.json`. After a current `pass`, record `task demo <id> --file demo.json`. Manual acceptance requires `--actor user --confirmation - task accept <id> --file accept.json` with a `task.accept` grant.

## Get and use a grant

Run `confirm describe --action … --target …` with parameters via `--file`. In `chat`, display `question` verbatim on a “Yes, I confirm” / “No” card. On consent save `asked`, `answer`, and the native `message_ref` in JSON. Run `confirm chat --action … --target … --hash … --file chat.json` or use `--file -`. Give the code via stdin with `--actor user --confirmation -` and run exactly that action. In `terminal`, the human runs `confirm issue`. Use `policy channel terminal` without a grant or `policy channel chat` with a terminal grant. [Procedure and bindings](../../docs/CONFIRMATIONS.md).

After a break, check `status`, `task list`, and `task show`. Report ID, state, version, evidence, and the next role.
