# Rebasing — Rewriting Commit History

## What is Rebasing?

Rebasing takes commits from one branch and replays them on top of another base. Unlike merging (which creates a merge commit), rebasing produces a linear history.

```
Before rebase:
main       ──●──●──●
              \
feature    ────●──●──●

After rebase (feature onto main):
main       ──●──●──●
                    \
feature              ●──●──●

After merge (for comparison):
main       ──●──●──●────●── (merge commit)
              \        /
feature    ────●──●──●
```

```mermaid
gitGraph
   commit id: "A"
   commit id: "B"
   branch feat
   checkout feat
   commit id: "C"
   commit id: "D"
   checkout main
   commit id: "E"
   checkout feat
   commit id: "F (rebased)"
   type: HIGHLIGHT
   checkout main
   merge feat
```

---

## Basic Rebase

```bash
# While on feature branch, replay its commits on top of main
git switch feat
git rebase main

# This is equivalent to:
# 1. Find the common ancestor
# 2. Save feature's commits as patches
# 3. Move feature pointer to main's tip
# 4. Re-apply each saved commit
```

### Rebase vs Merge

```bash
# Merge approach
git switch main
git merge feat      # creates a merge commit

# Rebase approach
git switch feat
git rebase main     # linearizes history
git switch main
git merge feat      # now fast-forward
```

| Aspect | Rebase | Merge |
|--------|--------|-------|
| History | Linear, clean | Preserves branch structure |
| Merge commits | None | One per merge |
| Collaboration | **Dangerous** after push | Safe after push |
| Readability | Easy to follow | Shows parallel work |
| Conflict frequency | Each commit replayed (potentially multiple) | Single resolution |

---

## Interactive Rebase (`-i`)

Interactive rebase lets you modify commits as they're replayed.

```bash
git rebase -i HEAD~3        # rebase the last 3 commits
git rebase -i main          # rebase all commits after diverging from main
```

This opens an editor with a todo list:

```
pick a1b2c3d feat: add login form
pick e4f5g6h feat: add form validation
pick h7i8j9k fix: correct email regex
pick l0m1n2o feat: style login page

# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# d, drop <commit> = remove commit
```

### Interactive Commands

| Command | Action |
|---------|--------|
| `pick` | Keep the commit as-is |
| `reword` | Change the commit message |
| `edit` | Stop to amend the commit content or message |
| `squash` | Combine into previous commit (keep message) |
| `fixup` | Combine into previous commit (discard message) |
| `drop` | Delete the commit entirely |
| `exec` | Run a shell command |

### Example: Squashing Commits

```
pick a1b2c3d feat: add login form
squash e4f5g6h wip: login form tweaks
squash h7i8j9k fix: login style
pick l0m1n2o feat: add dashboard
```

After saving, Git combines three commits into one and prompts for a new message.

### Example: Rewording

```
reword a1b2c3d feat: add login form
pick e4f5g6h feat: add form validation
```

Git will stop at each `reword` commit and open an editor for the new message.

---

## The Golden Rule of Rebasing

> **Never rebase commits that have been pushed to a shared branch.**

```
YOU PUSH commits to main:
main ──●──●──●──●──PUSHED──●──●

YOU rebase those commits:
main ──●──●──●──●──NEW (rewritten)

COLLEAGUE pulls:
main ──●──●──●──●──PUSHED──●──● (diverged!)
```

**Why?** Rebasing rewrites commit hashes. If someone else already has the old commits, their history will permanently diverge from yours. They'll face duplicate commits and confusing conflicts.

### Safe Rebasing Rules

- ✅ Rebase **local** feature branches before pushing
- ✅ Rebase your own feature branch to incorporate `main` changes
- ✅ Use interactive rebase to clean up local commits before a PR
- ❌ Never rebase `main`, `develop`, or any shared branch
- ❌ Never rebase commits you've already pushed (unless the branch is yours alone and no one else has pulled)

---

## Rebase Workflow

The standard **rebase workflow** keeps history linear:

```bash
# 1. Start a feature branch
git switch -c feat/payments

# 2. Work, commit locally
echo "stripe" > payment.ts
git add -A && git commit -m "feat: add Stripe integration"
echo "paypal" > payment.ts
git add -A && git commit -m "feat: add PayPal option"

# 3. Meanwhile, main has new commits
git switch main
git pull
git switch feat/payments

# 4. Rebase onto latest main
git rebase main
# Resolve any conflicts during replay

# 5. Squash WIP commits before PR
git rebase -i HEAD~2
# squash e4f5g6h into a1b2c3d

# 6. Push (force needed because history changed)
git push --force-with-lease origin feat/payments

# 7. Open PR — clean, linear history
```

---

## Rebasing with Conflicts

When a conflict occurs during rebase:

```
git rebase main
# Auto-merging payment.ts
# CONFLICT (content): Merge conflict in payment.ts
# error: could not apply a1b2c3d... feat: add Stripe integration
```

```bash
# 1. Resolve the conflict in the file
code payment.ts

# 2. Stage the resolved file
git add payment.ts

# 3. Continue rebasing
git rebase --continue

# Or to skip this commit entirely
git rebase --skip

# Or to abort everything and go back
git rebase --abort
```

---

## Real-World Scenario

```bash
# Team practice: rebase feature branches before PR

# Developer starts feature
git switch -c feat/api-endpoints

# Makes multiple small commits (WIPs)
git commit -m "WIP: add GET endpoint"
git commit -m "WIP: add POST endpoint"
git commit -m "fix typo in route"
git commit -m "WIP: add DELETE endpoint"

# Main has moved forward, so rebase
git fetch origin
git rebase origin/main

# Clean up history with interactive rebase
git rebase -i origin/main

# In editor:
# pick  a1b2c3d WIP: add GET endpoint
# squash e4f5g6h WIP: add POST endpoint
# fixup h7i8j9k fix typo in route
# pick  l0m1n2o WIP: add DELETE endpoint

# After save:
# 2 meaningful commits
# a1b2c3d feat: add GET and POST endpoints
# l0m1n2o feat: add DELETE endpoint

# Push (force since history changed)
git push --force-with-lease origin feat/api-endpoints
```

---

## Quick Reference

```bash
git rebase main                   # replay current branch on main
git rebase -i HEAD~3              # interactively rebase last 3 commits
git rebase -i main                # interactively rebase diverged commits
git rebase --continue             # continue after resolving conflict
git rebase --skip                 # skip current commit during rebase
git rebase --abort                # abort the rebase entirely
git push --force-with-lease       # safe force push after rebase
```
