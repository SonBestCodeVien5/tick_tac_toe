# 📝 Bài 3: GitHub Setup và Remote Repositories

## 🎯 Mục tiêu học
- Hiểu về remote repositories và GitHub
- Setup GitHub account và repository
- Nắm vững push/pull operations
- Collaborate với GitHub Issues và Pull Requests

---

## 📖 Lý thuyết

### 1. GitHub là gì?

**GitHub** là platform hosting Git repositories với features:
- **Cloud storage** - Backup code trên cloud
- **Collaboration** - Làm việc nhóm dễ dàng
- **Issue tracking** - Quản lý bugs và features
- **Pull Requests** - Code review process
- **Actions** - CI/CD automation
- **Pages** - Host static websites

### 2. Local vs Remote Repositories

```
Local Repository (Your Computer)
├── .git/
├── src/
└── README.md

Remote Repository (GitHub)
├── Same content
├── Accessible by team
└── Backup & collaboration hub
```

### 3. Basic Remote Operations

#### **Clone repository:**
```bash
# Download repository từ GitHub
git clone https://github.com/username/repo-name.git

# Clone vào specific folder
git clone https://github.com/username/repo-name.git my-project

# Clone specific branch
git clone -b feature-branch https://github.com/username/repo-name.git
```

#### **Add remote:**
```bash
# Add remote repository
git remote add origin https://github.com/username/repo-name.git

# View remotes
git remote -v

# Remove remote
git remote remove origin
```

#### **Push changes:**
```bash
# Push to remote
git push origin main

# Push new branch
git push origin feature/new-feature

# Set upstream (first time)
git push -u origin main
```

#### **Pull changes:**
```bash
# Fetch và merge from remote
git pull origin main

# Fetch only (không merge)
git fetch origin

# Pull với rebase
git pull --rebase origin main
```

---

## 💡 Ví dụ thực tế: Setup GitHub cho Tic Tac Toe Project

### **Bước 1: Tạo GitHub Repository**

1. **Truy cập GitHub.com**
2. **Click "New repository"**
3. **Fill repository details:**
   ```
   Repository name: tick-tac-toe-cpp
   Description: C++ Tic Tac Toe game with AI - Learning project
   Visibility: Public
   Initialize: ☐ Add README (we already have one)
               ☐ Add .gitignore (we already have one)
               ☐ Choose license
   ```
4. **Click "Create repository"**

### **Bước 2: Connect Local Repository**

```bash
# Navigate to project directory
cd C:\coder\tick_tac_toe

# Add GitHub remote
git remote add origin https://github.com/yourusername/tick-tac-toe-cpp.git

# Verify remote
git remote -v
# origin  https://github.com/yourusername/tick-tac-toe-cpp.git (fetch)
# origin  https://github.com/yourusername/tick-tac-toe-cpp.git (push)

# Push existing code
git push -u origin main
```

### **Bước 3: Verify Upload**

```bash
# Check repository status
git status

# View commit history
git log --oneline

# Check remote branches
git branch -r
```

### **Bước 4: Setup Repository Settings**

#### **A. Add Repository Description**
```markdown
# tick-tac-toe-cpp

🎮 **C++ Tic Tac Toe Game with AI**

An educational project for learning C++ programming, Object-Oriented Programming, and game development concepts.

## Features
- 2-player console game
- AI opponent with multiple difficulty levels
- Save/Load game functionality
- Statistics tracking
- Clean, extensible code architecture

## Learning Topics
- C++ fundamentals
- Object-oriented programming
- Data structures and algorithms
- Git version control
- Game development patterns

## Getting Started
```bash
git clone https://github.com/yourusername/tick-tac-toe-cpp.git
cd tick-tac-toe-cpp
# Follow instructions in lessons/ folder
```

## Project Structure
```
tick-tac-toe-cpp/
├── lessons/          # Learning materials
├── exercises/        # Practice exercises
├── src/             # Source code
├── tests/           # Unit tests
└── docs/            # Documentation
```
```

#### **B. Add Topics/Tags**
```
Topics: cpp, game-development, tic-tac-toe, ai, minimax, learning, educational, console-game
```

