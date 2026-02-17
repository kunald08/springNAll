# Git & Source Control Management — From Basics to Expert

---

## 1. Source Control Management (SCM)

### What is SCM?

**Source Control Management** (also called Version Control) is the practice of tracking and managing changes to code. It allows multiple developers to work on the same codebase simultaneously without conflicts.

### Types of Version Control

| Type | Description | Examples |
|------|-------------|---------|
| **Local** | Changes tracked on local machine only | RCS |
| **Centralized (CVCS)** | Single central server, everyone commits there | SVN, CVS, Perforce |
| **Distributed (DVCS)** | Every developer has a full copy of the repo | **Git**, Mercurial |

### Under the Hood: Why Git is Distributed

```
Centralized (SVN):                 Distributed (Git):
                                   
   ┌──────────┐                    ┌──────────┐
   │  Central  │                   │  Remote   │
   │  Server   │                   │  (GitHub) │
   └────┬─────┘                    └──┬──┬──┬──┘
        │                             │  │  │
   ┌────┴────┐                ┌──────┘  │  └──────┐
   │         │                │         │         │
┌──┴──┐  ┌──┴──┐          ┌──┴──┐  ┌──┴──┐  ┌──┴──┐
│Dev A│  │Dev B│          │Dev A│  │Dev B│  │Dev C│
│(no  │  │(no  │          │FULL │  │FULL │  │FULL │
│copy)│  │copy)│          │REPO │  │REPO │  │REPO │
└─────┘  └─────┘          └─────┘  └─────┘  └─────┘

In Git, each developer has the ENTIRE repository history locally.
You can commit, branch, merge — all OFFLINE.
```

---

## 2. Git Fundamentals

### What is Git?

Git is a **distributed version control system** created by **Linus Torvalds** in 2005 (the creator of Linux). It tracks changes in source code during software development.

### Under the Hood: How Git Stores Data

Git doesn't store diffs — it stores **snapshots**:

```
Commit 1:    Commit 2:    Commit 3:
┌────────┐  ┌────────┐  ┌────────┐
│ file A ──→│ file A'──→│ file A'│  (A' = changed, A' = unchanged)
│ file B │  │ file B │  │ file B'│
│ file C │  │ file C'│  │ file C'│
└────────┘  └────────┘  └────────┘

Each commit is a SNAPSHOT of all files at that moment.
Unchanged files are stored as REFERENCES (pointers) to previous versions.
Git uses SHA-1 hashes to identify every object (commit, tree, blob).
```

### Git Objects (Internal Architecture)

```
.git/objects/
├── blob    → File content (the actual data)
├── tree    → Directory listing (maps names to blobs/trees)
├── commit  → Snapshot + metadata (author, message, parent commit)
└── tag     → Named reference to a commit

Commit Object:
┌────────────────────────────────┐
│ tree    → a1b2c3...           │ ← Points to root tree
│ parent  → d4e5f6...           │ ← Points to previous commit
│ author  → Kunal <k@mail.com>  │
│ message → "Add login feature" │
└────────────────────────────────┘
         │
         ▼
Tree Object (root):
┌────────────────────────────────┐
│ blob a1b2... → README.md       │
│ blob c3d4... → App.java        │
│ tree e5f6... → src/            │
└────────────────────────────────┘
```

### The Three Areas of Git

```
┌─────────────┐    git add     ┌─────────────┐   git commit   ┌─────────────┐
│  Working     │ ────────────→ │   Staging    │ ────────────→ │  Repository │
│  Directory   │               │   Area       │               │  (.git)     │
│  (modified)  │ ←──────────── │  (index)     │               │  (history)  │
└─────────────┘    (editing)   └─────────────┘               └─────────────┘

Working Directory: Where you edit files
Staging Area:      Where you prepare changes for commit (also called "index")
Repository:        Where commits are permanently stored
```

---

## 3. Working Locally with Git

When you work with Git, most of your day-to-day activity happens **locally** on your own computer. You don't need an internet connection to commit changes, create branches, or view history — Git has everything stored on your machine. The basic daily workflow is: (1) make changes to your files, (2) **stage** the changes you want to save (using `git add`), and (3) **commit** them as a permanent snapshot (using `git commit`). Think of it like taking a photo of your project at an important moment — you can always go back to that photo later.

### Configuration

