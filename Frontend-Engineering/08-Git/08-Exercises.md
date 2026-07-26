# Git Exercises — Practice Scenarios

Set up a practice directory:

```bash
mkdir git-exercises && cd git-exercises && git init
git config user.name "Your Name" && git config user.email "you@example.com"
```

---

## Exercise 1: Initialize and Commit

**Scenario:** Start a new project and make your first commits.

```bash
# 1. Create index.html with "<h1>Hello</h1>"
# 2. Stage and commit it
# 3. Create style.css with "body { color: red; }"
# 4. Check status, stage, commit
# 5. View the log
```
**Expected outcome:** Two commits in history; `git log --oneline` shows both.

<details>
<summary>Solution</summary>

```bash
echo "<h1>Hello</h1>" > index.html
git add index.html && git commit -m "feat: add index.html"
echo "body { color: red; }" > style.css
git status && git add style.css && git commit -m "feat: add style.css"
git log --oneline
```
</details>

---

## Exercise 2: .gitignore

**Scenario:** Prevent `node_modules/`, `.env`, and `.DS_Store` from being tracked.

```bash
# 1. Create .gitignore with the above patterns
# 2. Create node_modules/ folder with a test file inside
# 3. Create .env with fake secrets
# 4. Run git status — node_modules and .env should NOT appear
```
**Expected outcome:** `git status` shows only `.gitignore` as untracked.

<details>
<summary>Solution</summary>

```bash
cat > .gitignore << 'EOF'
node_modules/
.env
.DS_Store
EOF
mkdir node_modules && touch node_modules/test.js
echo "SECRET=abc123" > .env
git status
```
</details>

---

## Exercise 3: Branching Basics

**Scenario:** Create branches, switch between them, understand branch pointers.

```bash
# 1. On main, create file1.txt and commit
# 2. Create branch feat-a, switch to it, create file2.txt and commit
# 3. Switch back to main — file2.txt disappears
# 4. Create feat-b from main, create file3.txt and commit
# 5. View with git log --oneline --graph --all
```
**Expected outcome:** Graph shows branches `feat-a`, `feat-b`, and `main`.

<details>
<summary>Solution</summary>

```bash
echo "main" > file1.txt && git add . && git commit -m "initial"
git checkout -b feat-a && echo "feat-a" > file2.txt && git add . && git commit -m "feat-a work"
git checkout main
git checkout -b feat-b && echo "feat-b" > file3.txt && git add . && git commit -m "feat-b work"
git log --oneline --graph --all
```
</details>

---

## Exercise 4: Fast-Forward Merge

**Scenario:** Merge a branch that hasn't diverged from main.

```bash
# 1. From main, create fast-feat, add feature.txt, commit
# 2. Switch to main, merge fast-feat
# 3. Check log — no merge commit
```
**Expected outcome:** Linear history, no merge commit.

<details>
<summary>Solution</summary>

```bash
git checkout -b fast-feat
echo "feature" > feature.txt && git add . && git commit -m "feat: add feature"
git checkout main && git merge fast-feat
git log --oneline
```
</details>

---

## Exercise 5: 3-Way Merge

**Scenario:** Both branches diverge — force a true merge commit.

```bash
# 1. On main, commit A and B
# 2. Create feature, commit C and D
# 3. Back on main, commit E and F (diverging)
# 4. Merge feature into main
```
**Expected outcome:** Merge commit appears with two parents.

<details>
<summary>Solution</summary>

```bash
echo "A" > a.txt && git add . && git commit -m "A"
echo "B" > b.txt && git add . && git commit -m "B"
git checkout -b feature
echo "C" > c.txt && git add . && git commit -m "C"
echo "D" > d.txt && git add . && git commit -m "D"
git checkout main
echo "E" > e.txt && git add . && git commit -m "E"
echo "F" > f.txt && git add . && git commit -m "F"
git merge feature
git log --oneline --graph
```
</details>

---

## Exercise 6: Merge Conflict

**Scenario:** Both branches modify the same line. Resolve the conflict.

```bash
# 1. Create conflict.txt with "line1\nline2\nline3", commit on main
# 2. Branch edit-a: change line2 to "line A", commit
# 3. Switch to main: change line2 to "line B", commit
# 4. Merge edit-a — conflict occurs
# 5. Resolve: line2 = "line A and B", remove markers, stage, commit
```
**Expected outcome:** Conflict resolved, file contains "line A and B".

<details>
<summary>Solution</summary>

