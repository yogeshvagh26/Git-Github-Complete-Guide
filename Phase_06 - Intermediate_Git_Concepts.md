# Phase 6: Intermediate Git Concepts

Welcome to Phase 6! You have mastered the daily workflow, branching, and GitHub collaboration. Now it is time to go deeper. This phase covers Git's internal mechanics, powerful safety nets for undoing work, stashing, tagging releases, forensic history analysis, and productivity-boosting aliases. These tools separate casual users from Git power users.

---

## Topic 1: Understanding HEAD and Git Internals

### Concept Explanation
- **HEAD**: A special pointer that represents your current position in the repository. Usually, it points to a branch reference (e.g., `refs/heads/main`), which in turn points to a commit. In a "detached HEAD" state, HEAD points directly to a specific commit.
- **Git Objects**: Git is fundamentally a key-value store. It stores four primary object types:
    1.  **Blob** (File contents) – stored as a hash of the file's content.
    2.  **Tree** (Directory structure) – lists filenames and their corresponding blob/tree hashes.
    3.  **Commit** (Snapshot) – contains the tree hash, author, committer, message, and parent commit(s).
    4.  **Tag** – points to a specific commit (we cover tags in Topic 4).
- **`.git` Directory**: The hidden folder where all this magic lives. `objects/` stores blobs/trees/commits, `refs/` stores branch pointers and tags.

### Real-World Example
Imagine a library (`.git` folder). **Blobs** are the actual physical books (content). **Trees** are the shelves and sections (directory structures). **Commits** are the library catalog cards that record *which* books were on *which* shelf at a specific date. **HEAD** is the librarian's bookmark showing exactly where they are currently walking in the aisles.

### Git Command Syntax (Inspecting Internals)
```bash
# Check what HEAD is pointing to
cat .git/HEAD

# Show the commit hash that HEAD points to
git rev-parse HEAD

# View the type of an object (blob, tree, commit)
git cat-file -t <hash>

# View the content of an object (pretty print)
git cat-file -p <hash>

# List the contents of a tree object
git ls-tree <tree-hash>
```

### Multiple Examples
- **Example 1 (Attached HEAD):** `cat .git/HEAD` outputs `ref: refs/heads/main`. You are on the `main` branch.
- **Example 2 (Detached HEAD):** `git checkout <commit-hash>`. Now `cat .git/HEAD` outputs the raw hash. You are not on any branch; any commits made here are orphaned unless you create a new branch.
- **Example 3 (Exploring a commit):** `git log --oneline` → pick a hash → `git cat-file -p <hash>` → shows the tree hash, author, parent, and message.
- **Example 4 (Exploring a tree):** Take the tree hash from above and run `git ls-tree <tree-hash>` → shows the file blobs inside that snapshot.

### Visual Table Illustration (Git Object Relationships)
| Object Type | Stores | Identified By | Example Command to View |
| :--- | :--- | :--- | :--- |
| **Blob** | Raw file content (compressed). | SHA-1 hash of content. | `git cat-file -p <blob-hash>` |
| **Tree** | Directory listings (file names + object hashes). | SHA-1 hash of the directory structure. | `git ls-tree <tree-hash>` |
| **Commit** | Tree hash, parent hash, author, message. | SHA-1 hash of commit metadata. | `git show <commit-hash>` |
| **HEAD** | Current branch reference or direct commit. | Special pointer (not a content hash). | `cat .git/HEAD` |

### Practice Questions
- **Q1:** How can you tell if your HEAD is detached without using `git status`? (Hint: look at the file).
- **Q2:** What command shows you the content of the root tree object of the latest commit?

### Quiz
1. What does `git rev-parse HEAD` display? a) The current branch name. b) The commit hash that HEAD points to. c) The list of staged files. d) The Git version. *(Answer: b)*
2. Which Git object directly stores the content of a file? a) Tree b) Commit c) Blob d) Tag *(Answer: c)*

