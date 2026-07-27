# Phase 7: Advanced Git & GitHub

Welcome to Phase 7—the pinnacle of your Git journey! This phase covers the tools that give you surgical precision over your commit history, advanced collaboration techniques, strategies for massive repositories, and the automation power of GitHub Actions. These are the skills that distinguish Senior Developers from Juniors.


---

## Topic 1: Rebasing (`git rebase`)

### Concept Explanation
**Rebasing** is the process of moving or combining a sequence of commits to a new base commit. It rewrites history by taking the commits from your branch and *replaying* them onto the tip of another branch (e.g., `main`). The result is a **linear, clean history** without the "merge commits" that clutter the graph. 
- **Rebase vs Merge:** `git merge` creates a merge commit and preserves the true timeline (branching structure). `git rebase` rewrites your branch's commits to appear as if they were created *after* the latest `main` commits, resulting in a straight line.

### Real-World Example
You are writing a report (feature branch) based on an outdated draft (old `main`). While you write, your colleague updates the master draft (new commits on `main`). Instead of awkwardly stapling the two drafts together and adding a sticky note ("merge commit"), you **rebase** your report: you take your pages, cut them out, and re-type them onto the latest master draft as if you wrote them just now. The timeline looks perfectly sequential.

### Git Command Syntax
```bash
# Rebase current branch onto another branch
git rebase <base-branch>          # e.g., git rebase main

# Rebase a specific branch onto another
git rebase <base> <topic>         # e.g., git rebase main feature/branch

# Abort a rebase that has conflicts
git rebase --abort

# Continue a rebase after resolving conflicts
git add <resolved-file>
git rebase --continue

# Skip the current commit during rebase
git rebase --skip

# Standard rebase workflow:
git checkout feature/login
git rebase main
# (resolve conflicts if any)
git push --force-with-lease origin feature/login
```

### Multiple Examples
- **Example 1 (Basic Rebase):** `git checkout feature/payment` → `git rebase main` → Moves all commits from `feature/payment` onto the tip of `main`.
- **Example 2 (Rebase with Conflict):** Rebase hits a conflict. Git pauses. You edit the file, `git add .`, then `git rebase --continue`. Repeat until all commits are replayed.
- **Example 3 (Rebase onto a different branch):** `git rebase develop` → Rebases your current branch onto the `develop` branch instead of `main`.
- **Example 4 (Golden Rule):** `git rebase main` while on a **local branch that no one else is using**. This is safe. **Never** rebase a shared branch (e.g., `main` itself).

### Visual Table Illustration (Rebase vs Merge)
| Aspect | `git merge` | `git rebase` |
| :--- | :--- | :--- |
| **History** | Preserves the exact timeline (includes merge commit). | Rewrites history to appear linear (no merge commit). |
| **Commit Hashes** | Your commits keep their original hashes. | Your commits get **new hashes** (rewritten). |
| **Safety** | Safe for shared branches. | Dangerous for shared branches (rewrites history). |
| **Readability** | Shows "when" and "where" branches diverged. | Shows a clean, straight line of changes. |
| **Use Case** | Public branches, teams. | Private branches, cleanup before PR. |

### Practice Questions
- **Q1:** You are on the `feature/header` branch and want to apply the latest `main` updates to it. What command do you run?
- **Q2:** Why is it dangerous to rebase a branch that other developers are already working on?

### Quiz
1. Which command replays your current branch's commits onto the tip of `main`? a) `git merge main` b) `git rebase main` c) `git cherry-pick main` d) `git reset main` *(Answer: b)*
2. The Golden Rule of Rebase is: a) Always rebase `main`. b) Never rebase a shared branch. c) Always use `--force`. d) Rebase only in production. *(Answer: b)*

### Interview Questions
- **Beginner:** "Explain the difference between `git merge` and `git rebase`."
- **Intermediate:** "When would you choose rebase over merge, and vice versa?"

### Assignment
- Create a repo with a `main` branch (2 commits). Create a `feature` branch (2 commits). Add 1 more commit to `main`. Now, on `feature`, run `git rebase main`. Observe the graph with `git log --oneline --graph`. Note how your commits now appear *after* the new `main` commit.

### Summary
- **Rebase** rewrites history to keep it linear.
- It creates a cleaner project history but **rewrites commit hashes**.
- **Rule:** Rebase private/feature branches; **never** rebase public/shared `main` or `develop`.

---

