# Phase 2: Basic Git Commands

Welcome to Phase 2! This lesson covers the essential, everyday commands you will use constantly as a developer. Mastering `status`, `add`, `commit`, `log`, `diff`, file management, and `.gitignore` will give you full control over your project's history.

---

## 1. Checking Repository Status (`git status`)

### Concept Explanation
`git status` is your compass. It displays the state of your working directory and staging area. It tells you:
- Which files are **untracked** (new files Git doesn't know about).
- Which files are **modified** but not yet staged.
- Which files are **staged** (added to the index) and ready to be committed.
- Which branch you are currently on and how it compares to its remote counterpart.

### Real-World Example
Imagine you are a chef in a kitchen. `git status` is like looking at your prep table. It shows you which ingredients are prepped (staged), which are chopped but not put on the plate (modified but not staged), and which are still in the grocery bag (untracked). You run this command constantly to know exactly what you are working with before you "cook" (commit).

### Git Command Syntax
```bash
git status
# Shorter, cleaner output (recommended)
git status -s
```

### Multiple Examples
- **Example 1 (Clean state):** `git status` → *"nothing to commit, working tree clean"*.
- **Example 2 (Modified file):** `git status` → *"Changes not staged for commit: modified: index.html"*.
- **Example 3 (Untracked file):** `git status` → *"Untracked files: new-feature.js"*.

### Visual Table Illustration (Status Short Format)
| Symbol | Meaning | Description |
| :--- | :--- | :--- |
| `??` | Untracked | New file, never added/committed. |
| ` M` | Modified (not staged) | File changed but not added. |
| `M ` | Modified (staged) | File changed and added to staging. |
| `A ` | Added (staged) | New file added to staging. |
| `D ` | Deleted (staged) | File deleted and staged. |

---

## 2. Adding Files (`git add`)

### Concept Explanation
`git add` moves changes from your **Working Directory** to the **Staging Area**. It tells Git, "I want to include these specific changes in my next snapshot (commit)." You can stage entire files, specific changes within a file (interactively), or all changes at once.

### Real-World Example
Staging is like selecting items to put into a box before shipping. You have a table full of items (working directory). You pick up `index.html` and `style.css` and place them into the shipping box (staging area). The `README.md` remains on the table because you are not ready to ship it yet.

### Git Command Syntax
```bash
# Stage a specific file
git add filename.txt

# Stage all changes in the current directory and subdirectories
git add .

# Stage all changes, including deletions
git add -A

# Interactive staging (pick hunks)
git add -p
```

### Multiple Examples
- **Example 1:** `git add script.js` → Stages only that JavaScript file.
- **Example 2:** `git add .` → Stages all modified and new files in the current folder.
- **Example 3:** `git add *.html` → Stages all HTML files.

### Visual Table Illustration (File States Before/After Add)
| File | Initial State | After `git add` |
| :--- | :--- | :--- |
| `about.html` | Modified (Working Dir) | **Staged** |
| `contact.js` | Untracked | **Staged** |
| `config.yml` | Modified (Staged previously) | **Staged** (updated to latest) |

---

## 3. Creating Commits (`git commit`)

### Concept Explanation
`git commit` takes everything currently in the **Staging Area** and permanently saves it as a snapshot (a commit) in your **Local Repository**. Each commit gets a unique hash (SHA-1 ID) and requires a commit message that explains *what* changed and *why*.

### Real-World Example
The commit is like sealing that shipping box and stamping it with a unique tracking number (hash) and a label (message). Once sealed, you cannot change the contents (without rewriting history). You send it to your local warehouse (`.git` folder).

### Git Command Syntax
```bash
# Commit with an inline message
git commit -m "Fix login button alignment"

# Commit and open editor for a multi-line message
git commit

# Commit everything that is modified/untracked (skip git add)
git commit -a -m "Update all tracked files"

# Amend the previous commit (change message or add forgotten files)
git commit --amend -m "New corrected message"
```

### Multiple Examples
- **Example 1:** `git commit -m "Add user authentication middleware"` → Creates a commit.
- **Example 2:** `git commit -a -m "Hotfix: resolve null pointer"` → Automatically stages and commits all tracked (previously committed) files.
- **Example 3:** `git commit --amend --no-edit` → Adds currently staged changes to the *last* commit without changing the message.

### Visual Table Illustration (Commit Anatomy)
| Component | Description | Example |
| :--- | :--- | :--- |
| **Hash** | Unique 40-character SHA-1 ID. | `a1b2c3d...` |
| **Author** | Who made the commit (from `user.name`/`user.email`). | `Alice <alice@dev.com>` |
| **Date** | Timestamp of the commit. | `Mon Mar 10 14:23:45 2025` |
| **Message** | Human-readable description. | `"Fix broken API endpoint"` |
| **Parent** | Previous commit hash (links history). | `parent: f4e5d6c...` |

---

## 4. Viewing Commit History (`git log`)

### Concept Explanation
`git log` displays the entire commit history of your repository in reverse chronological order. It shows hashes, authors, dates, and messages. This is your project's diary—essential for debugging, understanding project evolution, and finding when a bug was introduced.

### Real-World Example
This is like reading the ship's logbook. Every entry (commit) tells you who steered the ship, when, and what course correction they made. You can trace back to find the exact moment a leak started.

### Git Command Syntax
```bash
# Full log with hash, author, date, and message
git log

# Condensed, one-line per commit
git log --oneline

# Show the last N commits
git log -n 5

# Show commits with a graphical representation of branches
git log --oneline --graph --all

# Show commits by a specific author
git log --author="Alice"
```

### Multiple Examples
- **Example 1:** `git log --oneline` → Output: `a1b2c3d Fix login bug` (just the short hash and message).
- **Example 2:** `git log -p -2` → Shows the last 2 commits with the full code differences (`patch`).
- **Example 3:** `git log --since="2 days ago"` → Shows commits from the last 48 hours.

### Visual Table Illustration (Log Output Comparison)
| Command | Output Detail | Best Used For |
| :--- | :--- | :--- |
| `git log` | Full details, multi-line. | Deep investigation, seeing dates/emails. |
| `git log --oneline` | One line per commit (hash + msg). | Quick daily overview. |
| `git log --stat` | Shows files changed and lines added/deleted. | Understanding the size of changes. |

---

## 5. Comparing Changes (`git diff`)

### Concept Explanation
`git diff` shows the differences between various states of your repository. It compares:
- **Working Directory vs Staging Area** (what is unstaged).
- **Staging Area vs Last Commit** (what will be committed).
- **Two specific commits or branches**.
This is the "line-by-line" change viewer.

### Real-World Example
You have a rough draft (working dir) and a final edited copy (staged). `git diff` acts like a red-pen track-changes tool, highlighting exactly which sentences you added or deleted before you finalize the chapter.

### Git Command Syntax
```bash
# Show unstaged changes (Working vs Staging)
git diff

# Show staged changes (Staging vs Last Commit)
git diff --staged

# Compare two commits
git diff commit1-hash commit2-hash

# Compare two branches
git diff main feature-branch

# Show changes in a specific file
git diff -- filename.txt
```

### Multiple Examples
- **Example 1:** `git diff` → Outputs red lines (deleted) and green lines (added) for unstaged edits.
- **Example 2:** `git diff --staged` → Shows what will go into your next `git commit`.
- **Example 3:** `git diff HEAD~1` → Compare current state with the previous commit.

### Visual Table Illustration (Diff States)
| Git State | Comparing | Command |
| :--- | :--- | :--- |
| Working Directory vs Staging | Unstaged changes | `git diff` |
| Staging vs Last Commit (HEAD) | Staged changes | `git diff --staged` |
| Working Directory vs Last Commit | All changes (unstaged + staged) | `git diff HEAD` |
| Two Commits | Historical comparison | `git diff hashA hashB` |

---

## 6. Renaming and Removing Files

### Concept Explanation
Git tracks content, not files. However, renaming/deleting files requires explicit commands to stage these changes properly. Using `git mv` avoids confusion and stages the rename immediately. `git rm` stages the deletion.

### Real-World Example
If you move a file from one drawer to another in a filing cabinet, you must update the index. `git mv` is like telling your assistant, "Move this folder to a new name," and they update the catalog instantly.

### Git Command Syntax
```bash
# Rename a file (stage the rename)
git mv old_filename.txt new_filename.txt

# Remove a file (stage the deletion)
git rm filename.txt

# Remove a file from Git but keep it on disk (untrack it)
git rm --cached filename.txt
```

### Multiple Examples
- **Example 1:** `git mv index.html homepage.html` → Stages the rename. `git status` shows `renamed: index.html -> homepage.html`.
- **Example 2:** `git rm old_script.js` → Deletes the file and stages the deletion.
- **Example 3:** `git rm --cached secret.env` → Stops tracking the file (add to `.gitignore` later) but keeps it on your local machine.

### Visual Table Illustration (File Management Commands)
| Command | Effect on Working Dir | Effect on Staging Area |
| :--- | :--- | :--- |
| `git mv A B` | Renames file A to B. | Stages the rename. |
| `git rm A` | Deletes file A. | Stages the deletion. |
| `git rm --cached A` | Keeps file A on disk. | Removes it from staging/tracking. |

---

## 7. Ignoring Files with `.gitignore`

### Concept Explanation
`.gitignore` is a plain text file that tells Git which files or folders to completely ignore—they will never be tracked, staged, or committed. This is crucial for excluding:
- Build artifacts (`/dist`, `/build`).
- Dependency folders (`node_modules/`).
- Environment secrets (`.env`).
- OS junk files (`.DS_Store`).

### Real-World Example
Think of a `.gitignore` as a "do not enter" sign for your security guard (Git). You tell Git: "Never look inside the `/tmp` folder or the `secrets.json` file, no matter what."

### Git Command Syntax (Patterns)
```bash
# Ignore a specific file
secret.env

# Ignore all .log files
*.log

# Ignore an entire directory
node_modules/

# Ignore a directory at any level (recursive)
**/temp/

# Negation (don't ignore this specific file, even if pattern matches)
!important.log
```

### Multiple Examples
- **Example 1 (Node.js):** `node_modules/` → Ignores the dependency folder.
- **Example 2 (Python):** `*.pyc` → Ignores compiled Python bytecode.
- **Example 3 (Mac):** `.DS_Store` → Ignores system metadata files.

### Visual Table Illustration (Common `.gitignore` Entries by Language)
| Language/Framework | Common Patterns |
| :--- | :--- |
| **Node.js** | `node_modules/`, `npm-debug.log`, `.env` |
| **Python** | `__pycache__/`, `*.pyc`, `venv/` |
| **Java** | `target/`, `*.class`, `*.jar` |
| **C++** | `*.o`, `*.exe`, `build/` |
| **IDEs** | `.vscode/`, `.idea/`, `*.iml` |

---

## Comprehensive Practice Questions
1. **Q1:** You edit `app.js`. What command shows you the exact lines you changed *before* staging them?
2. **Q2:** You accidentally staged `temp.txt`. How do you unstage it without deleting the file?
3. **Q3:** Write a `.gitignore` pattern to ignore all `.csv` files inside any folder named `data`.
4. **Q4:** What is the difference between `git commit -m "msg"` and `git commit -a -m "msg"`?

---

## Comprehensive Quiz (Multiple Choice)
1. Which command shows the status of your working directory and staging area in a concise format?
   a) `git log -s`    b) `git status -s`    c) `git diff -s`    d) `git list`