### Interview Questions
- **Beginner:** "What is HEAD in Git and what does it mean to be in a detached HEAD state?"
- **Intermediate:** "Explain the relationship between a Commit, a Tree, and a Blob in Git's internal model."

### Assignment
- Navigate to any repository. Run `git log --oneline` to find a commit hash. Run `git cat-file -p <hash>` to see the commit details. Note the `tree` hash. Now run `git ls-tree <tree-hash>`. Pick a blob hash from that output and run `git cat-file -p <blob-hash>` to see the raw file content. Document your findings.

### Summary
- Git is a content-addressable filesystem with Blobs, Trees, and Commits.
- **HEAD** is the current position pointer.
- Understanding internals helps demystify complex operations like rebase and reset.

---

## Topic 2: Undoing Changes (`git restore`, `git reset`, `git revert`)

### Concept Explanation
- **`git restore`** (Safety net for local changes): Safely undoes changes in your Working Directory or Staging Area. It is the modern replacement for `git checkout --` and `git reset` for file-level unstage operations.
- **`git reset`** (Rewind history for local branches): Moves the current branch pointer backward. Has three modes:
  - `--soft`: Only moves HEAD. Keeps staged changes and working directory untouched.
  - `--mixed` (default): Moves HEAD and unstages changes. Keeps working directory untouched.
  - `--hard`: Moves HEAD, unstages changes, AND overwrites the working directory. **Dangerous** – loses uncommitted changes.
- **`git revert`** (Safe undo for shared history): Creates a *new commit* that rolls back the changes of a previous commit. It is the only safe way to undo changes on public/shared branches because it does not rewrite history.

### Real-World Example
- **Restore**: You drew a line on a whiteboard (working dir). You erase it with a tissue (`git restore`). You also erase it from your "to-do" list before the teacher sees it (`git restore --staged`).
- **Reset**: You are editing a video timeline. You hit "undo" 3 times (`git reset HEAD~3`). `--soft` keeps the clips on the clipboard (staged); `--mixed` puts them back on the desk (unstaged); `--hard` deletes the clips entirely.
- **Revert**: A published newspaper has a typo. You cannot rewrite the printed edition (history). Instead, you print a new edition (revert commit) that explicitly retracts the previous statement.

### Git Command Syntax
```bash
# --- git restore (Focus on uncommitted changes) ---
# Discard changes in working directory for a specific file
git restore <file>

# Unstage a file (keep the local modifications)
git restore --staged <file>

# Restore a file to a specific previous commit's state
git restore --source=HEAD~1 <file>

# --- git reset (Rewind local commits) ---
# Move HEAD back 1 commit, keep changes staged (--soft)
git reset --soft HEAD~1

# Move HEAD back 1 commit, unstage changes (--mixed, default)
git reset HEAD~1

# Move HEAD back 1 commit, discard all changes (--hard) - USE WITH CAUTION!
git reset --hard HEAD~1

# --- git revert (Create a new commit that undoes a previous one) ---
# Revert the last commit (creates a new commit)
git revert HEAD

# Revert a specific commit by hash
git revert <commit-hash>
```

