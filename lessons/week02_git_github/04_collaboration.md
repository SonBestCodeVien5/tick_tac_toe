# 📝 Bài 4: Collaboration với Git và GitHub

## 🎯 Mục tiêu học
- Hiểu team collaboration workflows
- Nắm vững code review process
- Biết cách handle team conflicts
- Thực hành professional development practices

---

## 📖 Lý thuyết

### 1. Team Collaboration Models

#### **Centralized Workflow**
```
Central Repository (GitHub)
         ↑↓
    Developer A
         ↑↓  
    Local Repo A

         ↑↓
    Developer B  
         ↑↓
    Local Repo B
```

#### **Feature Branch Workflow**
```
main:     A---B---E---F---G
               \     /     \
feature-1:      C---D     
                           \
feature-2:                  H---I
```

#### **Gitflow Workflow**
```
main:     A-------E-------I
           \       \     /
develop:    \   C---D---F---G---H
             \ /         \ /
feature:      B           J---K
```

### 2. Pull Request Workflow

#### **Standard Process:**
1. **Fork/Clone** repository
2. **Create feature branch** from main
3. **Develop feature** với focused commits
4. **Push branch** to remote
5. **Create Pull Request** với description
6. **Code review** và discussion
7. **Address feedback** và update PR
8. **Merge** after approval

---

## 💡 Ví dụ thực tế: Team Development Simulation

### **Scenario: 3-Person Team Game Development**

#### **Team Structure:**
- **Alice (Lead)**: Repository owner, code reviews
- **Bob (Backend)**: Game logic, AI implementation  
- **Charlie (UI)**: User interface, display formatting

### **Setup Team Repository**

#### **Alice (Repository Owner):**
```bash
# 1. Create và setup main repository
git clone https://github.com/alice/tick-tac-toe-team.git
cd tick-tac-toe-team

# 2. Setup branch protection rules on GitHub
# Settings > Branches > Add rule for main:
# - Require pull request reviews before merging
# - Require status checks to pass
# - Restrict pushes to matching branches

# 3. Add team members as collaborators
# Settings > Manage access > Invite collaborators
```

#### **Bob và Charlie (Team Members):**
```bash
# Option 1: Direct collaboration (if added as collaborators)
git clone https://github.com/alice/tick-tac-toe-team.git

# Option 2: Fork workflow (for open source)
# Fork repository on GitHub first, then:
git clone https://github.com/bob/tick-tac-toe-team.git
git remote add upstream https://github.com/alice/tick-tac-toe-team.git
```

---

## 🔄 Feature Development Process

### **Bob's Task: Implement AI Player**

#### **Step 1: Create Feature Branch**
```bash
# Start from updated main
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/ai-player-implementation

# Push branch to establish remote tracking
git push -u origin feature/ai-player-implementation
```

#### **Step 2: Development với Regular Commits**
```bash
# Day 1: Basic structure
# Create src/AIPlayer.h
git add src/AIPlayer.h
git commit -m "Add AIPlayer class structure

- Define AI player interface
- Add difficulty levels enum
- Prepare for minimax implementation"

# Day 2: Core algorithm
# Implement src/AIPlayer.cpp
git add src/AIPlayer.cpp
git commit -m "Implement basic AI move selection

- Add random move for easy difficulty
- Add simple strategy for medium difficulty
- Prepare minimax for hard difficulty"

# Push work regularly
git push origin feature/ai-player-implementation
```

#### **Step 3: Create Pull Request**
```markdown
## Pull Request: Implement AI Player

### Description
Adds AI player functionality with multiple difficulty levels:
- Easy: Random valid moves
- Medium: Basic strategy (center, corners, then edges)
- Hard: Minimax algorithm implementation

### Changes Made
- Created `AIPlayer` class extending `Player`
- Implemented difficulty-based move selection
- Added comprehensive unit tests
- Updated main game loop to support AI vs Human

### Testing
- [x] All existing tests pass
- [x] New AI tests added and passing
- [x] Manual testing completed for all difficulty levels
- [x] No regression in existing functionality

### Screenshots
[Add screenshots showing AI gameplay]

### Related Issues
Closes #15 - Add AI opponent
Addresses #12 - Multiple difficulty levels

### Checklist
- [x] Code follows project style guide
- [x] Unit tests added
- [x] Documentation updated
- [x] No merge conflicts
- [x] Ready for review
```

---

## 📋 Code Review Process

### **Alice's Review Guidelines**

#### **Review Checklist:**
```markdown
## Code Review Checklist

### Functionality ✓
- [ ] Feature works as described
- [ ] Edge cases handled appropriately
- [ ] No breaking changes to existing code
- [ ] Performance considerations addressed

### Code Quality ✓
- [ ] Code is readable and well-structured
- [ ] Follows project naming conventions
- [ ] Appropriate error handling
- [ ] No code duplication

### Testing ✓
- [ ] Unit tests cover new functionality
- [ ] Tests are meaningful and thorough
- [ ] Manual testing documented
- [ ] No test failures

### Documentation ✓
- [ ] Code comments for complex logic
- [ ] API documentation updated
- [ ] README updated if needed
- [ ] Change log entry added
```

