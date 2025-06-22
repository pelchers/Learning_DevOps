# Git Installation and Setup

## 📖 What This File Does
This guide covers Git installation, initial configuration, and basic setup for DevOps workflows. It establishes the foundation for all version control operations.

## 🎯 Learning Objectives
- Install Git on different operating systems
- Configure Git with user credentials and preferences
- Understand Git configuration levels (global, local, system)
- Set up SSH keys for secure authentication
- Configure Git for optimal DevOps workflows

## 📋 Prerequisites
- Basic understanding of command-line operations
- Administrative access to your system
- Internet connection for downloads

---

## Git Installation

### Windows Installation

#### Method 1: Git for Windows (Recommended)
```powershell
# Download from: https://git-scm.com/download/win
# Or using Chocolatey
choco install git

# Or using Windows Package Manager
winget install --id Git.Git -e --source winget
```

#### Method 2: GitHub Desktop (GUI Option)
```powershell
# Download from: https://desktop.github.com/
# Includes Git CLI automatically
```

### Linux Installation

#### Ubuntu/Debian
```bash
# Update package index
sudo apt update

# Install Git
sudo apt install git

# Verify installation
git --version
```

#### CentOS/RHEL/Fedora
```bash
# CentOS/RHEL
sudo yum install git

# Fedora
sudo dnf install git

# Verify installation
git --version
```

### macOS Installation

#### Method 1: Homebrew (Recommended)
```bash
# Install Homebrew if not installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Git
brew install git
```

#### Method 2: Xcode Command Line Tools
```bash
# Install Xcode Command Line Tools
xcode-select --install
```

---

## Initial Git Configuration

### Essential User Configuration
```bash
# Set your name (required for commits)
git config --global user.name "Your Full Name"

# Set your email (required for commits)
git config --global user.email "your.email@example.com"

# Verify configuration
git config --list --global
```

### Core Configuration Settings
```bash
# Set default branch name to 'main'
git config --global init.defaultBranch main

# Set default editor (choose one)
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "nano"         # Nano
git config --global core.editor "vim"          # Vim

# Enable colored output
git config --global color.ui auto

# Set line ending handling
git config --global core.autocrlf true   # Windows
git config --global core.autocrlf input  # Linux/macOS
```

### Advanced DevOps Configuration
```bash
# Set up merge strategy
git config --global merge.tool vimdiff

# Configure push behavior
git config --global push.default simple

# Enable rerere (reuse recorded resolution)
git config --global rerere.enabled true

# Set up aliases for common commands
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
```

---

## SSH Key Setup for Secure Authentication

### Generate SSH Key Pair
```bash
# Generate new SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# If ed25519 is not supported, use RSA
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Start SSH agent
eval "$(ssh-agent -s)"

# Add SSH key to agent
ssh-add ~/.ssh/id_ed25519
# OR for RSA
ssh-add ~/.ssh/id_rsa
```

### Add SSH Key to GitHub/GitLab
```bash
# Copy public key to clipboard (Linux)
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard

# Copy public key to clipboard (macOS)
pbcopy < ~/.ssh/id_ed25519.pub

# Copy public key to clipboard (Windows)
type $env:USERPROFILE\.ssh\id_ed25519.pub | clip
```

**Manual Steps:**
1. Go to GitHub/GitLab Settings → SSH Keys
2. Click "New SSH Key"
3. Paste the public key content
4. Give it a descriptive title
5. Click "Add SSH Key"

### Test SSH Connection
```bash
# Test GitHub connection
ssh -T git@github.com

# Test GitLab connection
ssh -T git@gitlab.com

# Expected response for GitHub:
# Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Git Configuration Levels

### Understanding Configuration Hierarchy
```bash
# System-wide configuration (affects all users)
git config --system user.name "System User"

# Global configuration (affects current user)
git config --global user.name "Global User"

# Local configuration (affects current repository only)
git config --local user.name "Local User"

# View configuration from all levels
git config --list --show-origin
```

### Configuration File Locations
```bash
# System configuration
# Windows: C:\Program Files\Git\etc\gitconfig
# Linux/macOS: /etc/gitconfig

# Global configuration
# Windows: C:\Users\{username}\.gitconfig
# Linux/macOS: ~/.gitconfig

# Local configuration
# {repository}/.git/config
```

---

## DevOps-Specific Git Configuration

### Commit Signing Setup
```bash
# Generate GPG key for commit signing
gpg --full-generate-key

# List GPG keys
gpg --list-secret-keys --keyid-format LONG

# Configure Git to use GPG key
git config --global user.signingkey {KEY_ID}
git config --global commit.gpgsign true

# Export public key for GitHub/GitLab
gpg --armor --export {KEY_ID}
```

### Git Hooks Configuration
```bash
# Set global hooks directory
git config --global core.hooksPath ~/.git-hooks

# Create hooks directory
mkdir -p ~/.git-hooks

