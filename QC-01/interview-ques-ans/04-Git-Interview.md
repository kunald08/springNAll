# Git & SCM — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is Version Control / SCM? Why do we need it?

**Answer:**
Version Control (also called Source Code Management or SCM) is a system that **tracks changes** to files over time. It lets multiple developers work on the same codebase simultaneously without overwriting each other's work.

We need it because:
- It maintains a **complete history** of every change — who changed what, when, and why.
- It allows **collaboration** — multiple people can work in parallel on different branches.
- We can **revert** to any previous version if something breaks.
- It enables **code reviews** through pull requests.
- It's the foundation for **CI/CD** pipelines.

Without version control, we'd be emailing zip files around — and that's a recipe for disaster.

---

## 2. What is Git? How is it different from other VCS tools?

**Answer:**
Git is a **distributed version control system** created by Linus Torvalds in 2005. The key word is **distributed** — every developer has a full copy of the entire repository, including all history, on their local machine.

Compared to older **centralized** systems like SVN or CVS:
- In SVN, there's ONE central server. If it goes down, nobody can commit. In Git, everyone has the full repo — I can commit, branch, and work completely offline.
- Git is **much faster** because most operations are local.
- Git's branching is **lightweight** — creating a branch is instantaneous. In SVN, branching was expensive.
- Git tracks content by **snapshots** (not diffs), using SHA-1 hashes for integrity.

---

## 3. Explain the three areas/stages in Git.

**Answer:**
Git has three main areas that files move through:

1. **Working Directory** — where I edit files. These are the actual files on my disk. Changes here are "untracked" or "modified."

2. **Staging Area (Index)** — a preparation area. When I run `git add file.txt`, the file moves here. Think of it as a "draft" of my next commit. I can selectively add files — I don't have to commit everything at once.

3. **Repository (.git directory)** — when I run `git commit`, the staged snapshot is permanently saved here with a unique SHA hash, a message, and metadata.

The flow is: **Edit → Stage (`git add`) → Commit (`git commit`)**

This three-stage design gives me fine control over exactly what goes into each commit.

---

## 4. What are the most common Git commands you use daily?

**Answer:**
My daily workflow involves:

- `git status` — check what's modified, staged, untracked. I run this constantly.
- `git add .` or `git add file.txt` — stage changes
- `git commit -m "message"` — commit with a message
- `git pull origin main` — fetch and merge remote changes
- `git push origin feature-branch` — push my branch to remote
- `git branch` — list branches
- `git checkout -b feature-xyz` or `git switch -c feature-xyz` — create and switch to a new branch
- `git log --oneline` — quick view of commit history
- `git diff` — see unstaged changes
- `git stash` — temporarily save uncommitted work

---

## 5. What is the difference between `git fetch` and `git pull`?

**Answer:**
- **`git fetch`** — downloads new commits from the remote but does NOT merge them into my working branch. It's safe — it just updates my knowledge of what's on the remote. I can review the changes first with `git log origin/main` before merging.

- **`git pull`** — does a `git fetch` PLUS a `git merge` in one step. It downloads and immediately integrates the remote changes into my current branch.

I prefer **`git fetch` followed by `git merge`** when I want to review changes before merging. But for routine syncing on a feature branch, `git pull` is fine.

---

## 6. What is a branch in Git? Why do we use branches?

**Answer:**
A branch is simply a **lightweight, movable pointer to a commit**. Under the hood, it's just a file containing a 40-character SHA hash pointing to the latest commit on that branch.

We use branches to **isolate work**. Each feature, bugfix, or experiment gets its own branch. This way:
- Multiple developers can work simultaneously without conflicts.
- The `main` branch stays stable and deployable.
- We can experiment freely — if it doesn't work out, we delete the branch.
- Code reviews happen on branches via **pull requests** before merging to main.

A typical flow: branch off `main` → work on feature → push → create Pull Request → code review → merge → delete branch.

---

## 7. What is the difference between `git merge` and `git rebase`?

**Answer:**
Both integrate changes from one branch into another, but differently:

**`git merge`** — creates a **merge commit** that joins two branches together. The history shows the branch structure — you can see when the branch was created and when it was merged.

**`git rebase`** — takes my commits and **replays them** on top of the target branch, creating a **linear history**. It rewrites commit hashes, so the history looks as if I branched off from the latest point.

When I'd use each:
- **Merge** — for merging a feature branch into main. It preserves the full history.
- **Rebase** — for keeping my feature branch up-to-date with main. `git rebase main` on my feature branch brings in the latest main changes with a clean history.

**Golden rule**: NEVER rebase commits that have been pushed and shared with others. Rebase rewrites history, which causes problems for anyone who already has those commits.

---

