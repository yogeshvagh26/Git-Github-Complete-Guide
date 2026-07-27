# Phase 3: Working with Branches

Welcome to Phase 3—the heart of Git's power! Branching allows you to diverge from the main line of development and work independently without disrupting the stable code. This lesson covers everything from the conceptual model to resolving merge conflicts and adopting industry-standard strategies.

Since this Phase covers multiple interconnected topics, we will apply the **Teaching Format** to each concept individually, followed by a consolidated Quiz, Interview Questions, Assignment, and Summary at the end.

---

## 1. Understanding Branches

### Concept Explanation
A **branch** in Git is simply a lightweight, movable pointer to a specific commit. The default branch is usually named `main` (or `master`). When you create a new branch, you are creating a new pointer that allows you to diverge from the main history. Branches enable **parallel development**—you can work on a new feature, a bug fix, or an experiment, all while the main branch remains stable. Git's branching model is incredibly efficient because it doesn't copy files; it just creates a new pointer (41 bytes).

### Real-World Example
Think of a **tree trunk** (the `main` branch). You want to grow a new branch to bear fruit (a new feature) without risking the health of the main trunk. You can grow a side branch (feature branch), let it mature, and if the fruit is good, you graft it back onto the trunk (merge). If the fruit is bad, you simply cut the branch off—the trunk remains untouched.

### Git Command Syntax (Conceptual)
(No specific command to "understand," but we view branches with:)
```bash
git branch          # List local branches
git branch -r       # List remote branches
git branch -a       # List all branches (local + remote)
```

### Multiple Examples
- **Example 1 (Feature Branch):** You are working on a "dark mode" feature. You create `feature/dark-mode` from `main`. The `main` branch continues to receive bug fixes for the live site.
- **Example 2 (Hotfix):** A critical bug is found in production. You create `hotfix/login-error` directly from `main`, fix it, and merge it back immediately, without waiting for other unfinished features.
- **Example 3 (Experiment):** You want to try a new architecture. You create `experiment/graphql` to test it. If it fails, you delete the branch—no impact on `main`.

### Visual Table Illustration (Branch as Pointers)
| Branch | Points To (Commit Hash) | Status |
| :--- | :--- | :--- |
| `main` | `a1b2c3d` (Stable v1.0) | Production-ready. |
| `feature/login` | `e4f5g6h` (added OAuth) | In progress, 2 commits ahead of `main`. |
| `feature/payment` | `i7j8k9l` (added Stripe) | In progress, 3 commits ahead of `main`. |
| `hotfix/typo` | `m0n1o2p` (fixes typo) | 1 commit ahead, merges quickly. |

---

## 2. Creating Branches (`git branch`)

### Concept Explanation
Creating a branch is instantaneous. The `git branch` command creates a new pointer at your current commit (HEAD). It does **not** switch you to the new branch; it just creates it. Think of it as saying, "I want to start a new line of work here, but I'll stay where I am for now."

### Real-World Example
You are reading a book and decide to place a bookmark at page 100 (creating a branch). You are still on page 100 (current branch), but you now have a marker that lets you return to this exact page later.

### Git Command Syntax
```bash
# Create a branch (stays on current branch)
git branch <branch-name>

# Create a branch and switch to it immediately (common shortcut)
git checkout -b <branch-name>   # Older style
git switch -c <branch-name>     # Newer, safer style

# Create a branch from a specific commit or tag
git branch <branch-name> <commit-hash>
```

### Multiple Examples
- **Example 1:** `git branch feature/dashboard` → Creates a branch called `feature/dashboard` but keeps you on `main`.
- **Example 2:** `git switch -c feature/profile` → Creates `feature/profile` AND switches to it immediately.
- **Example 3:** `git branch hotfix-2.1 a1b2c3d` → Creates a branch named `hotfix-2.1` starting from commit `a1b2c3d` (perhaps a tagged release).

### Visual Table Illustration (Create vs Switch)
| Command | Does it create a branch? | Does it switch to it? |
| :--- | :--- | :--- |
| `git branch new-branch` | ✅ Yes | ❌ No |
| `git checkout -b new-branch` | ✅ Yes | ✅ Yes |
| `git switch -c new-branch` | ✅ Yes | ✅ Yes |
| `git switch new-branch` | ❌ No (must exist) | ✅ Yes |

---

## 3. Switching Branches (`git switch` & `git checkout`)

