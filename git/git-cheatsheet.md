# 🔀 The Ultimate Git Cheatsheet

### *Because version control shouldn't make you lose your version of sanity*

---

## 🎬 Act I: Getting Started (a.k.a. "Hello, Repository")

### Installation & Setup

```bash
# Install Git (Linux)
sudo apt install git        # Ubuntu/Debian
sudo yum install git        # CentOS/RHEL

# Install Git (macOS)
brew install git

# Install Git (Windows)
# Download from git-scm.com

# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# View configuration
git config --list
git config user.name
git config user.email

# Set default editor
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim
git config --global core.editor "nano"          # Nano
```

### Initializing Repositories

```bash
# Initialize new repository
git init

# Clone existing repository
git clone <url>
git clone https://github.com/user/repo.git
git clone git@github.com:user/repo.git         # SSH
git clone <url> <directory>                     # Clone to specific directory

# Clone specific branch
git clone -b <branch> <url>

# Clone with depth (shallow clone)
git clone --depth 1 <url>                      # Only latest commit
```

---

## 📦 Act II: Basic Workflow (The Daily Grind)

### Checking Status

```bash
# Check status
git status
git status -s                # Short format
git status -u                # Show untracked files

# Check what changed
git diff                     # Unstaged changes
git diff --staged            # Staged changes
git diff HEAD                # All changes (staged + unstaged)
git diff <commit1> <commit2>  # Between commits
```

### Adding & Committing

```bash
# Stage files
git add <file>
git add file1 file2 file3
git add .                    # All files in current directory
git add -A                   # All files (including deletions)
git add -u                   # Only tracked files

# Stage interactively
git add -p                   # Patch mode (choose hunks)

# Unstage files
git reset <file>
git reset HEAD <file>         # Explicit
git restore --staged <file>  # Newer syntax

# Commit
git commit -m "Message"
git commit -m "Title" -m "Description"  # Multi-line

# Amend last commit
git commit --amend
git commit --amend -m "New message"
git commit --amend --no-edit  # Keep message, add changes

# Skip staging area
git commit -a -m "Message"    # Stage and commit tracked files
```

---

## 🌿 Act III: Branches (The Parallel Universe)

### Branch Operations

```bash
# List branches
git branch
git branch -a                # All branches (including remote)
git branch -r                # Remote branches only
git branch -v                # With last commit

# Create branch
git branch <branch-name>
git branch <branch-name> <start-point>  # From specific commit

# Switch branch
git checkout <branch>
git switch <branch>          # Newer syntax

# Create and switch
git checkout -b <branch>
git switch -c <branch>       # Newer syntax

# Delete branch
git branch -d <branch>       # Safe delete (merged)
git branch -D <branch>       # Force delete (unmerged)

# Rename branch
git branch -m <old-name> <new-name>
git branch -m <new-name>     # Rename current branch

# Track remote branch
git branch --set-upstream-to=origin/<branch> <branch>
git branch -u origin/<branch> <branch>
```

### Merging

```bash
# Merge branch into current
git merge <branch>
git merge --no-ff <branch>   # No fast-forward (creates merge commit)
git merge --squash <branch>  # Squash all commits into one

# Abort merge
git merge --abort

# Resolve conflicts
# Edit conflicted files, then:
git add <resolved-files>
git commit
```

---

## 🔄 Act IV: Remote Operations (The Cloud Connection)

### Remote Management

```bash
# List remotes
git remote
git remote -v                # With URLs

# Add remote
git remote add <name> <url>
git remote add origin https://github.com/user/repo.git

# Remove remote
git remote remove <name>
git remote rm <name>

# Rename remote
git remote rename <old> <new>

# Change remote URL
git remote set-url <name> <new-url>

# Show remote info
git remote show <name>
```

### Fetch & Pull

```bash
# Fetch from remote
git fetch
git fetch <remote>
git fetch <remote> <branch>
git fetch --all              # All remotes

# Pull (fetch + merge)
git pull
git pull <remote> <branch>
git pull --rebase            # Rebase instead of merge
git pull --ff-only           # Fast-forward only

# Pull specific branch
git pull origin main
```

