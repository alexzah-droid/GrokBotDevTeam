# Changelog

**English** · [Русский](CHANGELOG.ru.md)

## 1.6.0 — 2026-11-18 — first stable release

This is the fifth version of the project. Team behavior matches 1.5.3. The release channel is stable, and the [live installation evidence](VALIDATION.md) is recorded. Existing teams stay pinned to their installed version. See [migration](docs/OPERATIONS.md#update-and-disconnect).

Added acknowledgements

## 1.5.3 — pre-release

This is the fifth version of the project. New bots wait for the Lead to configure them, and installation binds each bot immediately after creation. Task cards quote a short goal. The README describes the built-in self-improvement loop and links to the security section from the top. The confirmation guide explains how project binding works in each mode.

## 1.5.2 — pre-release

This is the fifth version of the project. Pilot follow-up: choose improvement mode at setup, put new bots in the Lead’s sidebar folder, and use clearer confirmation cards. Local commits link to reviewed snapshots. Generated Python caches stay out of verification. Project binding requires approval in approval mode. Setup offers optional watchdog restart. Native call references and role-specific startup messages are clearer. The affected instruction and skill hashes changed.

## 1.5.1 — pre-release

This is the fifth version of the project. Removed the live-installation caveats from public descriptions. Controller behavior and bot instructions are unchanged.

## 1.5.0 — 2026-11-18 — pilot pre-release

This is the fifth version of the project. Live install not verified (`live_verified: false`). This version includes three roles, six file-backed skills, task orchestration, independent verification, and two modes for changing instructions. It records versions, supports trials, and rolls back failed changes. A live pilot still needs to confirm that bots load instructions and that native handoffs arrive.

Confirmations default to chat. In `approval` mode, separate grants authorize starting a trial and accepting its result. In `autonomous` mode, passing checks and observations suffice. The terminal channel remains available. Setting up the team in autonomous mode requires a grant.

Grants are single-use, expire, and bind to the action and full diff. Evidence references alone grant no authority. Candidate authorship is restricted. The installation smoke test uses Git and Python and includes a deliberately broken implementation. The watchdog tolerates failed iterations and ships with a restart template. The Reviewer may add checks while preserving the base profile. Project symlinks must resolve inside the project.

Archives use a single root directory and derive their version from release metadata. Packaging ignores caches and AppleDouble and rejects runtime state in the release tree. The standard-library PEP 517 backend builds a complete pip package. Python 3.10 is the minimum. CI covers Python 3.10–3.13, uses read-only permissions, and pins Actions by commit SHA.

GrokBotDevTeam is an unofficial project, independent of xAI. The original code is copyright alexzah-droid.

The startup rule stops the role when fresh instructions cannot be read. The confirmation card reports how many diff lines it shows and keeps the command for retrieving the full diff. Public pages are available in English and Russian.