## 8. How do you resolve merge conflicts?

**Answer:**
Merge conflicts happen when Git can't automatically combine changes — usually when two people edited the **same lines** of the same file.

Git marks the conflict in the file like this:
```
<<<<<<< HEAD
my changes
=======
their changes
>>>>>>> feature-branch
```

My resolution process:
1. Open the conflicting file.
2. Look for the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
3. Decide which code to keep — mine, theirs, or a combination.
4. Remove the conflict markers.
5. `git add` the resolved file.
6. `git commit` to complete the merge.

In VS Code, there are helpful buttons: "Accept Current", "Accept Incoming", "Accept Both" that make this easier. For complex conflicts, I talk to the other developer to understand their intent.

---

## 9. What is `git stash`? When do you use it?

**Answer:**
`git stash` temporarily **saves uncommitted changes** (both staged and unstaged) and reverts the working directory to a clean state. It's like putting your work on a shelf.

I use it when:
- I need to **switch branches** but I'm not ready to commit my current work.
- An urgent bug comes in and I need a clean workspace immediately.
- I want to pull remote changes but have local modifications that would conflict.

Commands:
- `git stash` — save changes to the stash
- `git stash list` — see all stashed items
- `git stash pop` — apply the latest stash and remove it from the stash list
- `git stash apply` — apply but keep it in the stash list
- `git stash drop stash@{0}` — delete a specific stash
- `git stash -m "WIP: login feature"` — stash with a descriptive message

---

## 10. What is a Pull Request (PR)? What's your PR workflow?

**Answer:**
A Pull Request is a **request to merge** my branch into another branch (usually `main`). It's not a Git feature — it's a platform feature from **GitHub, GitLab, or Bitbucket**. It's the mechanism for **code review**.

My typical PR workflow:
1. Create a feature branch: `git checkout -b feature/user-login`
2. Make commits with clear messages.
3. Push: `git push origin feature/user-login`
4. Open a PR on GitHub targeting `main`.
5. Write a description — what I changed, why, and any testing notes.
6. Request reviewers.
7. Address review comments — push additional commits if needed.
8. Once approved, merge (usually "Squash and Merge" for a clean history).
9. Delete the feature branch.

PRs ensure no code goes to `main` without at least one other person reviewing it.

---

## 11. What is `.gitignore`? What do you typically put in it?

**Answer:**
`.gitignore` is a file that tells Git which files to **ignore** — they won't be tracked, staged, or committed.

I typically ignore:
- **Build output**: `target/`, `build/`, `dist/`, `*.class`, `*.jar`
- **Dependencies**: `node_modules/`, `venv/`, `.gradle/`
- **IDE files**: `.idea/`, `.vscode/`, `*.iml`
- **OS files**: `.DS_Store`, `Thumbs.db`
- **Secrets**: `.env`, `*.pem`, `*.key` — NEVER commit secrets
- **Logs**: `*.log`, `logs/`

The patterns use globs: `*` matches anything, `**` matches directories at any depth, `/` at the start means root only, `!` negates a pattern.

If I've already committed a file and then add it to `.gitignore`, it won't be removed. I need to: `git rm --cached file.txt` first, then commit.

---

## 12. How do you undo changes in Git?

**Answer:**
Depends on what stage the changes are in:

**Not yet staged (working directory):**
- `git checkout -- file.txt` or `git restore file.txt` — discard changes in a specific file
- `git checkout -- .` — discard ALL unstaged changes

**Staged but not committed:**
- `git reset HEAD file.txt` or `git restore --staged file.txt` — unstage a file (keeps the changes in working directory)

**Already committed (locally):**
- `git reset --soft HEAD~1` — undo last commit, keep changes staged
- `git reset --mixed HEAD~1` — undo last commit, keep changes unstaged (default)
- `git reset --hard HEAD~1` — undo last commit AND discard all changes (destructive!)

**Already pushed to remote:**
- `git revert <commit-hash>` — creates a NEW commit that undoes the specified commit. This is safe for shared branches because it doesn't rewrite history.

**Rule: Use `revert` for pushed commits, `reset` for local-only commits.**

---

## 13. What is `git cherry-pick`?

**Answer:**
`git cherry-pick` applies a **specific commit** from one branch to another — without merging the entire branch. It copies that single commit.

Use case: I fixed a critical bug on my feature branch, and I need that fix on `main` right now without merging the entire feature.

```bash
git checkout main
git cherry-pick abc1234    # Apply commit abc1234 to main
```

I use it sparingly because it duplicates commits (creates a new commit with different hash). It's great for hotfixes but shouldn't be a regular workflow.

---

## 14. What is the difference between `git reset` and `git revert`?