#### **Review Comments Examples:**

**Constructive Feedback:**
```markdown
**Overall**: Great work on the AI implementation! The difficulty levels are well thought out.

**Specific Comments:**

Line 45: Consider adding input validation here:
```cpp
// Current
int AIPlayer::makeMove(const GameBoard& board) {
    // ...
}

// Suggestion
int AIPlayer::makeMove(const GameBoard& board) {
    if (!board.isValid()) {
        throw std::invalid_argument("Invalid board state");
    }
    // ...
}
```

Line 78: This could be extracted to a helper function for better readability.

Line 102: Add comment explaining the heuristic scoring logic.

**Questions:**
- How does the AI handle draw situations?
- Should we add a timeout for the minimax algorithm?

**Approval:** ✅ Looks good with minor changes requested.
```

### **Bob's Response to Review:**
```bash
# Address review comments
# Fix input validation, add comments, extract helper function

git add .
git commit -m "Address code review feedback

- Add input validation to makeMove method
- Extract heuristic scoring to helper function
- Add detailed comments for minimax logic
- Add timeout handling for complex scenarios"

git push origin feature/ai-player-implementation

# Comment on PR:
# "Thanks for the thorough review! I've addressed all the feedback:
# - Added input validation as suggested
# - Extracted helper function for better readability  
# - Added comprehensive comments
# - Included timeout handling for edge cases
# Ready for re-review!"
```

---

## 🤝 Handling Merge Conflicts

### **Scenario: Charlie's UI conflicts với Bob's changes**

#### **Charlie's situation:**
```bash
# Charlie working on UI improvements
git checkout -b feature/enhanced-ui

# Meanwhile, Bob's AI feature gets merged to main
# Charlie's branch is now behind main

# Try to merge main into feature branch
git checkout feature/enhanced-ui
git merge main

# CONFLICT!
Auto-merging src/GameDisplay.cpp
CONFLICT (content): Merge conflict in src/GameDisplay.cpp
Automatic merge failed; fix conflicts and then commit the result.
```

#### **Conflict Resolution Process:**

**1. Understand the conflict:**
```cpp
// src/GameDisplay.cpp
void GameDisplay::showBoard(const GameBoard& board) {
<<<<<<< HEAD
    // Charlie's version - enhanced UI
    std::cout << "╔═══════════════╗" << std::endl;
    std::cout << "║ TIC TAC TOE   ║" << std::endl;
    std::cout << "╚═══════════════╝" << std::endl;
=======
    // Bob's version - AI integration
    std::cout << "=== TIC TAC TOE GAME ===" << std::endl;
    if (hasAIPlayer) {
        std::cout << "Playing against AI (Level " << aiLevel << ")" << std::endl;
    }
>>>>>>> main
    
    // Display board grid...
}
```

**2. Communicate với team:**
```markdown
**Slack/Discord message:**
Hey @Bob, I'm getting merge conflicts in GameDisplay.cpp. 
I see you added AI level display in the header. 
Can we combine both our changes? 
I'll integrate your AI info into my enhanced UI design.
```

**3. Resolve conflict:**
```cpp
// Combined solution
void GameDisplay::showBoard(const GameBoard& board) {
    // Charlie's enhanced UI with Bob's AI info
    std::cout << "╔═══════════════╗" << std::endl;
    std::cout << "║ TIC TAC TOE   ║" << std::endl;
    if (hasAIPlayer) {
        std::cout << "║ vs AI Level " << aiLevel << "  ║" << std::endl;
    }
    std::cout << "╚═══════════════╝" << std::endl;
    
    // Display board grid...
}
```

**4. Complete merge:**
```bash
# Mark conflict as resolved
git add src/GameDisplay.cpp

# Complete merge
git commit -m "Merge main into enhanced-ui feature

Resolved conflicts in GameDisplay.cpp by combining:
- Enhanced UI formatting (Charlie)
- AI level display (Bob)

Both features now work together seamlessly."

# Push updated branch
git push origin feature/enhanced-ui
```

---

## 📊 Project Management với GitHub

### 1. Project Boards

#### **Setup Kanban Board:**
```
TODO              IN PROGRESS       REVIEW           DONE
│                 │                 │                │
├─ Add AI player  ├─ Enhanced UI    ├─ Save/Load     ├─ Basic game
├─ Save game      ├─ Unit tests     │                ├─ Input validation
├─ Statistics     │                 │                ├─ Win detection
├─ Sound effects  │                 │                │
```

