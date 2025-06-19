# 📝 Bài 5: Advanced Git Operations

## 🎯 Mục tiêu học
- Nắm vững advanced Git commands
- Hiểu về rebasing và history manipulation
- Biết cách sử dụng stash và cherry-pick
- Troubleshooting và recovery techniques

---

## 📖 Lý thuyết

### 1. Git Rebase

#### **Rebase vs Merge:**

**Merge** (creates merge commit):
```
main:    A---B---E---F
              \     /
feature:       C---D
```

**Rebase** (linear history):
```
main:    A---B---E---F---C'---D'
```

#### **Interactive Rebase:**
```bash
# Rebase last 3 commits interactively
git rebase -i HEAD~3

# Rebase from specific commit
git rebase -i abc1234

# Rebase feature branch onto main
git checkout feature-branch
git rebase main
```

#### **Interactive Rebase Options:**
```
Commands:
p, pick = use commit
r, reword = use commit, but edit the commit message
e, edit = use commit, but stop for amending
s, squash = use commit, but meld into previous commit
f, fixup = like "squash", but discard this commit's log message
d, drop = remove commit
```

### 2. Git Stash

#### **Basic Stash Operations:**
```bash
# Save current work
git stash

# Save with message
git stash save "Work in progress on input validation"

# List stashes
git stash list

# Apply latest stash
git stash apply

# Apply specific stash
git stash apply stash@{2}

# Pop (apply and remove) stash
git stash pop

# Delete stash
git stash drop stash@{1}

# Clear all stashes
git stash clear
```

#### **Advanced Stash:**
```bash
# Stash only tracked files
git stash save --keep-index

# Stash including untracked files
git stash save --include-untracked

# Stash specific files
git stash push -m "message" file1.cpp file2.h
```

### 3. Cherry-pick

```bash
# Pick specific commit from another branch
git cherry-pick abc1234

# Pick multiple commits
git cherry-pick abc1234 def5678

# Pick range of commits
git cherry-pick abc1234..def5678

# Pick without committing (for editing)
git cherry-pick --no-commit abc1234
```

---

## 💡 Ví dụ thực tế: Advanced Workflows

### **Scenario 1: Clean Up Messy Commit History**

#### **Problem: Multiple small commits need cleaning**
```bash
git log --oneline
abc1234 Fix typo in comment
def5678 Add validation logic  
ghi9012 Fix compilation error
jkl3456 Add input validation
mno7890 Work in progress
```

#### **Solution: Interactive Rebase**
```bash
# Start interactive rebase
git rebase -i HEAD~5

# In editor, reorganize commits:
pick jkl3456 Add input validation
squash def5678 Add validation logic
squash abc1234 Fix typo in comment
squash ghi9012 Fix compilation error
drop mno7890 Work in progress

# Result: Clean single commit
jkl3456 Add comprehensive input validation with error handling
```

### **Scenario 2: Apply Hotfix to Multiple Branches**

#### **Problem: Critical bug fix needed in multiple versions**
```bash
# Bug found and fixed in main
git checkout main
git commit -m "Fix critical null pointer exception in checkWin()

- Add null pointer validation
- Add bounds checking
- Add unit test for edge case

Fixes #42"

# Need to apply same fix to release branches
git log --oneline -1
abc1234 Fix critical null pointer exception in checkWin()
```

#### **Solution: Cherry-pick to Release Branches**
```bash
# Apply fix to v1.0 release branch
git checkout release/v1.0
git cherry-pick abc1234

# Apply fix to v1.1 release branch  
git checkout release/v1.1
git cherry-pick abc1234

# Tag hotfix releases
git checkout release/v1.0
git tag v1.0.3
git push origin v1.0.3

git checkout release/v1.1
git tag v1.1.1
git push origin v1.1.1
```

### **Scenario 3: Complex Development Workflow**