## Topic 2: Interactive Rebase (`git rebase -i`)

### Concept Explanation
**Interactive Rebase** (`git rebase -i`) allows you to modify, reorder, combine, or delete commits while rebasing. It opens your default editor with a list of commits, where you can choose actions (commands) for each commit. This is the ultimate tool for history polishing before merging a feature branch.

**Key Actions:**
- `pick` (p): Use the commit as-is.
- `reword` (r): Change the commit message.
- `edit` (e): Stop to amend the commit (add/change files).
- `squash` (s): Merge this commit with the previous one, combining messages.
- `fixup` (f): Merge this commit with the previous one, discarding its message.
- `drop` (d): Delete the commit entirely.
- `exec` (x): Run a shell command (e.g., `npm test`).

### Real-World Example
You have made 5 messy commits on your feature branch: "fix", "typo fix", "actually works", "forgot test", "final". You want to present a professional history to the team. Interactive rebase is like editing a movie in final cut: you can reorder scenes (commits), rename them, cut the bad ones, and combine multiple scenes into one perfect highlight reel.

### Git Command Syntax
```bash
# Interactively rebase the last 3 commits
git rebase -i HEAD~3

# Interactively rebase from a specific commit (exclude the commit hash given)
git rebase -i <commit-hash>

# Core workflow:
git checkout feature/login
git rebase -i HEAD~4
# Editor opens with 4 commits listed
# Change 'pick' to 'squash'/'reword'/'edit' etc.
# Save and close
# Git executes the actions; if conflicts arise, resolve and continue.
git rebase --continue
```

### Multiple Examples
- **Example 1 (Squash last 3 commits):** `git rebase -i HEAD~3` → Change the 2nd and 3rd commits from `pick` to `squash`. Git combines them into one commit.
- **Example 2 (Reword a message):** Change `pick` to `reword` on the 2nd commit. The editor reopens for you to type a new message.
- **Example 3 (Delete a commit):** Change `pick` to `drop` or just delete the line. That commit vanishes.
- **Example 4 (Reorder):** Simply cut and paste the lines to change the order of commits.

### Visual Table Illustration (Interactive Rebase Commands)
| Command | Abbreviation | Effect | Best For |
| :--- | :--- | :--- | :--- |
| `pick` | `p` | Keep the commit unchanged. | Most commits. |
| `reword` | `r` | Change the commit message only. | Fixing typos in commit messages. |
| `edit` | `e` | Stop to allow amending the commit. | Splitting a commit or adding forgotten files. |
| `squash` | `s` | Merge into previous commit; combine messages. | Cleaning up "WIP" commits. |
| `fixup` | `f` | Merge into previous commit; discard its message. | Keeping history concise. |
| `drop` | `d` | Remove the commit entirely. | Deleting erroneous commits. |
| `exec` | `x` | Run a command during rebase. | Running tests after each commit. |

### Practice Questions
- **Q1:** You have 4 commits. You want to combine commits 2 and 3 into one. What command do you use on commit 3 during interactive rebase?
- **Q2:** You want to delete the first commit in the list during `git rebase -i HEAD~5`. What do you do?

### Quiz
1. Which interactive rebase command is used to merge a commit into the previous one while keeping its commit message? a) `fixup` b) `squash` c) `reword` d) `edit` *(Answer: b)*
2. Which command stops the rebase to allow you to change the actual code of a commit? a) `pick` b) `reword` c) `edit` d) `drop` *(Answer: c)*

### Interview Questions
- **Beginner:** "Explain how you would combine multiple WIP (Work In Progress) commits into a single clean commit before opening a PR."
- **Advanced:** "What is the difference between `squash` and `fixup`? When would you use one over the other?"

### Assignment
- Create a branch with 5 dummy commits: "Added file1", "Added file2", "WIP", "WIP2", "Final".
- Run `git rebase -i HEAD~5`.
- Squash "WIP" and "WIP2" into "Added file2". Rename "Final" to "Added file3". Drop the first dummy commit. Save and finish.
- Run `git log --oneline` and verify you now have only 3 clean commits.

### Summary
- **Interactive Rebase** is your history-editing workshop.
- `squash` and `fixup` combine commits; `reword` fixes messages; `drop` removes them.
- It is the standard final step before opening a Pull Request to showcase clean, logical work.

---

## Topic 3: Cherry-picking Commits (`git cherry-pick`)