```bash
echo -e "line1\nline2\nline3" > conflict.txt && git add . && git commit -m "initial"
git checkout -b edit-a
# change line2 to "line A"
git add . && git commit -m "change to A"
git checkout main
# change line2 to "line B"
git add . && git commit -m "change to B"
git merge edit-a  # CONFLICT
# Edit: line1\nline A and B\nline3
git add . && git commit
```
</details>

---

## Exercise 7: Basic Rebase

**Scenario:** Rebase a feature branch onto main for linear history.

```bash
# 1. Main: 2 commits. Feature: 2 commits. Main: 1 more commit.
# 2. On feature, git rebase main
# 3. On main, merge feature (fast-forward)
```
**Expected outcome:** Linear history: 1→2→3→F1→F2.

<details>
<summary>Solution</summary>

```bash
echo "1" > f.txt && git add . && git commit -m "1"
echo "2" > f.txt && git add . && git commit -m "2"
git checkout -b feature
echo "F1" > f1.txt && git add . && git commit -m "feature 1"
echo "F2" > f2.txt && git add . && git commit -m "feature 2"
git checkout main && echo "3" > f.txt && git add . && git commit -m "3"
git checkout feature && git rebase main
git checkout main && git merge feature
git log --oneline
```
</details>

---

## Exercise 8: Interactive Rebase — Squash

**Scenario:** Clean up messy WIP commits before a PR.

```bash
# 1. Make 4 commits: "WIP: start", "WIP: add more", "fix typo", "WIP: finalize"
# 2. git rebase -i HEAD~4 to squash into 2 clean commits
```
**Expected outcome:** 4 messy commits become 2 meaningful ones.

<details>
<summary>Solution</summary>

```bash
echo "s1" > f.txt && git add . && git commit -m "WIP: start"
echo "s2" > f.txt && git add . && git commit -m "WIP: add more"
echo "typo" > f.txt && git add . && git commit -m "fix typo"
echo "s3" > f.txt && git add . && git commit -m "WIP: finalize"
git rebase -i HEAD~4
# pick WIP: start -> squash WIP: add more -> fixup fix typo -> squash WIP: finalize
# Edit message to: "feat: implement full feature"
```
</details>

---

## Exercise 9: Interactive Rebase — Reword & Drop

**Scenario:** Edit a commit message and delete a bad commit.

```bash
# 1. 3 commits: "good commit", "bad commit", "another good"
# 2. rebase -i: reword "good" to "chore: add config", drop "bad", pick "another"
```
**Expected outcome:** 2 commits remain; first has new message.

<details>
<summary>Solution</summary>

```bash
echo "config" > c.txt && git add . && git commit -m "good commit"
echo "bad" > b.txt && git add . && git commit -m "bad commit"
echo "good2" > g.txt && git add . && git commit -m "another good"
git rebase -i HEAD~3
# reword "good commit" -> "chore: add config"
# drop "bad commit"
# pick "another good"
```
</details>

---

## Exercise 10: Cherry-Pick a Hotfix

**Scenario:** A fix on main needs to go to a release branch.

```bash
# 1. Main: 3 commits (3rd is "fix: critical bug")
# 2. Branch release/v1 from commit 2
# 3. Cherry-pick only the fix commit onto release/v1
```
**Expected outcome:** `release/v1` has the fix, not the other main commits.

<details>
<summary>Solution</summary>

```bash
echo "base" > b.txt && git add . && git commit -m "base"
echo "feature" > f.txt && git add . && git commit -m "feat: add feature"
echo "fix" > x.txt && git add . && git commit -m "fix: critical bug"
git checkout -b release/v1 HEAD~2
git cherry-pick main
git log --oneline  # base + fix
```
</details>

---

## Exercise 11: Cherry-Pick Range

**Scenario:** Selectively port specific commits from a feature branch.

```bash
# 1. Main: "initial". Branch feature: commits A, B, C, D
# 2. Back on main, cherry-pick only B and D
```
**Expected outcome:** Main has initial, B's changes, D's changes.

<details>
<summary>Solution</summary>

```bash
echo "init" > i.txt && git add . && git commit -m "initial"
git checkout -b feature
echo "A" > a.txt && git add . && git commit -m "A"
echo "B" > b.txt && git add . && git commit -m "B"
echo "C" > c.txt && git add . && git commit -m "C"
echo "D" > d.txt && git add . && git commit -m "D"
git checkout main
git cherry-pick <B-hash> <D-hash>
git log --oneline
```
</details>

