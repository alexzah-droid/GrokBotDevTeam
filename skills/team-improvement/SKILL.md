---
name: team-improvement
description: Propose, evaluate, and trial a reversible instruction improvement.
---

# Instruction improvement

1. Check `policy show`, `version <id>`, and evaluation configuration. The user authorizes `evaluation <role> --file config.json --evidence …`. [Suite and evaluator](../../docs/EVALUATION.md).
2. Gather repeatable failures and a hypothesis. Only the role itself or Lead runs `improve propose <role> --body candidate.md --hypothesis … --evidence …`. Edit only `behavior`.
3. Check the assigned `evaluator` with `improve show <id>`. Run `improve evaluate <id>` as that role. Never retry an interrupted evaluation automatically.
4. In `approval`, the Lead obtains an `improve.approve` grant for `improve approve` using [the task procedure](../team-workflow/SKILL.md#get-and-use-a-grant). In terminal mode give `--reviewed-diff`. In `autonomous` the trial starts automatically. If deferred, check `improve show` before `improve progress <id>`.
5. The independent evaluator records results with `improve observe <id> --file outcome.json`. At `awaiting_acceptance`, the Lead gets a new `improve.accept` grant for `improve accept`. If `autonomous` applies both at activation and at the final observation, accept success automatically. Failure or expiration rolls back the version, even while awaiting a reply.
6. Check `history <role>`, `version <id>`, `diff <id>`, and `events --object <id>`. Use the grant procedure for manual `improve cancel` and `restore`. [Recovery](../../docs/OPERATIONS.md#rollback).

On user request, change the mode with `policy set <mode> [--role <role>] --evidence …` and a grant. Use `inherit` to remove an override.