### Concept Explanation
**Cherry-picking** takes a single commit (or a range of commits) from *anywhere* in the repository and applies its changes onto your current branch. It creates a new commit with a new hash but with the same changes. It is useful for selectively porting bug fixes, features, or specific changes without merging an entire branch.

### Real-World Example
The `main` branch of a project has a "critical security fix" in commit `abc123`. Your `feature` branch is weeks behind `main` and you don't want to merge all of `main` yet. You simply "pick" that security fix and apply it to your feature branch, like taking a ripe apple from one tree and grafting it onto another.

### Git Command Syntax
```bash
# Cherry-pick a specific commit
git cherry-pick <commit-hash>

# Cherry-pick a range of commits (excludes start, includes end)
git cherry-pick start-hash..end-hash

# Cherry-pick with edit (prompts to edit commit message)
git cherry-pick -e <hash>

# Cherry-pick without creating a commit (stages changes)
git cherry-pick -n <hash>

# Abort a cherry-pick that has conflicts
git cherry-pick --abort

# Continue after resolving conflicts
git add <resolved-file>
git cherry-pick --continue
```

### Multiple Examples
- **Example 1 (Single Bug Fix):** `git cherry-pick a1b2c3d` → Applies only commit `a1b2c3d` to your current branch.
- **Example 2 (Multiple Commits):** `git cherry-pick f4e5d6c..g7h8i9j` → Applies the range of commits.
- **Example 3 (Hotfix to multiple branches):** A security fix is committed to `main`. You cherry-pick it to `release/v1.0` and `release/v2.0` without merging entire `main`.
- **Example 4 (Conflicts):** Cherry-pick conflicts. You resolve manually, `git add .`, then `git cherry-pick --continue`.

### Visual Table Illustration (Cherry-pick vs Merge vs Rebase)
| Operation | Applies | Creates New Hash? | Use Case |
| :--- | :--- | :--- | :--- |
| **Merge** | All commits from branch. | Yes (merge commit). | Integrating full features. |
| **Rebase** | All commits from branch, replayed. | Yes (all commits get new hashes). | Reapplying branch onto a new base. |
| **Cherry-pick** | Specific selected commits. | Yes (new hash for each picked commit). | Selective backporting fixes. |

### Practice Questions
- **Q1:** You found a critical bug fix on the `develop` branch (commit `xyz789`). You are on the `main` branch and want only that fix. What command do you use?
- **Q2:** How do you abort a cherry-pick when conflicts become too messy?

### Quiz
1. What does `git cherry-pick <hash>` do? a) Deletes the commit. b) Applies the changes from that commit to your current branch. c) Merges the entire branch. d) Reverts the commit. *(Answer: b)*
2. If a cherry-pick results in conflicts, which command do you use to continue after resolving them? a) `git rebase --continue` b) `git merge --continue` c) `git cherry-pick --continue` d) `git commit --continue` *(Answer: c)*

### Interview Questions
- **Beginner:** "Explain what cherry-picking does in Git."
- **Intermediate:** "You have a release branch and a development branch. A bug is fixed on development, but you need that fix on the release branch immediately. How do you do it without merging all of development?"

### Assignment
- Create a branch `feature/A` with 3 commits. Create a new branch `feature/B` from `main`. On `feature/B`, use `git log` to find the hash of the *second* commit from `feature/A`. Cherry-pick that single commit onto `feature/B`. Verify that only that specific change is applied.

### Summary
- **Cherry-pick** selectively applies commits from elsewhere.
- It is ideal for **hotfixes** and **backporting**.
- It rewrites history (new commit hashes) and can cause conflicts like any integration.

---

## Topic 4: Squashing Commits

### Concept Explanation
**Squashing** is the process of combining multiple commits into a single commit. It is usually done via interactive rebase (`squash` or `fixup`). The goal is to clean up a messy, incremental commit history (e.g., "WIP", "temp", "typo fix") into a logical, self-contained unit of work.

### Real-World Example
You are building a bookshelf. You have commits: "Cut wood", "Drilled holes", "Sanded", "Painted", "Fixed paint spot". Instead of 5 messy steps in the log, you squash them into one commit: "Assembled bookshelf" – which is how a professional wants to see the final result.

### Git Command Syntax
```bash
# Squash via interactive rebase (most common)
git rebase -i HEAD~n

# In the editor, change 'pick' to 'squash' for commits you want to merge into the previous one.
# Save and close. Git combines them and prompts for a new commit message.

# Squash the last 2 commits (without interactive prompt - risky, but possible via reset)
git reset --soft HEAD~2
git commit -m "Combined commit message"

# Squash all commits into one (for a branch)
git reset --soft <first-commit-hash>
git commit -m "Squashed branch history"
```

