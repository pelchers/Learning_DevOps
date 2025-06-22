# Git Commands and Workflow

## 📖 What This File Does
This guide covers essential Git commands, basic workflows, and repository management. It builds upon the installation and setup to provide practical Git usage skills.

## 🎯 Learning Objectives
- Master fundamental Git commands
- Understand the Git workflow and lifecycle
- Learn repository initialization and management
- Practice staging, committing, and history management
- Understand remote repository operations

## 📋 Prerequisites
- Git installed and configured (see `01-Git_Installation_And_Setup.md`)
- Basic command-line knowledge
- Understanding of file systems and directories

---

## Git Repository Lifecycle

### Understanding Git States
```
Working Directory  →  Staging Area  →  Repository
     (Modified)         (Staged)        (Committed)
```

**File States:**
- **Untracked**: New files not yet tracked by Git
- **Modified**: Changes made to tracked files
- **Staged**: Changes ready to be committed
- **Committed**: Changes saved to repository history

---

## Essential Git Commands

### Repository Initialization

#### Create New Repository
```bash
# Create new directory and initialize
mkdir my-project && cd my-project
git init

# Initialize with specific branch name
git init --initial-branch=main

# Clone existing repository
git clone https://github.com/user/repo.git
git clone git@github.com:user/repo.git  # SSH

# Clone to specific directory
git clone https://github.com/user/repo.git my-local-name
```

#### Repository Status and Information
```bash
# Check repository status
git status

# Short status format
git status -s

# Check repository information
git remote -v
git branch -a
git log --oneline
```

### File Operations

#### Adding Files to Staging
```bash
# Add specific file
git add filename.txt

# Add multiple files
git add file1.txt file2.txt file3.txt

# Add all files in directory
git add .

# Add all files recursively
git add -A

# Add files by pattern
git add *.txt
git add src/*.js

# Interactive adding
git add -i

# Add parts of a file (patch mode)
git add -p filename.txt
```

#### Committing Changes
```bash
# Basic commit
git commit -m "Commit message"

# Commit with detailed message
git commit -m "Short description" -m "Longer description explaining changes"

# Commit all tracked changes (skip staging)
git commit -am "Commit message"

# Amend last commit
git commit --amend -m "New commit message"

# Sign commits
git commit -S -m "Signed commit"
```

#### Removing and Moving Files
```bash
# Remove file from Git and filesystem
git rm filename.txt

# Remove file from Git but keep in filesystem
git rm --cached filename.txt

# Remove directory
git rm -r directory/

# Move/rename files
git mv oldname.txt newname.txt
```

### Viewing History and Changes

#### Log Commands
```bash
# View commit history
git log

# One line per commit
git log --oneline

# Show changes in each commit
git log -p

# Show last N commits
git log -3

# Show commits by author
git log --author="John Doe"

# Show commits in date range
git log --since="2023-01-01" --until="2023-12-31"

# Show graphical representation
git log --graph --oneline --all

# Show file statistics
git log --stat
```

#### Diff Commands
```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged
git diff --cached

# Compare specific files
git diff filename.txt

# Compare commits
git diff commit1 commit2

# Compare branches
git diff branch1 branch2

# Show changes in last commit
git show HEAD
```

### Undoing Changes

#### Unstaging Changes
```bash
# Unstage specific file
git reset filename.txt

# Unstage all files
git reset

# Reset to specific commit (keep changes)
git reset --soft HEAD~1

# Reset to specific commit (discard changes)
git reset --hard HEAD~1
```

#### Reverting Changes
```bash
# Discard changes in working directory
git checkout -- filename.txt

# Restore file from specific commit
git checkout commit-hash -- filename.txt

# Revert specific commit (creates new commit)
git revert commit-hash

# Create a new commit that undoes changes
git revert HEAD
```

#### Advanced Undo Operations
```bash
# Interactive rebase (rewrite history)
git rebase -i HEAD~3

# Reset to specific commit
git reset --hard commit-hash

# Reflog (recover lost commits)
git reflog
git reset --hard HEAD@{2}
```

---

## Working with Remote Repositories

### Remote Configuration
```bash
# List remote repositories
git remote -v

# Add remote repository
git remote add origin https://github.com/user/repo.git

# Change remote URL
git remote set-url origin git@github.com:user/repo.git

# Remove remote
git remote remove origin

# Rename remote
git remote rename origin upstream
```

### Fetching and Pulling
```bash
# Fetch changes from remote
git fetch origin

# Fetch all remotes
git fetch --all

# Pull changes (fetch + merge)
git pull origin main

# Pull with rebase
git pull --rebase origin main

# Pull all branches
git pull --all
```