```bash
# Set identity (required before first commit)
git config --global user.name "Kunal"
git config --global user.email "kunal@example.com"

# Set default editor
git config --global core.editor "code --wait"   # VS Code

# Set default branch name
git config --global init.defaultBranch main

# View all config
git config --list

# Config levels:
# --system  → /etc/gitconfig (all users)
# --global  → ~/.gitconfig (your user)
# --local   → .git/config (this repo only — highest priority)
```

### Initializing a Repository

```bash
# Create a new repo
mkdir my-project && cd my-project
git init
# Creates .git/ directory with all Git internals

# What's inside .git/?
.git/
├── HEAD            # Points to current branch (ref: refs/heads/main)
├── config          # Repo-specific config
├── objects/        # All Git objects (blobs, trees, commits)
├── refs/           # Branch and tag pointers
│   ├── heads/      # Local branches
│   └── tags/       # Tags
├── index           # Staging area (binary file)
├── hooks/          # Client/server-side scripts
└── logs/           # Reflog (history of HEAD movements)
```

### Basic Workflow

The Git workflow is straightforward: **edit → stage → commit**. First you edit your files normally. Then you use `git add` to tell Git which changes you want to include in the next snapshot (you might have changed 5 files but only want to commit 3 of them). Finally, `git commit` saves those staged changes as a permanent snapshot with a message describing what you changed. The `git status` command is your best friend — it always shows you what's going on.

```bash
# 1. Check status
git status

# 2. Stage changes
git add file.txt              # Stage specific file
git add .                     # Stage all changes
git add *.java                # Stage all Java files
git add -p                    # Interactive staging (pick hunks)

# 3. Commit
git commit -m "Add login feature"
git commit -am "Fix bug"      # Stage tracked files + commit (shortcuts)

# 4. View history
git log                       # Full log
git log --oneline             # Compact log
git log --oneline --graph     # Graph view
git log --oneline -5          # Last 5 commits
git log --stat                # Show files changed per commit
git log --author="Kunal"      # Filter by author
git log --since="2025-01-01"  # Filter by date
git log -- file.txt           # History of specific file
```

### Viewing Changes

`git diff` is how you see **exactly what changed** in your files. Before you commit, you can use `git diff` to review your changes — this is like holding up two versions of a document side by side and highlighting the differences. Green lines with `+` were added, red lines with `-` were removed. This is essential for catching mistakes before you commit them.

```bash
# Working directory vs staging area
git diff

# Staging area vs last commit
git diff --staged
git diff --cached             # Same as --staged

# Between two commits
git diff abc123..def456

# Between two branches
git diff main..feature-branch

# Word-level diff
git diff --word-diff

# Summary of changes
git diff --stat
```

### Undoing Changes

Mistakes happen. Git provides several ways to undo things, depending on how far along the mistake is:
- **File not staged yet**: `git restore file.txt` throws away your changes and goes back to the last committed version
- **File already staged**: `git restore --staged file.txt` unstages it (but keeps your changes in the file)
- **Already committed**: `git reset` can undo commits (various levels of severity), or `git revert` creates a new commit that undoes a previous one

The key difference between `reset` and `revert` is that `reset` **rewrites history** (the old commit disappears), while `revert` **adds new history** (creates an "undo" commit). On shared branches where others have already pulled your commits, always use `revert` — rewriting history that others depend on causes serious problems.

```bash
# Discard changes in working directory
git checkout -- file.txt      # Old way
git restore file.txt          # Modern way (Git 2.23+)

# Unstage a file
git reset HEAD file.txt       # Old way
git restore --staged file.txt # Modern way

# Amend last commit (change message or add files)
git add forgotten_file.txt
git commit --amend -m "Updated message"

# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last commit (keep changes unstaged)
git reset --mixed HEAD~1      # Default behavior
git reset HEAD~1

# Undo last commit (DISCARD changes — ⚠️ DANGEROUS)
git reset --hard HEAD~1

# Revert a commit (creates new "undo" commit — safe for shared branches)
git revert abc123
```

### Under the Hood: `reset` vs `revert`

```
git reset --hard HEAD~1:
Before: A ← B ← C (HEAD)
After:  A ← B (HEAD)         ← C is GONE (rewrites history)

git revert HEAD:
Before: A ← B ← C (HEAD)
After:  A ← B ← C ← C' (HEAD)  ← C' undoes C (safe, history preserved)

Rule: Use REVERT on shared branches, RESET on your own branches.
```

