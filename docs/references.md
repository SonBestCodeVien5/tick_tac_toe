# 📚 Tài liệu tham khảo - C++ Game Development

## 🎯 Mục đích
Tổng hợp các tài liệu, tutorials, và resources hữu ích cho việc học C++ và phát triển game.

---

## 📖 **C++ LEARNING RESOURCES**

### **📘 Sách khuyên đọc**

#### **Beginner Level:**
1. **"C++ Primer" by Stanley Lippman**
   - Comprehensive introduction to C++
   - Covers modern C++ features
   - Excellent for beginners with programming background

2. **"Programming: Principles and Practice Using C++" by Bjarne Stroustrup**
   - Written by C++ creator
   - Teaching-oriented approach
   - Good for complete beginners

3. **"C++ Crash Course" by Josh Lospinoso**
   - Modern C++ focus
   - Practical examples
   - Covers C++17/20 features

#### **Intermediate Level:**
1. **"Effective C++" by Scott Meyers**
   - 55 specific ways to improve programs
   - Best practices and idioms
   - Essential for intermediate developers

2. **"C++ Core Guidelines" (Online)**
   - Official guidelines from C++ standards committee
   - Modern best practices
   - Available free at: https://isocpp.github.io/CppCoreGuidelines/

#### **Advanced Level:**
1. **"More Effective C++" by Scott Meyers**
2. **"Exceptional C++" by Herb Sutter**
3. **"C++ Templates: The Complete Guide" by David Vandevoorde**

### **🌐 Online Resources**