#### **C. Setup GitHub Pages (Optional)**
```bash
# Create docs branch for GitHub Pages
git checkout -b gh-pages
git push origin gh-pages

# Enable Pages in repository settings
# Settings > Pages > Source: gh-pages branch
```

---

## 🔧 Working with Remotes

### 1. Multiple Remotes

```bash
# Add multiple remotes
git remote add origin https://github.com/yourusername/project.git
git remote add upstream https://github.com/original/project.git
git remote add backup https://github.com/backup/project.git

# Push to specific remote
git push origin main
git push backup main

# Pull from upstream
git pull upstream main
```

### 2. Authentication

#### **HTTPS with Personal Access Token:**
```bash
# Generate token: GitHub > Settings > Developer settings > Personal access tokens
# Use token instead of password

git clone https://github.com/username/repo.git
# Username: your-username
# Password: your-personal-access-token
```

#### **SSH Setup:**
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your-email@example.com"

# Add to SSH agent
ssh-add ~/.ssh/id_ed25519

# Copy public key
cat ~/.ssh/id_ed25519.pub

# Add to GitHub: Settings > SSH and GPG keys > New SSH key
# Then use SSH URLs:
git clone git@github.com:username/repo.git
```

### 3. Sync Strategies

#### **Fetch và merge manually:**
```bash
git fetch origin
git merge origin/main
```

#### **Pull (fetch + merge):**
```bash
git pull origin main
```

#### **Pull với rebase:**
```bash
git pull --rebase origin main
# Keeps linear history
```

---

## 📋 GitHub Issues và Project Management

### 1. Creating Issues

#### **Bug Report Template:**
```markdown
## Bug Description
Clear description of the bug

## Steps to Reproduce
1. Step 1
2. Step 2
3. Step 3

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- OS: Windows 10
- Compiler: g++ 9.3.0
- Version: commit abc123

## Additional Context
Screenshots, logs, etc.
```

#### **Feature Request Template:**
```markdown
## Feature Description
Clear description of the feature

## Use Case
Why is this feature needed?

## Proposed Solution
How should it work?

## Alternatives Considered
Other approaches considered

## Additional Context
Mockups, examples, etc.
```

### 2. Issue Management

```bash
# Reference issues in commits
git commit -m "Fix input validation bug

Resolves #15 - Handle invalid move coordinates
- Add bounds checking for row/col input
- Add unit tests for edge cases
- Update error messages"

# Close issues via commit
git commit -m "Add AI difficulty levels

Closes #23"
```

### 3. Labels và Milestones

#### **Common Labels:**
```
bug           - Something isn't working
enhancement   - New feature or request
documentation - Improvements to docs
good first issue - Good for newcomers
help wanted   - Extra attention needed
question      - Further information needed
wontfix      - Won't be fixed
duplicate    - Duplicate issue
```

#### **Milestones:**
```
v1.0.0 - Basic Game Release
v1.1.0 - AI Implementation
v1.2.0 - Advanced Features
v2.0.0 - Major Refactor
```

---

## 🔄 Pull Requests Workflow

### 1. Creating Pull Request

```bash
# 1. Create feature branch
git checkout -b feature/improved-ai

# 2. Make changes and commit
git add .
git commit -m "Improve AI algorithm efficiency"

# 3. Push branch
git push origin feature/improved-ai

# 4. Create PR on GitHub web interface
```

### 2. Pull Request Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests pass
- [ ] Manual testing completed
- [ ] No regression in existing features

## Screenshots (if applicable)
Add screenshots here

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
```

### 3. Code Review Process

#### **Review Guidelines:**
```markdown
## Code Review Checklist

### Functionality
- [ ] Code works as intended
- [ ] Edge cases handled
- [ ] Error handling appropriate

### Code Quality
- [ ] Code is readable and maintainable
- [ ] Follows project conventions
- [ ] No code duplication
- [ ] Appropriate comments

### Testing
- [ ] Unit tests included
- [ ] Tests cover edge cases
- [ ] Manual testing done

### Documentation
- [ ] API documentation updated
- [ ] User documentation updated
- [ ] Code comments adequate
```

---

## 📝 Bài tập thực hành

### **Exercise 1: Repository Setup**
1. Create GitHub repository for your project
2. Add comprehensive README.md
3. Setup topics và description
4. Push local repository to GitHub

