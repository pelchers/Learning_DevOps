# Branching and Merging

## 📖 What This File Does
This guide covers Git branching strategies, merging techniques, and conflict resolution. It teaches advanced version control workflows essential for team collaboration and DevOps practices.

## 🎯 Learning Objectives
- Understand branching concepts and strategies
- Master branch creation, switching, and management
- Learn different merging techniques (merge, rebase, squash)
- Handle merge conflicts effectively
- Implement branching workflows for DevOps

## 📋 Prerequisites
- Basic Git commands (see `02-Git_Commands_And_Workflow.md`)
- Understanding of Git repository structure
- Command-line proficiency

---

## Understanding Git Branches

### What are Branches?
A branch in Git is a lightweight, movable pointer to a specific commit. Branches allow you to:
- Work on features independently
- Experiment without affecting main code
- Collaborate with multiple developers
- Maintain different versions simultaneously

### Branch Visualization

#### **📊 ASCII Text Visualization (Simple & Fast)**
```
main:     A---B---C---F---G
               \         /
feature:        D---E---
```

> **🎯 ASCII Visualization Explained:**  
> This text-based diagram shows the git branch structure using simple characters. The `main` branch flows horizontally (A→B→C→F→G), while the `feature` branch splits off after commit B, develops independently (D→E), then merges back into main at commit F. The `\` shows where the branch diverged, and `/` shows where it merged back. This simple format works everywhere - in terminals, plain text files, chat messages, and documentation - without needing special rendering tools.
>
> **📍 What Each Letter Represents & How to Access:**
> 
> **Commits (Each Letter = A Specific Code Snapshot):**
> - **A, B, C, D, E** = Individual commits (code changes saved to git history)
> - **F** = Merge commit (combines feature branch changes back into main)
> - **G** = Latest commit on main branch (current branch head)
>
> **🔄 The F→G Progression Explained:**
>   - **F happens** when you merge feature branch into main (combining D+E with C)
>   - **G happens** when additional work occurs after the merge:
>     - You make more commits on main after merging
>     - Teammates push their changes to main  
>     - Automated systems (CI/CD) make commits
>     - You pull updates from remote that include other merges
>
> <div style="background-color: white; border: 1px solid #ddd; padding: 15px; border-radius: 5px; margin: 10px 0;">
>
> **📋 Understanding F vs G Commits - The Merge Process:**
>
>   **💡 Local Git Workflow - Command Line Merging:**
>   ```bash
>   # Starting from commit E (on feature branch):
>   git checkout feature                 # You're at commit E
>   git status                          # Confirm you're on feature branch
>   
>   # Method 1: Switch to main, then merge feature into main
>   git checkout main                   # Switch to main (now at commit C)
>   git merge feature                   # Creates commit F (merge commit)
>   # F = Special commit combining changes from D+E into main
>   
>   # After merge, you're still at F, but F becomes part of main's history
>   git log --oneline                   # Shows: G-F-C-B-A (F is the merge)
>   
>   # Method 2: Pull/push workflow (if working with remote)
>   git push origin main               # Push the merge (F) to remote
>   git pull origin main               # Get any other changes (creates G if others pushed)
>   
>   # Key Difference:
>   # F = The actual merge commit (combining feature work)
>   # G = Whatever comes after F (could be more commits, pushes, etc.)
>   ```
>
>   **🌐 GitHub Web Interface - Pull Request Merging:**
> 
> The GitHub web interface represents the **collaborative workflow** used by development teams, project maintainers, and contributors working on shared repositories. This approach is preferred when multiple developers are involved, code review is required, or when you want to maintain a clean history with documented merge decisions. Unlike the local workflow (which is immediate and individual), the web interface adds a **review and approval layer** that's essential for team-based development and open-source projects.
>
>   ```bash
>   # Setup: Feature branch E exists on GitHub
>   # 1. Create Pull Request (from GitHub web interface):
>   GitHub → "Compare & pull request" → feature branch → main branch
>   
>   # 2. Merge via GitHub Interface (creates F):
>   PR Page → "Merge pull request" button → Confirm merge
>   # GitHub automatically creates commit F (merge commit)
>   
>   # 3. Local sync after GitHub merge:
>   git checkout main                   # Switch to local main (still at C)
>   git pull origin main               # Downloads F from GitHub
>   # Now your local main includes the merge commit F
>   
>   # 4. Continued work creates G:
>   # If anyone (you or teammates) makes more commits after F,
>   # those become G, H, I, etc. - G is just "next commit after merge"
>   
>   # Alternative GitHub merge strategies:
>   # "Squash and merge" → Combines D+E into single commit (no F, direct to G)
>   # "Rebase and merge" → Replays D+E on main (no merge commit F)
>   ```
>
>   
>
>   **💻 Practical Example - Complete Workflow:**
>   ```bash
>   # You're at E, want to merge to main and continue working:
>   
>   # 1. Merge feature to main (creates F):
>   git checkout main                   # Go to main
>   git merge feature                   # F = merge commit created
>   git push origin main               # Push F to GitHub
>   
>   # 2. Continue working (creates G):
>   echo "new feature" >> app.js       # Make changes
>   git add app.js                     # Stage changes  
>   git commit -m "Add new feature"    # G = new commit after merge
>   git push origin main               # Push G to GitHub
>   
>   # Final state: main has C-F-G, where F merged in D+E
>   ```
>
> </div>
>
> **🔍 Local Git Commands - Navigating Commit History:**
> ```bash
> git log --oneline                    # See all commits with their hash IDs
> git checkout <commit-hash>           # Go to specific commit (like commit "C")
> git checkout main                    # Return to latest main (commit "G")
> git show <commit-hash>               # View what changed in specific commit
> git diff A..C                        # Compare between commit A and C
> ```
>
> **🌐 GitHub Web Interface - Viewing Historical States:**
> - **View commits:** GitHub → Repository → Commits tab → Click any commit
> - **Browse code at commit:** Click commit hash → "Browse files" button  
> - **Compare commits:** GitHub → Compare → Select commit ranges (A...C)
> - **Download at commit:** Click commit → "Browse files" → Green "Code" button → Download ZIP
>
> **🔄 Setting Your Working Directory to Specific Commit States:**
> ```bash
> # To get to commit "C" state (before feature branch merge):
> git checkout main                    # Switch to main branch
> git reset --hard <commit-C-hash>     # Move main pointer to commit C
> git log --oneline                    # Verify you're at commit C
>
> # To get to commit "E" state (latest feature work):
> git checkout feature                 # Switch to feature branch  
> git log --oneline                    # See you're at commit E
>
> # To get to commit "G" state (after merge, latest):
> git checkout main                    # Switch to main branch
> git pull origin main                 # Get latest from remote (includes merge)
> 
> # SAFE exploration (doesn't change your branches):
> git checkout <any-commit-hash>       # Explore any commit safely
> git checkout -                       # Return to your previous branch
> ```

---

## Basic Branch Operations

### Creating Branches
```bash
# Create new branch (doesn't switch to it)
git branch feature-branch