### Concept Explanation
Switching branches updates your **Working Directory** and **Staging Area** to reflect the state of the target branch. `HEAD` (the pointer to your current branch) moves to the new branch. 
- **`git checkout`** is the older, versatile command that does branch switching and file restoration. 
- **`git switch`** is a newer, more intuitive command introduced in Git 2.23 specifically for branch switching, reducing confusion.

### Real-World Example
Imagine you have multiple projects on your desk. Switching branches is like pushing Project A aside and pulling Project B onto your desk. Your workspace physically changes to show Project B's files.

### Git Command Syntax
```bash
# Switch to an existing branch (new style)
git switch <branch-name>

# Switch to an existing branch (old style)
git checkout <branch-name>

# Create and switch (new style)
git switch -c <new-branch-name>

# Create and switch (old style)
git checkout -b <new-branch-name>

# Switch back to the previous branch
git switch -
```

### Multiple Examples
- **Example 1:** `git switch main` → Moves your working directory to the `main` branch.
- **Example 2:** `git checkout feature/login` → Moves to `feature/login` (legacy syntax).
- **Example 3:** `git switch -` → Toggles back to whatever branch you were on before (handy for quick context switching).

### Visual Table Illustration (`checkout` vs `switch` usage)
| Operation | Using `git checkout` | Using `git switch` |
| :--- | :--- | :--- |
| Switch to existing branch | `git checkout main` | `git switch main` |
| Create + switch to new branch | `git checkout -b feat` | `git switch -c feat` |
| Restore a file to a previous state | `git checkout -- file.txt` | ❌ Not supported (use `git restore`) |
| **Best Practice** | Use for file operations. | Use for branch operations. |

---

## 4. Merging Branches (`git merge`)

### Concept Explanation
Merging integrates changes from one branch into another. Git finds the common ancestor commit (the "merge base") and applies the changes from the source branch into the target branch. There are two main types:
- **Fast-forward merge:** The target branch has no new commits; Git just moves the pointer forward.
- **Three-way merge:** Both branches have diverged; Git creates a new "merge commit" that combines the histories.

### Real-World Example
You and a colleague each have a copy of a document. Your colleague edits their copy (feature branch), and you edit yours (main). The merge is like comparing both copies, finding the common original, and combining the changes into a new final document (merge commit).

### Git Command Syntax
```bash
# Merge a branch into your current branch
git merge <branch-name>

# Merge without fast-forward (forces a merge commit)
git merge --no-ff <branch-name>

# Abort a merge in progress (if conflicts occur)
git merge --abort
```

### Multiple Examples
- **Example 1 (Fast-forward):** `git switch main` → `git merge feature/typo` → Since `main` hasn't moved, Git just moves `main` forward to the tip of `feature/typo`.
- **Example 2 (Three-way):** `git switch main` → `git merge feature/dashboard` → Both have diverged, Git creates a new merge commit with a default message like "Merge branch 'feature/dashboard'".
- **Example 3 (Merge with no-ff):** `git merge --no-ff feature/analytics` → Forces a merge commit even if fast-forward is possible, preserving the branch history.

### Visual Table Illustration (Merge Types)
| Scenario | Target Branch Status | Source Branch Status | Merge Type | Result |
| :--- | :--- | :--- | :--- | :--- |
| Linear history | `main` hasn't moved. | `feature` has new commits. | Fast-forward | `main` pointer moves to `feature`'s tip. |
| Diverged history | `main` has new commits. | `feature` has new commits. | Three-way | New merge commit is created. |
| Conflicting | Both changed the same lines. | Both changed the same lines. | Conflict | Merge pauses; user must resolve. |

---

## 5. Handling Merge Conflicts

### Concept Explanation
A **merge conflict** occurs when two branches have modified the **same lines** of the **same file** in different ways. Git does not know which change to keep. It pauses the merge and marks the conflicting areas in the file. It is your responsibility to resolve the conflict, stage the resolved files, and complete the merge commit.

### Real-World Example
Two editors simultaneously correct the same sentence in a manuscript. Editor A changes "the cat sat" to "the dog sat". Editor B changes "the cat sat" to "the cat slept". The publisher (Git) cannot decide which is correct and sends the manuscript back to you with both options highlighted, asking you to choose or rewrite.