---

## 4. Git Commit Best Practices

A good commit message is like a note to your future self (and your teammates). It should clearly explain **what** changed and **why**. Write them as if someone reading the code in 6 months needs to understand why this change was made. A common convention is to start with a type (feat, fix, docs, etc.) followed by a short description. Keep the first line under 72 characters — this shows up in `git log --oneline` and on GitHub.

### Commit Message Format

```
<type>(<scope>): <subject>

<body — optional, wrap at 72 chars>

<footer — optional>

Types:
  feat:     New feature
  fix:      Bug fix
  docs:     Documentation only
  style:    Formatting (no code change)
  refactor: Code restructuring (no behavior change)
  test:     Adding/fixing tests
  chore:    Build process, dependencies, tooling

Examples:
  feat(auth): add JWT token authentication
  fix(api): resolve null pointer in user service
  docs(readme): update installation instructions
  refactor(db): extract connection pool logic
```

---

## 5. Branching, Merging, and Rebasing

Branching is one of Git’s most powerful features. A **branch** is like creating a parallel copy of your project where you can make changes without affecting the main code. This lets you work on a new feature, experiment with ideas, or fix a bug — all without touching the stable main branch. When you're done, you **merge** your branch back into main to combine the changes.

In real teams, the main branch always has the stable, working code. Every new feature or bug fix gets its own branch. Once the work is tested and reviewed, it gets merged back. This way, even if someone's feature is broken, it doesn't break anything for the rest of the team.

### Branches

A branch is simply a **pointer to a commit**. Creating a branch is instant — Git just creates a new pointer.

```bash
# List branches
git branch              # Local branches
git branch -a           # All branches (local + remote)
git branch -v           # Branches with last commit

# Create branch
git branch feature-login

# Switch to branch
git checkout feature-login    # Old way
git switch feature-login      # Modern way (Git 2.23+)

# Create and switch
git checkout -b feature-login # Old way
git switch -c feature-login   # Modern way

# Delete branch
git branch -d feature-login   # Safe delete (must be merged)
git branch -D feature-login   # Force delete

# Rename branch
git branch -m old-name new-name
git branch -m new-name        # Rename current branch
```

### Under the Hood: What a Branch Really Is

```bash
# A branch is just a file containing a commit hash
cat .git/refs/heads/main
# Output: a1b2c3d4e5f6... (40-char SHA-1 hash)

# HEAD points to the current branch
cat .git/HEAD
# Output: ref: refs/heads/main

Branch Diagram:
                     feature
                       ↓
        ┌───┐  ┌───┐  ┌───┐
   ─────│ C │──│ D │──│ E │
        └───┘  └───┘  └───┘
       /
┌───┐  ┌───┐
│ A │──│ B │
└───┘  └───┘
              ↑
             main
              ↑
             HEAD
```

### Merging

Merging takes the changes from one branch and combines them into another branch. For example, after finishing a feature on the `feature-login` branch, you merge it into `main` so the feature becomes part of the main codebase. Git is smart about this — if the changes don't overlap, it merges them automatically. If two people changed the same lines, Git creates a **merge conflict** that you have to resolve manually.

```bash
# Merge feature branch into main
git switch main
git merge feature-login

# Merge types:
# 1. Fast-forward (linear history — no merge commit)
# 2. Three-way merge (creates a merge commit)
# 3. Squash merge (combines all commits into one)
```

### Fast-Forward Merge

```
Before:
main:     A ← B
                ↖
feature:          C ← D

After git merge feature (fast-forward):
main:     A ← B ← C ← D
                         ↑
                        HEAD

No merge commit needed — main just "moves forward" to D
```

### Three-Way Merge

```
Before:
main:     A ← B ← E
                ↖
feature:          C ← D

After git merge feature (three-way):
main:     A ← B ← E ──── M (merge commit)
                ↖        ↗
feature:          C ← D

M combines changes from both E and D, using B as the common ancestor
```

### Merge Conflicts

Merge conflicts happen when Git can't figure out which version to keep because **two branches modified the same lines** in a file. Git marks the conflicting sections in the file with `<<<<<<<`, `=======`, and `>>>>>>>` markers. Your job is to: (1) open the file, (2) decide which version to keep (or combine both), (3) remove the conflict markers, (4) stage and commit the resolved file. Conflicts sound scary but they're normal and happen regularly in team settings.