#### **Problem: Working on multiple features simultaneously**
```bash
# Working on AI improvements
git checkout -b feature/ai-improvements
# ... make changes to AI files ...

# Urgent UI bug reported - need to switch
git stash save "AI improvements - minimax optimization in progress"

# Fix UI bug
git checkout main
git checkout -b hotfix/ui-display-bug
# ... fix bug ...
git commit -m "Fix UI display alignment issue"

# Merge hotfix
git checkout main
git merge hotfix/ui-display-bug
git push origin main

# Return to AI work
git checkout feature/ai-improvements
git stash pop

# Continue working...
```

---

## 🔧 Git History Manipulation

### 1. Amending Commits

#### **Fix last commit:**
```bash
# Add forgotten file to last commit
git add forgotten-file.cpp
git commit --amend

# Change last commit message
git commit --amend -m "New improved commit message"

# Amend without changing message
git commit --amend --no-edit
```

### 2. Reset Operations

#### **Soft Reset (keep changes staged):**
```bash
git reset --soft HEAD~1
# Undoes last commit, keeps changes staged
```

#### **Mixed Reset (default - keep changes unstaged):**
```bash
git reset HEAD~1
# Undoes last commit, unstages changes
```

#### **Hard Reset (discard changes):**
```bash
git reset --hard HEAD~1
# ⚠️ Dangerous: Completely removes last commit and changes
```

### 3. Reflog Recovery

#### **View reflog:**
```bash
git reflog
# Shows history of HEAD movements
```

#### **Recover "lost" commits:**
```bash
# If you accidentally reset too far
git reset --hard abc1234  # Go back to specific reflog entry

# Recover deleted branch
git checkout -b recovered-branch abc1234
```

---

## 🎯 Professional Git Workflows

### 1. Feature Branch với Clean History

```bash
# Start feature
git checkout -b feature/advanced-ai
git commit -m "Add AI difficulty selection UI"
git commit -m "Implement easy AI logic"
git commit -m "Add medium AI heuristics"
git commit -m "Implement minimax for hard AI"

# Clean up before merge
git rebase -i main

# In rebase, squash related commits:
pick abc1234 Add AI difficulty selection UI
squash def5678 Implement easy AI logic  
pick ghi9012 Add medium AI heuristics
squash jkl3456 Implement minimax for hard AI

# Result: 2 clean commits
# 1. Add AI difficulty selection UI with easy mode
# 2. Add medium and hard AI difficulty levels
```

### 2. Collaborative Rebase Workflow

```bash
# Before pushing feature branch
git checkout feature/my-feature
git rebase main  # Get latest changes from main

# If conflicts occur during rebase
# Fix conflicts in each commit during rebase
git add .
git rebase --continue

# Push rebased branch (force with lease for safety)
git push --force-with-lease origin feature/my-feature
```

### 3. Release Branch Management

```bash
# Create release branch
git checkout -b release/v1.2.0 main

# Cherry-pick specific features for release
git cherry-pick feature1-commit
git cherry-pick feature2-commit

# Final release preparation
git commit -m "Bump version to 1.2.0"

# Tag release
git tag -a v1.2.0 -m "Release version 1.2.0

Features:
- Advanced AI with multiple difficulty levels
- Save/load game functionality  
- Enhanced UI with animations
- Comprehensive statistics tracking

Bug fixes:
- Fix input validation edge cases
- Resolve memory leaks in game loop
- Fix win condition detection accuracy"

# Merge to main and push
git checkout main
git merge release/v1.2.0
git push origin main
git push origin v1.2.0
```

---

## 🚨 Git Troubleshooting

### 1. Common Problems và Solutions

#### **Problem: Pushed wrong commit to main**
```bash
# If it's the last commit and no one else pulled yet
git reset --hard HEAD~1
git push --force-with-lease origin main

# If others have pulled, create revert commit
git revert HEAD
git push origin main
```

#### **Problem: Need to split large commit**
```bash
# Reset to before large commit
git reset HEAD~1

# Stage files selectively for multiple commits
git add file1.cpp file1.h
git commit -m "Add file1 functionality"

git add file2.cpp file2.h  
git commit -m "Add file2 functionality"
```