### Git Command Syntax
```bash
# When a conflict occurs, view the status
git status

# Conflict markers in the file:
# <<<<<<< HEAD     (your current branch's version)
# (code from your branch)
# =======
# (code from the incoming branch)
# >>>>>>> feature  (incoming branch's version)

# After manually editing the file:
git add <resolved-file>
git commit -m "Resolve merge conflict"

# Abort the merge entirely
git merge --abort

# Use a tool to resolve (e.g., VS Code, KDiff3)
git mergetool
```

### Multiple Examples
- **Example 1 (Manual):** You open `index.html`, see the conflict markers, delete the lines you don't want, keep the good ones, save, then `git add index.html` and `git commit`.
- **Example 2 (Using mergetool):** `git mergetool` launches Visual Studio Code's 3-way merge editor, letting you choose between "Current", "Incoming", or "Both".
- **Example 3 (Aborting):** You realize the conflict is too messy. `git merge --abort` returns everything to the state before you started the merge.

### Visual Table Illustration (Conflict Resolution Options)
| Action | Git Command | Outcome |
| :--- | :--- | :--- |
| Choose my version (HEAD) | Manually delete incoming code (between `=======` and `>>>>>>>`). | Keeps your current branch changes. |
| Choose their version (incoming) | Manually delete your code (between `<<<<<<<` and `=======`). | Keeps the feature branch changes. |
| Choose both (custom) | Edit the file to combine both logically. | Creates a hybrid version. |
| Abort merge | `git merge --abort` | Cancels merge, returns to pre-merge state. |

---

## 6. Branching Strategies

### Concept Explanation
Branching strategies are high-level workflows that dictate *how* and *when* you create branches, merge them, and release code. They are essential for team collaboration, CI/CD pipelines, and release management. The most popular strategies are **Git Flow**, **GitHub Flow**, and **Trunk-Based Development**.

### Real-World Example
Think of a **restaurant kitchen**:
- **Git Flow** is like a structured kitchen with different stations (sauce, grill, pastry) and a strict head chef (release manager).
- **GitHub Flow** is like a small food truck where every team member can try a new recipe (feature branch) and serve it immediately after a quick taste-test (PR + deploy).
- **Trunk-Based Development** is like a fast-food chain where everyone works on the same grill (main branch) and must be ready to serve at any minute (continuous integration).

### Git Command Syntax (Workflow specific)
(Not specific commands, but practices):
- **Git Flow:** Uses `develop`, `feature/*`, `release/*`, `hotfix/*` branches. Merges with `--no-ff`.
- **GitHub Flow:** Uses `main` and `feature/*` branches. Merges via **Pull Requests**.
- **Trunk-Based:** Uses `main` only, with very short-lived `feature` branches (< 1 day).

### Multiple Examples
- **Example 1 (Git Flow):** `develop` is your integration branch. `feature/login` → merge into `develop` → when ready, `release/v1.0` → merge into `main` and tag it.
- **Example 2 (GitHub Flow):** `main` is always deployable. Create `feature/add-login` → Open a Pull Request on GitHub → Review → Merge into `main` → Automatically deploy to production.
- **Example 3 (Trunk-Based):** Everyone branches from `main` for tiny changes. `git checkout -b fix-typo` → fix → merge back to `main` within hours. No long-lived feature branches.

### Visual Table Illustration (Strategy Comparison)
| Strategy | Main Branches | Feature Branches | Release Process | Best For |
| :--- | :--- | :--- | :--- | :--- |
| **Git Flow** | `main`, `develop` | Long-lived, use `--no-ff` merge. | Dedicated `release/*` branches. | Large projects with scheduled releases. |
| **GitHub Flow** | `main` only | Short-lived, deleted after PR. | Deploy from `main` after PR. | SaaS, web apps, continuous delivery. |
| **Trunk-Based** | `main` only | Very short-lived (< 1 day). | Commit directly or merge quickly; use feature flags. | High-velocity teams, DevOps culture. |

---

## Comprehensive Practice Questions
1. **Q1:** You are on `main` and create a branch called `feature/test` using `git branch feature/test`. Which branch are you currently on?
2. **Q2:** What command would you use to switch to the `develop` branch while simultaneously creating it if it doesn't exist?
3. **Q3:** Your team requires a merge commit to be created even if a fast-forward is possible. Which flag would you use with `git merge`?
4. **Q4:** You are in the middle of a messy merge and decide to start over. What command do you run?
5. **Q5:** Name the three main branching strategies and their core distinguishing characteristics.