---

## Exercise 12: Revert vs Reset

**Scenario:** Understand the difference between `git revert` and `git reset`.

```bash
# 1. Commits A, B, C
# 2. git revert B — creates revert commit (4 commits now)
# 3. git reset --hard HEAD~1 — removes revert (back to 3)
# 4. git reset --hard HEAD~1 — removes C (2 commits: A, B)
```
**Expected outcome:** Revert is safe (new commit), reset is destructive.

<details>
<summary>Solution</summary>

```bash
echo "A" > f.txt && git add . && git commit -m "A"
echo "B" > f.txt && git add . && git commit -m "B"
echo "C" > f.txt && git add . && git commit -m "C"
git revert HEAD~1 && git log --oneline   # 4 commits
git reset --hard HEAD~1 && git log --oneline  # 3 commits
git reset --hard HEAD~1 && git log --oneline  # 2 commits
```
</details>

---

## Exercise 13: Conflict During Rebase

**Scenario:** Resolve a conflict during rebase (not merge).

```bash
# 1. Main: shared.txt = "start". Branch feature: change to "feature work"
# 2. Main: change to "main work". Rebase feature onto main — conflict
# 3. Resolve to "feature work + main work", stage, rebase --continue
```
**Expected outcome:** Rebase completes, linear history.

<details>
<summary>Solution</summary>

```bash
echo "start" > s.txt && git add . && git commit -m "initial"
git checkout -b feature && echo "feature work" > s.txt && git add . && git commit -m "feature"
git checkout main && echo "main work" > s.txt && git add . && git commit -m "main"
git checkout feature && git rebase main
# Conflict! Edit to "feature work + main work"
git add . && git rebase --continue
git log --oneline --graph
```
</details>

---

## Exercise 14: Simulating Team Workflow

**Scenario:** Two developers collaborate on the same repo.

```bash
# Dev A: feat/search-bar, commit search.tsx
# Dev B: feat/navbar, commit navbar.tsx
# Dev A merges first, Dev B rebases/merges and resolves conflicts
```
**Expected outcome:** Both features integrated with conflict resolution.

<details>
<summary>Solution</summary>

```bash
# Dev A
echo "search bar" > search.tsx && git add . && git commit -m "feat: add search"
git checkout -b feat/search-bar && git checkout main
# Dev B
git checkout -b feat/navbar && echo "navbar" > navbar.tsx && git add . && git commit -m "feat: add navbar"
git checkout main && git merge feat/search-bar
git checkout feat/navbar && git rebase main
git checkout main && git merge feat/navbar
```
</details>

---

## Exercise 15: Interactive Rebase — Edit a Commit

**Scenario:** Go back to add a missing file to an earlier commit.

```bash
# 1. "add header" → "add footer"
# 2. Forgot to include styles.css in "add header"
# 3. rebase -i HEAD~2: edit "add header", add styles.css, amend, continue
```
**Expected outcome:** "add header" now includes styles.css.

<details>
<summary>Solution</summary>

```bash
echo "<header>Header</header>" > header.html && git add . && git commit -m "add header"
echo "<footer>Footer</footer>" > footer.html && git add . && git commit -m "add footer"
git rebase -i HEAD~2
# change "pick" to "edit" on "add header"
echo "body { margin: 0; }" > styles.css && git add styles.css
git commit --amend --no-edit && git rebase --continue
git log --oneline
```
</details>

---

## Exercise 16: GitHub Flow Simulation

**Scenario:** Full GitHub Flow — branch, PR, review, squash merge.

```bash
# 1. Main: README.md. Branch feat/user-auth: auth.js with 3 commits
# 2. "Review" with git diff main..feat/user-auth
# 3. Squash merge into main, delete branch
```
**Expected outcome:** Main has one squashed commit, feature branch gone.

<details>
<summary>Solution</summary>

```bash
echo "# My Project" > README.md && git add . && git commit -m "initial"
git checkout -b feat/user-auth
echo "function login() {}" > auth.js && git add . && git commit -m "feat: add login"
echo "function logout() {}" >> auth.js && git add . && git commit -m "feat: add logout"
echo "function reset() {}" >> auth.js && git add . && git commit -m "feat: add password reset"
git diff main..feat/user-auth
git checkout main && git merge --squash feat/user-auth
git commit -m "feat: add user authentication (#1)"
git branch -d feat/user-auth
git log --oneline --graph
```
</details>
