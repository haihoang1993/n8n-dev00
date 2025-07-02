# 🌿 n8n Git Workflow Guide - Hướng dẫn Git Workflow

## 📋 Tổng quan về Git Workflow

Trong dự án n8n, chúng ta sử dụng **Git Flow** kết hợp với **GitHub Flow** để quản lý code một cách hiệu quả và an toàn. Document này sẽ hướng dẫn bạn các best practices và workflows chuẩn.

### 🎯 Mục tiêu của Git Workflow

- **Đảm bảo code quality** thông qua review process
- **Tránh conflicts** và integration issues
- **Maintain stable main branch** luôn có thể deploy
- **Track changes** và history một cách rõ ràng
- **Collaborate effectively** trong team

---

## 🌿 Branch Strategy

### **Main Branches**

```
master (main)     ← Production-ready code
├── develop       ← Integration branch (if using Git Flow)
└── feature/*     ← Feature development branches
```

### **Branch Types**

| **Branch Type** | **Naming Convention** | **Purpose** | **Merge Target** |
|-----------------|----------------------|-------------|------------------|
| **Feature** | `feature/add-custom-node` | New features | `master` |
| **Bugfix** | `bugfix/fix-auth-issue` | Bug fixes | `master` |
| **Hotfix** | `hotfix/critical-security-fix` | Critical fixes | `master` |
| **Chore** | `chore/update-dependencies` | Maintenance tasks | `master` |
| **Docs** | `docs/update-readme` | Documentation | `master` |

### **Branch Naming Rules**

```bash
# ✅ Good examples
feature/add-telegram-node
bugfix/fix-webhook-timeout
hotfix/security-vulnerability
chore/update-typescript
docs/api-documentation

# ❌ Bad examples
my-feature
fix
update
temp-branch
```

---

## 🚀 Development Workflow

### **1. Setup Repository**

```bash
# Fork n8n repository (if contributing to main project)
# Go to https://github.com/n8n-io/n8n and click "Fork"

# Clone your fork
git clone https://github.com/YOUR_USERNAME/n8n.git
cd n8n

# Add upstream remote
git remote add upstream https://github.com/n8n-io/n8n.git

# Verify remotes
git remote -v
# origin    https://github.com/YOUR_USERNAME/n8n.git (fetch)
# origin    https://github.com/YOUR_USERNAME/n8n.git (push)
# upstream  https://github.com/n8n-io/n8n.git (fetch)
# upstream  https://github.com/n8n-io/n8n.git (push)
```

### **2. Start New Feature**

```bash
# Ensure you're on master and up to date
git checkout master
git pull upstream master

# Create and switch to feature branch
git checkout -b feature/add-awesome-node

# Push branch to origin
git push -u origin feature/add-awesome-node
```

### **3. Development Process**

```bash
# Make your changes
# Edit files, add features, fix bugs

# Stage changes
git add .

# Commit with descriptive message
git commit -m "feat: add awesome node for API integration

- Implement execute method with API calls
- Add proper error handling
- Include unit tests
- Update documentation"

# Push changes regularly
git push origin feature/add-awesome-node
```

### **4. Keep Branch Updated**

```bash
# Regularly sync with upstream master
git checkout master
git pull upstream master
git checkout feature/add-awesome-node
git rebase master

# Or merge if you prefer (less clean history)
git merge master

# Push updated branch
git push --force-with-lease origin feature/add-awesome-node
```

### **5. Create Pull Request**

```bash
# Push final changes
git push origin feature/add-awesome-node

# Go to GitHub and create Pull Request
# Fill out PR template with:
# - Description of changes
# - Testing instructions
# - Screenshots if UI changes
# - Breaking changes if any
```

---

## 📝 Commit Message Standards

### **Conventional Commits Format**

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### **Commit Types**

| **Type** | **Description** | **Example** |
|----------|-----------------|-------------|
| `feat` | New feature | `feat: add Telegram node` |
| `fix` | Bug fix | `fix: resolve webhook timeout issue` |
| `docs` | Documentation | `docs: update API reference` |
| `style` | Code style changes | `style: fix ESLint warnings` |
| `refactor` | Code refactoring | `refactor: simplify auth logic` |
| `test` | Add/update tests | `test: add unit tests for HTTP node` |
| `chore` | Maintenance tasks | `chore: update dependencies` |
| `perf` | Performance improvements | `perf: optimize workflow execution` |
| `ci` | CI/CD changes | `ci: add GitHub Actions workflow` |
| `build` | Build system changes | `build: update webpack config` |

### **Good Commit Examples**

