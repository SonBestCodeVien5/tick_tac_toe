# 📝 Bài 2: Constructors và Destructors

## 🎯 Mục tiêu học
- Hiểu về object lifecycle trong C++
- Nắm vững các loại constructors
- Biết về destructors và RAII
- Thực hành với game object management

---

## 📖 Lý thuyết

### 1. Object Lifecycle

```cpp
class Player {
public:
    Player();           // Constructor - khởi tạo object
    ~Player();          // Destructor - dọn dẹp object
};

// Object creation và destruction
{
    Player p1;          // Constructor được gọi
    Player* p2 = new Player();  // Constructor được gọi
    
    delete p2;          // Destructor được gọi
}   // p1 destructor được gọi tự động
```

### 2. Types of Constructors

#### **Default Constructor:**
```cpp
class GameBoard {
private:
    char board[3][3];
    int move_count;
    
public:
    // Default constructor
    GameBoard() {
        // Initialize board to empty
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                board[i][j] = ' ';
            }
        }
        move_count = 0;
    }
};
```

#### **Parameterized Constructor:**
```cpp
class Player {
private:
    std::string name;
    char symbol;
    int score;
    
public:
    // Parameterized constructor
    Player(const std::string& player_name, char player_symbol) {
        name = player_name;
        symbol = player_symbol;
        score = 0;
    }
    
    // Multiple constructors (overloading)
    Player(const std::string& player_name, char player_symbol, int initial_score) {
        name = player_name;
        symbol = player_symbol;
        score = initial_score;
    }
};
```

#### **Copy Constructor:**
```cpp
class Player {
private:
    std::string name;
    char symbol;
    int* statistics;  // Dynamic memory
    
public:
    // Copy constructor
    Player(const Player& other) {
        name = other.name;
        symbol = other.symbol;
        
        // Deep copy dynamic memory
        statistics = new int[10];
        for (int i = 0; i < 10; i++) {
            statistics[i] = other.statistics[i];
        }
    }
};
```

### 3. Member Initializer Lists

```cpp
class Player {
private:
    const int player_id;
    std::string name;
    char symbol;
    
public:
    // Using initializer list (preferred)
    Player(int id, const std::string& n, char s) 
        : player_id(id), name(n), symbol(s) {
        // Constructor body for additional logic
    }
    
    // Without initializer list (less efficient)
    Player(int id, const std::string& n, char s) {
        // player_id = id;  // ❌ Error! const member
        name = n;          // Assignment after default construction
        symbol = s;
    }
};
```

### 4. Destructors và RAII

```cpp
class GameSession {
private:
    std::string* game_log;
    std::ofstream* log_file;
    
public:
    GameSession(const std::string& filename) {
        game_log = new std::string[1000];  // Allocate memory
        log_file = new std::ofstream(filename);  // Open file
    }
    
    // Destructor - automatic cleanup
    ~GameSession() {
        delete[] game_log;    // Free memory
        log_file->close();
        delete log_file;      // Close and free file
    }
};
```

---

## 💡 Ví dụ thực tế: Complete Game Classes

### **Player Class với Full Constructor Support:**

```cpp
// Player.h
#ifndef PLAYER_H
#define PLAYER_H

#include <string>
#include <memory>

class Player {
private:
    std::string name;
    char symbol;
    int score;
    int games_played;
    std::unique_ptr<int[]> move_history;  // Smart pointer for RAII
    
public:
    // Default constructor
    Player();
    
    // Parameterized constructors
    Player(const std::string& name, char symbol);
    Player(const std::string& name, char symbol, int initial_score);
    
    // Copy constructor
    Player(const Player& other);
    
    // Assignment operator
    Player& operator=(const Player& other);
    
    // Move constructor (C++11)
    Player(Player&& other) noexcept;
    
    // Move assignment (C++11)
    Player& operator=(Player&& other) noexcept;
    
    // Destructor
    ~Player();
    
    // Member functions
    void makeMove(int position);
    void increaseScore();
    void displayStats() const;
};

#endif
```

### **Implementation:**