#### **Problem: Wrong branch for commit**
```bash
# Move last commit to different branch
git checkout correct-branch
git cherry-pick wrong-branch
git checkout wrong-branch
git reset --hard HEAD~1
```

### 2. Advanced Recovery Techniques

#### **Find lost commits:**
```bash
# Search for specific text in commits
git log --all --grep="search term"

# Find commits that touched specific file
git log --follow -- path/to/file

# Search in commit content
git log -S "function_name" --source --all
```

#### **Recover from repository corruption:**
```bash
# Check repository integrity
git fsck

# Rebuild corrupted index
git read-tree HEAD
git checkout-index -f -a
git update-index --refresh
```

---

## 📝 Bài tập thực hành

### **Exercise 1: Interactive Rebase**
1. Create branch với 5 messy commits
2. Use interactive rebase to:
   - Squash related commits
   - Reorder commits logically
   - Edit commit messages
   - Drop unnecessary commits

### **Exercise 2: Stash Workflow**
1. Start working on feature
2. Receive urgent bug report
3. Stash current work
4. Fix bug on different branch
5. Return và restore stashed work

### **Exercise 3: Cherry-pick Scenario**
1. Create multiple feature branches
2. Pick specific commits from each branch
3. Apply to main branch selectively
4. Handle any conflicts during cherry-pick

### **Exercise 4: Recovery Practice**
1. Simulate "lost" commits scenario
2. Use reflog to find lost work
3. Practice different reset operations
4. Recover deleted branch

---

## 🎯 Checklist hoàn thành

- [ ] Master interactive rebase operations
- [ ] Use stash effectively for context switching
- [ ] Apply cherry-pick for selective commits
- [ ] Understand different reset operations
- [ ] Practice history manipulation safely
- [ ] Know recovery techniques
- [ ] Complete 4 advanced exercises

---

## ⚠️ Advanced Git Warnings

### **1. Force Push Safety**
```bash
# ❌ Dangerous: Can overwrite others' work
git push --force origin main

# ✅ Safer: Only force if no conflicts
git push --force-with-lease origin feature-branch
```

### **2. History Rewriting Rules**
```bash
# ✅ Safe: Rewrite private branches
git rebase -i HEAD~3  # On feature branch

# ❌ Dangerous: Rewrite shared history
git rebase -i HEAD~3  # On main branch that others use
```

### **3. Data Loss Prevention**
```bash
# Always check what you're doing
git log --oneline -5  # Before reset
git reflog           # To see recent changes
git stash list       # Check stashed work

# Make backup branch before risky operations
git branch backup-before-rebase
git rebase -i HEAD~5
```

---

## 💡 Advanced Tips

### **1. Git Aliases for Efficiency**
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'

# Custom alias for pretty log
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

### **2. Git Hooks for Automation**
```bash
# Pre-commit hook example
#!/bin/bash
# .git/hooks/pre-commit
echo "Running pre-commit checks..."

# Run tests
make test
if [ $? -ne 0 ]; then
    echo "❌ Tests failed. Commit aborted."
    exit 1
fi

# Check code style
cpplint src/*.cpp src/*.h
if [ $? -ne 0 ]; then
    echo "❌ Code style issues. Commit aborted."
    exit 1
fi

echo "✅ All checks passed."
```

### **3. Advanced Configuration**
```bash
# Better diff for C++
git config diff.cpp.xfuncname '^[[:alpha:]$_].*[^:]$'

# Auto-correct typos
git config help.autocorrect 1

# Better push behavior
git config push.default simple

# Global gitignore
git config --global core.excludesfile ~/.gitignore_global
```

---

## 📚 Tài liệu tham khảo
- [Pro Git Book - Advanced Git](https://git-scm.com/book/en/v2)
- [Atlassian Advanced Git Tutorials](https://www.atlassian.com/git/tutorials/advanced-overview)
- [Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)
- [Interactive Git Tutorial](https://learngitbranching.js.org/)

---

**Trước đó**: [Bài 4 - Collaboration](04_collaboration.md)  
**Tiếp theo**: [Tuần 3 - OOP](../week03_oop/01_classes_objects.md)
