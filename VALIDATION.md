# Release 1.6.0 validation

This is the fifth version of the project. Local tests use a simulated platform and a deterministic evaluator, with no paid model calls.

## Live pilots

All three pilots are dated November 18, 2026. This release uses their results because the team behavior matches 1.5.3.

| Version | Result |
| --- | --- |
| 1.5.1 | The pilot summary records setup of two roles, passing probes, a running watchdog, and task acceptance after human approval. A separate detailed report from the same date describes a controller simulation. |
| 1.5.2 | Russian edition. The operator approved autonomous mode and both bots appeared in the selected folder. Independent review, demo, and task acceptance passed. |
| 1.5.3 | English edition. Both bots appeared in the selected folder and waited for setup. Installation, independent review, demo, and card-based acceptance passed. |

## Reproduce locally

Run with Python 3.10 and 3.13:

```text
<venv>/bin/python -B scripts/doctor.py
<venv>/bin/python -B scripts/release.py check
<venv>/bin/python -B scripts/validate_skills.py
<venv>/bin/python -B -W error::ResourceWarning -m unittest discover -s tests -v
<venv>/bin/python -B scripts/demo.py
<venv>/bin/python -B scripts/release.py build
```

The demo runs a task, independent checks, acceptance, and rollback. It exercises separate chat grants for starting and accepting an instruction trial. Tests check the active instruction, exact question, full diff binding, explicit reply, one-time use, strict channel, and consent for autonomous setup.

## Inspect the archives

Compare SHA-256 across two ZIP and TAR.GZ builds. Compare file contents with the manifest. The root is `GrokBotDevTeam-<version>/`. Release files exclude runtime state, SQLite, environment files, logs, caches, and local reports.

## Checks in your account

In your account, check native bot creation, durable memory, receipts, cards and reply IDs, execution of `role read`, application of instructions, and watchdog recovery. Processes on a shared host can forge confirmations. See [Security](SECURITY.md).