### Multiple Examples
- **Example 1 (Interactive Squash):** `git rebase -i HEAD~4` → Change commits 2,3,4 to `squash`. Git asks for a new message. Final result: 1 commit.
- **Example 2 (Fixup):** `git rebase -i HEAD~3` → Change commit 3 to `fixup`. Git discards the "WIP" message and merges changes into the previous commit.
- **Example 3 (Squash after PR feedback):** You implement code review feedback in a new commit. Then `git rebase -i HEAD~2` and `squash` the feedback commit into the original feature commit.

### Visual Table Illustration (Squash vs Fixup)
| Aspect | `squash` | `fixup` |
| :--- | :--- | :--- |
| **Effect** | Merges commit into the previous one. | Merges commit into the previous one. |
| **Commit Message** | Prompts to edit combined message. | Discards the squashed commit's message. |
| **Use Case** | When both messages matter (e.g., "feature + tests"). | For trivial fixups ("typo", "temp"). |

### Practice Questions
- **Q1:** You have 3 commits. You want to combine all 3 into a single commit. What steps do you take?
- **Q2:** What is the difference between `squash` and `fixup` in interactive rebase?

### Quiz
1. Squashing commits is primarily used to: a) Delete commits. b) Combine multiple commits into one. c) Rename a branch. d) Create a merge conflict. *(Answer: b)*
2. Which command is typically used to squash the last 4 commits? a) `git commit --squash` b) `git rebase -i HEAD~4` c) `git merge --squash` d) `git reset --hard HEAD~4` *(Answer: b)*

### Interview Questions
- **Beginner:** "Why would you squash commits before opening a Pull Request?"
- **Intermediate:** "Describe a scenario where squashing commits would be inappropriate and why."

### Assignment
- Create a new branch with 5 commits with messages like "WIP 1", "WIP 2", "WIP 3", "Actually final", "Last fix".
- Use `git rebase -i HEAD~5` to squash all of them into a single commit with a meaningful message like "Add login feature". Verify with `git log`.

### Summary
- **Squashing** combines multiple commits into one.
- It is a history-cleanup tool for PRs, *not* a substitute for proper commit hygiene.
- Use `squash` for combining, `fixup` to discard messages.

---

## Topic 5: Advanced Merge Strategies

### Concept Explanation
By default, `git merge` uses a 3-way merge when branches have diverged. But sometimes you need more control:
- **`--no-ff` (No Fast-Forward):** Forces a merge commit even when fast-forward is possible. Preserves the fact that a branch existed.
- **`--ff-only` (Fast-Forward Only):** Only allows a merge if it can be fast-forwarded. Rejects the merge if branches have diverged (useful for keeping linear history in CI pipelines).
- **`--squash`:** Merges the changes from the source branch but *does not create a merge commit*. It stages the combined changes, allowing you to commit them as a single, custom commit. This effectively squashes all changes into one commit.
- **`--strategy-option` / `-X`:** Pass options like `theirs` or `ours` to automatically resolve conflicts favoring one side.

### Real-World Example
- **`--no-ff`:** You want a "sticky note" on your project timeline saying, "Here's where the feature branch was merged."
- **`--squash`:** You want to take all the messy commits from a feature branch and present them as a single clean feature implementation.
- **`-X theirs`:** You are merging a branch and know for certain that the incoming branch's version is always correct (e.g., generated files), so you auto-accept it.

### Git Command Syntax
```bash
# Force a merge commit (preserve branch history)
git merge --no-ff feature/branch

# Only merge if fast-forward is possible (reject otherwise)
git merge --ff-only feature/branch

# Squash merge: stage changes but don't create merge commit
git merge --squash feature/branch
git commit -m "Add feature X as a single commit"

# Automatically resolve conflicts: favor the branch being merged in (theirs) or current branch (ours)
git merge -X theirs feature/branch
git merge -X ours feature/branch

# Recursive strategy with options (e.g., favor ours recursively)
git merge -s recursive -X ours feature/branch
```