```cpp
// Player.cpp
#include "Player.h"
#include <iostream>
#include <algorithm>

// Default constructor
Player::Player() : name("Unknown"), symbol(' '), score(0), games_played(0) {
    move_history = std::make_unique<int[]>(100);
    std::cout << "Player default constructor called" << std::endl;
}

// Parameterized constructor với initializer list
Player::Player(const std::string& player_name, char player_symbol) 
    : name(player_name), symbol(player_symbol), score(0), games_played(0) {
    move_history = std::make_unique<int[]>(100);
    std::cout << "Player " << name << " created with symbol " << symbol << std::endl;
}

// Constructor với initial score
Player::Player(const std::string& player_name, char player_symbol, int initial_score)
    : name(player_name), symbol(player_symbol), score(initial_score), games_played(0) {
    move_history = std::make_unique<int[]>(100);
    std::cout << "Player " << name << " created with initial score " << initial_score << std::endl;
}

// Copy constructor
Player::Player(const Player& other) 
    : name(other.name), symbol(other.symbol), score(other.score), games_played(other.games_played) {
    move_history = std::make_unique<int[]>(100);
    // Copy move history
    for (int i = 0; i < games_played && i < 100; i++) {
        move_history[i] = other.move_history[i];
    }
    std::cout << "Player copied: " << name << std::endl;
}

// Assignment operator
Player& Player::operator=(const Player& other) {
    if (this != &other) {  // Self-assignment check
        name = other.name;
        symbol = other.symbol;
        score = other.score;
        games_played = other.games_played;
        
        // Copy move history
        for (int i = 0; i < games_played && i < 100; i++) {
            move_history[i] = other.move_history[i];
        }
        
        std::cout << "Player assigned: " << name << std::endl;
    }
    return *this;
}

// Move constructor
Player::Player(Player&& other) noexcept
    : name(std::move(other.name)), symbol(other.symbol), 
      score(other.score), games_played(other.games_played),
      move_history(std::move(other.move_history)) {
    
    // Reset other object
    other.symbol = ' ';
    other.score = 0;
    other.games_played = 0;
    
    std::cout << "Player moved: " << name << std::endl;
}

// Move assignment
Player& Player::operator=(Player&& other) noexcept {
    if (this != &other) {
        name = std::move(other.name);
        symbol = other.symbol;
        score = other.score;
        games_played = other.games_played;
        move_history = std::move(other.move_history);
        
        // Reset other object
        other.symbol = ' ';
        other.score = 0;
        other.games_played = 0;
        
        std::cout << "Player move assigned: " << name << std::endl;
    }
    return *this;
}

// Destructor
Player::~Player() {
    std::cout << "Player " << name << " destroyed" << std::endl;
    // Smart pointers automatically clean up
}

void Player::makeMove(int position) {
    if (games_played < 100) {
        move_history[games_played] = position;
    }
    games_played++;
}

void Player::increaseScore() {
    score++;
}

void Player::displayStats() const {
    std::cout << "Player: " << name << " (" << symbol << ")" << std::endl;
    std::cout << "Score: " << score << std::endl;
    std::cout << "Games: " << games_played << std::endl;
}
```

---

## 🎯 Best Practices

### 1. Rule of Three/Five/Zero

#### **Rule of Three (C++98):**
If you need one, you probably need all three:
- Copy constructor
- Assignment operator  
- Destructor

#### **Rule of Five (C++11):**
Add move semantics:
- Move constructor
- Move assignment operator

#### **Rule of Zero (Modern C++):**
Use smart pointers và RAII - let compiler generate:
```cpp
class ModernPlayer {
private:
    std::string name;
    char symbol;
    std::unique_ptr<int[]> data;  // Smart pointer handles everything
    
public:
    ModernPlayer(const std::string& n, char s) : name(n), symbol(s) {}
    // Compiler generates all special members correctly!
};
```

### 2. RAII (Resource Acquisition Is Initialization)

```cpp
class FileManager {
private:
    std::unique_ptr<std::ofstream> file;
    
public:
    FileManager(const std::string& filename) {
        file = std::make_unique<std::ofstream>(filename);
        if (!file->is_open()) {
            throw std::runtime_error("Cannot open file");
        }
    }
    
    // Destructor automatically closes file via smart pointer
    ~FileManager() = default;
    
    void write(const std::string& data) {
        *file << data;
    }
};
```

---

## 📝 Bài tập thực hành

### **Exercise 1: Complete Class Design**
Design GameSession class với:
- Multiple constructors
- Proper resource management
- Copy/move semantics
- RAII compliance

### **Exercise 2: Memory Management**
Create ResourceManager class that:
- Allocates dynamic arrays
- Implements deep copy
- Handles move operations
- Prevents memory leaks

### **Exercise 3: Object Lifecycle**
Write program demonstrating:
- Constructor call orders
- Destructor call orders
- Copy vs move operations
- Exception safety

---

## 🎯 Checklist hoàn thành

- [ ] Understand constructor types và usage
- [ ] Implement proper destructors
- [ ] Use member initializer lists
- [ ] Apply Rule of Three/Five/Zero
- [ ] Practice RAII principles
- [ ] Complete 3 exercises

---

**Tiếp theo**: [Bài 3 - Inheritance](03_inheritance.md)