### Multiple Examples
- **Example 1 (I messed up the file I'm editing):** `git restore index.html` → Discards all uncommitted edits to `index.html`. Gone forever (unless your IDE has local history).
- **Example 2 (I added a secret file to staging):** `git add .env` → `git restore --staged .env` → Removes it from staging, but keeps `.env` on your disk (so you can add it to `.gitignore`).
- **Example 3 (My last 2 commits were garbage locally):** `git reset --hard HEAD~2` → Permanently deletes the last 2 commits locally. Only do this if you haven't pushed.
- **Example 4 (I pushed a buggy commit to the team):** `git revert HEAD` → Creates a new commit "Revert 'Added buggy code'". Push this new commit. The team pulls it, the bug is "undone" without rewriting history.

### Visual Table Illustration (Undo Command Comparison)
| Command | Affects Working Dir | Affects Staging | Rewrites History | Safe for Shared Branches? |
| :--- | :--- | :--- | :--- | :--- |
| `git restore <file>` | ✅ Yes (overwrites) | ❌ No | ❌ No | ✅ Yes (local only) |
| `git restore --staged <file>` | ❌ No | ✅ Yes (unstages) | ❌ No | ✅ Yes |
| `git reset --soft HEAD~1` | ❌ No | ✅ Yes (keeps staged) | ✅ Yes (moves pointer) | ❌ No |
| `git reset --mixed HEAD~1` | ❌ No | ✅ Yes (unstages) | ✅ Yes | ❌ No |
| `git reset --hard HEAD~1` | ✅ Yes (destroys) | ✅ Yes | ✅ Yes | ❌ No |
| `git revert HEAD` | ❌ No (adds new commit) | ❌ No | ❌ No (additive) | ✅ **Yes** |

### Practice Questions
- **Q1:** You staged a large log file accidentally (`git add debug.log`). How do you unstage it without deleting the file from your computer?
- **Q2:** You pushed a commit to `main` that broke the build. What is the safest way to fix this for your entire team?
- **Q3:** What is the difference between `git reset --hard` and `git revert`?

### Quiz
1. Which command is the modern replacement for `git checkout -- <file>` to discard local changes? a) `git restore` b) `git reset` c) `git revert` d) `git rm` *(Answer: a)*
2. Which `git reset` mode keeps your working directory and staging area exactly as they are, but only moves the branch pointer? a) `--hard` b) `--mixed` c) `--soft` d) `--keep` *(Answer: c)*
3. Which undo operation is considered safe for shared repositories because it doesn't rewrite history? a) `git reset --hard` b) `git commit --amend` c) `git revert` d) `git rebase` *(Answer: c)*

### Interview Questions
- **Beginner:** "You accidentally committed a secret password in a file. What do you do?"
- **Intermediate:** "Explain a scenario where you would use `git reset --hard` and a scenario where you would use `git revert`. Why is one dangerous on shared branches?"
- **Advanced:** "Can you revert a merge commit? What additional flag is required and why?"

### Assignment
- Create a test repository. Make 3 commits (`commit-1`, `commit-2`, `commit-3`).
- Practice `git reset --soft HEAD~1` and check `git status` (changes should be staged).
- Then run `git reset --mixed HEAD~1` and check `git status` (changes should be unstaged).
- Then run `git reset --hard HEAD~1` (this discards `commit-2` entirely—note the warning).
- Now, create a `bugfix` commit, push it (simulate shared). Then use `git revert HEAD` to create a new commit that undoes it. View the log to see both the original and the revert.

### Summary
- **Restore**: Local safety (unstage, discard).
- **Reset**: Rewind local history (use `--soft`/`--mixed` generally; avoid `--hard`).
- **Revert**: Safe, additive undo for shared history.
- Rule of thumb: *Never reset a branch that others have pulled.*

---

## Topic 3: Stashing Changes (`git stash`)

### Concept Explanation
Stashing temporarily shelves uncommitted changes (both tracked and untracked) so you can switch branches, pull updates, or perform other operations without committing half-done work. It operates like a **stack** (LIFO – Last In, First Out). You can stash, apply, pop, drop, and even stash specific files.

### Real-World Example
You are working on a messy report (changes in `report.docx`). Suddenly, your boss asks you to urgently fix a typo on the website's homepage. You cannot commit the messy report. You put the messy report into a filing cabinet drawer (`git stash`), clean your desk (switch to main branch), fix the typo, and then take the messy report back out of the drawer (`git stash pop`) to continue working.