```bash
# Conflicts happen when two branches modify the SAME lines

# Git marks conflicts in the file:
<<<<<<< HEAD
This is the main branch version
=======
This is the feature branch version
>>>>>>> feature-login

# Resolution steps:
# 1. Open the conflicting file
# 2. Decide which version to keep (or combine)
# 3. Remove the conflict markers (<<<<<<, ======, >>>>>>)
# 4. Stage the resolved file
git add resolved_file.txt
# 5. Complete the merge
git commit
```

### Rebasing

Rebasing is an alternative to merging. Instead of creating a merge commit, rebase **replays your commits on top of** the target branch, making the history look like a straight line. The result is the same code, but with a cleaner, linear history that's easier to read. The trade-off is that rebase rewrites commit history (creates new commit hashes), so you should **never rebase commits that have been shared with others** — it causes confusion and conflicts for your teammates.

Rebase moves your branch's commits **on top of** another branch, creating a linear history:

```bash
# Rebase feature onto main
git switch feature-login
git rebase main

# Before rebase:
main:     A ← B ← E
                ↖
feature:          C ← D

# After rebase:
main:     A ← B ← E
                     ↖
feature:               C' ← D'

# C' and D' are NEW commits (different SHA) — replayed on top of E
```

### Merge vs Rebase

```
Merge:
  - Preserves complete history
  - Creates merge commits (can be noisy)
  - Safe for shared branches
  - Non-destructive

Rebase:
  - Creates linear, clean history
  - No merge commits
  - ⚠️ NEVER rebase shared/public branches
  - Rewrites commit history

Golden Rule: Never rebase commits that have been pushed to a shared branch.
```

### Interactive Rebase (Powerful!)

```bash
# Rewrite the last 3 commits
git rebase -i HEAD~3

# Opens editor with:
pick abc123 First commit
pick def456 Second commit
pick ghi789 Third commit

# Commands:
# pick   — use commit as-is
# reword — change commit message
# edit   — pause for amending
# squash — combine with previous commit
# fixup  — like squash but discard message
# drop   — remove commit
```

---

## 6. Working Remotely with Git

So far, everything has been on your own computer. But in real projects, you need to share your code with teammates and keep everyone’s changes in sync. This is where **remote repositories** come in. A remote is simply a copy of your repository stored on a server (like GitHub, GitLab, or Bitbucket). You **push** your local commits to the remote so others can see them, and **pull** their changes from the remote to stay up to date.

The most common remote is called `origin` — it's the server your repository was originally cloned from. The typical team workflow is: pull the latest changes from the remote, create a branch for your work, make your changes and commit them, push your branch to the remote, then open a Pull Request for code review.

### Remote Repositories

```bash
# Clone a repository
git clone https://github.com/user/repo.git
git clone git@github.com:user/repo.git         # SSH (recommended)

# View remotes
git remote -v
# origin  https://github.com/user/repo.git (fetch)
# origin  https://github.com/user/repo.git (push)

# Add remote
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git

# Remove/rename remote
git remote remove origin
git remote rename origin upstream
```

### Push, Pull, Fetch

**Push** sends your local commits to the remote server so others can see your work. **Fetch** downloads changes from the remote but doesn't change your local files (just updates your knowledge of what's on the server). **Pull** is fetch + merge — it downloads AND applies remote changes to your local branch. Think of it this way: `fetch` is like checking your mailbox, `pull` is checking your mailbox AND reading the letters.

```bash
# Push to remote
git push origin main              # Push main branch
git push origin feature-login     # Push feature branch
git push -u origin main           # Set upstream (first push)
git push                          # After -u, just use this

# Fetch (download but don't merge)
git fetch origin                  # Fetch all branches
git fetch origin main             # Fetch specific branch

# Pull (fetch + merge)
git pull origin main              # Pull and merge
git pull --rebase origin main     # Pull and rebase (cleaner)

# Push a new branch
git push -u origin feature-login

# Delete remote branch
git push origin --delete feature-login
```

### Under the Hood: `fetch` vs `pull`

```
git fetch:
Remote:  A ← B ← C ← D ← E
Local:   A ← B ← C
                    ↖
origin/main:          D ← E  (updated, but YOUR branch unchanged)

git pull (= fetch + merge):
Remote:  A ← B ← C ← D ← E
Local:   A ← B ← C ← D ← E  (your branch is updated)
```

### Collaborative Workflow (GitHub Flow)

