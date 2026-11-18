# Publish to GitHub

This release goes to [GrokBotDevTeam](https://github.com/alexzah-droid/GrokBotDevTeam).

## Verify the package

Follow [Validation](../VALIDATION.md), compare both archive formats with the manifest, and confirm reproducible checksums. Only source and the manifest go into Git. Attach archives and checksums to the release.

## Publish

1. Review the staged files and wait for GitHub Actions on Python 3.10–3.13.
2. Check the [pilot results](../VALIDATION.md). Tag the verified commit `v1.6.0` and create a stable GitHub release marked **Latest**.
3. Attach the two archives and their SHA-256 files. Verify the downloaded assets and the manifest from the tag.
4. Share the [installation message](../install-prompt.txt) and verify that GitHub lists the published release as **Latest**.
