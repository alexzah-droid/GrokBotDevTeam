---
name: team-review
description: Verify the submitted snapshot against the criteria.
---

# Review

Read the request, submission, diff, and previous findings. Confirm `review` and verify the snapshot with [`project-verification`](../project-verification/SKILL.md). Check docs with `technical-writing`. If the snapshot changes, ask for a newly verified submission.

Add up to eight checks using `project add-check --file extra-argv.json` (an argv array). Preserve the base checks. Rerun the profile after adding a check.

Run `task review <id> --file review.json`. For `pass` give each criterion's ID, result, and evidence. For `changes_requested` describe the defect and reproduction. For `blocked` identify the missing access, environment, or evidence.