### Git Command Syntax
```bash
# Save current changes (including staged, excluding untracked by default)
git stash

# Save changes with a descriptive message
git stash push -m "WIP: login refactor"

# Include untracked files in the stash
git stash -u

# List all stashes
git stash list

# Apply the latest stash (keeps it in the stash stack)
git stash apply

# Apply a specific stash
git stash apply stash@{2}

# Apply the latest stash AND remove it from the stack (pop)
git stash pop

# Drop a specific stash (delete it)
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

### Multiple Examples
- **Example 1 (Basic):** `git add .` → `git stash` → Switches branch to fix bug → `git switch main` → `git stash pop`.
- **Example 2 (Naming stashes):** `git stash push -m "frontend styling partial"` → Later, `git stash list` shows `stash@{0}: On main: frontend styling partial`.
- **Example 3 (Stashing specific files):** `git stash push -m "just config" -- config.yml` → Only stashes `config.yml`.
- **Example 4 (Popping with conflict):** You stash changes, apply them on a different branch, and Git finds conflicts. You resolve them manually, and then `git stash drop` to clean the stash (since `pop` didn't auto-drop due to conflict).

### Visual Table Illustration (Stash Operations)
| Operation | Effect on Working Dir | Effect on Stash Stack |
| :--- | :--- | :--- |
| `git stash` | Removes changes; resets to HEAD. | Adds a new entry to the stack. |
| `git stash apply` | Applies latest stash changes. | Stays in the stack. |
| `git stash pop` | Applies latest stash changes. | Removes the latest entry from the stack. |
| `git stash drop` | No effect on working dir. | Removes the specified entry. |
| `git stash clear` | No effect on working dir. | Removes ALL entries. |

### Practice Questions
- **Q1:** You have three stashes. How do you apply the second oldest stash to your current branch?
- **Q2:** You stashed changes but forgot to include a new file `temp.log`. What flag do you use to stash untracked files?

### Quiz
1. What is the default behavior of `git stash` regarding untracked files? a) It stashes them. b) It ignores them. c) It deletes them. d) It commits them. *(Answer: b)*
2. Which command applies the latest stash AND removes it from the stash list? a) `git stash apply` b) `git stash pop` c) `git stash drop` d) `git stash clear` *(Answer: b)*

### Interview Questions
- **Beginner:** "You are working on a feature but need to urgently switch branches. How do you save your progress without committing?"
- **Intermediate:** "What happens if you `git stash pop` and there is a merge conflict? How do you clean up the stash afterwards?"

### Assignment
- In your practice repo, create a new file `feature.txt` and add some content. Stage it and run `git stash push -m "feature draft"`.
- Verify the working directory is clean (`git status`).
- Create a new file `hotfix.txt` and commit it.
- Now, apply the stash using `git stash apply`. Resolve any conflicts (if any, otherwise just edit).
- Finally, `git stash drop` the stash since you applied it manually.

### Summary
- **Stashing** safely stores unfinished work.
- Use `pop` to apply and remove, `apply` to apply and keep.
- Untracked files need `-u` to be stashed.
- Stashes are stack-based (LIFO).

---

## Topic 4: Tagging Releases (`git tag`)

### Concept Explanation
Tags are **permanent markers** attached to specific commits. They are used to mark release points (e.g., `v1.0.0`, `v2.1.3`). There are two types:
- **Lightweight tags**: Just a name pointing to a commit (like a branch that never moves).
- **Annotated tags**: Stored as full objects with metadata: tagger name, email, date, and a message. They are cryptographically verifiable (GPG-signable). **Always use annotated tags for releases.**

### Real-World Example
You are a software company. You package your product and ship it to customers as "Version 2.0". You put a permanent gold sticker (`v2.0`) on the exact source code that was shipped. You never move this sticker. If a bug is found, you fix it and ship `v2.0.1` (a new tag).

### Git Command Syntax
```bash
# Create a lightweight tag
git tag v1.0

# Create an annotated tag (RECOMMENDED)
git tag -a v1.0 -m "Release version 1.0 with login feature"

# List all tags
git tag

# List tags matching a pattern
git tag -l "v1.*"

