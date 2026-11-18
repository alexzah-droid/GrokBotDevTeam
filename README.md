# GrokBotDevTeam

**English** · [Русский](README.ru.md)

This is the fifth version of the project. Pre-release 1.5.3.

Install on a separate computer without work secrets. See [Security](#security-first).

Russian edition with bot instructions in Russian: [GrokBotDevTeamRU](https://github.com/alexzah-droid/GrokBotDevTeamRU).

This is an unofficial project, not affiliated with xAI. Grok is a trademark of xAI. The original code is copyright alexzah-droid.

GrokBotDevTeam sets up a three-bot software development team from a single message. Your existing Grok Bot becomes the **Lead** and keeps its usual duties. It adds a **Developer** to implement tasks and a **Reviewer** to verify the result independently. Together they clarify requests, plan work, maintain documentation, and demonstrate the result for acceptance.

## Built-in self-improvement loop

The team tracks recurring failures, and a bot proposes a change to its own instructions. An independent evaluator checks the candidate, and the team collects observations during a trial. In approval mode, you approve the start of the trial and its outcome separately. In autonomous mode, the controller accepts a successful result automatically. If the trial fails or expires, the controller restores the previous instructions. Run `history <role>` to see the full history.

[Instruction improvement skill](skills/team-improvement/SKILL.md) · [Rollback](docs/OPERATIONS.md#rollback)

## Install

Installation uses the published tag `v1.5.3`. Paste either message into your existing Grok Bot chat. The installer checks the available native Grok Bot tools before creating the team and reports any missing capability.

### English message

```text
Install GrokBotDevTeam from https://github.com/alexzah-droid/GrokBotDevTeam using the v1.5.3 pre-release.
Read and follow the English INSTALL.md from that tag. Stay my Lead and create only a Developer and a Reviewer.
Ask me to choose the instruction mode first and put the new bots in your folder if possible.
Check that the team is ready, then ask for my first task.
```

### Russian message

```text
Установи GrokBotDevTeam из https://github.com/alexzah-droid/GrokBotDevTeam, prerelease v1.5.3.
Прочитай и выполни английский INSTALL.md из этого тега. Останься моим Руководителем, создай только Разработчика и Проверяющего.
Сначала предложи выбрать режим инструкций. По возможности помести новых ботов в свою папку.
Проверь готовность команды и предложи прислать первую задачу.
```

The [full installation message](install-prompt.txt) includes recovery and permission rules. The first card offers approval or automatic instruction improvement. Automatic mode needs a second confirmation. A decline or no reply keeps approval. The Lead uses its sidebar folder when available, or offers a folder choice.

## What you get

| Component | Purpose |
| --- | --- |
| Three role instructions and six skills | Planning, development, project checks, documentation, review, and instruction improvement |
| Local controller and native tool bridge | Task state, resumable bot setup and handoffs, checks, and version history |
| Instruction improvement and watchdog | Independent evaluation, trials, acceptance, and automatic rollback |

## Instruction improvement modes

The default team mode is `approval`. Set the team mode to `approval` or `autonomous`. Each role inherits that setting unless it has an override. Ask your Lead to show effective modes, compare versions, or restore a historical instruction.

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

## Confirmations and trust

The default channel is `chat`. The Lead presents the exact question, records your explicit answer, and gets a short-lived, single-use grant bound to the action, target, and state. In the normal path, `improve.approve` and `improve.accept` grants are bound to distinct actions. You can also use the `terminal` channel. See [Human confirmations](docs/CONFIRMATIONS.md).

For a new or changed project binding, the Lead runs `project bind` without a card in team mode `autonomous`. In `approval`, the Lead needs a single-use `project.bind` grant.

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