### Pushing Changes
```bash
# Push to remote branch
git push origin main

# Push all branches
git push --all origin

# Push tags
git push --tags

# Force push (use with caution)
git push --force origin main

# Set upstream branch
git push -u origin main
```

---

## Git Workflow Patterns

### Basic Workflow
```bash
# 1. Check status
git status

# 2. Add changes
git add .

# 3. Commit changes
git commit -m "Description of changes"

# 4. Push to remote
git push origin main
```

### Feature Development Workflow
```bash
# 1. Start from main branch
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/new-feature

# 3. Make changes and commit
git add .
git commit -m "Add new feature"

# 4. Push feature branch
git push origin feature/new-feature

# 5. Create pull request (on GitHub/GitLab)
# 6. Merge and cleanup (after review)
git checkout main
git pull origin main
git branch -d feature/new-feature
```

### Hotfix Workflow
```bash
# 1. Create hotfix branch from main
git checkout main
git checkout -b hotfix/critical-bug

# 2. Fix the issue
git add .
git commit -m "Fix critical bug"

# 3. Push and merge quickly
git push origin hotfix/critical-bug

# 4. Merge to main immediately
# 5. Tag the release
git tag -a v1.0.1 -m "Hotfix release"
git push --tags
```

---

## Advanced Git Commands

### Stashing Changes
```bash
# Stash current changes
git stash

# Stash with message
git stash save "Work in progress on feature X"

# List stashes
git stash list

# Apply most recent stash
git stash apply

# Apply specific stash
git stash apply stash@{2}

# Pop stash (apply and remove)
git stash pop

# Drop stash
git stash drop stash@{1}

# Clear all stashes
git stash clear
```

### Tagging
```bash
# Create lightweight tag
git tag v1.0

# Create annotated tag
git tag -a v1.0 -m "Version 1.0 release"

# Tag specific commit
git tag -a v1.0 commit-hash

# List tags
git tag
git tag -l "v1.*"

# Show tag information
git show v1.0

# Push tags
git push origin v1.0
git push --tags

# Delete tag
git tag -d v1.0
git push origin --delete v1.0
```

### Searching and Finding
```bash
# Search in commit messages
git log --grep="bug fix"

# Search in code
git grep "function name"

# Show commits that changed a file
git log --follow filename.txt

# Show who changed each line
git blame filename.txt

# Find when bug was introduced
git bisect start
git bisect bad HEAD
git bisect good v1.0
```

---

## Git Configuration and Customization

### Useful Aliases
```bash
# Set up common aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.df diff
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

### Git Ignore Patterns
```bash
# Create .gitignore file
echo "node_modules/" >> .gitignore
echo "*.log" >> .gitignore
echo ".env" >> .gitignore

# Global .gitignore
git config --global core.excludesFile ~/.gitignore_global
```

Common `.gitignore` patterns:
```gitignore
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
*.exe
*.dll

# Environment files
.env
.env.local

# IDE files
.vscode/
.idea/
*.swp

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Temporary files
tmp/
temp/
```

---

## Troubleshooting Common Issues

### Issue 1: Merge Conflicts
```bash
# When merge conflict occurs
git status  # Shows conflicted files

# Edit files to resolve conflicts
# Look for conflict markers: <<<<<<<, =======, >>>>>>>

# After resolving conflicts
git add resolved-file.txt
git commit -m "Resolve merge conflict"
```

### Issue 2: Accidentally Committed Wrong Changes
```bash
# Undo last commit but keep changes
git reset --soft HEAD~1

# Undo last commit and discard changes
git reset --hard HEAD~1

# Amend last commit
git commit --amend -m "Corrected commit message"
```

### Issue 3: Need to Change Author Information
```bash
# Change author of last commit
git commit --amend --author="New Author <email@example.com>"

# Change author for multiple commits
git rebase -i HEAD~3
# Change 'pick' to 'edit' for commits to modify
# For each commit:
git commit --amend --author="New Author <email@example.com>"
git rebase --continue
```

### Issue 4: Recovery from Reset
```bash
# View reflog to find lost commits
git reflog

# Reset to previous state
git reset --hard HEAD@{2}

# Create branch from lost commit
git branch recovery-branch commit-hash
```

---

## Best Practices

### 🔧 Command Best Practices
1. **Use descriptive commit messages** following conventional commits
2. **Commit frequently** with logical, atomic changes
3. **Review changes** before committing with `git diff`
4. **Use branching** for features and experiments
5. **Keep history clean** with meaningful commits

### 📝 Commit Message Guidelines
```
type(scope): short description

Longer description explaining what and why

- List of changes
- Another important change

