# Pre-release 1.5.0 validation

This is the fifth version of the project. Live install not verified (`live_verified: false`). Local tests use a simulated platform and a deterministic evaluator, with no paid model calls.

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

## Live pilot scope

Check native creation, durable memory, receipts, cards and reply IDs, instruction application, and watchdog restart in a live pilot. Processes on a shared host can forge confirmations. See [Security](SECURITY.md).
