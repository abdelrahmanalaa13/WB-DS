# AI ERP Design System - Project Configuration

## Repository

- **Remote:** https://github.com/abdelrahmanalaa13/WB-DS.git
- **Branch:** main
- **Owner:** abdelrahmanalaa13

## Git Identity

- **Name:** Grand Master
- **Email:** mohamed.mostafa@widebot.net

## Versioning Convention

Use semantic versioning with build date: vMAJOR.MINOR.PATCH-build.YYYYMMDD

- MAJOR: breaking changes to tokens, component schemas, or knowledge graph structure
- MINOR: new components, patterns, templates, or features added
- PATCH: fixes, documentation updates, token adjustments, small refinements
- Build date (-build.YYYYMMDD): appended to make every release uniquely identifiable by date

The dash-dot format is used instead of semver `+` metadata because `+` causes conflicts in npm, Docker tags, GitHub release URLs, and shell scripts.

VERSION file format: `1.0.0-build.20260326` (no "v" prefix in the file, prefix used in commit tags)

Commit message format:
```
[v1.0.0-build.20260326] Short description of what changed

- Bullet list of specific changes
```

Examples:
```
[v1.1.0-build.20260401] Add notification-center and command-palette components
[v1.0.1-build.20260328] Fix button token mapping for Hulul dark theme
[v2.0.0-build.20260515] Restructure token architecture to flat namespace
```

Rules:
- Never reuse a version number
- Never skip versions without explanation
- Always tag the version in the commit message (including build date)
- Keep a running VERSION file at the repo root tracking the current version
- Before committing, check the last version used in git log to determine the next one
- Build date must match the actual release date
- The semver comparison ignores the build suffix (1.0.0 == 1.0.0 regardless of date)

## Release Workflow

After every approved version, release, or significant batch of changes:

1. Determine the correct next version based on the type of changes (major/minor/patch)
2. Summarize what changed and present the proposed version number to the user
3. Ask the user: "Ready to sync and push vX.Y.Z to GitHub?"
4. On approval, update the VERSION file, sync all files, commit with the versioned message, and push

Never push without explicit user approval. Always confirm the version number and what will be included in the commit before pushing.

## Push Procedure

```bash
# Clone if not already cloned
git clone https://github.com/abdelrahmanalaa13/WB-DS.git wb-ds-repo 2>/dev/null || true
cd wb-ds-repo
rsync -a --delete --exclude='.git' --exclude='.DS_Store' <workspace-folder>/ .
git add -A
git -c user.name="Grand Master" -c user.email="mohamed.mostafa@widebot.net" commit -m "<message>"
git push origin main
```

Note: Authentication requires a GitHub PAT. Ask the user for a fresh token if the previous one has expired or been revoked.
