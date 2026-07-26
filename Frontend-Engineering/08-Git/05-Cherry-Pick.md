# Cherry-Pick — Selective Commit Application

## What is Cherry-Pick?

`git cherry-pick` applies the changes from one or more existing commits onto the current branch as new commits. Unlike merge or rebase (which bring entire branches), cherry-pick lets you select individual commits.

```mermaid
gitGraph
   commit id: "A"
   commit id: "B"
   branch release
   checkout release
   commit id: "C"
   commit id: "D"
   checkout main
   commit id: "E"
   commit id: "F"
   checkout release
   commit id: "G"
   commit id: "H"
   checkout main
   commit id: "I"
   branch hotfix
   checkout hotfix
   commit id: "J (cherry-pick from release)"
   type: HIGHLIGHT
```

---

## Basic Usage

```bash
# Cherry-pick a single commit onto current branch
git cherry-pick a1b2c3d

# Cherry-pick a range of commits
git cherry-pick a1b2c3d..e4f5g6h    # picks all commits from a1b2c3d (exclusive) to e4f5g6h
git cherry-pick a1b2c3d^..e4f5g6h   # picks inclusive range (includes a1b2c3d)

# Cherry-pick multiple non-contiguous commits
git cherry-pick a1b2c3d e4f5g6h h7i8j9k

# Cherry-pick without committing immediately
git cherry-pick -n a1b2c3d           # -n = --no-commit, stages changes but doesn't commit
```

---

## Common Options

| Option | Description |
|--------|-------------|
| `-n` / `--no-commit` | Apply changes to working directory without committing |
| `-x` | Append "(cherry picked from commit ...)" to the commit message |
| `-e` / `--edit` | Edit the commit message before committing |
| `--signoff` | Add a Signed-off-by trailer |
| `--ff` | If the cherry-pick results in the same tree as the source, just move the branch pointer |

---

## Use Cases

### 1. Hotfixes to Multiple Release Branches

When a critical bug fix is committed on `main` but needs to go to active release branches:

```bash
# Fix committed on main
git checkout main
# ... fix the bug
git commit -m "fix: prevent XSS in search input"

# Apply same fix to v1.x release branch
git checkout v1.x
git cherry-pick -x a1b2c3d

# Apply to v2.x release branch
git checkout v2.x
git cherry-pick -x a1b2c3d
```

### 2. Selective Feature Backport

A feature was developed on `main` but needs a subset of its commits backported to a stable branch:

```bash
git checkout stable/v1
# Only pick the API-related commits, skip UI refactoring
git cherry-pick 1234abc 5678def
```

### 3. Undo a Mistaken Commit (Alternative to Revert)

If a commit was made on the wrong branch:

```bash
# Commit was accidentally made on main
git checkout feat/user-profile
git cherry-pick a1b2c3d        # bring the commit here
git checkout main
git revert a1b2c3d              # undo it on main (or use reset if unpushed)
```

### 4. Extracting Commits from an Abandoned Branch

```bash
git branch -D experimental          # delete the branch
git reflog                          # find the commit hashes
git cherry-pick a1b2c3d e4f5g6h    # save the useful commits
```

---

## Cherry-Pick Ranges

```bash
# Pick all commits between commitX (exclusive) and commitZ (inclusive)
git cherry-pick commitX..commitZ

# Pick all commits between commitX (inclusive) and commitZ (inclusive)
git cherry-pick commitX^..commitZ

# Pick commits from commitX up to the tip of feature-branch
git cherry-pick commitX..feature-branch
```

**Important:** The range `X..Z` excludes X. Use `X^..Z` to include X.

---

## Handling Conflicts During Cherry-Pick

When a cherry-pick results in conflicts:

```bash
$ git cherry-pick a1b2c3d
# Auto-merging src/app.ts
# CONFLICT (content): Merge conflict in src/app.ts
# error: could not apply a1b2c3d... feat: add login form
```

```bash
# 1. View the conflict
git status
# both modified:   src/app.ts

# 2. Resolve manually
code src/app.ts

# 3. Stage the resolved file
git add src/app.ts

# 4. Continue cherry-pick
git cherry-pick --continue

# Or skip this commit
git cherry-pick --skip

# Or abort entirely
git cherry-pick --abort
```

---

## Real-World Scenario

```bash
# A bug is reported in production (version 1.5).
# The fix was already applied to main (commit 9f8e7d6).

# Step 1: Switch to the release branch
git checkout release/v1.5

# Step 2: Cherry-pick the fix
git cherry-pick -x 9f8e7d6

# Step 3: Verify the fix
git log --oneline -3

# Step 4: Push the release branch
git push origin release/v1.5

# Step 5: Tag the hotfix release
git tag v1.5.1
git push origin v1.5.1
```

### Collaborator Scenario

```bash
# Alice commits a feature on feat/dashboard branch
# Bob needs only one of those commits for his feat/chart branch

# Bob does:
git checkout feat/chart
git cherry-pick 7a8b9c0

# If conflict occurs:
# 1. Resolve
git add .
git cherry-pick --continue
# 2. Write message: "chart: integrate data from dashboard module"
```

---

## Cherry-Pick vs Rebase vs Merge

| Operation | Scope | Creates New Commits? | Best For |
|-----------|-------|---------------------|----------|
| `merge` | All commits from a branch | Merge commit | Integrating complete branches |
| `rebase` | All commits from a branch | New commits (rewritten) | Linearizing feature branch history |
| `cherry-pick` | Selected commit(s) | New commits | Selective backporting / hotfixes |

---

## Important Notes

- Cherry-pick creates **new commits with different hashes**, even if the content is identical.
- Use `-x` to document where the commit originated for traceability.
- Cherry-picking does **not** remove the original commit.
- Avoid cherry-picking between branches that will later be merged — it causes duplicate commits.

---

## Quick Reference

```bash
git cherry-pick <hash>               # apply a single commit
git cherry-pick <hash1> <hash2>      # apply multiple commits
git cherry-pick A..B                 # range (excludes A)
git cherry-pick A^..B                # range (includes A)
git cherry-pick -n <hash>            # apply without committing
git cherry-pick -x <hash>            # add source reference in message
git cherry-pick --continue           # continue after conflict resolution
git cherry-pick --abort              # abort the cherry-pick
git cherry-pick --skip               # skip current commit
```