# View tag details
git show v1.0

# Push a specific tag to remote
git push origin v1.0

# Push ALL tags to remote
git push origin --tags

# Delete a local tag
git tag -d v1.0

# Delete a remote tag
git push origin --delete v1.0

# Checkout a specific tag (creates detached HEAD)
git checkout v1.0
```

### Multiple Examples
- **Example 1 (Creating a release):** `git tag -a v1.0.0 -m "Official launch of billing module"` → then `git push origin v1.0.0`.
- **Example 2 (Semantic Versioning):** `v1.2.3` → Major (breaking changes), Minor (new features), Patch (bug fixes).
- **Example 3 (Hotfix tag):** After fixing a critical bug in production, `git tag -a v1.0.1 -m "Hotfix for null pointer exception"`.
- **Example 4 (Lightweight for temp use):** `git tag temp-fix` (not recommended for permanent releases because it lacks metadata).

### Visual Table Illustration (Lightweight vs Annotated)
| Feature | Lightweight Tag | Annotated Tag |
| :--- | :--- | :--- |
| **Stored as** | Just a pointer (ref). | Full Git object. |
| **Contains metadata** | ❌ No (just the commit hash). | ✅ Yes (tagger, date, message, GPG signature). |
| **Best for** | Personal internal bookmarks. | Public releases, official versions. |
| **Command** | `git tag v1` | `git tag -a v1 -m "msg"` |

### Practice Questions
- **Q1:** Write the command to create a tag called `v2.1` with the message "Security patch applied".
- **Q2:** You pushed a tag by accident. How do you remove it from the remote repository?

### Quiz
1. Which tag type is recommended for official software releases? a) Lightweight b) Annotated c) Internal d) Remote *(Answer: b)*
2. Which command pushes all local tags to the remote repository? a) `git push origin tags` b) `git push --all` c) `git push origin --tags` d) `git tag push` *(Answer: c)*

### Interview Questions
- **Beginner:** "How do you mark a specific commit as a release version 1.0?"
- **Intermediate:** "Explain the difference between a lightweight and an annotated tag. Why would a CI/CD pipeline use annotated tags?"

### Assignment
- In your repo, create an annotated tag `v0.1-alpha` with a message "First testable version".
- Push this tag to your remote GitHub repository.
- Go to GitHub, refresh, and click on "Releases" (or tags) to see it displayed.
- Then, delete the local tag and delete the remote tag as a clean-up exercise.

### Summary
- **Tags** mark specific points in history.
- Prefer **annotated tags** (`-a -m`) for releases.
- Tags are not automatically pushed; you must `git push origin <tagname>` or `--tags`.

---

## Topic 5: Viewing Advanced History

### Concept Explanation
Beyond `git log --oneline`, Git offers powerful forensic and visualization tools:
- **`git log -p`**: Shows the actual diff (patch) introduced by each commit.
- **`git log --graph`**: Displays a visual ASCII representation of branch history.
- **`git log --since/--until`**: Filters by date.
- **`git reflog`**: A chronological log of **all** times HEAD changed (commits, checkouts, resets, merges). This is your "emergency undo" safety net.
- **`git blame`**: Shows, line-by-line, who last modified each line of a file and in which commit.
- **`git bisect`**: A binary search algorithm to find the exact commit that introduced a bug.

### Real-World Example
- **`git log --graph`**: Looking at a tree map of the family tree.
- **`git reflog`**: The black box recorder on an airplane. Even if you lose your way (e.g., `git reset --hard`), the reflog records your movements for 90 days.
- **`git blame`**: CSI investigation—who wrote this line of code that just crashed the server?
- **`git bisect`**: Trying to find which light switch in a building of 1000 switches turns on a specific lamp by flipping half the switches at a time.

### Git Command Syntax
```bash
# --- Advanced Logging ---
# Show patches (diffs) for commits
git log -p -n 3