# Create and switch to new branch
git checkout -b feature-branch

# Modern syntax (Git 2.23+)
git switch -c feature-branch

# Create branch from specific commit
git checkout -b hotfix commit-hash

# Create branch from another branch
git checkout -b feature-v2 feature-v1
```

### Switching Between Branches
```bash
# Switch to existing branch
git checkout main
git switch main  # Modern syntax

# Switch to previous branch
git checkout -
git switch -

# Switch and create if doesn't exist
git checkout -b new-feature
git switch -c new-feature
```

### Listing Branches
```bash
# List local branches
git branch

# List all branches (local and remote)
git branch -a

# List remote branches only
git branch -r

# Show branch with last commit
git branch -v

# Show merged branches
git branch --merged

# Show unmerged branches
git branch --no-merged
```

### Deleting Branches
```bash
# Delete merged branch (safe)
git branch -d feature-branch

# Force delete branch (even if unmerged)
git branch -D feature-branch

# Delete remote branch
git push origin --delete feature-branch
git push origin :feature-branch  # Alternative syntax

# Clean up remote tracking branches
git remote prune origin
```

---

## Branching Strategies

### 1. Git Flow
**Structure:**
- `main`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: Individual features
- `release/*`: Release preparation
- `hotfix/*`: Critical fixes

```bash
# Start new feature
git checkout develop
git checkout -b feature/user-authentication

# Work on feature
git add .
git commit -m "Add user login functionality"

# Finish feature
git checkout develop
git merge --no-ff feature/user-authentication
git branch -d feature/user-authentication

# Create release branch
git checkout -b release/1.2.0 develop

# Finish release
git checkout main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "Release version 1.2.0"
git checkout develop
git merge --no-ff release/1.2.0
git branch -d release/1.2.0
```

### 2. GitHub Flow
**Structure:**
- `main`: Always deployable
- `feature/*`: Feature branches

```bash
# Create feature branch
git checkout main
git pull origin main
git checkout -b feature/add-search

# Work and push frequently
git add .
git commit -m "Add search functionality"
git push origin feature/add-search

# Create pull request (via GitHub)
# After review and merge, cleanup
git checkout main
git pull origin main
git branch -d feature/add-search
```

### 3. GitLab Flow
**Structure:**
- `main`: Development
- `production`: Production-ready
- Environment branches for staging

```bash
# Work on main
git checkout main
git add .
git commit -m "Add new feature"

# Deploy to staging
git checkout staging
git merge main

# Deploy to production
git checkout production
git merge staging
```

---

## Merging Techniques

### 1. Fast-Forward Merge
When target branch hasn't diverged:
```bash
# Before merge:
main:    A---B---C
              \
feature:       D---E

# Fast-forward merge
git checkout main
git merge feature

# After merge:
main:    A---B---C---D---E
```

### 2. Three-Way Merge
When branches have diverged:
```bash
# Before merge:
main:    A---B---C---F
              \     /
feature:       D---E

# Three-way merge
git checkout main
git merge feature

# After merge (creates merge commit):
main:    A---B---C---F---G
              \         /
feature:       D---E---
```

### 3. Squash Merge
Combines all commits into one:
```bash
# Squash merge
git checkout main
git merge --squash feature
git commit -m "Add complete feature implementation"

# Result: All feature commits become one commit
```

### 4. Rebase and Merge
Creates linear history:
```bash
# Rebase feature branch
git checkout feature
git rebase main

# Then merge (will be fast-forward)
git checkout main
git merge feature
```

---

## Advanced Merging

### Interactive Rebase
```bash
# Rebase last 3 commits interactively
git rebase -i HEAD~3

# Options in interactive rebase:
# pick - use commit as is
# reword - change commit message
# edit - modify commit
# squash - combine with previous commit
# fixup - like squash but discard message
# drop - remove commit
```

### Cherry-Pick
Apply specific commits from other branches:
```bash
# Apply specific commit
git cherry-pick commit-hash

# Apply multiple commits
git cherry-pick commit1 commit2 commit3

# Apply commit range
git cherry-pick start-commit..end-commit

# Cherry-pick without committing
git cherry-pick --no-commit commit-hash
```

### Merge vs Rebase Decision Matrix

| Scenario | Use Merge | Use Rebase |
|----------|-----------|------------|
| Public branches | ✅ | ❌ |
| Feature integration | ✅ | ✅ |
| Preserve context | ✅ | ❌ |
| Clean linear history | ❌ | ✅ |
| Multiple developers | ✅ | ❌ |
| Local cleanup | ❌ | ✅ |

---

## Handling Merge Conflicts

### Understanding Conflicts
Conflicts occur when Git can't automatically merge changes:
```
<<<<<<< HEAD
Current branch content
=======
Incoming branch content
>>>>>>> feature-branch
```

### Resolving Conflicts Step-by-Step

#### 1. Identify Conflicted Files
```bash
# Start merge
git merge feature-branch

# Check status
git status
# Shows files with conflicts

# View conflicted content
git diff
```

#### 2. Resolve Conflicts Manually
```bash
# Edit conflicted files
nano conflicted-file.txt

# Remove conflict markers and choose content:
# - Keep current (HEAD) version
# - Keep incoming version
# - Combine both versions
# - Write completely new content
```

#### 3. Complete the Merge
```bash
# After resolving all conflicts
git add resolved-file.txt
git commit -m "Resolve merge conflict in file.txt"
```

### Conflict Resolution Tools
```bash
# Configure merge tool
git config --global merge.tool vimdiff

# Use merge tool
git mergetool

# Available tools: vimdiff, meld, kdiff3, Beyond Compare
```

### Abort Merge
```bash
# If you want to cancel merge
git merge --abort

# Or during rebase
git rebase --abort
```

---

## Branch Management Best Practices

### Naming Conventions
```bash
# Feature branches
feature/user-authentication
feature/payment-processing
feat/add-search

# Bug fixes
bugfix/login-error
fix/memory-leak
hotfix/critical-security-patch

# Release branches
release/v1.2.0
release/2023-q4

# Experimental
experiment/new-ui
spike/performance-test
```

### Branch Lifecycle Management
```bash
# Regular maintenance
# 1. List old branches
git for-each-ref --format='%(refname:short) %(committerdate)' refs/heads | sort -k2

# 2. Clean merged branches
git branch --merged main | grep -v main | xargs -n 1 git branch -d

# 3. Update all branches
git fetch --all --prune
```

### Protecting Important Branches
GitHub/GitLab branch protection rules:
- Require pull request reviews
- Require status checks
- Require branches to be up to date
- Restrict who can push
- Require signed commits

---

## DevOps Branching Workflows

### Continuous Integration Workflow
```bash
# 1. Feature development
git checkout -b feature/new-api
# ... develop and test locally ...
git push origin feature/new-api

# 2. Automated CI runs on push
# 3. Create pull request
# 4. Code review
# 5. Merge after approval and CI passes

# 6. Automatic deployment from main
git checkout main
git pull origin main
# ... automated deployment triggers ...
```

### Release Management Workflow
```bash
# 1. Create release branch
git checkout -b release/v2.1.0 develop

# 2. Final testing and bug fixes
git commit -m "Fix release blocker"

# 3. Tag and merge to main
git checkout main
git merge release/v2.1.0
git tag -a v2.1.0 -m "Release version 2.1.0"

# 4. Merge back to develop
git checkout develop
git merge release/v2.1.0

# 5. Deploy tagged version
git push origin main --tags
```

### Hotfix Workflow
```bash
# 1. Create hotfix from main
git checkout main
git checkout -b hotfix/security-patch

# 2. Fix critical issue
git commit -m "Fix security vulnerability"

# 3. Test and merge immediately
git checkout main
git merge hotfix/security-patch
git tag -a v2.1.1 -m "Security hotfix"

# 4. Merge to develop
git checkout develop
git merge hotfix/security-patch

# 5. Deploy immediately
git push origin main --tags
```

---

## Troubleshooting Branch Issues

### Issue 1: Diverged Branches
```bash
# When branches have diverged significantly
git checkout feature-branch
git rebase main

# If conflicts occur
git rebase --continue  # after resolving each conflict
git rebase --abort     # to cancel rebase
```

### Issue 2: Lost Commits
```bash
# Find lost commits
git reflog

# Recover lost branch
git checkout -b recovered-branch commit-hash

# Or reset to lost commit
git reset --hard commit-hash
```

### Issue 3: Wrong Branch
```bash
# Committed to wrong branch
git log --oneline -1  # note the commit hash

# Reset current branch
git reset --hard HEAD~1

# Switch to correct branch and cherry-pick
git checkout correct-branch
git cherry-pick commit-hash
```

### Issue 4: Large Binary Files
```bash
# Remove large file from history
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch large-file.bin' \
  --prune-empty --tag-name-filter cat -- --all

# Or use git-filter-repo (modern alternative)
git filter-repo --path large-file.bin --invert-paths
```

---

## Advanced Branch Techniques

### Worktrees
Work on multiple branches simultaneously:
```bash
# Create worktree for different branch
git worktree add ../project-feature feature-branch

# List worktrees
git worktree list

# Remove worktree
git worktree remove ../project-feature
```

### Subtrees
Include another repository as subdirectory:
```bash
# Add subtree
git subtree add --prefix=vendor/library \
  https://github.com/user/library.git main --squash

# Update subtree
git subtree pull --prefix=vendor/library \
  https://github.com/user/library.git main --squash
```

### Submodules
Link to external repositories:
```bash
# Add submodule
git submodule add https://github.com/user/library.git vendor/library

# Initialize submodules
git submodule init
git submodule update

# Or in one command
git submodule update --init --recursive
```

---

## Testing and Validation

### Pre-merge Testing
```bash
# Test merge without actually merging
git merge --no-commit --no-ff feature-branch
git diff --cached
git reset --merge

# Check if merge is possible
git merge-tree $(git merge-base HEAD feature) HEAD feature
```

### Branch Comparison
```bash
# Compare branches
git diff main..feature-branch
git log main..feature-branch

# Show commits in feature not in main
git log main..feature-branch --oneline

# Show commits in main not in feature
git log feature-branch..main --oneline
```

---

## Automation and Scripting

### Automated Branch Cleanup
```bash
#!/bin/bash
# cleanup-branches.sh

# Delete merged feature branches
git branch --merged main | grep "feature/" | xargs -n 1 git branch -d

# Delete remote tracking branches that no longer exist
git remote prune origin

# List branches older than 30 days
git for-each-ref --format='%(refname:short) %(committerdate)' refs/heads | \
  awk '$2 < "'$(date -d'30 days ago' -I)'"' | cut -d' ' -f1
```

### Pre-commit Hooks
```bash
#!/bin/bash
# .git/hooks/pre-commit

# Prevent commits to main
if [ "$(git branch --show-current)" = "main" ]; then
  echo "Direct commits to main are not allowed"
  exit 1
fi

# Run tests before commit
npm test
if [ $? -ne 0 ]; then
  echo "Tests failed. Commit aborted."
  exit 1
fi
```

---

## Next Steps

After mastering branching and merging:

1. **Learn GitHub/GitLab workflows** → See `../Github_Workflows/`
2. **Practice collaboration strategies** → See `../Collaboration_Strategies/`
3. **Set up automated testing** → See CI/CD modules
4. **Explore advanced Git topics** → hooks, custom workflows

---

## 🔧 Configuration Notes

- **Merge Strategy**: Configure default merge behavior for your team
- **Branch Protection**: Set up branch protection rules on remote repositories
- **Automated Testing**: Integrate branch testing with CI/CD pipelines
- **Code Review**: Establish mandatory code review processes

## 📚 Additional Resources

### Essential Reading
- ["The Lean Startup" by Eric Ries](https://www.amazon.com/Lean-Startup-Entrepreneurs-Continuous-Innovation/dp/0307887898) - Feature branching and continuous integration principles
- ["Team of Teams" by General Stanley McChrystal](https://www.amazon.com/Team-Teams-Rules-Engagement-Complex/dp/1591847486) - Managing complex branching strategies across teams
- ["The Five Dysfunctions of a Team" by Patrick Lencioni](https://www.amazon.com/Five-Dysfunctions-Team-Leadership-Fable/dp/0787960756) - Conflict resolution in code merging
- ["Fearless Change" by Linda Rising and Mary Lynn Manns](https://www.amazon.com/Fearless-Change-Patterns-Introducing-Ideas/dp/0201755920) - Implementing new branching strategies

### Videos
- [Git Branching and Merging (Programming with Mosh)](https://www.youtube.com/watch?v=S2TUommS3O0) - Complete branching guide
- [Git Flow vs GitHub Flow (GitKraken)](https://www.youtube.com/watch?v=gW6dFpTMk8s) - Branching strategy comparison
- [Merge Conflicts Resolution (The Net Ninja)](https://www.youtube.com/watch?v=g8BRcB9NLp4) - Practical conflict resolution
- [Advanced Git Branching (Academind)](https://www.youtube.com/watch?v=FyAAIHHClqI) - Professional branching techniques

### Guides
- [Git Branching - Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
- [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html)
- [Atlassian Git Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)
- [Microsoft Git Branching Guide](https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-guidance)

### Articles
- [Git Branching Strategies](https://www.flagship.io/git-branching-strategies/)
- [Feature Branch Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow)
- [Git Merge vs Rebase](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
- [Branch Protection Rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/about-protected-branches)

### Cultural Assessment Tools
- [Branching Strategy Maturity Model](https://www.devops-culture.com/assessment/)
- [Code Integration Assessment](https://continuousdelivery.com/implementing/patterns/)

### Communities and Events
- [Git Flow Community](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Community Discussions](https://github.com/community/community)
- [DevOps Branching Strategies Meetups](https://www.meetup.com/topics/devops/)
- [Continuous Integration Events](https://continuousdelivery.com/events/) 