2. How do you stage all changes (including deletions) in the entire repository?
   a) `git add .`    b) `git add -A`    c) `git commit -a`    d) `git stage -all`
3. What does `git log --oneline --graph` do?
   a) Shows only the latest commit.    b) Shows a visual ASCII representation of branches.    c) Shows the file tree.    d) Deletes old commits.
4. What is the purpose of `git rm --cached app.log`?
   a) Delete `app.log` forever.    b) Stop tracking `app.log` but keep it locally.    c) Rename `app.log`.    d) Show differences in `app.log`.
5. Which `.gitignore` entry would ignore a file named `secret.json` inside the `config` folder?
   a) `secret.json`    b) `config/secret.json`    c) `**/secret.json`    d) Both b and c.

*(Answers: 1-b, 2-b, 3-b, 4-b, 5-d)*

---

## Interview Questions
- **Beginner:** "You have made changes to three files. You only want to commit two of them. What is the sequence of commands you would use?"
- **Intermediate:** "Explain the difference between `git diff`, `git diff --staged`, and `git diff HEAD`. In what scenario would each be used?"
- **Advanced (for this phase):** "If you accidentally commit a sensitive file (e.g., `.env`), how would you properly remove it from history? (Hint: involves `.gitignore` and `git rm --cached`, but also rewriting history.)"