**Answer:**
- **`git reset`** — moves the branch pointer backward, effectively **erasing commits** from history. It's destructive and should ONLY be used on local, unpushed commits. Three modes: `--soft` (keeps staged), `--mixed` (keeps unstaged), `--hard` (deletes everything).

- **`git revert`** — creates a **new commit** that undoes the changes of a specific commit. History is preserved — you can see both the original commit and the revert. Safe for shared/pushed branches.

Think of it this way: `reset` is like going back in time and pretending something never happened. `revert` is like saying "I made a mistake, here's the correction" — the mistake and correction are both in the history.

---

## 15. What is a Git tag? When do you use it?

**Answer:**
A tag is a **permanent label** pointing to a specific commit. Unlike branches, tags don't move — they always point to the same commit.

We use tags for **releases**: `v1.0.0`, `v2.1.0`, etc.

```bash
git tag v1.0.0                          # Lightweight tag
git tag -a v1.0.0 -m "Release 1.0.0"   # Annotated tag (preferred — includes metadata)
git push origin v1.0.0                   # Push tag to remote
git push origin --tags                   # Push all tags
```

Annotated tags store the tagger's name, email, date, and a message — I always use annotated tags for releases. Lightweight tags are just pointers.

---

## 16. Explain Git Flow or the branching strategy you follow.

**Answer:**
I follow **GitHub Flow**, which is simpler than Git Flow:

1. **`main`** branch is always deployable.
2. For any new work, create a **feature branch** off `main`: `feature/user-authentication`.
3. Commit to the feature branch frequently.
4. Push and open a **Pull Request**.
5. Team reviews the PR. CI/CD runs automated tests.
6. Once approved and tests pass, merge to `main`.
7. Deploy from `main`.
8. Delete the feature branch.

For larger teams or projects with scheduled releases, **Git Flow** adds more branches: `develop`, `release/*`, `hotfix/*`. But for most modern projects with continuous deployment, GitHub Flow is sufficient.

---

## 17. How do you write good commit messages?

**Answer:**
I follow a conventional format:

```
<type>: <short summary in imperative mood>

<optional body explaining WHY, not WHAT>
```

Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `style`

Good: `feat: add password reset functionality`
Bad: `updated stuff` or `fix` or `asdfgh`

Rules I follow:
- Use **imperative mood**: "add feature" not "added feature"
- Keep the subject line under 50 characters
- Capitalize the first word
- Don't end with a period
- The body explains WHY the change was made, not what changed (the diff shows what)

Good commit messages make `git log` useful and make debugging with `git bisect` possible.

---

## 18. What is `git bisect`?

**Answer:**
`git bisect` helps me find which specific commit **introduced a bug** using binary search. Instead of checking every commit one by one, it cuts the search in half each time.

```bash
git bisect start
git bisect bad          # Current commit has the bug
git bisect good v1.0.0  # This version was working fine
# Git checks out a middle commit — I test it
git bisect good         # If this commit is fine
# or
git bisect bad          # If this commit has the bug
# Repeat until Git identifies the exact commit
git bisect reset        # Go back to original state
```

It's incredibly efficient — for 1000 commits, it takes only about 10 steps. I don't use it often, but when I need it (tracking down a regression), it's invaluable.

---

## 19. What is `git reflog`? When would you use it?

**Answer:**
`git reflog` is a **safety net** — it records every change to the HEAD pointer, including commits, resets, checkouts, and rebases. Even if I do a `git reset --hard` and seemingly lose commits, they're still in the reflog for about 30 days.

Use case: I accidentally ran `git reset --hard HEAD~5` and lost 5 commits. To recover:
```bash
git reflog              # Find the SHA of the lost commit
git checkout abc1234    # Go to that commit
git branch recovery     # Create a branch to save it
```

It's basically Git's "undo history" — it tracks everything, even things that don't show up in `git log`.

---

## 20. You accidentally committed a secret (API key) to Git. How do you handle it?

**Answer:**
This is a serious situation. Simply deleting the file and committing won't help — the secret is still in the Git history.

Steps:
1. **Immediately rotate the secret** — generate a new API key and revoke the old one. This is the most important step because the key might already be compromised.

2. **Remove from history**: Use `git filter-branch` or the newer `git filter-repo` to rewrite history and remove the file from ALL commits:
   ```bash
   git filter-repo --path secrets.env --invert-paths
   ```

3. **Force push** to overwrite the remote: `git push --force`

4. **Add to `.gitignore`** so it doesn't happen again.

5. **Use environment variables** or a secrets manager (like AWS Secrets Manager or HashiCorp Vault) instead of committing secrets.

Prevention: set up pre-commit hooks with tools like `git-secrets` or `trufflehog` that scan for credentials before allowing a commit.