# Visual graph with all branches, oneline, and decorated with tags/branches
git log --oneline --graph --all --decorate

# Show commits by author
git log --author="Alice"

# Show commits from the last 2 days
git log --since="2 days ago"

# --- Reflog (Life Saver) ---
# Show all HEAD movements
git reflog

# Recover a lost commit using reflog (checkout the hash shown)
git checkout HEAD@{2}

# --- Blame (Forensics) ---
# Show who changed each line in a file
git blame index.html

# Show blame with commits from a specific range
git blame -L 10,20 index.html

# --- Bisect (Bug Hunting) ---
git bisect start
git bisect bad HEAD      # Current commit is bad
git bisect good <hash>   # Old commit that was good
# Git checks out a commit in the middle.
# You test, then run `git bisect good` or `git bisect bad`.
git bisect reset         # End bisect session
```

### Multiple Examples
- **Example 1 (Graph):** `git log --oneline --graph --all` → Shows a beautiful ASCII tree of all branches. Essential for understanding complex merge histories.
- **Example 2 (Reflog Recovery):** You ran `git reset --hard HEAD~5` by mistake. `git reflog` shows `a1b2c3d HEAD@{2}: reset: moving to HEAD~5`. You run `git reset --hard a1b2c3d` to restore the lost commit.
- **Example 3 (Blame):** `git blame app.js` → Output: `a1b2c3d (Alice 2025-03-10 14:23:45 1) const API_KEY = '...'` → Alice is responsible for that line.
- **Example 4 (Bisect):** You notice a bug in v2.0 that wasn't in v1.0. You run `git bisect start`, mark v2.0 as `bad`, v1.0 as `good`, and let Git test commits until it finds the culprit.

### Visual Table Illustration (History Tools)
| Tool | Purpose | Best For |
| :--- | :--- | :--- |
| `git log --oneline` | Quick summary of commits. | Daily overview. |
| `git log --graph` | Visualize branch merging structure. | Understanding branching complexity. |
| `git reflog` | Log of all HEAD movements. | **Recovering lost commits** (life-saver). |
| `git blame` | Find who changed a specific line. | Debugging / responsibility tracking. |
| `git bisect` | Binary search for bug introduction. | Finding the exact commit that broke something. |

### Practice Questions
- **Q1:** You accidentally `git reset --hard` to an old commit and lost your last 3 commits. How do you recover them?
- **Q2:** What command shows a visual tree of your entire repository's branch history?

### Quiz
1. Which command is used to find out who last modified a specific line in a file? a) `git log` b) `git blame` c) `git reflog` d) `git bisect` *(Answer: b)*
2. `git reflog` is primarily useful for: a) Viewing remote branches. b) Recovering lost commits. c) Merging branches. d) Creating tags. *(Answer: b)*

### Interview Questions
- **Beginner:** "How would you find out who introduced a specific buggy line in `app.js`?"
- **Advanced:** "Explain how `git bisect` works. How would you use it to debug a performance regression that spans 100 commits?"

### Assignment
- In your practice repo, make 5 commits.
- View the graph using `git log --oneline --graph --all`.
- Use `git reset --hard HEAD~2` to simulate losing 2 commits.
- Run `git reflog`. Find the hash of the lost commits.
- Recover them using `git reset --hard <hash>`.
- Pick a file and run `git blame` on it. Note who (yourself) authored each line.

### Summary
- **`git log`** with flags gives deep historical insight.
- **`git reflog`** is your emergency parachute for recovering lost work.
- **`git blame`** finds the culprit.
- **`git bisect`** is an efficient bug-hunting assistant.

---

## Topic 6: Using Git Aliases

### Concept Explanation
Aliases allow you to create custom shortcuts for Git commands. They save keystrokes and make complex commands easy to remember. You can configure them globally (for all repos) or locally (per repo). They are stored in your `.gitconfig` file.

### Real-World Example
You have a long phone number (command) that you call frequently. Instead of dialing 10 digits every time, you program speed-dial button `1` (alias) to dial that number. You now just press `1` to connect.

### Git Command Syntax (Configuration)
```bash
# View existing aliases
git config --global --get-regexp alias

