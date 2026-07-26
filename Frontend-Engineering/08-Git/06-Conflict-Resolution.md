# Conflict Resolution — Handling Merge Conflicts

## What is a Merge Conflict?

A merge conflict occurs when Git cannot automatically reconcile changes from two branches that modified the **same part of the same file**. Git needs your help to decide which version to keep.

---

## Conflict Anatomy

When a conflict is detected, Git modifies the affected file with conflict markers:

```diff
<<<<<<<<< HEAD
const theme = "dark";
const apiUrl = "https://api.example.com/v1";
=========
const theme = "light";
const apiUrl = "https://api.example.com/v2";
>>>>>>>>> feat/theming
```

```
<<<<<<<<< HEAD          ─── Your current branch's version
=========               ─── Divider between versions
>>>>>>>>> feat/theming  ─── Incoming branch's version
```

### Conflict Types

| Type | Description | Example |
|------|-------------|---------|
| **Content conflict** | Same lines changed differently | Both branches changed `header()` function |
| **Modified/deleted conflict** | One branch modifies, the other deletes | Branch A deletes `utils.js`, branch B modifies it |
| **Rename/rename conflict** | File renamed differently in both branches | Branch A: `User.ts` → `Customer.ts`, Branch B: `User.ts` → `Account.ts` |
| **File addition conflict** | Same new file created differently | Both branches add `config.json` with different content |

---

## Step-by-Step Conflict Resolution

### Step 1: Identify the Conflict

```bash
$ git merge feat/theming
Auto-merging src/config.ts
CONFLICT (content): Merge conflict in src/config.ts
Automatic merge failed; fix conflicts and then commit the result.

$ git status
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  both modified:   src/config.ts
```

### Step 2: Understand the Branches

```bash
# See which commits led to the conflict
git log --oneline --merge

# See what changes are incoming
git diff HEAD...feat/theming -- src/config.ts

# See both versions of the conflicted file
git show HEAD:src/config.ts     # our version
git show feat/theming:src/config.ts  # their version
```

### Step 3: Resolve the Conflict

Open the file and edit it. Choose one of three strategies:

**Option A — Keep ours (current branch):**
```ts
// Remove their version and markers: keep only HEAD's content
const theme = "dark";
const apiUrl = "https://api.example.com/v1";
```

**Option B — Keep theirs (incoming branch):**
```ts
// Remove our version and markers: keep only incoming content
const theme = "light";
const apiUrl = "https://api.example.com/v2";
```

**Option C — Combine both (manual resolution):**
```ts
// Merge both changes intelligently
const theme = "light";  // new design theme
const apiUrl = "https://api.example.com/v1";  // keep current API
```

**Always remove the conflict markers** (`<<<<<<<`, `=======`, `>>>>>>>`) after resolving.

### Step 4: Stage and Commit

```bash
# Mark the file as resolved
git add src/config.ts

# Verify no more conflicts
git status

# Complete the merge
git commit
# Git opens an editor with a pre-filled merge commit message
```

---

## Using Merge Tools

### VS Code

```bash
# Configure VS Code as the merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd "code --wait $MERGED"
```

VS Code's source control panel shows:
- **Current Change** (ours / HEAD)
- **Incoming Change** (theirs)
- **Result** (the resolved file)

Buttons: Accept Current, Accept Incoming, Accept Both, Compare.

### Beyond Compare

```bash
git config --global merge.tool bc
git config --global mergetool.bc.path "C:/Program Files/Beyond Compare 4/BComp.exe"
```

### KDiff3

```bash
git config --global merge.tool kdiff3
git config --global mergetool.kdiff3.path "C:/Program Files/KDiff3/kdiff3.exe"
```

### Launching the Tool

```bash
# Launch configured merge tool for all conflicts
git mergetool

# Launch for a specific file
git mergetool src/config.ts
```

---

## Aborting a Merge

If the conflict is too complex or you've changed your mind:

```bash
# Abort the merge entirely (reverts to pre-merge state)
git merge --abort

# Or reset to the last commit
git reset --hard HEAD
```

---

## Conflict Prevention Strategies

### 1. Frequent Integration

Merge or rebase `main` into your feature branch regularly:

```bash
git checkout feat/my-feature
git merge main        # do this daily
```

Small, frequent integrations produce smaller, easier-to-resolve conflicts.

### 2. Clear Communication

- Use a branching strategy that defines ownership
- Discuss large refactors with the team before starting
- Use PR descriptions to warn about potentially conflicting changes

### 3. Keep Commits Small

Each commit should be a logical, atomic change. Small commits are easier to reason about during conflicts.

### 4. Use `.gitattributes` for Consistent Line Endings

```gitattributes
# Normalize line endings
* text=auto
*.js text eol=lf
```

### 5. Lock Binary Files (Git LFS)

Use `git lfs` for binaries, which uses file locking to prevent concurrent edits.

---

## Advanced Conflict Scenarios

### Scenario 1: File Deleted in One Branch, Modified in Another

```bash
$ git merge feat/cleanup
CONFLICT (modify/delete): src/old-utils.js deleted in HEAD
  and modified in feat/cleanup. Version feat/cleanup of src/old-utils.js left in tree.
```

```bash
# Keep the deleted version (remove the file)
git rm src/old-utils.js

# Or keep the modified version
git add src/old-utils.js
```

### Scenario 2: Both Added the Same File

```bash
CONFLICT (add/add): Merge conflict in config.json
```

Same resolution process — open, merge, stage, commit.

### Scenario 3: Rename Conflict

```bash
CONFLICT (rename/rename): Rename "Header.ts"->"SiteHeader.ts" in branch "main"
  and "Header.ts"->"AppHeader.ts" in "feat/header"
```

Decide on a final name and update all imports.

---

## Real-World Scenario: Conflict During Rebase

```bash
# Developer rebases feature branch onto main
git switch feat/payments
git rebase main

# Conflict on payments.ts
git status
git diff

# Open file, see:
# <<<<<<< HEAD (current changes from main)
# const taxRate = 0.08;
# =======
# const taxRate = 0.10;
# >>>>>>> 3f4a5b6 (feat: add tax calculation)

# Resolution: the correct rate is 0.10
# Edit file to: const taxRate = 0.10;

git add payments.ts

# Continue rebase (not merge!)
git rebase --continue

# Or if the commit is no longer needed:
git rebase --skip

# Or abort everything:
git rebase --abort
```

---

## Conflict Resolution Strategy Comparison

| Strategy | When to Use | Command |
|----------|-------------|---------|
| **Manual edit** | Always the safest — full control | Edit file directly |
| **Accept ours** | We know our change is correct | `git checkout --ours file` |
| **Accept theirs** | Incoming change is definitely correct | `git checkout --theirs file` |
| **Merge tool** | Complex conflicts with multiple changes | `git mergetool` |
| **Abort** | Merge was a mistake or too complex | `git merge --abort` |

```bash
# Accept entire file from one side
git checkout --ours src/config.ts     # keep HEAD version
git checkout --theirs src/config.ts   # keep incoming version
git add src/config.ts
```

---

## Quick Reference

```bash
git status                    # see conflicted files
git diff                      # see conflict details
git merge --abort             # abort the merge
git rebase --abort            # abort the rebase
git mergetool                 # launch merge tool
git checkout --ours <file>    # keep our version
git checkout --theirs <file>  # keep their version
git add <file>                # mark as resolved
git merge --continue          # complete merge
git rebase --continue         # continue rebase
```