#### **Free Tutorials:**
1. **[learncpp.com](https://www.learncpp.com)**
   - Comprehensive C++ tutorial
   - Well-structured lessons
   - Regular updates

2. **[cppreference.com](https://cppreference.com)**
   - Complete C++ reference
   - Function documentation
   - Code examples

3. **[GeeksforGeeks C++](https://www.geeksforgeeks.org/c-plus-plus/)**
   - Practical examples
   - Interview questions
   - Data structures and algorithms

#### **Video Courses:**
1. **YouTube Channels:**
   - **The Cherno C++ Series**
   - **CppCon Talks**
   - **Jason Turner C++ Weekly**

2. **Paid Platforms:**
   - **Pluralsight C++ Path**
   - **Udemy C++ Courses**
   - **Coursera C++ Specializations**

---

## 🎮 **GAME DEVELOPMENT RESOURCES**

### **📘 Game Programming Books**

1. **"Game Programming Patterns" by Robert Nystrom**
   - Design patterns for games
   - Available free online: https://gameprogrammingpatterns.com
   - Excellent for architecture understanding

2. **"Real-Time Rendering" by Tomas Akenine-Möller**
   - Graphics programming bible
   - Advanced rendering techniques
   - Mathematical foundations

3. **"AI for Games" by Ian Millington**
   - Comprehensive AI techniques
   - Pathfinding, decision making
   - Practical implementations

### **🎮 Game Development Frameworks**

#### **Beginner-Friendly:**
1. **SFML (Simple and Fast Multimedia Library)**
   - C++ multimedia library
   - 2D graphics, audio, input
   - Great for learning: https://www.sfml-dev.org

2. **SDL (Simple DirectMedia Layer)**
   - Cross-platform development
   - Low-level access
   - Used by many commercial games

#### **Advanced Engines:**
1. **Unreal Engine (C++)**
   - Professional game engine
   - Blueprint visual scripting
   - Free for learning

2. **Custom Engines**
   - Build from scratch
   - Complete control
   - Educational value

### **🧠 Algorithm and AI Resources**

#### **Minimax Algorithm:**
1. **[Minimax Algorithm Explained](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-1-introduction/)**
2. **[Alpha-Beta Pruning](https://en.wikipedia.org/wiki/Alpha%E2%80%93beta_pruning)**
3. **[Game AI Pro Series](http://www.gameaipro.com/)** - Free online books

#### **Data Structures for Games:**
1. **Trees and Graphs** - For AI decision trees
2. **Hash Tables** - For fast lookups
3. **Priority Queues** - For pathfinding algorithms

---

## 🛠️ **DEVELOPMENT TOOLS**

### **💻 IDEs and Editors**

#### **Recommended for C++:**
1. **Visual Studio Code**
   - Lightweight, extensible
   - Excellent C++ support with extensions
   - Free and cross-platform

2. **Visual Studio Community**
   - Full-featured IDE
   - Excellent debugging tools
   - Windows-focused

3. **CLion by JetBrains**
   - Professional C++ IDE
   - Advanced refactoring tools
   - Student license available

#### **Essential VS Code Extensions:**
```json
{
    "recommendations": [
        "ms-vscode.cpptools",
        "ms-vscode.cmake-tools",
        "ms-vscode.cpptools-extension-pack",
        "formulahendry.code-runner",
        "ms-vscode.hexeditor",
        "twxs.cmake",
        "vadimcn.vscode-lldb"
    ]
}
```

### **🔨 Build Systems**

#### **CMake (Recommended):**
```cmake
cmake_minimum_required(VERSION 3.10)
project(TicTacToe)

set(CMAKE_CXX_STANDARD 17)

# Add executable
add_executable(TicTacToe 
    src/main.cpp
    src/GameBoard.cpp
    src/Player.cpp
    src/GameLogic.cpp
)

# Include directories
target_include_directories(TicTacToe PRIVATE include)

# Compiler flags
if(MSVC)
    target_compile_options(TicTacToe PRIVATE /W4)
else()
    target_compile_options(TicTacToe PRIVATE -Wall -Wextra -Wpedantic)
endif()
```

#### **Makefile (Alternative):**
```makefile
CXX = g++
CXXFLAGS = -std=c++17 -Wall -Wextra -g
SRCDIR = src
OBJDIR = obj
SOURCES = $(wildcard $(SRCDIR)/*.cpp)
OBJECTS = $(SOURCES:$(SRCDIR)/%.cpp=$(OBJDIR)/%.o)
TARGET = TicTacToe

all: $(TARGET)

$(TARGET): $(OBJECTS)
	$(CXX) $(OBJECTS) -o $@

$(OBJDIR)/%.o: $(SRCDIR)/%.cpp
	@mkdir -p $(OBJDIR)
	$(CXX) $(CXXFLAGS) -c $< -o $@

clean:
	rm -rf $(OBJDIR) $(TARGET)

.PHONY: all clean
```

### **🧪 Testing Frameworks**

#### **Google Test:**
```cpp
#include <gtest/gtest.h>
#include "GameBoard.h"

TEST(GameBoardTest, InitialBoardIsEmpty) {
    GameBoard board;
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            EXPECT_EQ(board.getCell(i, j), ' ');
        }
    }
}

TEST(GameBoardTest, PlaceSymbolSuccessfully) {
    GameBoard board;
    EXPECT_TRUE(board.makeMove(0, 0, 'X'));
    EXPECT_EQ(board.getCell(0, 0), 'X');
}
```

#### **Catch2 (Alternative):**
```cpp
#define CATCH_CONFIG_MAIN
#include <catch2/catch.hpp>
#include "Player.h"

TEST_CASE("Player creation and basic operations") {
    Player player("Alice", 'X');
    
    REQUIRE(player.getName() == "Alice");
    REQUIRE(player.getSymbol() == 'X');
    REQUIRE(player.getScore() == 0);
}
```

---

## 🔍 **DEBUGGING AND PROFILING**

### **🐛 Debugging Tools**

#### **GDB (GNU Debugger):**
```bash
# Compile with debug info
g++ -g -o game src/*.cpp

# Start debugging
gdb ./game

# Basic GDB commands
(gdb) break main
(gdb) run
(gdb) step
(gdb) next
(gdb) print variable_name
(gdb) continue
```

#### **Valgrind (Memory checking):**
```bash
# Check for memory leaks
valgrind --leak-check=full ./game

# Check for memory errors
valgrind --tool=memcheck ./game
```

### **⚡ Performance Tools**

#### **Profiling with gprof:**
```bash
# Compile with profiling
g++ -pg -o game src/*.cpp

# Run program
./game

# Generate profile
gprof game gmon.out > profile.txt
```

---

## 📊 **PROJECT MANAGEMENT**

### **🔄 Version Control Best Practices**

#### **Git Workflow:**
```bash
# Feature branch workflow
git checkout -b feature/new-feature
# ... work on feature ...
git add .
git commit -m "Implement new feature"
git checkout main
git merge feature/new-feature

# Semantic versioning
git tag v1.0.0  # Major.Minor.Patch
```

#### **Conventional Commits:**
```
feat: add new game mode
fix: resolve input validation bug
docs: update README with setup instructions
style: format code according to style guide
refactor: extract common functions to utility class
test: add unit tests for game logic
```

### **📋 Documentation Tools**

#### **Doxygen (Code documentation):**
```cpp
/**
 * @brief Represents a game board for Tic Tac Toe
 * 
 * This class manages the 3x3 game board, handles move validation,
 * and provides methods for checking win conditions.
 */
class GameBoard {
private:
    char board[3][3];  ///< The game board matrix
    
public:
    /**
     * @brief Makes a move on the board
     * @param row The row index (0-2)
     * @param col The column index (0-2)
     * @param symbol The player symbol ('X' or 'O')
     * @return true if move was successful, false otherwise
     */
    bool makeMove(int row, int col, char symbol);
};
```

---

## 🌟 **COMMUNITY AND LEARNING**

### **💬 Communities and Forums**

1. **Stack Overflow** - Programming questions
2. **Reddit r/cpp** - C++ discussions
3. **cpplang Slack** - Real-time C++ chat
4. **GameDev.net** - Game development community
5. **IndieDB** - Indie game showcase

### **🏆 Coding Challenges**

1. **LeetCode** - Algorithm practice
2. **HackerRank** - Programming challenges
3. **Codewars** - Coding kata
4. **Project Euler** - Mathematical programming

### **📅 Conferences and Events**

1. **CppCon** - Annual C++ conference
2. **GDC (Game Developers Conference)** - Game industry event
3. **Local C++ User Groups** - Meetups in your area

---

## 🎯 **LEARNING PATH RECOMMENDATIONS**

### **📅 Monthly Learning Plan**

#### **Month 1: C++ Fundamentals**
- Complete basic syntax and data types
- Master functions and control flow
- Practice with simple console programs
- Read: C++ Primer chapters 1-5

#### **Month 2: Object-Oriented Programming**
- Learn classes and objects
- Understand inheritance and polymorphism
- Practice with small projects
- Read: C++ Primer chapters 6-12

#### **Month 3: Advanced C++ Features**
- Templates and generic programming
- STL containers and algorithms
- Exception handling
- Read: Effective C++

#### **Month 4: Game Development Basics**
- Learn SFML or SDL
- Create simple 2D games
- Understand game loops
- Read: Game Programming Patterns

#### **Month 5: AI and Algorithms**
- Implement minimax algorithm
- Study pathfinding algorithms
- Create AI opponents
- Read: AI for Games

#### **Month 6: Polish and Portfolio**
- Code review and refactoring
- Create polished game projects
- Build portfolio on GitHub
- Apply for internships/jobs

---

## 📚 **QUICK REFERENCE CARDS**

### **C++ Quick Reference:**
```cpp
// Data types
int, float, double, char, bool, string

// Containers
vector<T>, array<T,N>, map<K,V>, set<T>

// Algorithms
sort(), find(), for_each(), transform()

// Smart pointers
unique_ptr<T>, shared_ptr<T>, weak_ptr<T>

// Common patterns
RAII, Factory, Singleton, Observer
```

### **Git Quick Reference:**
```bash
git status          # Check repository status
git add .           # Stage all changes
git commit -m "msg" # Commit with message
git push            # Push to remote
git pull            # Pull from remote
git branch          # List branches
git checkout -b     # Create new branch
git merge           # Merge branches
```

---

## 🔗 **USEFUL LINKS**

### **Official Documentation:**
- [C++ Standard](https://isocpp.org/)
- [cppreference.com](https://cppreference.com/)
- [CMake Documentation](https://cmake.org/documentation/)

### **Tools:**
- [Compiler Explorer (Godbolt)](https://godbolt.org/)
- [C++ Insights](https://cppinsights.io/)
- [Quick Bench](http://quick-bench.com/)

### **Practice:**
- [LeetCode](https://leetcode.com/)
- [HackerRank](https://www.hackerrank.com/)
- [Codewars](https://www.codewars.com/)

---

**Last Updated**: June 19, 2025  
**Maintained by**: Tic Tac Toe Learning Project