---

## Comprehensive Quiz (Multiple Choice)
1. What is a Git branch essentially?
   a) A full copy of the repository.    b) A lightweight movable pointer to a commit.    c) A tag for releases.    d) A file containing code.
2. Which command creates a new branch *and* switches to it in the modern Git syntax?
   a) `git branch -c new`    b) `git checkout -b new`    c) `git switch -c new`    d) `git move new`
3. A fast-forward merge occurs when:
   a) The target branch has diverged.    b) The target branch has no new commits since the branch was created.    c) There are merge conflicts.    d) You use the `--no-ff` flag.
4. In a merge conflict, the `=======` marker separates:
   a) The file name from the content.    b) Your changes (HEAD) from the incoming changes.    c) The commit hash from the message.    d) Staged and unstaged changes.
5. Which branching strategy is best suited for projects with strict release cycles and versioned software (e.g., mobile apps)?
   a) GitHub Flow    b) Trunk-Based Development    c) Git Flow    d) Feature-Flag Driven
6. What command safely switches you back to the branch you were previously on?
   a) `git switch --`    b) `git switch -`    c) `git checkout ..`    d) `git back`

*(Answers: 1-b, 2-c, 3-b, 4-b, 5-c, 6-b)*

---

## Interview Questions
- **Beginner:** "Explain the difference between `git branch` and `git switch`."
- **Intermediate:** "You and a teammate both edited the same function in `utils.js` on different branches. Walk me through the steps you take to resolve the merge conflict."
- **Advanced (for this phase):** "Why might a team choose `--no-ff` merges over fast-forwards? What is the trade-off in terms of commit history clarity vs. complexity?"
- **Scenario:** "Your team uses GitHub Flow. A developer creates a feature branch, works on it for 3 days, and opens a PR. What are the potential risks, and how would you mitigate them?"

---

## Assignment
**Hands-on Branching Simulation:**

1. Create a new directory `phase3-lab` and initialize a Git repository.
2. Create a `index.html` file with a `<h1>Welcome</h1>` and commit it to `main`.
3. Create and switch to a new branch called `feature/header`.
4. Edit `index.html` to change the `<h1>` to `<h1>Hello, World!</h1>` and commit this change.
5. Switch back to `main`.
6. Edit `index.html` on `main` to add a `<p>Footer</p>` and commit this change. (Now `main` and `feature/header` have diverged).
7. Merge `feature/header` into `main` using `git merge feature/header`. This should trigger a 3-way merge commit.
8. View the commit graph with `git log --oneline --graph`.
9. Create a new branch `hotfix/typo` from `main`. Change "Hello, World!" to "Hello, World!" (fix a letter) and commit.
10. Merge `hotfix/typo` into `main` (this will likely be a fast-forward).
11. Now, deliberately create a conflict: Create a new branch `feature/conflict` from `main`. Change the `<p>Footer</p>` to `<p>Copyright 2025</p>` and commit. Switch back to `main`. Change the same line to `<p>All Rights Reserved</p>` and commit.
12. Merge `feature/conflict` into `main` and observe the conflict.
13. Resolve the conflict by editing the file to say `<p>Copyright 2025 All Rights Reserved</p>`, stage the file, and commit.
14. Push all branches to a new GitHub repository and verify the network graph online.
15. Finally, delete both feature branches locally and remotely (if pushed).

---

## Summary
- **Branches** are lightweight pointers enabling isolated, parallel development.
- **`git branch`** creates a pointer; **`git switch`** (or `git checkout`) moves your workspace to that pointer.
- **Merging** integrates branches. **Fast-forward** moves the pointer; **three-way** creates a merge commit.
- **Merge conflicts** occur when the same lines are changed differently. Resolve them manually by editing markers (`<<<<<<<`, `=======`, `>>>>>>>`), then `git add` and `git commit`.
- **Branching strategies** define the rules of engagement:
  - **Git Flow:** Heavyweight, multiple long-lived branches (ideal for scheduled releases).
  - **GitHub Flow:** Lightweight, continuous deployment via Pull Requests.
  - **Trunk-Based:** Extremely fast, short-lived branches, continuous integration.
- Effective branching is the hallmark of a mature development team. Mastery of these concepts makes collaboration seamless and production deployments safe.


---


<br/><br/><br/>
<center> <b>Happy Learning! 😊</b> </center>