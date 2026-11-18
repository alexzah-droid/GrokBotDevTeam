# Install the team with one message

Remain the user's personal bot. Keep existing memory and duties. Create only a Developer and a Reviewer.

## 1. Prepare the environment

Get `v1.6.0` from https://github.com/alexzah-droid/GrokBotDevTeam. Use the stable GitHub release marked `Latest`. Use separate permanent absolute paths for `<release>`, `<venv>`, and `<runtime>` and Python 3.10+ (`<python>`).

```text
git clone --depth 1 --branch v1.6.0 https://github.com/alexzah-droid/GrokBotDevTeam.git <release>
<python> -m venv <venv>
<venv>/bin/python -B <release>/scripts/doctor.py
<venv>/bin/python -B <release>/scripts/release.py check
<venv>/bin/python -B -m pip install --no-deps --no-index <release>
```

Discover native tools. Report missing creation, handoff, memory or check tools. Use exact argv:

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime>
```

## 2. Choose mode and folder

First, before creating bots: `--actor installer install-options --lead-id <lead-id>` returns `mode_card`. Show it verbatim. Record the choice via `--choice approval|autonomous`. Decline, dismissal, or no reply means `approval`. On resume, read the saved choice without asking again.

Find your folder using a native tool. If unknown, call `ListSections`. Show `folder_card` with up to five folders and the no-folder option. Put yours first if known. Use real IDs. No list means no folder.

Before `--actor installer install --lead-id <lead-id>` add `--section-id <id>` if a folder was chosen. For `autonomous`, first show the second card from `confirm describe --action install --target <lead-id> --file params.json`. Only an explicit yes earns a grant via `confirm chat` for `install --mode autonomous`. On decline, dismissal, or silence, record `approval` and install without a grant. On resume use the original reply, or keep `approval`. [Confirmation parameters](docs/CONFIRMATIONS.md).

## 3. Work through the queue

Use `--actor installer` without `--mock`. Read `bridge pending`. For `pending`: `bridge begin <op>`, native call, `bridge ack <op> --file receipt.json`. For `dispatched`, reconcile history before retrying.

- **`create`.** Pass `name`, `description`, and `section_id` from `data` to `CreateAgent` verbatim. Omit the folder if unsupported or retry without it on explicit failure without creation. Record actual `agent_id`, `external_ref`, and `section_id` (or `null`). Matching names alone prove nothing. After `bridge ack`, immediately `bind` this bot before creating the next one.
- **`bind`.** Save `data.bootstrap` to the intended bot's memory. Have it read the rule and skills and run `--actor <role> role probe <role>`. Check persistence natively and repeat the check where possible. Add `"persistent":true` and `external_ref` to the verified probe.
- **`smoke`.** Developer, then Reviewer run `--actor <role> role smoke <role>`. Add the handoff `external_ref`. If `SendToAgent` or `UpdateMemory` returns no ID, record `agent-message:<id>:<step>:<time>` or `native-memory:profile:<id>:<step>:<time>` per [formats](docs/FORMATS.md). This records an observation rather than delivery.

## 4. Confirm readiness

Run `--actor installer service start` and check `watchdog_alive=true` in `status`. Run `--actor installer install-finish`. When `ready`, report roles, mode, overrides, and actual or unknown folders. Ask for the first task. Offer optional startup `service start` in one line via the [routine template](routines/watchdog-restart.md). On failure report the blocker. On resume check `status` and queue.

[Operations](docs/OPERATIONS.md) · [Trust model](SECURITY.md).