### Multiple Examples
- **Example 1 (`--no-ff` for feature branches):** `git checkout main` → `git merge --no-ff feature/login` → Creates a merge commit even if `main` hasn't moved, clearly showing a feature was added.
- **Example 2 (`--ff-only` for CI/CD):** CI pipeline runs `git merge --ff-only develop`. If `develop` has diverged, it fails, forcing the developer to rebase first.
- **Example 3 (`--squash` for messy branches):** `git merge --squash feature/experiment` → Stages all changes from the experiment branch. You then `git commit -m "Add experimental feature"` without carrying over 15 garbage commits.
- **Example 4 (`-X theirs`):** You are merging a branch that updates a large autogenerated `package-lock.json`. You run `git merge -X theirs feature/deps` to automatically accept all dependency changes.

### Visual Table Illustration (Merge Strategies)
| Strategy | Command | Result | Use Case |
| :--- | :--- | :--- | :--- |
| **No Fast-Forward** | `git merge --no-ff` | Always creates a merge commit. | Feature branches, preserving branch topology. |
| **Fast-Forward Only** | `git merge --ff-only` | Rejects if divergence exists. | CI/CD safety; ensures linearity. |
| **Squash Merge** | `git merge --squash` | Stages combined changes (no merge commit). | Cleaning up WIP branches. |
| **Conflict Resolution (ours/theirs)** | `git merge -X theirs` | Auto-resolves conflicts in favor of one side. | Autogenerated files, or deterministic code. |

### Practice Questions
- **Q1:** Your team mandates that every feature branch merge must create a merge commit. Which flag do you use?
- **Q2:** You want to merge a branch but avoid bringing in its 20 individual "WIP" commits. What strategy do you use?

### Quiz
1. Which merge strategy prevents a merge if the target branch has not moved, effectively enforcing a linear history? a) `--no-ff` b) `--ff-only` c) `--squash` d) `-X ours` *(Answer: b)*
2. `git merge --squash feature/branch` does NOT create a merge commit. What does it do instead? a) Deletes the feature branch. b) Stages the changes in the working directory. c) Immediately commits them. d) Creates a tag. *(Answer: b)*

### Interview Questions
- **Beginner:** "What does `--no-ff` do and why might a team require it for feature branches?"
- **Advanced:** "Explain a scenario where you would use `-X theirs` and what the risks are."

### Assignment
- Create a `main` branch with 1 commit. Create a `feature` branch with 2 commits.
- Merge `feature` into `main` using `git merge --no-ff feature`. Observe the merge commit.
- Create a new branch `feature2` with 2 commits.
- Merge it using `git merge --squash feature2`. Note that no merge commit is created; instead, you get staged changes. Commit them yourself.
- Run `git log --oneline --graph` and compare the two merge results.

### Summary
- **`--no-ff`** preserves branch history in the graph.
- **`--ff-only`** ensures linearity.
- **`--squash`** collapses multiple commits into one staged change.
- **`-X`** strategies help auto-resolve conflicts in deterministic situations.

---

## Topic 6: Managing Large Projects

### Concept Explanation
As repositories grow (large binary files, enormous history, many branches), Git's performance can degrade. Strategies include:
- **Shallow Clones** (`--depth`): Clone only the most recent N commits to save time and disk space.
- **`git filter-repo` / BFG Repo-Cleaner**: Remove large files or sensitive data from history permanently.
- **Git LFS (Large File Storage)**: Replaces large files (e.g., `.psd`, `.zip`, `.mp4`) with text pointers, storing the actual file on a separate server. This keeps the repo lightweight.
- **Partial Clones** (`--filter=blob:none`): Clone only the commit history, downloading blobs on demand.
- **Sparse Checkout** (`git sparse-checkout`): Checkout only a subset of files/directories from the repo.

### Real-World Example
You are maintaining a game development repository. It has 10 GB of 3D assets (models, textures). Pushing/pulling is painfully slow. Git LFS acts like a "download on demand" library: you have a tiny text pointer in Git, but when you need the actual asset, you download it from the LFS server. A newcomer can clone the code in seconds without downloading the 10 GB of assets.

### Git Command Syntax
```bash
# Shallow Clone (only last 10 commits)
git clone --depth 10 <url>

# Partial Clone (clone commits, but not file contents)
git clone --filter=blob:none <url>

# Sparse Checkout (clone only a specific folder)
git clone --depth 1 --filter=blob:none --sparse <url>
cd repo
git sparse-checkout set src/backend

# Install and track a large file with Git LFS
brew install git-lfs (or appropriate package)
git lfs install
git lfs track "*.psd"
git add .gitattributes
git add file.psd
git commit -m "Add PSD asset with LFS"
git push origin main

# Permanently remove a large file from history (Git filter-repo - external tool)
pip install git-filter-repo
git filter-repo --path bigfile.zip --invert-paths
```

