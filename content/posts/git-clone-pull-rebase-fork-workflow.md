---
title: "Git in Real Workflows: Cloning, Branching, Rebasing, Merging, and Fixing Everything"
seoTitle: "Git in Real Workflows: Cloning, Branching, Rebasing, Merging"
description: "Step-by-step explanation of Git operations in real workflows including clone, remotes, branching, pull, rebase, forks, tracking, cleanup and recovery."
slug: git-clone-pull-rebase-fork-workflow
date: 2025-06-24T08:00:00+05:30
categories:
	- technology
tags:
  - git
---

Git works fine when you're just committing and pushing, but the moment you deal with forks, remotes, rebases, or cleaning up history, it becomes unclear what Git is actually doing. This guide explains each concept in order - what happens, why it happens, and how to use it confidently in real workflows.

We’ll begin at the very start, cloning a repository, and build up step by step from there. Here's everything we'll cover in sequence:

##
## Table of Contents

- [Step 1: You run `git clone`](#step-1-you-run-git-clone)
- [Step 2: What branches exist after cloning?](#step-2-what-branches-exist-after-cloning)
- [Step 3: What exactly is `origin/main`?](#step-3-what-exactly-is-originmain)
- [Step 4: What does `git branch -r` show?](#step-4-what-does-git-branch--r-show)
- [Step 5: What does `git fetch` do?](#step-5-what-does-git-fetch-do)
- [Step 6: What does `git merge` do?](#step-6-what-does-git-merge-do)
- [Step 7: What does `git rebase` do?](#step-7-what-does-git-rebase-do)
- [Step 8: What does `git pull` do?](#step-8-what-does-git-pull-do)
- [Step 9: What does `git pull --rebase` do?](#step-9-what-does-git-pull---rebase-do)
- [Step 10: What does `git pull origin` mean?](#step-10-what-does-git-pull-origin-mean)
- [Step 11: Working with multiple remotes](#step-11-working-with-multiple-remotes)
- [Step 12: What is branch tracking in Git?](#step-12-what-is-branch-tracking-in-git)
- [Step 13: Why you always push to `origin` and pull from `upstream`](#step-13-why-you-always-push-to-origin-and-pull-from-upstream)
- [Step 14: Syncing a feature branch with upstream’s updated main](#step-14-syncing-a-feature-branch-with-upstreams-updated-main)
- [Step 15: Rebasing multiple dependent branches safely](#step-15-rebasing-multiple-dependent-branches-safely)
- [Step 16: Splitting a large branch into multiple pull requests](#step-16-splitting-a-large-branch-into-multiple-pull-requests)
- [Step 17: Rewriting commit history using interactive rebase](#step-17-rewriting-commit-history-using-interactive-rebase)
- [Step 18: Undoing mistakes using `reflog`, `reset`, and `restore`](#step-18-undoing-mistakes-using-reflog-reset-and-restore)
- [Step 19: Deleting local and remote branches cleanly](#step-19-deleting-local-and-remote-branches-cleanly)
- [Step 20: Dealing with diverged branches and force-push](#step-20-dealing-with-diverged-branches-and-force-push)
- [Git Command Summary](#-git-command-summary)

##
## Step 1: You run git clone

Example:

```bash
git clone https://github.com/company/project.git
```
This is the starting point. You're copying a project from a central server (like GitHub) to your own laptop. When you do this, Git performs several exact actions:

### A. Creates a new directory

Git makes a new folder called project/ and puts all the files and internal Git data inside it.

### B. Downloads all contents

Git downloads:
* The latest snapshot of the default branch (usually main)
* The entire commit history
* All other branches and tags
* Everything is stored inside .git/, which is a hidden folder containing Git's internal database

### C. Sets up a remote connection and names it origin

Now this part is critical:
* Git needs to remember where you cloned the repo from so it can pull/fetch/push code later
* It saves that remote URL internally (e.g. https://github.com/company/project.git)
* It assigns a shortcut name to this URL: origin

Why this shortcut? Because Git allows multiple remotes. You could later add another one called upstream, staging, etc. Instead of typing the full URL each time, Git uses short names like:
* origin - the one you cloned from (default)
* upstream - maybe the original repo you forked from
* backup - maybe another remote server

You can view or confirm this by running:

```bash
git remote -v
```
Output:
```bash
origin  https://github.com/company/project.git (fetch)
origin  https://github.com/company/project.git (push)
```
That means:
* Git knows the URL you cloned from
* Git has saved it with the nickname origin
* It will use that for future git fetch, git pull, and git push

You can even rename it later:

```bash
git remote rename origin mygithub
```
But origin is the default that Git sets up when you clone.

##
## Step 2: What branches exist after cloning?

After the clone completes, your Git repository has two types of branches stored inside:

### A. One local branch

Git checks out the default branch from the remote - usually main.

You can confirm this by running:

```bash
git branch
```
Output:
```
* main
```
This is your working branch. You can:
* Edit files
* Make commits
* Switch to other branches
* Push your changes later

Only this branch is created locally after clone.

### B. Multiple remote-tracking branches

Here comes the important part:

Git does not create local branches for all the other branches on the remote.
Instead, it creates remote-tracking branches for all of them.

You can list them using:

```bash
git branch -r
```
Example output:
```bash
origin/main
origin/dev
origin/feature-x
```
What do these mean?
* These are read-only branch pointers
* They show what each remote branch looked like at the time you cloned
* They are stored in: `.git/refs/remotes/origin/`
* You cannot commit to them directly
* They are updated only by git fetch or git pull

They help Git remember:

> This is what I last saw on the server for each branch.

So yes - when you clone, Git does not just get origin/main, it fetches and saves all remote branches into your local .git, under the prefix origin/.

##
## Step 3: What exactly is origin/main?

This is not a local branch. It is a remote-tracking branch. Git uses this to track the last known state of the main branch on the remote server (origin). It exists only so you can compare your local work with what's on the remote. It is stored internally like `.git/refs/remotes/origin/main`

This file contains the commit hash of the most recent commit on the remote main branch when you cloned or last fetched. This is how Git knows how far ahead or behind your local main is compared to what's on the server. You can't check out origin/main directly (without creating a new local branch), and you can't commit to it.

##
## Step 4: What does git branch -r show?

```bash
git branch -r
```
This command shows only remote-tracking branches.

That means:
* origin/main
* origin/dev
* origin/feature-x
* etc.

These reflect the remote-side branches Git is tracking locally - in read-only mode. They help you compare your local branches with the branches on the server. They live in `.git/refs/remotes/origin/`. Each file inside that folder contains the last known commit hash for that remote branch.

##
## Step 5: What does git fetch do?

```bash
git fetch
```

Git contacts the origin server and checks for updates on all remote branches.
* If any branch (e.g. main, dev, feature-x) has new commits, Git downloads those commits
* Git then updates the corresponding `origin/<branch>` pointers

That means:
* origin/main now points to the latest commit on GitHub's main
* origin/dev now points to the latest commit on GitHub's dev
* etc.

Your own local branches (main, dev, etc.) are untouched. Nothing in your working directory changes. To bring updates into your local branch, you must explicitly do:

```bash
git merge origin/main
```
OR
```bash
git rebase origin/main
```
Until then, your local code remains as-is.

##
## Step 6: What does git merge do?

You use git merge to bring changes from one branch into another.

Situation:
* You are on your local main branch
* You fetched the latest from remote → so origin/main is now ahead
* You want to update your branch with the new commits from origin/main

Command:

```bash
git merge origin/main
```
What happens:
* Git compares your main branch with origin/main
* It creates a merge commit that combines both sets of changes
* Your commit history now shows a branch-merge structure

Notes:
* If there are no conflicting changes, the merge completes cleanly
* If there are conflicts, Git will pause and ask you to resolve them manually, then commit

##
## Step 7: What does git rebase do?

You use git rebase to move your local commits on top of new commits from another branch.

Situation:
* origin/main has new commits
* Your main also has new commits that aren't pushed yet
* You want your commits to appear after the latest from the remote

Command:

```bash
git rebase origin/main
```
What happens:
* Git re-applies your commits one by one on top of the latest origin/main
* It rewrites history (commit hashes change)
* Final result: linear commit history, no merge commit

Key difference:

|git merge|git rebase|
|--|--|
|Keeps both histories | Makes it look like you committed after the remote did |
|Creates a merge commit | No merge commit |
|Preserves original commit IDs | Rewrites your commit IDs |

##
## Step 8: What does git pull do?

Git pull is a shortcut for two commands combined:

```bash
git fetch
git merge origin/<your-branch>
```
So:
* It contacts the remote
* Downloads new commits
* Automatically merges them into your current branch

Example:

You are on main, and you run:

```bash
git pull
```

Git will:
* Fetch latest changes from origin/main
* Merge them into your local main

##
## Step 9: What does git pull --rebase do?

This is like git pull, but instead of merging, it rebases your local commits on top of the remote.

```bash
git pull --rebase
```
It's a shortcut for:

```bash
git fetch
git rebase origin/<your-branch>
```
When to use:
* If you want a clean, linear history
* If you haven't pushed your local commits yet
* If your team prefers "no merge commits" in shared branches

##
## Step 10: What does git pull origin mean?

```bash
git pull origin
```
This tells Git:
* Pull from the origin remote
* Fetch and merge the corresponding branch for the current local branch

But it still depends on the tracking configuration of your current branch.

Better form:

```bash
git pull origin main
```
This is explicit. It means:
> Pull from remote origin, branch main, into my current branch.

##
## Step 11: Working with multiple remotes

Use case:

You fork a project. Now you have:
* Your GitHub repo → called origin
* The original project → called upstream

Add the second remote:

```bash
git remote add upstream https://github.com/original/project.git
```
Now check:

```bash
git remote -v
```
Output:
```bash
origin    https://github.com/yourname/project.git (fetch)
upstream  https://github.com/original/project.git (fetch)
```
To fetch new changes from upstream:

```bash
git fetch upstream
```
This brings in:
* upstream/main
* upstream/dev
* etc.

You can now merge or rebase from them:

```bash
git rebase upstream/main
```
or
```bash
git merge upstream/main
```
You usually don't push to upstream. You only push to origin.

##
## Step 12: What is branch tracking in Git?

When you create a local branch based on a remote branch, Git can link the two together. This link is called a tracking relationship. It tells Git:

> When I run git pull, git push, or git status on this local branch - which remote branch should I compare or sync with?

This connection saves you from typing the full remote name and branch name every time.

### Example 1: When you clone a repo

You run:

```bash
git clone https://github.com/company/project.git
```
Now you're inside the repo, and Git has automatically:
* Created a local branch main
* Set it to track origin/main

So when you run:

```bash
git pull
```

Git already knows:
* Pull from origin
* Use the main branch

You don't need to type:

```bash
git pull origin main
```
(but you still can)

### Example 2: You create a local branch from a remote one

Let's say you want to work on the remote branch origin/dev.

You run:

```bash
git checkout -b dev origin/dev
```
This creates a new local branch dev, and Git automatically links it to origin/dev.

Now:
* dev is a normal local branch (you can edit/commit)
* It tracks origin/dev

Which means:
* git pull on dev will pull from origin/dev
* git push will push to origin/dev
* git status will show if your local dev is ahead or behind origin/dev

### How to check what your branch is tracking?

```bash
git branch -vv
```
Output:
```bash
* dev   abc123 [origin/dev]  your commit message here
  main  def456 [origin/main] another message
```
Explanation:
* You are currently on dev (*)
* dev is linked to origin/dev
* main is linked to origin/main

### How to change or set tracking manually

If the link is missing or wrong, you can manually fix it:

```bash
git branch --set-upstream-to=origin/dev dev
```
This tells Git:

> The dev branch should now track origin/dev.

Now all future pull or push commands on dev will default to origin/dev.

##
## Step 13: Why you always push to origin and pull from upstream in a fork-based setup

When working with a forked repository, there are two remotes involved:
* origin → this is your personal copy (your fork) on GitHub
* upstream → this is the original repository you forked from

When you clone your fork, Git sets up origin by default:
```bash
git clone https://github.com/yourname/project.git
cd project
```
Now origin is set to https://github.com/yourname/project.git, and it is the only remote so far. You can verify it using:
```bash
git remote -v
```
Output:
```bash
origin  https://github.com/yourname/project.git (fetch)
origin  https://github.com/yourname/project.git (push)
```
This means Git knows your fork's location and will use this for all pull/push operations unless told otherwise. You own this fork, so you can push to it freely.

Next, you add the original repository as a second remote called upstream, using:
```bash
git remote add upstream https://github.com/company/project.git
```
Now if you run git remote -v again, you get:
```bash
origin    https://github.com/yourname/project.git (fetch)
origin    https://github.com/yourname/project.git (push)
upstream  https://github.com/company/project.git (fetch)
```
You now have two remotes:
* origin: you can pull from and push to this
* upstream: you can only pull from this (you don't have push permission)

Suppose the original repo (upstream) has received new commits on the main branch, and you want to update your local branch to match. First, fetch those updates from upstream:
```bash
git fetch upstream
```
This downloads all branch heads from the upstream repo. For example, it updates a remote-tracking branch called upstream/main. It does not modify your local main branch yet. To bring the upstream changes into your local main, run:
```bash
git merge upstream/main
```
or
```bash
git rebase upstream/main
```
This updates your local main to include the latest commits from the original repo. If you've made local changes, rebase re-applies them on top of upstream's latest commits. If you haven't made local changes, it simply fast-forwards.

After this, your local main is now up to date. Now you want to push your updated main branch to your own fork (origin). You do that with:
```bash
git push origin main
```
This updates the main branch in your forked repo on GitHub. You never push to upstream because you don't have write access. If you try:
```bash
git push upstream main
```
It will fail with a permission denied error unless you're a maintainer of that repo.

Once your fork (origin) has the updated code, you go to GitHub and raise a pull request from your fork's main to the original project's main. That's the standard open-source collaboration flow:
* Pull from upstream
* Push to origin
* Create pull request from origin/main to upstream/main

This keeps your fork in sync with the main project and allows your changes to be reviewed and merged.

##
## Step 14: Syncing a feature branch in your fork with upstream's updated main

Let's say you've created a feature branch called feature/foo in your fork (origin), based on main. The upstream repo (company/project.git) has moved ahead - more commits were added to main. You now want to bring those new commits from upstream into your feature branch, without polluting the history or introducing unrelated noise.

Here's the correct sequence:

You already have:
* origin → your fork
* upstream → original repo
* Local branch: feature/foo (tracking origin/feature/foo)

Now:

1. Fetch the latest changes from upstream
   ```bash
   git fetch upstream
   ```
   This updates upstream/main locally. It doesn't affect any of your branches.
2. Switch to your feature branch
   ```bash
   git checkout feature/foo
   ```
   Make sure you're on the branch you want to update.
3. Rebase your feature branch onto upstream's latest main
   ```bash
   git rebase upstream/main
   ```

This does the following:
* Takes the current state of upstream/main
* Re-applies your feature branch commits on top of it
* Keeps history clean and avoids merge commits

This makes your feature branch look like it was freshly branched from the latest upstream state.

If there are conflicts, Git pauses and lets you resolve them. After fixing each conflict:
```bash
git add <file>
git rebase --continue
```
You repeat this until the rebase completes.

4. Push your updated feature branch to your fork
```bash
git push origin feature/foo
```
If the rebase changed commit hashes (it always does), Git will reject a normal push. Use force:
```bash
git push --force origin feature/foo
```
This updates the feature/foo branch in your fork to match your rebased local branch.

Now, your feature branch is based on the latest upstream main. When you raise a pull request from feature/foo to upstream/main, your changes will cleanly sit on top of the latest upstream history.

##
## Step 15: How to rebase multiple topic branches safely when they depend on each other

Let's say you're working on a feature that you split into three local branches, each building on top of the previous:

**main → feature-a → feature-b → feature-c**

Each branch adds new code, but feature-b depends on code in feature-a, and feature-c depends on feature-b.

Now upstream main has moved forward, and you want to rebase your entire stack of branches onto the latest upstream/main.

The key rule is:

Always rebase from the bottom up - never top-down.

Commands (assuming upstream is already set):
```bash
git fetch upstream
```
Now rebase each branch one by one:
```bash
git checkout feature-a
git rebase upstream/main
git push --force origin feature-a
```
Then:
```bash
git checkout feature-b
git rebase feature-a
git push --force origin feature-b
```
Then:
```bash
git checkout feature-c
git rebase feature-b
git push --force origin feature-c
```
This way, all three branches now sit on top of the latest upstream/main, and their dependency chain is preserved cleanly.

If you do this in reverse (rebase feature-c before feature-a), the history will break and conflicts will be harder to fix.

##
## Step 16: How to split a large feature branch into multiple pull requests using cherry-pick or stacking

Suppose you created a massive branch called feature/huge with 30 commits, but now the team wants smaller, reviewable pull requests. You don't want to dump all 30 commits in one PR.

You can split this using two approaches:

### Approach A: Stacking branches (cleaner)
1. Start by checking out the base branch:
   ```bash
   git checkout main
   ```
2. Create a new branch for the first logical chunk:
   ```bash
   git checkout -b feature/part1
   ```
3. Use interactive rebase to split commits:
   ```bash
   git rebase -i feature/huge
   ```
   In the interactive menu, pick only the commits relevant to part 1, and drop or skip the rest.
4. Push part 1:
   ```bash
   git push origin feature/part1
   ```
5. Create a new branch for part 2, based on part 1:
   ```bash
   git checkout -b feature/part2
   ```
6. Cherry-pick the next group of commits from feature/huge:
   ```bash
   git cherry-pick <commit1> <commit2> ...
   ```
Repeat this for as many parts as needed. Each feature/partN becomes a separate PR, and each one is based on the previous.

This is called stacked PRs, where each branch builds on the previous one.

### Approach B: Cherry-pick into multiple independent branches

If the parts are truly unrelated, you can skip stacking and do:
```bash
git checkout main
git checkout -b part-a
git cherry-pick <commit1> <commit2>
git push origin part-a
```
Then:

```bash
git checkout main
git checkout -b part-b
git cherry-pick <commit3> <commit4>
git push origin part-b
```
Now each part is a separate branch, independent of each other. You can raise separate PRs against main.

Use this only if the commits don't depend on each other. Otherwise, prefer the stacked model.

##
## Step 17: How to rewrite commit history using git rebase -i (interactive rebase)

This is used to clean up your branch before pushing or raising a PR - especially if the history has noisy commits like "fix typo", "debug print", "oops", etc.

Say your branch has 6 commits on top of main:
```bash
main
  └── Commit A
      └── Commit B
          └── Commit C
              └── Commit D
                  └── Commit E
                      └── Commit F   ← HEAD
```

Now you want to:
* Combine related commits (squash)
* Rename some commit messages (reword)
* Remove junk commits (drop)
* Merge tiny fixes into earlier commits (fixup)
* Rearrange the commit order (reorder)

You run:
```bash
git rebase -i main
```
This opens an editor with a list of commits in reverse order (latest first):
```bash
pick 123456 Commit F
pick 234567 Commit E
pick 345678 Commit D
pick 456789 Commit C
pick 567890 Commit B
pick 678901 Commit A
```
Now you can replace the word pick with:
* squash - combine this commit with the one above and edit the message
* fixup - combine silently (no new commit message)
* reword - keep commit, change its message
* drop - delete this commit
* move lines up/down to reorder commits

Example:
```bash
reword 678901 Initial commit
pick 567890 Add login form
squash 456789 Fix login CSS
pick 345678 Add logout logic
drop 234567 Console.log debug
```
After saving, Git replays the commits one by one as per your instructions. If conflicts occur, you fix and continue:
```bash
git rebase --continue
```
If you want to abort:
```bash
git rebase --abort
```
When done, your branch history is rewritten, clean, compact, and well-labeled.

If the branch is already pushed, you will need to force-push:
```bash
git push --force origin your-branch
```
Use this only on branches you control, not shared ones.

##
## Step 18: How to undo mistakes using reflog, reset, and restore

Git never forgets anything immediately. Most mistakes can be reversed safely.

### A. `git reflog`: recover any previous state

Git logs every position your HEAD has pointed to - even after rebases, resets, etc.

Run:
```bash
git reflog
```
Output:
```bash
a1b2c3d HEAD@{0}: rebase: updated feature-x
f6e7d8c HEAD@{1}: checkout main
89ab123 HEAD@{2}: commit: fix bug in handler
...
```
This means: even if you messed up and lost commits, you can go back to any earlier state.

To recover:
```bash
git reset --hard HEAD@{2}
```
Or to branch off from there:
```bash
git checkout -b rescue HEAD@{2}
```

### B. `git reset`: move branches

Used to move the current branch pointer to some other commit.
* Soft: move pointer only, keep files/changes staged
  ```bash
  git reset --soft HEAD~2
  ```
* Mixed: move pointer, keep files in working directory but unstage them
  ```bash
  git reset --mixed HEAD~2
  ```
* Hard: move pointer and erase all changes
  ```bash
  git reset --hard HEAD~2
  ```

Only use `--hard` if you are 100% sure you want to discard changes.

### C. `git restore`: recover or discard files (working tree level)
* To discard local changes to a file:
  ```bash
  git restore somefile.java
  ```
* To restore a deleted file:
  ```bash
  git restore --staged --worktree somefile.java
  ```
* To unstage a file (after git add but before commit):
  ```bash
  git restore --staged file.txt
  ```

This is safer than reset for touching files without touching the branch pointer.

##
## Step 19: How to properly delete local and remote branches

Branches are cheap in Git, but they pile up fast. Once a branch is merged or no longer needed, it should be deleted - locally and remotely - to keep things clean.

### Deleting a local branch

List local branches:
```bash
git branch
```
To delete a local branch (e.g. `feature/foo`):
```bash
git branch -d feature/foo
```
This only works if the branch is fully merged. If not, Git will block the deletion.

To delete forcefully (even if not merged):
```bash
git branch -D feature/foo
```
This removes the reference from `.git/refs/heads/feature/foo`. It does not affect remotes.

### Deleting a remote branch

To delete a branch from the remote (e.g. from origin):

```bash
git push origin --delete feature/foo
```

This sends an explicit delete instruction to the remote. On GitHub, the branch will disappear from the branch list and PR dropdowns.

You can confirm by fetching and listing remote branches:

```bash
git fetch -p
git branch -r
```

Note: `-p` (prune) removes stale remote-tracking references like `origin/feature/foo` if the remote branch was deleted.

##
## Step 20: How to deal with diverged branches and safely force-push

Sometimes you try to push and Git says:

```bash
! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to ...
hint: Updates were rejected because the remote contains work that you do
hint: not have locally.
```

This means:
* Your local branch and the remote branch have both moved forward, but in different directions
* Git cannot safely push without losing commits from the remote

This is called a diverged branch

### Option 1: First pull, then push

The safe approach is to bring in the remote changes first:
```bash
git pull --rebase
```
This replays your local commits on top of the remote's latest state. Then push:
```bash
git push
```

This avoids merge commits and keeps a linear history.

### Option 2: Force push if your local branch is correct

If you intentionally rewrote history (e.g. interactive rebase) and you know your branch is the new correct version, force push it:
```bash
git push --force origin branchname
```
This will overwrite the branch on the remote with your local version. Use this only if:
* You're working on your own fork or a private feature branch
* Nobody else is depending on that branch

If others are using it, coordinate before forcing.

### Option 3: Overwrite your local branch with remote (discard local changes)

If your local branch is wrong and you want to reset it to match the remote exactly:
```bash
git fetch origin
git reset --hard origin/branchname
```
This deletes all local commits and makes your branch identical to the remote.

##
## Git Commands Summary

| Task                                 | Command                                           |
|--------------------------------------|---------------------------------------------------|
| Clone a repo                         | `git clone <url>`                                 |
| View remotes                         | `git remote -v`                                   |
| Add upstream remote                  | `git remote add upstream <url>`                   |
| Fetch all remotes                    | `git fetch` / `git fetch upstream`                |
| See local/remote branches            | `git branch` / `git branch -r`                    |
| Set tracking branch                  | `git branch --set-upstream-to=origin/dev dev`     |
| Pull with merge                      | `git pull`                                        |
| Pull with rebase                     | `git pull --rebase`                               |
| Merge remote into local              | `git merge origin/main`                           |
| Rebase onto remote                   | `git rebase origin/main`                          |
| Rebase feature branch on upstream    | `git rebase upstream/main`                        |
| Push to origin                       | `git push origin branchname`                      |
| Force push after rebase              | `git push --force origin branchname`              |
| Cherry-pick specific commits         | `git cherry-pick <commit>`                        |
| Interactive rebase (rewrite history) | `git rebase -i main`                              |
| Delete local/remote branch           | `git branch -d name` / `git push origin --delete` |
| Undo via reflog                      | `git reflog`, `git reset --hard HEAD@{n}`         |
| Discard local changes                | `git restore <file>` / `git reset --hard`         |
| Fix diverged push                    | `git pull --rebase` / `git push --force`          |
