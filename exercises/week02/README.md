# 🚀 Git Practice - Tuần 2

## 🎯 Mục tiêu
Thực hành Git workflow thông qua các scenario thực tế trong phát triển dự án.

---

## 📝 **BÀI TẬP 1: SETUP REPOSITORY**

### **Scenario:** 
Bạn bắt đầu dự án mới và cần setup Git repository proper.

### **Tasks:**
1. **Initialize Git repository**
2. **Create comprehensive .gitignore**
3. **Setup remote repository trên GitHub**
4. **Make initial commit với meaningful structure**

### **Step-by-step:**
```bash
# 1. Navigate to project directory
cd C:\coder\tick_tac_toe

# 2. Initialize Git (if not done)
git init

# 3. Create .gitignore
touch .gitignore
```

### **.gitignore content:**
```gitignore
# Compiled Object files
*.o
*.obj
*.exe
*.out

# IDE files
.vscode/
*.user
*.suo

# Build directories  
build/
bin/
debug/
release/

# Log files
*.log
*.tmp

# OS files
.DS_Store
Thumbs.db

# Documentation build
docs/build/
```

### **Commands to practice:**
```bash
# Check status
git status

# Add files selectively
git add .gitignore
git add README.md
git add lessons/

# Make commits with good messages
git commit -m "Initial project setup

- Add comprehensive .gitignore for C++ development
- Create project structure with lessons and exercises
- Add initial documentation"

# Add remote (replace with your GitHub repo)
git remote add origin https://github.com/yourusername/tick_tac_toe.git

# Push to remote
git push -u origin main
```

---

## 📝 **BÀI TẬP 2: FEATURE DEVELOPMENT WORKFLOW**

### **Scenario:**
Bạn cần thêm feature mới và muốn maintain clean Git history.

### **Feature:** Player Statistics System

### **Workflow:**
```bash
# 1. Create feature branch
git checkout -b feature/player-statistics

# 2. Work on feature
# Create src/PlayerStats.h
# Create src/PlayerStats.cpp  
# Update main.cpp

# 3. Stage changes incrementally
git add src/PlayerStats.h
git commit -m "Add PlayerStats class header

- Define player statistics structure
- Add methods for tracking wins, losses, games played
- Include calculation methods for win rate"

git add src/PlayerStats.cpp
git commit -m "Implement PlayerStats class

- Add constructor and initialization
- Implement statistics tracking methods
- Add file I/O for persistent storage"

git add src/main.cpp
git commit -m "Integrate PlayerStats into main game loop

- Add statistics tracking to game flow
- Display player stats after each game
- Add menu option to view detailed statistics"

# 4. Switch back to main and merge
git checkout main
git merge feature/player-statistics

# 5. Clean up
git branch -d feature/player-statistics

# 6. Push changes
git push origin main
```

### **Files to create:**

**src/PlayerStats.h:**
```cpp
#ifndef PLAYERSTATS_H
#define PLAYERSTATS_H

#include <string>

class PlayerStats {
private:
    std::string player_name;
    int wins;
    int losses;
    int ties;
    
public:
    PlayerStats(const std::string& name);
    
    void addWin();
    void addLoss();
    void addTie();
    
    int getWins() const;
    int getLosses() const;
    int getTies() const;
    int getTotalGames() const;
    double getWinRate() const;
    
    void displayStats() const;
    void saveToFile(const std::string& filename) const;
    void loadFromFile(const std::string& filename);
};

#endif
```

---

## 📝 **BÀI TẬP 3: COLLABORATION SIMULATION**

### **Scenario:**
Simulate working with team members (multiple developers).

### **Setup:** Create multiple "developers"
```bash
# Create different working directories to simulate team members
mkdir C:\temp\dev1_tick_tac_toe
mkdir C:\temp\dev2_tick_tac_toe

# Clone repository for each "developer"
cd C:\temp\dev1_tick_tac_toe
git clone https://github.com/yourusername/tick_tac_toe.git .

cd C:\temp\dev2_tick_tac_toe  
git clone https://github.com/yourusername/tick_tac_toe.git .
```

### **Developer 1 Task:** Add AI Player
```bash
# Developer 1 working directory
cd C:\temp\dev1_tick_tac_toe

# Create branch
git checkout -b feature/ai-player

# Create files
# - src/AIPlayer.h
# - src/AIPlayer.cpp

# Commit work
git add src/AIPlayer.*
git commit -m "Add basic AI player implementation"

# Push branch to remote
git push origin feature/ai-player
```

### **Developer 2 Task:** Improve UI
```bash
# Developer 2 working directory  
cd C:\temp\dev2_tick_tac_toe

# Create branch
git checkout -b feature/improved-ui

# Modify files
# - src/GameDisplay.h
# - src/GameDisplay.cpp

# Commit work
git add src/GameDisplay.*
git commit -m "Enhance game display with colors and animations"

# Push branch
git push origin feature/improved-ui
```