---

## Assignment
**Hands-on Practice Lab:**

1. Create a new directory named `phase2-lab` and initialize it as a Git repository.
2. Create three files: `index.html`, `style.css`, and `app.js`.
3. Stage `index.html` and commit it with the message "Add HTML structure".
4. Edit `index.html` (add a `<h1>` tag). Run `git diff` to see the changes.
5. Stage the modified `index.html` and run `git diff --staged` to verify.
6. Create a folder called `node_modules` and add a dummy file inside it.
7. Create a `.gitignore` file and add `node_modules/` to it. Verify with `git status` that the folder does not appear (it should be ignored).
8. Rename `style.css` to `main.css` using `git mv`.
9. Commit all changes with the message "Update styles and ignore dependencies".
10. View the commit history using `git log --oneline --graph`.
11. Delete `app.js` using `git rm` and commit the deletion.
12. Push this repository to GitHub (create a new remote repo) and verify all commits are visible.

---

## Summary
- **`git status`** is your daily checkpoint—run it frequently.
- **`git add`** moves files to the **Staging Area**; **`git commit`** permanently saves the snapshot.
- **`git log`** lets you time-travel through your project's history.
- **`git diff`** is your safety net for reviewing changes before staging or committing.
- **`git mv`** and **`git rm`** are the proper ways to manage file renames/deletions.
- **`.gitignore`** keeps your repository clean by excluding temporary, build, or sensitive files.
- Mastering these commands forms the core of your daily Git workflow—edit, stage, commit, and review.


----


<br/><br/><br/>
<center> <b>Happy Learning! 😊</b> </center>