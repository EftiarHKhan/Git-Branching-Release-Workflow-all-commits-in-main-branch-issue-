Git Branching & Release Workflow

This document explains how to migrate an existing single-branch Git repository (main) to a two-branch workflow (dev + main) and how to keep the main branch clean for releases.

1. Initial Migration: Create dev Branch

If you have been committing everything to main and want to start a proper dev → main workflow:

# Make sure main is up-to-date
git checkout main
git pull origin main

# Create dev branch from current main
git branch dev
git push -u origin dev


Notes:

dev branch will hold full development history.

main branch will be used for clean release commits.

2. Daily Development Workflow

Create short-lived feature branches off dev:

git checkout dev
git pull
git checkout -b feature/my-feature


Work, commit, and push:

git add -A
git commit -m "feat: add my feature"
git push -u origin feature/my-feature


Open a PR: feature/* → dev. Review, test, merge.

Keep dev green and functional.

3. Release Workflow (Clean main Branch)
Step A — Prepare Release
git checkout dev
git pull origin dev
git checkout main
git add -A
git commit -m "Release v1.0.1"

Step B — Clean main Branch
git checkout --orphan temp-main
git add -A
git commit -m "Release v1.0.1"

git branch -D main
git branch -m main
git push origin main --force

Step C — Tag the Release
git tag -a v1.0.1 -m "Release v1.0.1"
git push origin v1.0.1

4. Future Releases
git checkout main
git pull
git merge dev --squash
git commit -m "Release v1.0.2"
git push origin main
git tag -a v1.0.2 -m "Release v1.0.2"
git push origin v1.0.2

5. Workflow Diagram
            +----------------+
            |     dev        |
            | (full history) |
            +----------------+
                    |
          create feature/*
          work, commit, merge
                    |
                    v
            +----------------+
            |     dev        |
            | (stable code)  |
            +----------------+
                    |
          merge/squash into main
                    |
                    v
            +----------------+
            |     main       |
            | 1 clean commit |
            |  tagged vX.Y  |
            +----------------+
                    |
          deploy / release

6. Notes & Best Practices

Always develop on dev or feature/* branches.

Keep main branch clean — only release commits.

Use semantic versioning (v1.0.1, v1.0.2, etc.).

Force push is required only when cleaning history of main.

Optional: Protect main branch in GitHub settings to avoid accidental commits.