# Create a simple alias
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.ci commit

# Create an alias with multiple commands (using ! to run shell commands)
git config --global alias.unstage 'reset HEAD --'

# Create a complex alias for a pretty log
git config --global alias.hist "log --oneline --graph --all --decorate"

# Create an alias to show the last commit hash
git config --global alias.last "log -1 --oneline"

# Create a shell alias to add and commit in one go
git config --global alias.ac '!git add -A && git commit -m'
```

### Multiple Examples
- **Example 1 (Speed):** `git config --global alias.s status` → Now `git s` shows status.
- **Example 2 (Unstage):** `git config --global alias.unstage 'reset HEAD --'` → Now `git unstage file.txt` is equivalent to `git reset HEAD -- file.txt`.
- **Example 3 (Hist):** `git config --global alias.hist "log --oneline --graph --all --decorate"` → Now `git hist` shows a beautiful full-graph log.
- **Example 4 (Lazy Commit):** `git config --global alias.ac '!git add -A && git commit -m'` → Now `git ac "My message"` adds everything and commits in one command.

### Visual Table Illustration (Useful Aliases)
| Alias Name | Command | New Shortcut |
| :--- | :--- | :--- |
| `co` | `checkout` | `git co main` |
| `br` | `branch` | `git br -a` |
| `st` | `status -s` | `git st` |
| `ci` | `commit -m` | `git ci "msg"` |
| `hist` | `log --oneline --graph --all` | `git hist` |
| `unstage` | `reset HEAD --` | `git unstage file` |
| `last` | `log -1 HEAD --stat` | `git last` |
| `amend` | `commit --amend --no-edit` | `git amend` (add to last commit) |

### Practice Questions
- **Q1:** Write the Git config command to create an alias `df` that shows `git diff` in color.
- **Q2:** You want an alias `who` that runs `git blame` on a file. How do you set it?

### Quiz
1. Which flag allows you to run a shell command (not just a Git subcommand) in an alias? a) `--shell` b) `!` c) `%` d) `--exec` *(Answer: b)*
2. Where are global Git aliases stored? a) `.git/config` b) `~/.gitconfig` c) `~/.bashrc` d) `/etc/gitconfig` *(Answer: b)*

### Interview Questions
- **Beginner:** "What are Git aliases and why are they useful?"
- **Intermediate:** "How would you create an alias that adds all changes and commits them with a message in one command?"

### Assignment
- Set up the following aliases globally:
  - `co` = `checkout`
  - `st` = `status -s`
  - `hist` = `log --oneline --graph --all --decorate`
  - `unstage` = `restore --staged`
- Create an alias `wip` that stages all changes and commits with the message "WIP: $(date)" (hint: use `!` and shell commands).
- Practice using `git st`, `git hist`, and `git wip` in your practice repo.
- View your aliases with `git config --global --get-regexp alias`.

### Summary
- **Aliases** save time and reduce typos.
- Simple aliases replace Git commands (`co` -> `checkout`).
- Complex aliases (with `!`) can run full shell scripts.
- They are essential for power users to speed up daily workflows.

---

## Comprehensive Practice Questions (All Topics)
1. Explain the difference between `git revert` and `git reset`. Which one would you use on `main` and why?
2. How do you recover a commit that you lost with `git reset --hard`?
3. What is the difference between a lightweight and an annotated tag? Which is better for releases?
4. Write an alias that shows a one-line graph of all branches with decorations.
5. What does `git stash` do and when is it useful?

---

## Comprehensive Quiz (Multiple Choice)
1. Which command shows a visual ASCII graph of your commit history? a) `git log --oneline` b) `git log --graph` c) `git reflog` d) `git blame` *(Answer: b)*
2. You have uncommitted changes and need to switch branches. Which command safely shelves them? a) `git commit` b) `git reset` c) `git stash` d) `git tag` *(Answer: c)*
3. Which reset mode discards all changes in the working directory? a) `--soft` b) `--mixed` c) `--hard` d) `--keep` *(Answer: c)*
4. An annotated tag is created with: a) `git tag v1` b) `git tag -a v1 -m "msg"` c) `git tag -l v1` d) `git push v1` *(Answer: b)*
5. Which Git command is used to find the commit that introduced a bug using a binary search? a) `git blame` b) `git bisect` c) `git log` d) `git reflog` *(Answer: b)*
6. You want to create a shortcut `git hist` for a complex log command. What do you use? a) A bash script b) `git config --global alias.hist "log --oneline --graph"` c) `git shortcuts` d) `git hist="log"` *(Answer: b)*

---

## Interview Questions
- **Beginner:** "You have accidentally deleted a file and committed that deletion. How do you get the file back?"
- **Intermediate:** "Explain the three modes of `git reset` (`--soft`, `--mixed`, `--hard`) and give a real-world use case for each."
- **Advanced:** "A developer rewrites history with `git rebase` on a shared branch and force pushes. What is the immediate impact on other developers' local repositories, and how can they recover?"
- **Scenario:** "You are the release manager. You need to tag version 1.2.3 on a specific commit that is not the tip of `main`. Walk me through the process."

---

## Comprehensive Assignment (Intermediate Mastery)
**Objective:** Demonstrate proficiency in undo operations, internals, stashing, tagging, and recovery.

1. **Setup:** Create a new repo `phase6-lab`. Make 4 commits: `c1`, `c2`, `c3`, `c4` (each adding a file or line).
2. **Tagging:** Create an annotated tag `v1.0` on `c2`. Push it to a remote (simulated or real).
3. **Reset Soft:** Reset to `c1` using `git reset --soft HEAD~3`. Check `git status` – all changes from `c2`, `c3`, `c4` should be staged. Commit them as a single `squash-commit` to combine them.
4. **Stashing:** Create some untracked files (`temp.log`, `debug.txt`). Stash them with `git stash -u`. Verify the working directory is clean.
5. **Bisect Simulation:** (Theory) Write a 5-step plan on how you would use `git bisect` to find a bug introduced between `v1.0` and the latest commit.
6. **Revert:** Make a new commit `buggy` that introduces a syntax error. Revert it using `git revert HEAD`.
7. **Recovery:** Use `git reflog` to find the hash of the `buggy` commit (before revert). Create a new branch `recovered-bug` pointing to that hash.
8. **Aliases:** Set up a global alias `graph` that runs `log --oneline --graph --all --decorate`.
9. **Cleanup:** Use `git stash pop` to retrieve your stashed `temp.log`.
10. **Final Verification:** Run `git graph` and document the state of your repository.

---

## Phase 6 Summary
- **Git Internals** (HEAD, Blob, Tree, Commit) demystify how Git operates under the hood.
- **Undoing Changes** is nuanced:
  - `git restore` – safe local undo (unstage/discard).
  - `git reset` – powerful rewind (use locally, `--soft` and `--mixed` are safer than `--hard`).
  - `git revert` – the only safe way to undo shared/pushed commits.
- **`git stash`** temporarily shelves work, acting as a stack of unfinished tasks.
- **Tags** (`git tag`) provide permanent markers for releases; annotated tags are preferred.
- **Advanced History** tools like `git reflog` (recovery), `git blame` (forensics), and `git bisect` (bug hunting) make you a debugging hero.
- **Aliases** (`git config --global alias.*`) transform long commands into snappy shortcuts, boosting your daily velocity.

You have now graduated from a Git user to a Git craftsman. These intermediate concepts give you the confidence to handle almost any situation Git throws at you. Well done!

---


<br/><br/><br/>
<center> <b>Happy Learning! 😊</b> </center>