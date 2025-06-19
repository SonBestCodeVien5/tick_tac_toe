# 📝 Bài 2: Branching và Merging trong Git

## 🎯 Mục tiêu học
- Hiểu về Git branches và tại sao cần dùng
- Nắm vững cách tạo, chuyển đổi, và merge branches
- Biết cách resolve conflicts
- Thực hành workflow phát triển tính năng

---

## 📖 Lý thuyết

### 1. Branches là gì?

**Branch** là một "nhánh" phát triển độc lập từ main codebase, cho phép:
- **Parallel development** - Nhiều features cùng lúc
- **Isolated changes** - Thay đổi không ảnh hưởng main
- **Experimentation** - Thử nghiệm an toàn
- **Collaboration** - Mỗi developer có branch riêng

### 2. Branch Workflow

```
main branch:     A---B---C---F---G
                      \         /
feature branch:        D---E---/
```

- **main**: Stable production code
- **feature**: New feature development
- **merge**: Combine feature back to main

### 3. Basic Branch Commands

#### **View branches:**
```bash
# List local branches
git branch

# List all branches (local + remote)
git branch -a

# List remote branches
git branch -r

# Show current branch
git status
```

#### **Create and switch branches:**
```bash
# Create new branch
git branch feature/new-game-mode

# Switch to branch
git checkout feature/new-game-mode

# Create and switch in one command
git checkout -b feature/player-stats

# Switch back to main
git checkout main
```

#### **Modern Git (2.23+):**
```bash
# Switch branches (clearer command)
git switch main
git switch feature/new-game-mode

# Create and switch
git switch -c feature/ai-improvements
```

### 4. Working with Branches

#### **Feature development workflow:**
```bash
# 1. Start from updated main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/save-load-game

# 3. Work on feature
# ... make changes to files ...
git add .
git commit -m "Add game save functionality"

# 4. Continue working
# ... more changes ...
git commit -m "Add game load functionality"

# 5. Push feature branch
git push origin feature/save-load-game
```

### 5. Merging Branches

#### **Fast-forward merge:**
```bash
# When no new commits on main since branch creation
git checkout main
git merge feature/simple-fix

# Result: linear history
main: A---B---C---D (D from feature branch)
```

#### **Three-way merge:**
```bash
# When main has new commits since branch creation
git checkout main
git merge feature/complex-feature

# Result: merge commit created
main:    A---B---E---F
              \ /
feature:       C---D
```

#### **Merge with commit message:**
```bash
git merge feature/new-feature -m "Merge new game feature

- Add multiplayer support
- Implement player statistics
- Update UI for better UX"
```

---

## 💡 Ví dụ thực tế: Feature Development

### **Scenario: Thêm AI Player vào game**

#### **Bước 1: Setup và Planning**
```bash
# Ensure main is up to date
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/ai-player

# Check current branch
git branch
# * feature/ai-player
#   main
```

#### **Bước 2: Implement AI Header**
```cpp
// Create src/AIPlayer.h
#ifndef AIPLAYER_H
#define AIPLAYER_H

#include "Player.h"
#include "GameBoard.h"

class AIPlayer : public Player {
private:
    int difficulty_level;
    
public:
    AIPlayer(const std::string& name, char symbol, int difficulty = 1);
    
    // AI-specific methods
    std::pair<int, int> makeMove(const GameBoard& board) override;
    int evaluatePosition(const GameBoard& board, char symbol);
    std::pair<int, int> findBestMove(const GameBoard& board);
    void setDifficulty(int level);
};

#endif
```

```bash
# Commit header file
git add src/AIPlayer.h
git commit -m "Add AIPlayer class header

- Define AI player interface extending Player class
- Add difficulty levels and move evaluation methods
- Prepare for minimax algorithm implementation"
```

#### **Bước 3: Implement AI Logic**
```cpp
// Create src/AIPlayer.cpp with implementation
// ... (detailed implementation) ...
```

```bash
# Commit implementation
git add src/AIPlayer.cpp
git commit -m "Implement basic AI player logic

- Add move evaluation using simple heuristics
- Implement difficulty levels (1-3)
- Add random move selection for easy mode
- Add basic strategy for medium/hard modes"
```

#### **Bước 4: Update Main Game**
```cpp
// Update src/main.cpp to include AI option
// ... add AI player selection in menu ...
```

```bash
# Commit integration
git add src/main.cpp
git commit -m "Integrate AI player into main game

- Add AI vs Human game mode option
- Update game loop to handle AI moves
- Add difficulty selection menu"
```

#### **Bước 5: Add Tests**
```cpp
// Create tests/test_aiplayer.cpp
// ... unit tests for AI functionality ...
```

```bash
# Commit tests
git add tests/test_aiplayer.cpp
git commit -m "Add unit tests for AI player

- Test move evaluation accuracy
- Test difficulty level behaviors
- Test edge cases and invalid moves"
```

### **Bước 6: Merge Feature**
```bash
# Switch back to main
git checkout main

# Ensure main is still up to date
git pull origin main

# Merge feature branch
git merge feature/ai-player

# Push updated main
git push origin main

# Delete feature branch (optional)
git branch -d feature/ai-player
git push origin --delete feature/ai-player
```

