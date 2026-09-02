# Day 24: Advanced Git Master Solution

## Step 1: Git Merge & Conflicts
**Objective:** Perform Fast-Forward merge, 3-Way merge, and manually resolve an intentional merge conflict.
```bash
# Simulating a conflict by editing the same line on both branches
git switch -c feature-conflict
echo "Line 1: Edited by feature branch" > conflict-test.txt
git commit -am "docs: edit line 1 in feature branch"

git switch main
echo "Line 1: Edited by main branch" > conflict-test.txt
git commit -am "docs: edit line 1 in main branch"

# Trigger conflict and resolve manually
git merge feature-conflict
echo "Line 1: Resolved text keeping both main and feature changes" > conflict-test.txt
git commit -am "fix: resolved merge conflict in conflict-test.txt"
```
**Output Screenshot:**
<img width="2154" height="776" alt="Image" src="https://github.com/user-attachments/assets/134d125b-f59f-4def-963d-df111842febd" />

---

## Step 2: Git Rebase
**Objective:** Rewrite commit history to maintain a perfectly linear project timeline without merge loops.
```bash
# Branching off, committing, and moving main ahead
git switch -c feature-dashboard
echo "Dashboard UI" > dashboard-ui.txt
git add dashboard-ui.txt
git commit -m "feat: add dashboard UI"

# Rebasing feature onto the new tip of main
git switch feature-dashboard
git rebase main

# Viewing the linear history
git log --oneline --graph --all -n 12
```
**Output Screenshot:**
<img width="2058" height="924" alt="Image" src="https://github.com/user-attachments/assets/27311191-4e05-4ec1-b363-7a1bc768a275" />

---

## Step 3: Squash Commit vs Regular Merge
**Objective:** Condense multiple messy Work-In-Progress (WIP) commits into a single clean commit for `main`.
```bash
# Squashing 5 commits from feature-profile into 1
git switch main
git merge --squash feature-profile
git commit -m "feat: add complete profile feature (squashed 5 commits into 1)"

# Regular merge comparison
git switch main
git merge feature-settings --no-ff -m "Merge branch 'feature-settings' into main"
```
**Output Screenshot:**
<img width="1882" height="756" alt="Image" src="https://github.com/user-attachments/assets/f58d9bc1-e938-48ce-a3ad-e0ffcf538a80" />

---

## Step 4: Git Stash
**Objective:** Temporarily save uncommitted changes to safely switch branches during emergencies.
```bash
# Saving uncommitted work to the stash locker
echo "Doing some complex work..." > urgent-work.txt
git add urgent-work.txt
git stash push -m "saving complex work temporarily"

# Popping the stash after returning to the branch
git stash pop

# Applying a specific stash from the list
git stash list
git stash apply stash@{1}
```
**Output Screenshot:**
<img width="1788" height="246" alt="Image" src="https://github.com/user-attachments/assets/eed2df48-2851-4885-9135-82e257466355" />

---

## Step 5: Git Cherry Picking
**Objective:** Extract a single, specific commit from another branch and apply it to the current branch.
```bash
# Grabbing the hash of a specific commit on feature-hotfix
CHERRY_PICK_HASH=$(git rev-parse HEAD)

# Switching to main and applying ONLY that specific commit
git switch main
git cherry-pick $CHERRY_PICK_HASH
```
**Output Screenshot:**
<img width="1716" height="298" alt="Image" src="https://github.com/user-attachments/assets/e97e1882-c391-4e86-b0fd-54dfe50e06ca" />

---

## 5 Key Points What I Learned
1. **Merge Conflicts are Protections, Not Errors:** Git physically prevents code overwrites when lines clash, forcing the developer to manually verify and resolve the final state of the code.
2. **Rebase Keeps History Linear:** While `git merge` preserves the exact chronological branching timeline, `git rebase` rewrites history to place feature commits perfectly on top of the main branch, avoiding diamond-shaped merge loops.
3. **Squash Merging Cleans Up Garbage:** Using `--squash` is the best way to combine dozens of messy, minor WIP commits ("typo fix", "test again") into one pristine, meaningful commit on the `main` branch.
4. **Stash is the Ultimate Context-Switcher:** `git stash` safely locks away uncommitted changes so you can jump to another branch for an urgent fix without losing your current progress or making a broken commit.
5. **Cherry-Picking Isolates Hotfixes:** You don't have to merge an entire branch to get a specific fix. `git cherry-pick <hash>` allows you to surgically extract exactly the one commit you need for production.