```bash
# Feature addition
git commit -m "feat(nodes): add Slack node with message sending

- Implement execute method for sending messages
- Add credential configuration for Slack API
- Include proper error handling and validation
- Add unit tests and documentation

Closes #1234"

# Bug fix
git commit -m "fix(core): resolve memory leak in workflow execution

- Fix event listener cleanup in WorkflowExecute
- Add proper disposal of resources
- Update tests to verify fix

Fixes #5678"

# Documentation
git commit -m "docs: update custom node development guide

- Add examples for trigger nodes
- Clarify credential setup process
- Fix typos and improve formatting"
```

### **Bad Commit Examples**

```bash
# ❌ Too vague
git commit -m "fix bug"
git commit -m "update"
git commit -m "changes"

# ❌ Too long subject line
git commit -m "feat: add new super awesome node that integrates with multiple APIs and provides extensive functionality for data processing"

# ❌ Wrong format
git commit -m "Added new feature"
git commit -m "FIXED: bug in auth"
```

---

## 🔍 Code Review Process

### **Before Creating PR**

```bash
# Self-review checklist
- [ ] Code follows style guidelines
- [ ] All tests pass locally
- [ ] No console.log or debug code
- [ ] Documentation updated if needed
- [ ] Breaking changes documented
- [ ] Commit messages follow convention
```

### **PR Template**

```markdown
## Description
Brief description of changes and motivation.

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
Include screenshots for UI changes.

## Checklist
- [ ] My code follows the style guidelines
- [ ] I have performed a self-review
- [ ] I have commented my code where necessary
- [ ] My changes generate no new warnings
- [ ] New and existing unit tests pass
```

### **Review Guidelines**

#### **For Authors**
- Keep PRs small and focused
- Provide clear description and context
- Respond to feedback promptly
- Test changes thoroughly

#### **For Reviewers**
- Review code logic and architecture
- Check for potential bugs and edge cases
- Verify tests are adequate
- Ensure code style consistency
- Be constructive in feedback

---

## 🛠️ Advanced Git Techniques

### **Interactive Rebase**

```bash
# Clean up commit history before PR
git rebase -i HEAD~3

# In the editor:
pick a1b2c3d feat: add basic functionality
squash e4f5g6h fix: typo in variable name
squash h7i8j9k docs: update comments

# Result: Clean single commit
```

### **Cherry Picking**

```bash
# Apply specific commit to another branch
git checkout hotfix/critical-fix
git cherry-pick abc123def456

# Apply multiple commits
git cherry-pick abc123..def456
```

### **Stashing Work**

```bash
# Save current work temporarily
git stash push -m "WIP: working on new feature"

# List stashes
git stash list

# Apply stash
git stash pop

# Apply specific stash
git stash apply stash@{1}
```

### **Conflict Resolution**

```bash
# When merge conflicts occur
git status  # See conflicted files

# Edit files to resolve conflicts
# Look for <<<<<<< ======= >>>>>>> markers

# After resolving
git add resolved-file.ts
git commit -m "resolve: merge conflicts in auth module"
```

---

## 🔧 Git Configuration

### **Global Configuration**

```bash
# Set user information
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default editor
git config --global core.editor "code --wait"

# Set default branch name
git config --global init.defaultBranch main

# Enable helpful colors
git config --global color.ui auto

# Set up aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
```

### **Project-specific Configuration**

```bash
# In n8n project directory
cd n8n

# Set up hooks
cp .githooks/pre-commit .git/hooks/
chmod +x .git/hooks/pre-commit

# Configure line endings (important for cross-platform)
git config core.autocrlf input  # On Linux/Mac
git config core.autocrlf true   # On Windows
```

### **Useful Git Aliases**

```bash
# Add to ~/.gitconfig
[alias]
    # Logs
    lg = log --oneline --decorate --graph --all
    lol = log --graph --decorate --pretty=oneline --abbrev-commit
    lola = log --graph --decorate --pretty=oneline --abbrev-commit --all
    
    # Status and diff
    st = status -s
    df = diff
    dc = diff --cached
    
    # Branch management
    br = branch
    co = checkout
    cob = checkout -b
    
    # Commit shortcuts
    ci = commit
    cm = commit -m
    ca = commit --amend
    
    # Stash shortcuts
    sl = stash list
    sa = stash apply
    ss = stash save
    
    # Remote operations
    pu = push
    pl = pull
    fo = fetch origin
    fu = fetch upstream
    
    # Rebase shortcuts
    rb = rebase
    rbi = rebase -i
    rbc = rebase --continue
    rba = rebase --abort
```

---

## 🧪 Testing Integration

### **Pre-commit Hooks**

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running pre-commit checks..."

# Run linting
npm run lint
if [ $? -ne 0 ]; then
    echo "❌ Linting failed. Please fix errors before committing."
    exit 1