### Push

```bash
# Push to remote
git push
git push <remote> <branch>
git push origin main

# Push new branch
git push -u origin <branch>  # Set upstream
git push --set-upstream origin <branch>

# Force push (dangerous!)
git push --force
git push --force-with-lease  # Safer force push

# Push all branches
git push --all

# Push tags
git push --tags
```

---

## 🏷️ Act V: Tags (The Milestones)

### Tag Operations

```bash
# List tags
git tag
git tag -l "v1.*"            # Pattern matching

# Create tag
git tag <tagname>
git tag -a <tagname> -m "Message"  # Annotated tag
git tag <tagname> <commit>   # Tag specific commit

# Show tag info
git show <tagname>

# Delete tag
git tag -d <tagname>
git push origin --delete <tagname>  # Delete remote tag
git push origin :refs/tags/<tagname>  # Alternative

# Push tags
git push origin <tagname>
git push origin --tags       # All tags
```

---

## 📜 Act VI: History & Logs (The Time Machine)

### Viewing History

```bash
# Commit log
git log
git log --oneline           # Compact format
git log --graph             # With branch graph
git log --all --graph --oneline --decorate  # Full view

# Filter log
git log -n 10               # Last 10 commits
git log --since="2 weeks ago"
git log --until="2024-01-01"
git log --author="Name"
git log --grep="pattern"    # Search in messages
git log -S "string"          # Search in code

# Log with file changes
git log --stat
git log -p                  # With patches
git log --follow <file>      # Follow file renames

# Pretty log
git log --pretty=format:"%h - %an, %ar : %s"
```

### Finding Commits

```bash
# Find commit by message
git log --grep="pattern"

# Find commit by content
git log -S "function_name"

# Find commit by author
git log --author="Name"

# Find commit by date
git log --since="2024-01-01" --until="2024-12-31"
```

---

## 🔍 Act VII: Inspecting & Comparing (The Detective Work)

### Inspecting Changes

```bash
# Show commit
git show <commit>
git show HEAD
git show <commit>:<file>     # Show file at commit

# Show file at specific commit
git show <commit>:<file>

# Compare commits
git diff <commit1> <commit2>
git diff HEAD~1 HEAD         # Last commit vs current

# Compare branches
git diff <branch1>..<branch2>
git diff main..feature

# Compare with remote
git diff origin/main
```

### Blame & Annotate

```bash
# Who changed what and when
git blame <file>
git blame -L 10,20 <file>    # Lines 10-20
git blame -w <file>          # Ignore whitespace
```

---

## 🔄 Act VIII: Undoing Changes (The Time Machine)

### Undo Working Directory Changes

```bash
# Discard changes in working directory
git checkout -- <file>
git restore <file>           # Newer syntax

# Discard all changes
git checkout -- .
git restore .                # Newer syntax

# Remove untracked files
git clean -n                 # Dry run (see what would be removed)
git clean -f                 # Force remove
git clean -fd                # Remove directories too
git clean -fX                # Remove ignored files too
```

### Undo Staged Changes

```bash
# Unstage file
git reset HEAD <file>
git restore --staged <file>  # Newer syntax

# Unstage all
git reset HEAD
git restore --staged .       # Newer syntax
```

### Undo Commits

```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (unstage changes)
git reset HEAD~1
git reset --mixed HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1      # DANGEROUS!

# Undo multiple commits
git reset --hard HEAD~3     # Last 3 commits

# Revert commit (creates new commit)
git revert <commit>
git revert HEAD
```

---

## 🗂️ Act IX: Stashing (The Temporary Storage)

### Stash Operations

```bash
# Stash changes
git stash
git stash save "Message"
git stash push -m "Message"

# List stashes
git stash list

# Apply stash
git stash apply              # Keep stash
git stash pop                # Apply and remove
git stash apply stash@{0}    # Apply specific stash

# Show stash
git stash show
git stash show -p            # With diff

# Drop stash
git stash drop stash@{0}
git stash clear              # Remove all stashes

# Stash specific files
git stash push <file>
git stash push -u             # Include untracked files
```

