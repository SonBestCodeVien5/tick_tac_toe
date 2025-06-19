# 📋 Git Workflow Quick Reference

Chọn method phù hợp với bạn - cả hai đều dẫn đến kết quả giống nhau!

---

## 🔄 **Daily Workflow Comparison**

### **⌨️ Command Line Method**
```bash
# 1. Start working
cd c:\coder\tick_tac_toe
git pull origin main

# 2. After editing files
git add .
git commit -m "Week 1: Complete lesson 3 - Functions"
git push origin main

# 3. Check status anytime
git status
git log --oneline -5
```

### **🖥️ GitHub Desktop Method**
```
1. Open GitHub Desktop app
2. Select "tick_tac_toe" repository
3. Click "Fetch origin" button
4. Edit files in VS Code
5. Return to GitHub Desktop
6. Review changes in "Changes" tab
7. Write commit message
8. Click "Commit to main"
9. Click "Push origin"
```

---

## 🌟 **Branch Workflow Comparison**

### **⌨️ Command Line - Feature Branch**
```bash
# Create và switch to feature branch
git checkout -b feature/week5-game-engine

# Work và commit
git add .
git commit -m "Implement GameBoard class"
git push origin feature/week5-game-engine

# Merge when ready
git checkout main
git merge feature/week5-game-engine
git push origin main

# Clean up
git branch -d feature/week5-game-engine
```

### **🖥️ GitHub Desktop - Feature Branch**
```
1. Click "Current branch" dropdown
2. Click "New branch"
3. Name: "feature/week5-game-engine"
4. Click "Create branch"

5. Edit files và commit via UI
6. Click "Publish branch"

7. When ready to merge:
   - Switch to "main" branch
   - Branch menu → "Merge into current branch"
   - Select feature branch
   - Click "Create a merge commit"
   - Push to origin
```

---

## 📊 **When to Use Each Method**

### **✅ Use Command Line When:**
- Learning professional Git skills
- Need advanced operations (rebase, cherry-pick, stash)
- Working trong automated scripts
- Troubleshooting Git issues
- Working với multiple repositories
- SSH key authentication

### **✅ Use GitHub Desktop When:**
- Learning Git concepts visually
- Want to see file changes side-by-side
- Prefer clicking over typing
- Need quick daily commits
- Visual branch management
- Beginner to Git concepts

---

## 💡 **Learning Strategy**

### **Week 1-2: Foundation**
- **GitHub Desktop**: Learn concepts visually
- **Command Line**: Practice basic commands
- **Goal**: Understand Git workflow principles

### **Week 3-4: Building Skills**  
- **Use preferred method** for daily work
- **Practice other method** weekly
- **Goal**: Comfortable với both approaches

### **Week 5-8: Professional Development**
- **Gradually shift** to command line
- **Use GitHub Desktop** for quick visual checks
- **Goal**: Command line proficiency

---

## 🎯 **Quick Decision Guide**

**Choose method based on current task:**

| Task | Command Line | GitHub Desktop |
|------|-------------|----------------|
| Daily commits | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Learning Git | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| File comparison | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| Branch management | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Advanced operations | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| Professional skill | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| Beginner friendly | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| Speed (experienced) | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |

---

## 📚 **Resources for Both Methods**

### **Command Line Git:**
- [Pro Git Book](https://git-scm.com/book) - Comprehensive guide
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Interactive Git Tutorial](https://learngitbranching.js.org/)

### **GitHub Desktop:**
- [GitHub Desktop Documentation](https://docs.github.com/en/desktop)
- [GitHub Desktop Tutorial](https://docs.github.com/en/desktop/installing-and-configuring-github-desktop)
- [GitHub Desktop Keyboard Shortcuts](https://docs.github.com/en/desktop/installing-and-configuring-github-desktop/configuring-and-customizing-github-desktop/keyboard-shortcuts)

---

## 🎮 **Bottom Line**

**Both methods achieve the same goal** - managing your Tic Tac Toe project development!

**Choose based on:**
- Your learning style (visual vs command-driven)
- Your experience level (beginner vs experienced)
- Your career goals (hobby vs professional development)

**Remember**: The best Git workflow is the one you'll actually use consistently! 🚀
