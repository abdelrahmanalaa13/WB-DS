# AI ERP Design System - Project Configuration

## Repository

- **Remote:** https://github.com/abdelrahmanalaa13/WB-DS.git
- **Branch:** main
- **Owner:** abdelrahmanalaa13

## Git Identity

- **Name:** Grand Master
- **Email:** mohamed.mostafa@widebot.net

## Release Workflow

After every approved version, release, or significant batch of changes:

1. Ask the user: "Ready to sync and push to GitHub?"
2. On approval, sync all files to the local repo clone
3. Commit with a clear message describing what changed
4. Push to origin/main

Never push without explicit user approval. Always confirm what will be included in the commit before pushing.

## Push Procedure

```bash
cd /sessions/jolly-friendly-mendel/wb-ds-repo
rsync -a --exclude='.git' --exclude='.DS_Store' /sessions/jolly-friendly-mendel/mnt/erp-ds-builder/ .
git add -A
git -c user.name="Grand Master" -c user.email="mohamed.mostafa@widebot.net" commit -m "<message>"
git push origin main
```

Note: Authentication requires a GitHub PAT. Ask the user for a fresh token if the previous one has expired or been revoked.
