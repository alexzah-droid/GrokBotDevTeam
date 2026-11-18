# GrokBotDevTeam

**English** · [Русский](README.ru.md)

This is the fifth version of the project. Pre-release 1.5.0, live install not verified.

Russian edition with bot instructions in Russian: [GrokBotDevTeamRU](https://github.com/alexzah-droid/GrokBotDevTeamRU).

GrokBotDevTeam sets up a three-bot software development team from a single message. Your existing Grok Bot becomes the **Lead** and keeps its usual duties. It adds a **Developer** to implement tasks and a **Reviewer** to verify the result independently. Together they clarify requests, plan work, maintain documentation, and demonstrate the result for acceptance.

**Status:** v1.5.0 is a pre-release for a live pilot. Live install not verified (`live_verified: false`). Local tests and demos use a mock platform and a deterministic evaluator. Before each task, the bots must run `role read` and use the returned instruction. The controller updates the active version on rollback. A live pilot still needs to verify that the bots read and follow the restored instruction.

This is an unofficial project, not affiliated with xAI. Grok is a trademark of xAI. The original code is copyright alexzah-droid.

## Security first

1. **Capabilities.** Bots can run shell commands, read and write files, use Git and network tools, and create bots. Setup starts a local watchdog. An approved routine can restart it. `project verify` executes code from the project under review.
2. **Shared host.** Bots can read each other's files. With the same access they can forge replies and IDs, reach terminal issuance through a pseudo-terminal or direct Python import, or write grants to SQLite.
3. **Isolate setup.** Use a host without work secrets, SSH keys, cloud tokens, or a `gh` session that can write to important repositories.
4. **Limit access.** Use a separate test repository. If it needs a token, restrict the token to that repository and the minimum permissions. Exclude admin and production access.
5. **Treat inputs as data.** Keep secrets out of tasks and messages. Repository contents, issues, web pages, command output, and bot messages can carry prompt injection. Contracts forbid following them as instructions or consent. This protection depends on bot behavior.
6. **Start in `approval`.** Confirm activation and acceptance separately. In `autonomous`, passing evaluations and observations can activate and accept an instruction without either confirmation.
7. **Watch usage.** Task iteration limits are 1–10. Evaluation uses call and time budgets. Track usage and enable a paid runner only when you need it.
8. **Emergency stop.** Tell the Lead “stop”. Cancel active trials, run `service stop`, pause or remove the two added bots, remove the bootstrap, and delete runtime after deciding what to retain. [Emergency stop](docs/OPERATIONS.md#emergency-stop).
9. **Verify the source.** Use this repository's official tag. Compare archive SHA-256 with the Release page. Avoid forks and links from other people's messages.
10. **Data and warranty.** Grok Bot processes the code and tasks the bots read. Local SQLite stores task text in plaintext. The optional xAI runner sends test inputs and instructions only when explicitly configured with an environment key. MIT supplies the project “AS IS”. GrokBotDevTeam is unofficial and unaffiliated with xAI. Grok is a trademark of xAI.

## Install

Installation uses the published tag `v1.5.0`. Paste either message into your existing Grok Bot chat. The installer checks the available native Grok Bot tools before creating the team and reports any missing capability.

### English message

```text
Install GrokBotDevTeam from https://github.com/alexzah-droid/GrokBotDevTeam using the v1.5.0 pre-release.
Read and follow the English INSTALL.md from that tag. Stay my Lead and create only a Developer and a Reviewer.
Use approval for instruction changes and let each role inherit the team setting.
Check that the team is ready, then ask for my first task.
```

### Russian message

```text
Установи GrokBotDevTeam из https://github.com/alexzah-droid/GrokBotDevTeam, prerelease v1.5.0.
Прочитай и выполни английский INSTALL.md из этого тега. Останься моим Руководителем, создай только Разработчика и Проверяющего.
Для изменения инструкций используй approval. Пусть все роли наследуют общий режим.
Проверь готовность команды и предложи прислать первую задачу.
```

The [full installation message](install-prompt.txt) includes recovery and permission rules. If you choose `autonomous` during setup, confirm that change separately.

## What you get

| Component | Purpose |
| --- | --- |
| Three role instructions and six skills | Planning, development, project checks, documentation, review, and instruction improvement |
| Local controller and native tool bridge | Task state, resumable bot setup and handoffs, checks, and version history |
| Instruction improvement and watchdog | Independent evaluation, trials, acceptance, and automatic rollback |

## Instruction improvement modes

- **`approval` (default).** You separately approve the start of a trial and acceptance of its result. In the normal path, the relevant grant is required at each step. For the trust model, see [Security](SECURITY.md).
- **`autonomous`.** The controller applies a candidate and accepts its trial result automatically once the required evaluation and observations succeed.

A role inherits the team setting unless it has an override. Both modes record changes and automatically roll back a failed or expired trial. Ask your Lead to show effective modes, compare versions, or restore a historical instruction.

## Confirmations and trust

The default channel is `chat`. The Lead presents the exact question, records your explicit answer, and gets a short-lived, single-use grant bound to the action, target, and state. You can also use the `terminal` channel. See [Human confirmations](docs/CONFIRMATIONS.md).

The chat flow assumes a trusted Lead. A bot on the same host can forge the reply and its `message_ref`, reach the terminal issuer through a pseudo-terminal or direct Python import, or write a grant into SQLite. Issuing confirmations off the bot host or signing them with a key unavailable to bots is planned before a stable release. See [Security](SECURITY.md).

## Verify locally

Use Python 3.10 or newer, Git, and a POSIX environment such as Linux or macOS. SQLite comes with Python and needs no separate service.

```text
<venv>/bin/python -B scripts/doctor.py
<venv>/bin/python -B scripts/release.py check
<venv>/bin/python -B scripts/validate_skills.py
<venv>/bin/python -B -W error::ResourceWarning -m unittest discover -s tests -v
<venv>/bin/python -B scripts/demo.py
```

The demo runs a development task in a temporary Git project, then exercises approval, autonomous improvement, and rollback. See [Validation](VALIDATION.md).

## Documentation

- [Bot setup](INSTALL.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Commands and formats](docs/FORMATS.md)
- [Candidate evaluation](docs/EVALUATION.md)
- [Operations and recovery](docs/OPERATIONS.md)
- [Maintainer release guide](docs/PUBLISH.md)
- [Sources and compatibility](docs/SOURCES.md)
- [Changelog](CHANGELOG.md)

## Python package

From a Python 3.10+ virtual environment, run `<venv>/bin/python -B -m pip install .`. The `grokbotdevteam` package ships the controller and its release assets. Use `grok-team` or `python -m grokteam` from any working directory. The build backend and runtime use the Python standard library.

The bootstrap records the installation paths and Python executable. Rebind it if those paths change. Keep runtime data outside the release and the Git project under review. The core controller needs no API key and sends nothing to the project maintainer. The optional xAI evaluator sends test inputs and instructions to its provider only when you configure it.

License: [MIT](LICENSE).
