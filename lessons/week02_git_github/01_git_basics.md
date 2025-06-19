# 📝 Bài 1: Git Basics - Version Control Cơ bản

## 🎯 Mục tiêu học
- Hiểu Git là gì và tại sao cần dùng
- Nắm vững basic Git commands
- Biết cách commit và track changes
- Setup repository cho dự án C++

---

## 📖 Lý thuyết

### 1. Git là gì?

**Git** là một **Distributed Version Control System (DVCS)** giúp:
- **Track changes** - Theo dõi thay đổi trong code
- **Collaborate** - Nhiều người làm việc cùng lúc
- **Backup** - Lưu trữ an toàn
- **History** - Xem lại lịch sử thay đổi
- **Branching** - Phát triển tính năng song song

### 2. Git Workflow Cơ bản

```
Working Directory → Staging Area → Local Repository → Remote Repository
      (add)            (commit)         (push)
```

#### **3 trạng thái của file:**
- **Modified** - File đã thay đổi nhưng chưa commit
- **Staged** - File đã được add vào staging area
- **Committed** - File đã được lưu vào repository

### 3. Setup Git

#### **Cài đặt Git (Windows)**
1. Download từ [git-scm.com](https://git-scm.com)
2. Install với default settings
3. Mở Git Bash hoặc Command Prompt

#### **Config Git lần đầu**
```bash
# Set user information
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Check configuration
git config --list

# Set default editor (optional)
git config --global core.editor "code --wait"  # VS Code
```

### 4. Basic Git Commands

#### **Initialize Repository**
```bash
# Tạo repository mới
git init

# Clone repository từ remote
git clone https://github.com/username/repo-name.git
```

#### **Check Status**
```bash
# Xem trạng thái hiện tại
git status

# Xem changes chi tiết
git diff

# Xem staged changes
git diff --cached
```

#### **Add Files to Staging**
```bash
# Add specific file
git add filename.cpp

# Add all C++ files
git add *.cpp

# Add all files in directory
git add .

# Add all changes (including deletions)
git add -A
```

#### **Commit Changes**
```bash
# Commit với message
git commit -m "Add basic game board functionality"

# Commit và add tất cả modified files
git commit -am "Fix input validation bug"

# Commit với detailed message
git commit -m "Implement win condition checker

- Add checkWin function for rows, columns, diagonals
- Add unit tests for win conditions
- Update game loop to handle win state"
```

#### **View History**
```bash
# Xem commit history
git log

# Xem history ngắn gọn
git log --oneline

# Xem history với graph
git log --graph --oneline --all

# Xem specific number of commits
git log -3
```

---

## 💡 Ví dụ thực tế: Setup Git cho Tic Tac Toe Project

### **Bước 1: Initialize Git Repository**
```bash
# Mở terminal tại C:\coder\tick_tac_toe
cd C:\coder\tick_tac_toe

# Initialize git
git init

# Check status
git status
```

### **Bước 2: Create .gitignore**
```bash
# Tạo .gitignore file
touch .gitignore
```

`.gitignore` content:
```gitignore
# Compiled Object files
*.o
*.obj

# Executables
*.exe
*.out

# Visual Studio Code
.vscode/
!.vscode/tasks.json
!.vscode/launch.json

# Build directories
build/
bin/
obj/

# Debug files
*.pdb

# Log files
*.log

# Temporary files
*.tmp
*.temp

# OS generated files
.DS_Store
Thumbs.db

# IDE files
*.user
*.suo
*.sdf
```

### **Bước 3: First Commit**
```bash
# Add all files
git add .

# Check what will be committed
git status

# Make first commit
git commit -m "Initial project setup

- Add project structure with lessons, exercises, src folders
- Add comprehensive learning plan documentation
- Add .gitignore for C++ development
- Ready to start Week 1: C++ Basics"

# View commit
git log --oneline
```

### **Bước 4: Regular Workflow**
```bash
# Work on files...
# Edit lessons/week01_cpp_basics/01_variables_datatypes.md

# Check what changed
git status
git diff

# Stage changes
git add lessons/week01_cpp_basics/01_variables_datatypes.md

# Commit changes
git commit -m "Complete variables and data types lesson

- Add comprehensive examples with game context
- Include practical exercises
- Add common mistakes section"

# Continue working...
```

---

## 🔍 Git Best Practices

### 1. Commit Message Guidelines

#### **Good commit messages:**
```bash
git commit -m "Add player input validation"
git commit -m "Fix array bounds checking in board display"
git commit -m "Implement basic AI move selection"
git commit -m "Update documentation for setup instructions"
```

#### **Bad commit messages:**
```bash
git commit -m "update"
git commit -m "fix"
git commit -m "changes"
git commit -m "asdf"
```

#### **Detailed commit message format:**
```
Short summary (50 characters or less)

More detailed description if needed. Wrap at 72 characters.
Explain what was changed and why, not how.

- Add bullet points for multiple changes
- Reference issue numbers if applicable
- Use imperative mood: "Add feature" not "Added feature"
```

### 2. When to Commit

#### **✅ Good times to commit:**
- Completed a feature
- Fixed a bug
- Refactored code
- Added documentation
- Before trying something risky

#### **❌ Don't commit:**
- Broken/non-compiling code
- Temporary debugging code
- Large unrelated changes together
- Generated files (executables, etc.)

### 3. File Organization

```
tick_tac_toe/
├── .git/                    # Git repository data
├── .gitignore              # Files to ignore
├── README.md               # Project overview
├── src/                    # Source code
│   ├── main.cpp
│   └── GameBoard.cpp
├── lessons/                # Learning materials
└── exercises/              # Practice exercises
```

---

## 📝 Thực hành: Git Workflow

### **Exercise 1: Setup Repository**
1. Initialize Git trong project folder
2. Create appropriate .gitignore
3. Make initial commit với meaningful message
4. Add và commit một lesson file

### **Exercise 2: Practice Basic Workflow**
1. Tạo file `src/hello.cpp`:
```cpp
#include <iostream>

int main() {
    std::cout << "Hello, Git!" << std::endl;
    return 0;
}
```

2. Add và commit file
3. Modify file để add thêm output
4. Commit changes
5. View git log

### **Exercise 3: Working with Changes**
1. Tạo multiple files cùng lúc
2. Add only some files to staging
3. Commit staged files
4. Add remaining files và commit separately
5. Practice using git diff

---

## 🎯 Checklist hoàn thành

- [ ] Install và config Git successfully
- [ ] Understand Git workflow (Working → Staging → Repository)
- [ ] Biết basic commands: init, add, commit, status, log
- [ ] Tạo được appropriate .gitignore
- [ ] Write good commit messages
- [ ] Setup Git repository cho project
- [ ] Complete 3 practice exercises

---

## ⚠️ Common Mistakes

### **1. Forgetting to add files**
```bash
# Modified files but forgot to add
git commit -m "Add new feature"  # ❌ Nothing to commit!

# ✅ Correct workflow:
git add .
git commit -m "Add new feature"
```

### **2. Committing too large changes**
```bash
# ❌ Bad: huge commit with many unrelated changes
git add .
git commit -m "Update everything"

# ✅ Better: smaller, focused commits
git add src/GameBoard.cpp
git commit -m "Add GameBoard class implementation"

git add src/Player.cpp  
git commit -m "Add Player class with score tracking"
```

### **3. Poor commit messages**
```bash
git commit -m "fix"        # ❌ What was fixed?
git commit -m "changes"    # ❌ What kind of changes?

# ✅ Better:
git commit -m "Fix null pointer exception in checkWin function"
git commit -m "Update input validation to handle edge cases"
```

---

## 💡 Git Tips

### **1. Useful aliases**
```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.unstage 'reset HEAD --'

# Now use: git st instead of git status
```

### **2. View changes before commit**
```bash
git diff                    # See unstaged changes
git diff --cached          # See staged changes
git diff HEAD              # See all changes since last commit
```

### **3. Undo changes**
```bash
# Undo unstaged changes
git checkout -- filename.cpp

# Unstage files
git reset HEAD filename.cpp

# Amend last commit message
git commit --amend -m "New message"
```

---

## 📚 Tài liệu tham khảo
- [Pro Git Book](https://git-scm.com/book) - Free official Git book
- [Git Cheat Sheet](https://training.github.com/downloads/github-git-cheat-sheet/) - Quick reference
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials) - Interactive learning

---

**Tiếp theo**: [Bài 2 - Branching và Merging](02_branching_merging.md)