### **Integration:**
```bash
# Back to main project
cd C:\coder\tick_tac_toe

# Pull both features
git fetch origin
git checkout feature/ai-player
git checkout feature/improved-ui

# Merge both features
git checkout main
git merge feature/ai-player
git merge feature/improved-ui

# Resolve any conflicts if they exist
# Commit merge
git commit -m "Merge AI player and improved UI features"

# Push final result
git push origin main
```

---

## 📝 **BÀI TẬP 4: HOTFIX WORKFLOW**

### **Scenario:**
Critical bug discovered in production that needs immediate fix.

### **Bug:** Game crashes when checking win condition

### **Hotfix workflow:**
```bash
# 1. Create hotfix branch from main
git checkout main
git checkout -b hotfix/win-condition-crash

# 2. Identify and fix the bug
# Edit src/GameLogic.cpp
# Fix null pointer issue in checkWin() function

# 3. Test the fix
# Compile and test

# 4. Commit fix
git add src/GameLogic.cpp
git commit -m "Fix crash in win condition checking

- Add null pointer validation in checkWin()
- Add bounds checking for array access
- Add unit test to prevent regression

Fixes critical crash when game reaches win state."

# 5. Merge to main immediately
git checkout main
git merge hotfix/win-condition-crash

# 6. Tag the release
git tag -a v1.0.1 -m "Hotfix release v1.0.1 - Fix win condition crash"

# 7. Push everything
git push origin main
git push origin v1.0.1

# 8. Clean up
git branch -d hotfix/win-condition-crash
```

---

## 📝 **BÀI TẬP 5: ADVANCED GIT OPERATIONS**

### **Scenario:** Complex Git operations for project maintenance

### **Tasks:**

#### **A. Rebase Feature Branch**
```bash
# Create feature branch
git checkout -b feature/game-settings

# Make several commits
git commit -m "Add settings structure"
git commit -m "Add settings file I/O"  
git commit -m "Add settings validation"

# Main branch has new commits, rebase to keep history clean
git checkout main
git pull origin main

git checkout feature/game-settings
git rebase main

# Resolve conflicts if any, then continue
git rebase --continue

# Force push rebased branch
git push origin feature/game-settings --force-with-lease
```

#### **B. Interactive Rebase (Clean up commits)**
```bash
# Clean up last 3 commits
git rebase -i HEAD~3

# In editor, change 'pick' to 'squash' for commits to combine
# Edit commit messages to be more descriptive
```

#### **C. Cherry-pick Specific Commits**
```bash
# Pick specific commit from another branch
git log feature/experimental --oneline
git cherry-pick abc1234

# Cherry-pick multiple commits
git cherry-pick abc1234..def5678
```

#### **D. Stash Work**
```bash
# Save current work temporarily
git stash save "Work in progress on input validation"

# Switch branches, do other work
git checkout hotfix/urgent-bug
# ... fix bug ...

# Return and restore work
git checkout feature/game-settings
git stash pop
```

---

## 🎯 **SUBMISSION CHECKLIST**

### **Repository Requirements:**
- [ ] Clean commit history with meaningful messages
- [ ] Proper branch structure (main, feature branches)
- [ ] Comprehensive .gitignore file
- [ ] Tagged releases (at least v1.0.0)
- [ ] Remote repository on GitHub

### **Git Skills Demonstrated:**
- [ ] Basic operations (add, commit, push, pull)
- [ ] Branching and merging
- [ ] Conflict resolution
- [ ] Rebasing for clean history
- [ ] Stashing temporary work
- [ ] Tagging releases
- [ ] Cherry-picking commits

### **Documentation:**
- [ ] README.md with project description
- [ ] Commit messages follow conventional format
- [ ] Branch names are descriptive
- [ ] Pull request descriptions (if using GitHub)

---

## 💡 **BEST PRACTICES CHECKLIST**

### **Commit Messages:**
```bash
# Good examples:
git commit -m "Add player input validation

- Validate row/column range (0-2)
- Handle non-numeric input gracefully  
- Add error messages for invalid moves
- Update unit tests for validation logic"

git commit -m "Fix memory leak in GameBoard destructor"
git commit -m "Refactor Player class for better encapsulation"

# Bad examples:
git commit -m "update"
git commit -m "fix bug"
git commit -m "changes"
```

### **Branch Naming:**
```bash
# Good examples:
feature/ai-player
feature/save-load-game
hotfix/input-validation-crash
bugfix/score-calculation-error

# Bad examples:  
my-branch
test
branch1
```

### **File Organization:**
```
.git/
src/
  ├── main.cpp
  ├── GameBoard.h
  ├── GameBoard.cpp
  ├── Player.h
  └── Player.cpp
tests/
docs/
.gitignore
README.md
```

---

## 📚 **RESOURCES**

- [Pro Git Book](https://git-scm.com/book)
- [GitHub Guides](https://guides.github.com/)
- [Git Cheat Sheet](https://training.github.com/downloads/github-git-cheat-sheet/)
- [Conventional Commits](https://www.conventionalcommits.org/)

---

**Previous**: [Week 1 Exercises](../week01/)  
**Next**: [Week 3 Exercises](../week03/)