#### **Issue Assignment:**
```bash
# Assign issues to team members
# GitHub: Issue > Assignees > Select team member
# Labels: enhancement, bug, documentation
# Milestone: v1.0.0, v1.1.0, v2.0.0
```

### 2. Communication Practices

#### **Daily Standup (Virtual):**
```markdown
## Daily Standup - June 20, 2025

### Alice (Lead)
**Yesterday:** Reviewed Bob's AI PR, setup CI/CD pipeline
**Today:** Merge AI feature, plan v1.1.0 release
**Blockers:** None

### Bob (Backend)  
**Yesterday:** Addressed review feedback on AI implementation
**Today:** Start work on save/load functionality
**Blockers:** Need decision on file format (.json vs .txt)

### Charlie (UI)
**Yesterday:** Resolved merge conflicts, updated UI tests
**Today:** Finish enhanced UI PR, start sound integration
**Blockers:** Need assets from designer
```

#### **Team Communication Guidelines:**
```markdown
## Communication Best Practices

### Commit Messages
- Reference issue numbers: "Fix #15 - Add input validation"
- Use conventional commits: "feat:", "fix:", "docs:"
- Keep first line under 50 characters

### Pull Requests
- Always create PR from feature branch
- Add detailed description and testing notes
- Request specific reviewers
- Address feedback promptly

### Code Reviews
- Be constructive and helpful
- Explain reasoning behind suggestions
- Ask questions for clarification
- Approve when satisfied

### Issue Management
- Use templates for bug reports and features
- Add appropriate labels and milestones
- Assign to appropriate team member
- Update status regularly
```

---

## 📝 Bài tập thực hành

### **Exercise 1: Team Simulation**
1. Setup 3 different directories simulating team members
2. Create feature branches for different team members
3. Simulate parallel development
4. Practice merge conflict resolution

### **Exercise 2: Code Review Practice**
1. Create feature branch với intentional issues
2. Create pull request
3. Review code và provide constructive feedback
4. Address feedback và re-review

### **Exercise 3: Project Management**
1. Setup GitHub project board
2. Create issues với proper labels và assignments
3. Use milestones for release planning
4. Practice issue linking trong commits

### **Exercise 4: Advanced Collaboration**
1. Practice fork workflow
2. Setup automated testing với GitHub Actions
3. Implement branch protection rules
4. Create team documentation

---

## 🎯 Checklist hoàn thành

- [ ] Understand team collaboration workflows
- [ ] Practice feature branch development
- [ ] Create detailed pull requests
- [ ] Conduct thorough code reviews
- [ ] Resolve merge conflicts successfully
- [ ] Use GitHub project management tools
- [ ] Follow team communication best practices
- [ ] Complete 4 collaboration exercises

---

## ⚠️ Common Collaboration Issues

### **1. Merge conflicts due to poor communication**
```bash
# ❌ Problem: Two people working on same file without coordination
# ✅ Solution: Regular communication, small focused changes
```

### **2. Inadequate pull request descriptions**
```markdown
❌ Bad PR description:
"Fixed stuff"

✅ Good PR description:
"Implement AI player with difficulty levels

- Add AIPlayer class with minimax algorithm
- Support easy/medium/hard difficulty levels  
- Include comprehensive unit tests
- Update game loop to support AI vs human

Closes #15, addresses #12"
```

### **3. Not updating feature branches**
```bash
# ❌ Problem: Feature branch becomes stale
git checkout feature/old-feature  # 2 weeks old
git merge main  # Many conflicts!

# ✅ Solution: Regular updates
git checkout feature/current-feature
git merge main  # Daily or every few days
```

---

## 💡 Advanced Collaboration Tips

### **1. Automated Workflows**
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Setup C++
      run: sudo apt-get install g++
    - name: Build
      run: make build
    - name: Test
      run: make test
```

### **2. Code Quality Tools**
```bash
# Pre-commit hooks
# .githooks/pre-commit
#!/bin/bash
# Run tests before commit
make test
if [ $? -ne 0 ]; then
    echo "Tests failed. Commit aborted."
    exit 1
fi
```

### **3. Documentation Standards**
```cpp
/**
 * @brief AI player implementation for Tic Tac Toe
 * 
 * Provides multiple difficulty levels using different algorithms:
 * - Easy: Random move selection
 * - Medium: Simple heuristics
 * - Hard: Minimax with alpha-beta pruning
 * 
 * @author Bob Smith
 * @version 1.0
 * @since 2025-06-20
 */
class AIPlayer : public Player {
    // Implementation...
};
```

---

## 📚 Tài liệu tham khảo
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Pull Request Best Practices](https://github.blog/2015-01-21-how-to-write-the-perfect-pull-request/)
- [Code Review Guidelines](https://google.github.io/eng-practices/review/)
- [Git Team Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)

---

**Trước đó**: [Bài 3 - GitHub Setup](03_github_setup.md)  
**Tiếp theo**: [Bài 5 - Advanced Git](05_advanced_git.md)