### Multiple Examples
- **Example 1 (CI/CD):** CI pipeline does `git clone --depth 50` to save time. It only needs the recent history to run tests.
- **Example 2 (Monorepo Sparse Checkout):** You work only on `packages/frontend`. `git sparse-checkout set packages/frontend` ensures only that folder is checked out, saving disk space.
- **Example 3 (LFS Setup):** `git lfs track "*.mp4"` → Adds a tracking rule. Now `.mp4` files are stored on the LFS server.
- **Example 4 (Cleaning History):** Accidentally committed a 500MB video. `git filter-repo --path video.mp4 --invert-paths` removes it from all commits.

### Visual Table Illustration (Large Repo Tools)
| Tool/Technique | Best For | Command Example |
| :--- | :--- | :--- |
| **Shallow Clone** | CI/CD, quick builds. | `git clone --depth 1` |
| **Partial Clone** | Reducing initial clone size while keeping full history. | `git clone --filter=blob:none` |
| **Sparse Checkout** | Monorepos; focusing on a subset of files. | `git sparse-checkout set dir/` |
| **Git LFS** | Binary assets, large files (`.psd`, `.zip`). | `git lfs track "*.zip"` |
| **`git filter-repo`** | Removing sensitive/ huge files from history. | `git filter-repo --path secret.env` |

### Practice Questions
- **Q1:** You are setting up a CI pipeline and only need the latest code to build. What type of clone do you use?
- **Q2:** Your team works in a monorepo with 50 microservices. You only work on the `payment-service`. How do you avoid cloning the entire monorepo's files?

### Quiz
1. Which command clones only the most recent commit to save time and bandwidth? a) `git clone --shallow` b) `git clone --depth 1` c) `git clone --partial` d) `git clone --thin` *(Answer: b)*
2. Which tool is designed to handle large binary files by storing them externally with text pointers in Git? a) `git filter-repo` b) `git lfs` c) `git gc` d) `git prune` *(Answer: b)*

### Interview Questions
- **Beginner:** "How would you handle a Git repository that has become extremely slow due to large binary files?"
- **Advanced:** "Explain the difference between a shallow clone, a partial clone, and sparse checkout, and when you would use each."

### Assignment
- (Simulate large file management) Initialize a repo. Install Git LFS. Track a dummy file extension (`*.big`). Create a dummy `.big` file, add, commit, and push it (or simulate). Observe the `.gitattributes` file created.
- (Sparse Checkout) Clone a large public monorepo (e.g., `vuejs/core` or a similar large repo) with `--sparse` and checkout only the `src` folder.

### Summary
- **Large repos** require specialized strategies.
- **Shallow clones** save time for CI.
- **Sparse checkout** and **partial clones** save disk space.
- **Git LFS** is essential for binary assets.
- **`filter-repo`** cleans history permanently.

---

## Topic 7: GitHub Actions and CI/CD Basics

### Concept Explanation
**GitHub Actions** is a built-in CI/CD (Continuous Integration / Continuous Deployment) platform that automates workflows triggered by GitHub events (push, PR, issue, schedule). You define workflows in YAML files (`.github/workflows/*.yml`). These workflows run on GitHub's hosted runners (or self-hosted) and can:
- **Run tests** on every push.
- **Build artifacts** (e.g., Docker images, binaries).
- **Lint code** (check style).
- **Deploy** to cloud providers (AWS, Azure, Vercel).
- **Automate** release processes (create tags, publish packages).

### Real-World Example
You have a team of 10 developers. Every time someone pushes code, a robot (GitHub Actions) immediately checks out the code, installs dependencies, runs 500 unit tests, builds the app, and deploys it to a staging server. If tests fail, the robot posts a red "X" on the Pull Request. This robot never sleeps, is incredibly fast, and ensures no broken code ever reaches production.

### Git Command Syntax (YAML Workflow)
(Not Git commands, but the syntax for the workflow file).
```yaml
# .github/workflows/ci.yml
name: CI Pipeline
on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      # Step 1: Checkout the code
      - uses: actions/checkout@v4

      # Step 2: Setup Node.js
      - uses: actions/setup-node@v4
        with:
          node-version: '18'

      # Step 3: Install dependencies
      - run: npm install

      # Step 4: Run tests
      - run: npm test

      # Step 5: Build the app
      - run: npm run build

      # Step 6: Upload artifact (optional)
      - uses: actions/upload-artifact@v4
        with:
          name: build-output
          path: dist/
```