# Make hooks executable
chmod +x ~/.git-hooks/*
```

### Large File Storage (LFS) Setup
```bash
# Install Git LFS
git lfs install

# Track large file types
git lfs track "*.zip"
git lfs track "*.tar.gz"
git lfs track "*.exe"
git lfs track "*.dmg"

# Verify LFS configuration
git lfs env
```

---

## Verification and Testing

### Verify Git Installation
```bash
# Check Git version
git --version

# Check Git configuration
git config --list

# Check specific configuration
git config user.name
git config user.email
```

### Create Test Repository
```bash
# Create test directory
mkdir git-test && cd git-test

# Initialize Git repository
git init

# Create test file
echo "Hello, Git!" > test.txt

# Add and commit
git add test.txt
git commit -m "Initial commit"

# Check repository status
git status
git log --oneline
```

---

## Common Configuration Issues and Solutions

### Issue 1: Permission Denied (SSH)
```bash
# Check SSH key permissions
ls -la ~/.ssh/

# Fix permissions if needed
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

### Issue 2: Line Ending Problems
```bash
# For mixed development environments
git config --global core.autocrlf true

# Check current setting
git config core.autocrlf
```

### Issue 3: Credential Issues
```bash
# Clear stored credentials (Windows)
git config --global --unset credential.helper

# Set up credential manager (Windows)
git config --global credential.helper manager-core

# Clear credential cache (Linux/macOS)
git config --global --unset credential.helper
```

---

## Best Practices

### 🔧 Configuration Best Practices
1. **Always set user.name and user.email** before first commit
2. **Use SSH keys** instead of HTTPS for authentication
3. **Enable commit signing** for security
4. **Set up meaningful aliases** for common commands
5. **Configure appropriate line ending handling** for your OS

### 🚨 Security Best Practices
1. **Never commit credentials** or sensitive information
2. **Use SSH keys with passphrases**
3. **Regularly rotate SSH keys**
4. **Enable two-factor authentication** on Git hosting platforms
5. **Keep Git updated** to latest version

### 💡 DevOps Integration Tips
1. **Configure Git hooks** for automated testing
2. **Set up consistent configuration** across team
3. **Use Git LFS** for large binary files
4. **Configure merge strategies** for automated workflows
5. **Set up global .gitignore** for common files

---

## Next Steps

After completing this setup:

1. **Practice basic Git commands** → See `02-Git_Commands_And_Workflow.md`
2. **Learn branching strategies** → See `03-Branching_And_Merging.md`
3. **Set up GitHub workflows** → See `../Github_Workflows/`
4. **Explore collaboration strategies** → See `../Collaboration_Strategies/`

---

## 🔧 Configuration Notes

- **SSH Key Security**: Always use a passphrase for SSH keys in production
- **Configuration Sync**: Consider using dotfiles repository for consistent setup
- **Team Standards**: Establish team-wide Git configuration standards
- **Regular Updates**: Keep Git updated for security and new features

## 📚 Additional Resources

### Essential Reading
- ["The Lean Startup" by Eric Ries](https://www.amazon.com/Lean-Startup-Entrepreneurs-Continuous-Innovation/dp/0307887898) - Essential for understanding modern software development practices
- ["Team of Teams" by General Stanley McChrystal](https://www.amazon.com/Team-Teams-Rules-Engagement-Complex/dp/1591847486) - Collaboration and adaptation in complex environments
- ["The Five Dysfunctions of a Team" by Patrick Lencioni](https://www.amazon.com/Five-Dysfunctions-Team-Leadership-Fable/dp/0787960756) - Building effective development teams
- ["Fearless Change" by Linda Rising and Mary Lynn Manns](https://www.amazon.com/Fearless-Change-Patterns-Introducing-Ideas/dp/0201755920) - Introducing new tools and practices

### Videos
- [Git and GitHub for Beginners - Crash Course (freeCodeCamp)](https://www.youtube.com/watch?v=RGOj5yH7evk) - Comprehensive Git tutorial
- [Advanced Git Tutorial (Atlassian)](https://www.youtube.com/watch?v=0SJCYPunk_U) - Deep dive into Git workflows
- [Git Branching Strategies (GitKraken)](https://www.youtube.com/watch?v=gm-2DMlb4zQ) - Visual guide to branching
- [SSH Keys Explained (NetworkChuck)](https://www.youtube.com/watch?v=dPAw4opzN9g) - Security best practices

### Guides
- [Git Official Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book)
- [GitHub SSH Guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [GitLab SSH Guide](https://docs.gitlab.com/ee/ssh/)
- [Git Configuration Reference](https://git-scm.com/docs/git-config)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)

### Articles
- [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Git Best Practices](https://sethrobertson.github.io/GitBestPractices/)
- [SSH Key Management Best Practices](https://goteleport.com/blog/how-to-ssh-properly/)

### Cultural Assessment Tools
- [DevOps Culture Assessment](https://www.devops-culture.com/assessment/)
- [Team Health Check (Spotify Model)](https://labs.spotify.com/2014/09/16/squad-health-check-model/)

### Communities and Events
- [Git User Community](https://git-scm.com/community)
- [GitHub Community](https://github.community/)
- [Local Git Meetups](https://www.meetup.com/topics/git/)
- [DevOps Days Events](https://devopsdays.org/) 