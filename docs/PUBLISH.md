# Publish to GitHub

This pre-release goes to [GrokBotDevTeam](https://github.com/alexzah-droid/GrokBotDevTeam).

## Verify the package

Follow [Validation](../VALIDATION.md), compare both archive formats with the manifest, and confirm reproducible checksums. Only source and the manifest go into Git. Attach archives and checksums to the release.

## Publish and pilot

1. Review the staged files and wait for GitHub Actions on Python 3.10–3.13.
2. Tag the verified commit `v1.5.1`. Create a GitHub release marked **Pre-release**.
3. Attach the two archives and their SHA-256 files. Verify the downloaded assets and the manifest from the tag.
4. Share the [installation message](../install-prompt.txt). Verify two new bots, durable memory, native handoffs and receipts, smoke tests, watchdog, independent review, acceptance, and rollback in a live pilot.
5. Prepare a stable release after live behavior is verified and confirmations can be authenticated outside the bot host.