### Multiple Examples
- **Example 1 (PR Validation):** Workflow runs `eslint` and `jest` on every pull request. If any fail, the PR cannot be merged (combined with branch protection).
- **Example 2 (Automated Deployment):** On push to `main`, workflow builds the app and uses `actions/aws-cli` to deploy to S3.
- **Example 3 (Scheduled Jobs):** Workflow runs daily at 2 AM (`on: schedule: - cron: '0 2 * * *'`) to generate a nightly report.
- **Example 4 (Matrix Builds):** Test your code on multiple versions of Node.js, Python, or OS simultaneously (parallel jobs).

### Visual Table Illustration (GitHub Actions Components)
| Component | Description | Example |
| :--- | :--- | :--- |
| **Event** | What triggers the workflow. | `push`, `pull_request`, `schedule`, `issue` |
| **Runner** | The environment the job runs on. | `ubuntu-latest`, `windows-latest`, `macos-latest` |
| **Job** | A sequence of steps that run on the same runner. | `build`, `test`, `deploy` |
| **Step** | An individual task (can run commands or use actions). | `- run: npm test` |
| **Action** | A reusable piece of code (like a plugin). | `actions/checkout@v4`, `actions/setup-node@v4` |
| **Matrix** | A strategy to run jobs with multiple versions. | `node-version: [14, 16, 18]` |

### Practice Questions
- **Q1:** You want to run automated tests only when a Pull Request is opened against `main`. What event do you specify in the workflow?
- **Q2:** What is the purpose of the `actions/checkout@v4` step in a workflow?

### Quiz
1. GitHub Actions workflow files are stored in which directory? a) `/.git/` b) `/.github/workflows/` c) `/actions/` d) `/ci-cd/` *(Answer: b)*
2. Which of the following is NOT a valid event trigger for a GitHub Action? a) `push` b) `pull_request` c) `build` d) `schedule` *(Answer: c)*

### Interview Questions
- **Beginner:** "What is GitHub Actions and why is it useful?"
- **Intermediate:** "Describe a complete CI/CD pipeline you would set up for a web application using GitHub Actions."

### Assignment
- In your practice repository, create a `.github/workflows/ci.yml` file.
- Write a workflow that runs on `push` to `main`.
- The job should:
  1. Checkout the code.
  2. Setup Python (or Node.js).
  3. Print a message "Hello from GitHub Actions!" (or run a simple linter).
- Push this file to `main` and go to the "Actions" tab of your GitHub repository to watch the workflow run.
- (Optional) Add a step that fails intentionally (e.g., `- run: exit 1`) and see how it reports a failure.

### Summary
- **GitHub Actions** automate software workflows directly in your repository.
- Workflows are defined in **YAML** files in `.github/workflows/`.
- They run on **events** like pushes, PRs, or schedules.
- You can **test**, **build**, and **deploy** automatically.
- **Matrix builds** allow testing on multiple environments in parallel.

---

## Comprehensive Practice Questions (All Topics)
1. What is the difference between `git rebase` and `git merge`? When would you use each?
2. List the interactive rebase commands and describe what each does.
3. Explain how you would cherry-pick a commit from one branch to another.
4. Your feature branch has 10 messy commits. How do you squash them into 3 logical commits before merging?
5. Your repository is 5 GB due to large video files. What two approaches could you take to manage this?
6. What is the purpose of `--no-ff` in a merge?
7. Write a simple GitHub Actions workflow that runs `npm test` on every push.

---