---

## 🔧 Conflict Resolution

### 1. Understanding Conflicts

Conflicts occur when:
- Same line modified in different branches
- File deleted in one branch, modified in another
- Binary files changed differently

### 2. Conflict Example

```bash
# Start merge
git merge feature/ui-improvements

# Conflict message
Auto-merging src/GameBoard.cpp
CONFLICT (content): Merge conflict in src/GameBoard.cpp
Automatic merge failed; fix conflicts and then commit the result.
```

### 3. Conflict Markers

```cpp
// In conflicted file:
void GameBoard::display() {
<<<<<<< HEAD
    // Current branch (main) version
    std::cout << "=== TIC TAC TOE BOARD ===" << std::endl;
=======
    // Feature branch version  
    std::cout << "╔═══════════════╗" << std::endl;
    std::cout << "║ TIC TAC TOE   ║" << std::endl;
    std::cout << "╚═══════════════╝" << std::endl;
>>>>>>> feature/ui-improvements
    
    // Common code continues here...
}
```

### 4. Resolving Conflicts

#### **Manual resolution:**
```cpp
// Choose one version or combine both
void GameBoard::display() {
    // Keep the fancy UI from feature branch
    std::cout << "╔═══════════════╗" << std::endl;
    std::cout << "║ TIC TAC TOE   ║" << std::endl;
    std::cout << "╚═══════════════╝" << std::endl;
    
    // Continue with rest of function...
}
```

#### **Mark as resolved:**
```bash
# After manually fixing conflicts
git add src/GameBoard.cpp

# Complete the merge
git commit -m "Merge feature/ui-improvements

Resolved conflicts in GameBoard.cpp by adopting
the enhanced UI formatting from the feature branch."
```

### 5. Conflict Prevention

#### **Regular syncing:**
```bash
# Regularly update feature branch with main
git checkout feature/long-running-feature
git merge main

# Or use rebase for cleaner history
git rebase main
```

#### **Small, focused commits:**
```bash
# Better: small focused changes
git commit -m "Update GameBoard display formatting"
git commit -m "Add color support to console output"

# Avoid: large mixed changes
git commit -m "Update everything"
```

---

## 🎯 Best Practices

### 1. Branch Naming Conventions

```bash
# Good branch names:
feature/ai-player
feature/save-load-game
bugfix/input-validation
hotfix/crash-on-win
experiment/new-algorithm

# Bad branch names:
my-changes
test
branch1
fix
```

### 2. Keep Branches Focused

```bash
# ✅ Good: One feature per branch
feature/player-statistics
feature/game-settings
feature/sound-effects

# ❌ Bad: Multiple unrelated changes
feature/everything-new
update/lots-of-stuff
```

### 3. Regular Cleanup

```bash
# List merged branches
git branch --merged

# Delete merged branches
git branch -d feature/completed-feature

# Delete remote tracking branches
git remote prune origin
```

---

## 📝 Bài tập thực hành

### **Exercise 1: Basic Branching**
1. Create branch `feature/game-statistics`
2. Add file `src/Statistics.h` với basic structure
3. Commit changes
4. Switch back to main và merge

### **Exercise 2: Conflict Resolution**
1. Create two branches from main: `feature/ui-v1` và `feature/ui-v2`
2. Modify same function in different ways
3. Merge first branch to main
4. Try to merge second branch (should conflict)
5. Resolve conflict và complete merge

### **Exercise 3: Feature Workflow**
1. Implement complete feature trong separate branch
2. Make multiple focused commits
3. Test thoroughly
4. Merge to main với proper commit message

---

## 🎯 Checklist hoàn thành

- [ ] Hiểu concept của branching
- [ ] Biết tạo và switch branches
- [ ] Thực hiện được fast-forward và 3-way merges
- [ ] Resolve conflicts thành công
- [ ] Follow proper branch naming conventions
- [ ] Complete 3 practice exercises

---

## ⚠️ Common Mistakes

### **1. Working on main directly**
```bash
# ❌ Bad: working directly on main
git checkout main
# ... make changes ...
git commit -m "add feature"

# ✅ Better: use feature branch
git checkout -b feature/new-feature
# ... make changes ...
git commit -m "add feature"
```

### **2. Forgetting to update main**
```bash
# ❌ Bad: outdated main when creating branch
git checkout main
git checkout -b feature/new-feature  # Based on old main!

# ✅ Better: update main first
git checkout main
git pull origin main
git checkout -b feature/new-feature
```

### **3. Large merge commits**
```bash
# ❌ Bad: huge merge with conflicts
# (500 file changes, multiple conflicts)

# ✅ Better: frequent small merges
git merge main  # Keep feature branch updated
```

---

## 📚 Tài liệu tham khảo
- [Git Branching Tutorial](https://learngitbranching.js.org/)
- [Atlassian Git Branching](https://www.atlassian.com/git/tutorials/using-branches)
- [Git Flow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)

---

**Trước đó**: [Bài 1 - Git Basics](01_git_basics.md)  
**Tiếp theo**: [Bài 3 - GitHub Setup](03_github_setup.md)
