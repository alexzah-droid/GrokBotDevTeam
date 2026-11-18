# Changelog

**English** · [Русский](CHANGELOG.ru.md)

## 1.5.0 — 2026-11-18 — pilot pre-release

This is the fifth version of the project. Live install not verified (`live_verified: false`). This version includes three roles, six file-backed skills, task orchestration, independent verification, and two modes for changing instructions. It records versions, supports trials, and rolls back failed changes. A live pilot still needs to confirm that bots load instructions and that native handoffs arrive.

Confirmations default to chat. In `approval` mode, separate grants authorize starting a trial and accepting its result. In `autonomous` mode, passing checks and observations suffice. The terminal channel remains available. Setting up the team in autonomous mode requires a grant.

Grants are single-use, expire, and bind to the action and full diff. Evidence references alone grant no authority. Candidate authorship is restricted. The installation smoke test uses Git and Python and includes a deliberately broken implementation. The watchdog tolerates failed iterations and ships with a restart template. The Reviewer may add checks while preserving the base profile. Project symlinks must resolve inside the project.

Archives use a single root directory and derive their version from release metadata. Packaging ignores caches and AppleDouble and rejects runtime state in the release tree. The standard-library PEP 517 backend builds a complete pip package. Python 3.10 is the minimum. CI covers Python 3.10–3.13, uses read-only permissions, and pins Actions by commit SHA.

GrokBotDevTeam is an unofficial project, independent of xAI. The original code is copyright alexzah-droid.

The startup rule stops the role when fresh instructions cannot be read. The confirmation card reports how many diff lines it shows and keeps the command for retrieving the full diff. Public pages are available in English and Russian.