## Comprehensive Quiz (Multiple Choice)
1. Which command rewrites your current branch's commits to make them appear as if they were created after the latest `main` commits? a) `git merge main` b) `git rebase main` c) `git cherry-pick main` d) `git reset main` *(Answer: b)*
2. In interactive rebase, which command merges a commit into the previous one and discards its message? a) `squash` b) `fixup` c) `reword` d) `edit` *(Answer: b)*
3. Which operation applies a specific commit from another branch without merging the entire branch? a) `git rebase` b) `git merge` c) `git cherry-pick` d) `git fetch` *(Answer: c)*
4. Which merge flag forces a merge commit even if fast-forward is possible? a) `--ff-only` b) `--no-ff` c) `--squash` d) `-X theirs` *(Answer: b)*
5. Which tool is specifically designed to handle large binary files in Git without bloating the repository? a) `git filter-repo` b) `git lfs` c) `git sparse-checkout` d) `git clone --depth` *(Answer: b)*
6. GitHub Actions workflow files are written in which language? a) JSON b) YAML c) XML d) Python *(Answer: b)*
7. Which interactive rebase command allows you to change the commit message of a commit without changing its content? a) `edit` b) `reword` c) `squash` d) `drop` *(Answer: b)*
8. You want to clone a repository but only download the files for the `src/` directory to save space. Which feature do you use? a) `git clone --shallow` b) `git sparse-checkout` c) `git lfs` d) `git filter-repo` *(Answer: b)*

---

## Interview Questions
- **Beginner:** "You are reviewing a PR that has 20 commits with messages like 'wip', 'temp', 'fix'. How would you ask the contributor to clean up the history?"
- **Intermediate:** "Your team uses a rebase-based workflow. A developer rebased their feature branch onto `main` and force-pushed to the remote feature branch. Another developer had also pushed a commit to that remote branch. What is the problem and how do you fix it?"
- **Advanced:** "Explain the difference between `git merge --squash` and `git rebase -i` with squashing. When would you use one over the other for cleaning up a feature branch before merging?"
- **Scenario:** "A developer accidentally committed a 2GB database dump to `main` and pushed it. The repository is now extremely slow. Walk me through the complete process of removing it from history and preventing it from happening again."

---

## Comprehensive Assignment (Advanced Mastery)
**Objective:** Demonstrate full proficiency in advanced Git and GitHub operations.

1. **Rebase Practice:** Create a repo with a `main` (3 commits) and a `feature` (3 commits). Add 1 commit to `main`. Rebase `feature` onto `main`. Resolve any conflicts (create them intentionally).

2. **Interactive Rebase & Squash:** On the `feature` branch, use interactive rebase to squash all 3 feature commits into 1 commit with a clean message. Also, reword the message of the first `main` commit (just for practice—but note you are only rebasing `feature`, so this might not apply; adjust the exercise by rebasing from an older commit if needed).

3. **Cherry-pick:** Create a new `hotfix` branch from `main`. Add 1 commit. Switch back to `main`. Cherry-pick that hotfix commit into `main`.

4. **Merge Strategies:** Create a `release` branch. Merge your `feature` branch into `release` using `--no-ff`. Then merge `release` into `main` using `--squash` (simulating a release candidate with a clean commit).

5. **Large Project Simulation:** Install Git LFS (if possible). Track `*.iso` files. Create a dummy `.iso` file and add/commit/push it. Check the `.gitattributes` file.

6. **GitHub Actions:** Add a workflow that runs on `push` to `main` and does the following:
   - Checks out the code.
   - Prints "Build is successful" (or runs a dummy test).
   - Uploads a dummy artifact (create a `build.txt` file and upload it).
   - Push the workflow file and verify it runs on the Actions tab.

7. **Cleanup:** Delete all local branches except `main` and `develop`.

8. **Reflection:** Write a 200-word reflection on which tool (rebase, cherry-pick, stash, squash, LFS, Actions) you found most valuable and why.

---

## Phase 7 Summary
- **Rebasing** rewrites history for a clean, linear timeline. Use it on private branches; `git merge` on public ones.
- **Interactive Rebase** (`-i`) is a surgical workshop: `squash`, `fixup`, `reword`, `edit`, and `drop` give you perfect control over your history.
- **Cherry-picking** applies specific commits from anywhere—critical for backporting fixes.
- **Squashing** combines messy commits into polished units, essential for professional PRs.
- **Advanced Merge Strategies** (`--no-ff`, `--ff-only`, `--squash`, `-X ours/theirs`) give you nuanced control over integration.
- **Managing Large Projects** with shallow clones, sparse checkout, and Git LFS keeps your repo fast and efficient.
- **GitHub Actions** automates CI/CD, turning your repository into a powerful development engine that tests, builds, and deploys automatically.

Congratulations! You have completed the entire Git & GitHub curriculum. You now possess the knowledge of a seasoned developer, capable of handling complex history, team workflows, and automation. Go forth and code with confidence!


---


<br/><br/><br/>
<center> <b>Happy Learning! 😊</b> </center>