```
1. Create a branch from main
   git switch -c feature/user-auth

2. Make changes and commit
   git add . && git commit -m "feat(auth): add login endpoint"

3. Push to remote
   git push -u origin feature/user-auth

4. Open a Pull Request (PR) on GitHub

5. Code review by team members

6. Merge PR into main (on GitHub)

7. Delete feature branch
   git branch -d feature/user-auth
   git push origin --delete feature/user-auth

8. Update local main
   git switch main && git pull origin main
```

---

## 7. Useful Git Commands

Beyond the daily workflow, Git has tools for specific situations: **stashing** temporarily saves work-in-progress without committing it, **tagging** marks specific commits as important milestones (like release versions), **cherry-picking** applies a specific commit from one branch to another, and various **log tricks** help you search through history efficiently.

### Stashing (Temporary Save)

Sometimes you're in the middle of working on something and need to quickly switch branches (maybe to fix an urgent bug). But your current changes aren't ready to commit yet. `git stash` saves your uncommitted changes to a temporary storage area and gives you a clean working directory. When you're done with the urgent work, `git stash pop` brings your changes back. Think of it like putting your papers in a drawer so you can work on something else, then taking them back out later.

```bash
# Stash current changes (save and clean working directory)
git stash
git stash save "WIP: login feature"

# List stashes
git stash list
# stash@{0}: WIP on main: abc123 Last commit message

# Apply stash
git stash pop               # Apply and remove from stash list
git stash apply             # Apply but keep in stash list
git stash apply stash@{1}   # Apply specific stash

# Drop stash
git stash drop stash@{0}
git stash clear             # Remove all stashes
```

### Tagging

Tags are used to mark specific commits as important — usually **release versions** like `v1.0.0`, `v2.1.3`, etc. Unlike branches which move forward with new commits, tags are permanent markers that always point to the same commit. When you deploy version 2.0 of your application, you tag that commit so you can always find the exact code that was deployed.

```bash
# Create tag
git tag v1.0.0                           # Lightweight tag
git tag -a v1.0.0 -m "Release v1.0.0"   # Annotated tag (recommended)

# List tags
git tag
git tag -l "v1.*"

# Push tags
git push origin v1.0.0
git push origin --tags                    # Push all tags

# Delete tag
git tag -d v1.0.0                        # Local
git push origin --delete v1.0.0          # Remote
```

### Cherry-Pick

Cherry-picking lets you take a **single specific commit** from one branch and apply it to your current branch. This is useful when a bug fix was made on a feature branch but you need that same fix on the main branch right now, without merging the entire feature branch.

```bash
# Apply a specific commit from another branch
git cherry-pick abc123

# Cherry-pick without committing
git cherry-pick --no-commit abc123
```

### Git Log Tips

```bash
# Beautiful graph log
git log --oneline --graph --all --decorate

# Git alias for pretty log
git config --global alias.lg "log --oneline --graph --all --decorate"
git lg    # Now works as shortcut

# Search commit messages
git log --grep="login"

# Search code changes
git log -S "function_name"     # Pickaxe — find when code was added/removed
```

### `.gitignore`

The `.gitignore` file tells Git which files and folders to **completely ignore** — Git won't track them, stage them, or complain about them. This is essential because projects generate many files that shouldn't be in the repository: compiled output (`target/`, `build/`), downloaded dependencies (`node_modules/`), IDE settings (`.idea/`, `.vscode/`), environment secrets (`.env`), and OS junk files (`.DS_Store`). Keeping these out of Git keeps your repository clean and prevents accidentally sharing secrets or large files.

```gitignore
# Compiled files
*.class
*.o
*.pyc
__pycache__/

# Build directories
target/
build/
dist/
node_modules/

# IDE files
.idea/
.vscode/
*.iml
.project
.settings/

# OS files
.DS_Store
Thumbs.db

# Environment files
.env
.env.local
*.log

# Secrets
*.key
*.pem
credentials.json
```

---

## 8. Quick Reference

```bash
# Essential daily commands
git status                    # What's changed?
git add .                     # Stage everything
git commit -m "message"       # Commit
git push                      # Push to remote
git pull                      # Get latest changes
git switch -c branch-name     # Create and switch branch
git merge branch-name         # Merge branch
git log --oneline -10         # Recent history
git stash / git stash pop     # Temporary save
git diff                      # View changes
```