Fixes #123
```

**Types:** feat, fix, docs, style, refactor, test, chore

### 🚨 Safety Practices
1. **Always check status** before major operations
2. **Use `--dry-run`** for destructive commands when available
3. **Backup important work** before rebasing or resetting
4. **Test changes** before pushing to shared branches
5. **Use pull requests** for code review

---

## Hands-On Exercises

### Exercise 1: Basic Workflow
```bash
# Create practice repository
mkdir git-practice && cd git-practice
git init

# Create and modify files
echo "Hello Git" > hello.txt
git add hello.txt
git commit -m "Add hello.txt"

# Make changes
echo "Hello DevOps" > hello.txt
git diff
git add hello.txt
git commit -m "Update greeting"

# View history
git log --oneline
```

### Exercise 2: Working with Remotes
```bash
# Add remote repository
git remote add origin https://github.com/yourusername/git-practice.git

# Push to remote
git push -u origin main

# Make local changes
echo "Remote changes" >> hello.txt
git add hello.txt
git commit -m "Add remote changes note"
git push origin main
```

### Exercise 3: Branching Practice
```bash
# Create and switch to branch
git checkout -b feature-branch

# Make changes on branch
echo "Feature work" > feature.txt
git add feature.txt
git commit -m "Add feature file"

# Switch back to main
git checkout main
git log --oneline --all --graph
```

---

## Next Steps

After mastering these commands:

1. **Learn advanced branching** → See `03-Branching_And_Merging.md`
2. **Explore GitHub workflows** → See `../Github_Workflows/`
3. **Practice collaboration** → See `../Collaboration_Strategies/`
4. **Set up CI/CD integration** → See later modules

---

## 🔧 Configuration Notes

- **Command Shortcuts**: Set up aliases for frequently used commands
- **Editor Integration**: Configure your IDE for Git integration
- **Visual Tools**: Consider GUI tools like GitKraken, SourceTree, or VS Code Git
- **Workflow Consistency**: Establish team standards for commit messages and workflows

## 📚 Additional Resources

### Essential Reading
- ["The Lean Startup" by Eric Ries](https://www.amazon.com/Lean-Startup-Entrepreneurs-Continuous-Innovation/dp/0307887898) - Iterative development and version control strategies
- ["Team of Teams" by General Stanley McChrystal](https://www.amazon.com/Team-Teams-Rules-Engagement-Complex/dp/1591847486) - Collaborative workflows and team coordination
- ["The Five Dysfunctions of a Team" by Patrick Lencioni](https://www.amazon.com/Five-Dysfunctions-Team-Leadership-Fable/dp/0787960756) - Building trust for effective code collaboration
- ["Fearless Change" by Linda Rising and Mary Lynn Manns](https://www.amazon.com/Fearless-Change-Patterns-Introducing-Ideas/dp/0201755920) - Adopting new Git workflows and practices

### Videos
- [Git Commands Deep Dive (The Net Ninja)](https://www.youtube.com/watch?v=3RjQznt-8kE&list=PL4cUxeGkcC9goXbgTDQ0n_4TBzOO0ocPR) - Complete command reference
- [Git Workflow Strategies (GitKraken)](https://www.youtube.com/watch?v=aJnFGMclhU8) - Visual workflow comparison
- [Mastering Git (Traversy Media)](https://www.youtube.com/watch?v=SWYqp7iY_Tc) - Comprehensive Git mastery
- [Git Rebase Explained (Academind)](https://www.youtube.com/watch?v=f1wnYdLEpgI) - Advanced Git techniques

### Guides
- [Git Command Reference](https://git-scm.com/docs)
- [Interactive Git Tutorial](https://learngitbranching.js.org/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Workflows Comparison](https://www.atlassian.com/git/tutorials/comparing-workflows)
- [GitHub Git Handbook](https://guides.github.com/introduction/git-handbook/)
- [Git Immersion Tutorial](https://gitimmersion.com/)

### Articles
- [Git Best Practices](https://sethrobertson.github.io/GitBestPractices/)
- [A Note About Git Commit Messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
- [Git Branching - Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
- [Understanding Git Conceptually](https://www.sbf5.com/~cduan/technical/git/)

### Cultural Assessment Tools
- [Git Workflow Maturity Assessment](https://www.devops-culture.com/assessment/)
- [Version Control Practice Evaluation](https://www.pluralsight.com/paths/git)

### Communities and Events
- [Git User Community](https://git-scm.com/community)
- [Stack Overflow Git Tag](https://stackoverflow.com/questions/tagged/git)
- [Git Meetups Worldwide](https://www.meetup.com/topics/git/)
- [Open Source Contribution Events](https://www.firsttimersonly.com/) 