### **Exercise 2: Collaboration Simulation**
1. Create issues for bugs và features
2. Create feature branch to address issue
3. Make commits referencing issues
4. Create Pull Request với detailed description

### **Exercise 3: Remote Management**
1. Add multiple remotes (origin, backup)
2. Push to different remotes
3. Practice fetch/pull operations
4. Simulate conflict resolution

### **Exercise 4: GitHub Features**
1. Setup GitHub Pages for documentation
2. Create issue templates
3. Add labels và milestones
4. Practice code review process

---

## 🎯 Checklist hoàn thành

- [ ] Tạo GitHub repository thành công
- [ ] Connect local repository với remote
- [ ] Hiểu push/pull operations
- [ ] Setup repository documentation
- [ ] Create và manage issues
- [ ] Understand Pull Request workflow
- [ ] Complete 4 practice exercises

---

## ⚠️ Common Mistakes

### **1. Wrong remote URL**
```bash
# ❌ Wrong URL format
git remote add origin github.com/username/repo.git

# ✅ Correct format
git remote add origin https://github.com/username/repo.git
```

### **2. Pushing to wrong branch**
```bash
# ❌ Accidentally push to main from feature branch
git checkout feature/new-feature
git push origin main  # Wrong!

# ✅ Push to correct branch
git push origin feature/new-feature
```

### **3. Not pulling before pushing**
```bash
# ❌ Push without pulling latest changes
git push origin main  # May fail if remote has updates

# ✅ Pull first, then push
git pull origin main
git push origin main
```

---

## 💡 Best Practices

### **1. Repository Organization**
- Clear README with setup instructions
- Appropriate .gitignore for project type
- Meaningful repository description
- Proper license selection

### **2. Commit Messages**
- Reference issues when applicable
- Use conventional commit format
- Include context and reasoning
- Keep first line under 50 characters

### **3. Collaboration**
- Use Pull Requests for all changes
- Write detailed PR descriptions
- Review code thoroughly
- Test before merging

---

## 📚 Tài liệu tham khảo
- [GitHub Docs](https://docs.github.com/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [GitHub Pages](https://pages.github.com/)
- [SSH Key Setup](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

---

**Trước đó**: [Bài 2 - Branching và Merging](02_branching_merging.md)  
**Tiếp theo**: [Bài 4 - Collaboration](04_collaboration.md)

---

## 🖥️ Alternative: GitHub Desktop (Optional)

### **For Visual Learners**

Nếu bạn prefer giao diện đồ họa, có thể sử dụng **GitHub Desktop** song song với command line:

#### **GitHub Desktop Installation:**
1. Download từ: https://desktop.github.com/
2. Install và login GitHub account
3. Clone repository bằng GUI
4. Basic operations: commit, push, pull, branch

#### **GitHub Desktop Workflow:**
```
1. Clone repository → "Clone a repository from the Internet"
2. Make changes → Files hiển thị trong Changes tab
3. Commit → Write message và click "Commit to main"
4. Push → Click "Push origin" 
5. Pull → Click "Fetch origin"
```

#### **When to Use GitHub Desktop:**
- ✅ **Learning Git concepts** - Visual representation
- ✅ **Simple operations** - commit, push, pull
- ✅ **Branch management** - Easy branch switching
- ✅ **Merge conflict resolution** - Visual diff tool

#### **When to Use Command Line:**
- ✅ **Advanced operations** - rebase, cherry-pick, stash
- ✅ **Automation** - Scripts và CI/CD
- ✅ **Troubleshooting** - More detailed control
- ✅ **Professional development** - Industry standard

#### **Hybrid Approach (Recommended):**
```
Week 2: Learn both command line + GitHub Desktop
Week 3-4: Use whichever you're comfortable with
Week 5+: Gradually shift to command line for advanced features
```

### **GitHub Desktop Resources:**
- [GitHub Desktop Documentation](https://docs.github.com/en/desktop)
- [GitHub Desktop Tutorial](https://docs.github.com/en/desktop/installing-and-configuring-github-desktop)

**💡 Note**: Curriculum primarily teaches command line vì đó là industry standard, nhưng GitHub Desktop là excellent learning tool để visualize Git concepts!
