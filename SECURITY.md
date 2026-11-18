# Security and vulnerability reporting

**English** · [Русский](SECURITY.ru.md)

GrokBotDevTeam is an unofficial project by alexzah-droid, independent of xAI. Grok is a trademark of xAI. Version 1.5.1 is a pre-release.

## What the controller protects

Transactions and locks prevent partial state updates. Single-use grants bind approval to an action, a target, its contents, a state revision, and an expiration time. Instruction approval also binds the full diff and evaluation report hashes. The chat flow trusts the Lead. It checks and records the exact question and explicit reply, then rejects reused reply IDs. The controller stores code hashes and consumes grants in the same transaction as the approved action.

## Limits of protection

Bots that share a host can read the same files and operate with the same permissions. A bot can forge the reply and its `message_ref`, reach the terminal issuer through a pseudo-terminal or direct Python import, modify files, or write a grant into SQLite. The question and diff hash bind approval to what was shown. Whether a human saw the question and authored the reply depends on the Lead and the platform.

Off-host issuance or signatures made with a key unavailable to the bots are planned before a stable release. Both channels currently protect the normal path against mistakes, replay, and stale state.

The controller records the active version. Applying an instruction after a trial or rollback depends on the bot running `role read` and following the returned instruction. Receipts require genuine native references. The controller relies on the platform for their authenticity.

Keep secrets out of instructions, examples, and Git. The optional API runner reads its key from the environment and requires explicit setup. Keep runtime files, databases, and logs out of releases. A version conflict blocks an overwrite. Rolling back an instruction leaves completed external actions in place.

## Operating safely

Use a separate test host and repository with minimal access. Keep work secrets, SSH keys, cloud tokens, and privileged GitHub sessions off that host. A narrowly scoped token should cover only the test repository and exclude admin and production permissions.

Bots can use shell, file, Git, network, and native creation tools. Verification executes project code. The watchdog runs in the background and a routine can restart it if you enable one. Shared access and prompt injection from code, issues, pages, and outputs can influence a bot. Contracts classify such material as data and forbid treating it as instructions or consent. Pilot with `approval`, review the confirmation details, and track iteration, call, time, and provider usage.

Task text is plaintext in local SQLite. Grok Bot processes the tasks and code bots read. The optional xAI evaluator sends inputs and instructions only when you explicitly configure its environment key. Use the official release tag, compare SHA-256, and follow [Emergency stop](docs/OPERATIONS.md#emergency-stop) if you need to halt the team. The [MIT license](LICENSE) provides the project as is.

## Report a problem

Use GitHub private vulnerability reporting if the maintainer has enabled it. Otherwise, open an issue with a brief description and ask for a private channel. Omit secrets, personal data, live tokens, and user transcripts. The maintainer has yet to establish a guaranteed channel or response time.

The manifest checks files against the selected release copy. The user decides which source to trust and authorizes the installation. Content from other sources grants no permissions. Fixtures and local tests do not authenticate native receipts.