---

## 🔧 Act X: Advanced Operations

### Rebasing

```bash
# Rebase current branch
git rebase <branch>
git rebase origin/main

# Interactive rebase
git rebase -i HEAD~3         # Last 3 commits
git rebase -i <commit>       # From commit

# During rebase
git rebase --continue
git rebase --abort
git rebase --skip

# Rebase onto another branch
git rebase --onto <newbase> <oldbase> <branch>
```

### Cherry-picking

```bash
# Apply commit from another branch
git cherry-pick <commit>
git cherry-pick <commit1> <commit2>
git cherry-pick <commit1>..<commit2>  # Range

# Cherry-pick with no commit
git cherry-pick -n <commit>
git cherry-pick --no-commit <commit>
```

### Submodules

```bash
# Add submodule
git submodule add <url> <path>

# Initialize submodules
git submodule init
git submodule update
git submodule update --init --recursive

# Update submodules
git submodule update --remote
```

---

## 🎯 Act XI: Useful One-Liners

```bash
# Create branch from current
git checkout -b feature/new-feature

# Create branch from remote
git checkout -b local-branch origin/remote-branch

# Delete merged branches
git branch --merged | grep -v "\*\|main\|master" | xargs -n 1 git branch -d

# Update all submodules
git submodule foreach git pull origin main

# Find large files in history
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | awk '/^blob/ {print substr($0,6)}' | sort --numeric-sort --key=2 --reverse | head -10

# Remove file from Git but keep locally
git rm --cached <file>

# Change last commit message
git commit --amend -m "New message"

# Add all changes and commit
git add -A && git commit -m "Message"

# Push and set upstream
git push -u origin $(git branch --show-current)

# Show files changed in commit
git show --name-only <commit>

# Find when file was deleted
git log --diff-filter=D --summary | grep <file>

# List all files ever tracked
git log --all --full-history -- "*<file>*"

# Find commits that changed specific lines
git log -L :<function>:<file>

# Create patch from commits
git format-patch -1 <commit>
git format-patch HEAD~3      # Last 3 commits

# Apply patch
git am <patch-file>
```

---

## 🆘 Emergency Commands (When Everything is on Fire)

```bash
# Recover deleted branch
git reflog
git checkout -b <branch> <commit-from-reflog>

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes) - DANGEROUS!
git reset --hard HEAD~1

# Force push after rebase (if you know what you're doing)
git push --force-with-lease

# Remove file from Git history (requires BFG or filter-branch)
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch <file>" \
  --prune-empty --tag-name-filter cat -- --all

# Find lost commits
git fsck --lost-found
git reflog

# Reset to remote state
git fetch origin
git reset --hard origin/main

# Clean everything (unstaged changes and untracked files)
git clean -fdx
git reset --hard HEAD
```

---

## 📝 Quick Reference Card

| What You Want | Command |
|---------------|---------|
| Initialize repo | `git init` |
| Clone repo | `git clone <url>` |
| Check status | `git status` |
| Stage file | `git add <file>` |
| Commit | `git commit -m "Message"` |
| Push | `git push` |
| Pull | `git pull` |
| Create branch | `git checkout -b <branch>` |
| Switch branch | `git checkout <branch>` |
| Merge | `git merge <branch>` |
| View log | `git log --oneline` |
| Show changes | `git diff` |
| Stash | `git stash` |
| Apply stash | `git stash pop` |
| Undo changes | `git restore <file>` |
| Undo commit | `git reset HEAD~1` |
| Create tag | `git tag <name>` |
| Push tags | `git push --tags` |

---

## 🎬 The End Credits

Remember:
- Commit often, push regularly
- Write meaningful commit messages
- Use branches for features
- `git status` is your friend
- `git log` shows you the history
- `git diff` shows you what changed
- Never force push to main/master (unless you're sure)
- Read the output (Git tells you what to do)
- `--help` works: `git <command> --help`

**Now go version control something awesome!** 🔀

---

*Made with ☕ and the relief of finally understanding Git*

*Last updated: When you finally got that merge right*

