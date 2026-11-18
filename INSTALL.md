# Install the team with one message

Install as the user's personal bot. Create exactly one Developer and one Reviewer. Keep your existing duties and leave other teams alone. This request authorizes those steps. Use available tools within their permissions.

## 1. Prepare the environment

Get pre-release `v1.5.0` from https://github.com/alexzah-droid/GrokBotDevTeam. Stop and report a missing tag. Use separate permanent absolute paths for `<release>`, `<venv>`, and `<runtime>`. Choose Python 3.10+ (`<python>`) for the venv. Pass argv with exact paths.

```text
git clone --depth 1 --branch v1.5.0 https://github.com/alexzah-droid/GrokBotDevTeam.git <release>
<python> -m venv <venv>
<venv>/bin/python -B <release>/scripts/doctor.py
<venv>/bin/python -B <release>/scripts/release.py check
<venv>/bin/python -B -m pip install --no-deps --no-index <release>
```

Find tools to create bots, deliver targeted messages, save durable memory, and verify results. Report any missing capability. Prefix controller commands with:

```text
<venv>/bin/python -B <release>/teamctl.py --workspace <runtime>
```

## 2. Start installation

Run `--actor installer install --lead-id <lead-id>` with your actual ID. Reuse the runtime on retry. Defaults are `approval` and `chat`. Only if the user separately chooses autonomy, run `confirm describe --action install --target <lead-id> --file params.json` with `{"mode":"autonomous","adapter":"native","source":"https://github.com/alexzah-droid/GrokBotDevTeam"}`. The installer records an explicit human reply in chat. The human issues grants in terminal mode. Apply the grant to `install --mode autonomous` per [the confirmation procedure](docs/CONFIRMATIONS.md).

## 3. Work through the queue

Use `--actor installer` without `--mock` for live installs. Read `bridge pending`. For `pending`, run `bridge begin <op>`, call the native tool, then run `bridge ack <op> --file receipt.json`. For `dispatched`, find the actual result in history. Stop if uncertain. Read the queue after each acknowledgement.

- **`create`.** Create a bot named `data.name`. A matching name alone does not establish team membership. Record the actual `agent_id` and `external_ref` of the call or result.
- **`bind`.** Save `data.bootstrap` in your role's durable memory or send it to the assigned bot to save. Preserve existing memory. In that role, run the startup argv and read the prompt and referenced skills, then run `--actor <role> role probe <role>`. Verify persistence with a platform tool and a repeat check where possible. Add the actual `external_ref` and `"persistent":true` to the probe JSON only after verification.
- **`smoke`.** Send `--actor <role> role smoke <role>` to the Developer, then the Reviewer, to run in their own contexts. Add the native message `external_ref` to the receipt.

## 4. Confirm readiness

Run `--actor installer service start`. Read `status` until `watchdog_alive=true`. Use the [routine template](routines/watchdog-restart.md) for recovery only if the user chooses it. Verify its trigger.

Run `--actor installer install-finish`. At `ready`, report the three roles, team mode, and overrides. Ask for the first task. Otherwise state completed steps and the blocker. On reentry read `status` and resume the same queue.

[Operations, recovery, and removal](docs/OPERATIONS.md) · [Trust model](SECURITY.md).