fi

# Run tests
npm run test:unit
if [ $? -ne 0 ]; then
    echo "❌ Tests failed. Please fix failing tests before committing."
    exit 1
fi

# Check TypeScript compilation
npm run type-check
if [ $? -ne 0 ]; then
    echo "❌ TypeScript compilation failed."
    exit 1
fi

echo "✅ All pre-commit checks passed!"
```

### **GitHub Actions Integration**

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [ master, develop ]
  pull_request:
    branches: [ master ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'pnpm'
    
    - name: Install dependencies
      run: pnpm install
    
    - name: Run linting
      run: pnpm lint
    
    - name: Run tests
      run: pnpm test
    
    - name: Build project
      run: pnpm build
```

---

## 🚨 Common Issues & Solutions

### **Merge Conflicts**

```bash
# Problem: Merge conflicts during rebase/merge
# Solution: Resolve conflicts step by step

# 1. Identify conflicted files
git status

# 2. Open files and resolve conflicts
# Look for conflict markers: <<<<<<<, =======, >>>>>>>

# 3. Stage resolved files
git add resolved-file.ts

# 4. Continue rebase/merge
git rebase --continue
# or
git merge --continue
```

### **Accidentally Committed to Wrong Branch**

```bash
# Problem: Made commits on master instead of feature branch
# Solution: Move commits to correct branch

# 1. Create new branch from current position
git branch feature/my-feature

# 2. Reset master to previous position
git checkout master
git reset --hard HEAD~2  # Go back 2 commits

# 3. Switch to feature branch
git checkout feature/my-feature
```

### **Large File Issues**

```bash
# Problem: Accidentally committed large files
# Solution: Remove from history

# 1. Remove file from repository
git rm --cached large-file.zip

# 2. Add to .gitignore
echo "*.zip" >> .gitignore
git add .gitignore

# 3. Commit the removal
git commit -m "remove: large file and update gitignore"

# 4. For files already in history, use git filter-branch or BFG
```

### **Detached HEAD State**

```bash
# Problem: Accidentally in detached HEAD state
# Solution: Create branch or return to existing branch

# Option 1: Create new branch from current state
git checkout -b recovery-branch

# Option 2: Return to master and lose changes
git checkout master
```

---

## 📊 Git Workflow Metrics

### **Tracking Development**

```bash
# See commit activity
git log --oneline --since="1 month ago" --author="Your Name"

# Count commits by author
git shortlog -sn

# See branch activity
git for-each-ref --format='%(refname:short) %(committerdate)' refs/heads | sort -k2

# File change frequency
git log --name-only --pretty=format: | sort | uniq -c | sort -rn
```

### **Code Quality Metrics**

```bash
# Lines of code by author
git log --author="Your Name" --pretty=tformat: --numstat | \
awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'

# Most changed files
git log --name-only --pretty=format: | sort | uniq -c | sort -rn | head -10
```

---

## 🎯 Best Practices Summary

### **Do's ✅**

- **Commit often** with small, logical changes
- **Write descriptive** commit messages
- **Keep branches** focused on single features
- **Test before** committing
- **Review your own** code before requesting review
- **Rebase** to keep history clean
- **Use meaningful** branch names
- **Document** complex changes

### **Don'ts ❌**

- **Don't commit** broken code
- **Don't mix** unrelated changes in one commit
- **Don't force push** to shared branches
- **Don't commit** sensitive information
- **Don't ignore** merge conflicts
- **Don't use** generic commit messages
- **Don't work** directly on master
- **Don't commit** generated files

---

## 🔗 Resources & Tools

### **Git Tools**

- **[GitKraken](https://www.gitkraken.com/)**: Visual Git client
- **[SourceTree](https://www.sourcetreeapp.com/)**: Free Git GUI
- **[GitHub Desktop](https://desktop.github.com/)**: Simple GitHub integration
- **[Git Extensions](https://gitextensions.github.io/)**: Windows Git GUI

### **VS Code Extensions**

- **GitLens**: Supercharge Git in VS Code
- **Git Graph**: View Git graph and perform actions
- **Git History**: View git log and file history
- **GitHub Pull Requests**: Manage PRs from VS Code

### **Command Line Tools**

- **[hub](https://hub.github.com/)**: GitHub command line tool
- **[gh](https://cli.github.com/)**: Official GitHub CLI
- **[tig](https://jonas.github.io/tig/)**: Text-mode interface for Git

---

**🎉 Chúc bạn làm việc hiệu quả với Git trong n8n development!**

*Git workflow tốt sẽ giúp team collaborate hiệu quả và maintain code quality cao.* 