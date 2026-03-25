# AI ERP Design System - Project Configuration

## Repository

- **Remote:** https://github.com/abdelrahmanalaa13/WB-DS.git
- **Branch:** main
- **Owner:** abdelrahmanalaa13

## Git Identity

- **Name:** Grand Master
- **Email:** mohamed.mostafa@widebot.net

## Versioning Convention

Use semantic versioning: vMAJOR.MINOR.PATCH

- MAJOR: breaking changes to tokens, component schemas, or knowledge graph structure
- MINOR: new components, patterns, templates, or features added
- PATCH: fixes, documentation updates, token adjustments, small refinements

Commit message format:
```
[vX.Y.Z] Short description of what changed

- Bullet list of specific changes
```

Examples:
```
[v1.1.0] Add notification-center and command-palette components
[v1.0.1] Fix button token mapping for Hulul dark theme
[v2.0.0] Restructure token architecture to flat namespace
```

Rules:
- Never reuse a version number
- Never skip versions without explanation
- Always tag the version in the commit message
- Keep a running VERSION file at the repo root tracking the current version
- Before committing, check the last version used in git log to determine the next one

## Release Workflow

After every approved version, release, or significant batch of changes:

1. Determine the correct next version based on the type of changes (major/minor/patch)
2. Summarize what changed and present the proposed version number to the user
3. Ask the user: "Ready to sync and push vX.Y.Z to GitHub?"
4. On approval, update the VERSION file, sync all files, commit with the versioned message, and push

Never push without explicit user approval. Always confirm the version number and what will be included in the commit before pushing.

## Push Procedure

```bash
cd /sessions/jolly-friendly-mendel/wb-ds-repo
rsync -a --exclude='.git' --exclude='.DS_Store' /sessions/jolly-friendly-mendel/mnt/erp-ds-builder/ .
git add -A
git -c user.name="Grand Master" -c user.email="mohamed.mostafa@widebot.net" commit -m "<message>"
git push origin main
```

Note: Authentication requires a GitHub PAT. Ask the user for a fresh token if the previous one has expired or been revoked.
