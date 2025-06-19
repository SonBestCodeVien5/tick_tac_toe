# 📝 Bài 1: Classes và Objects trong C++

## 🎯 Mục tiêu học
- Hiểu về Object-Oriented Programming (OOP)
- Nắm vững cách tạo classes và objects
- Biết về access specifiers (public, private, protected)
- Thiết kế classes cho game Tic Tac Toe

---

## 📖 Lý thuyết

### 1. OOP là gì?

**Object-Oriented Programming** là paradigm lập trình dựa trên concept của **"objects"** - entities chứa:
- **Data** (attributes/properties)
- **Methods** (functions/behaviors)

#### **4 nguyên lý chính của OOP:**
1. **Encapsulation** - Đóng gói data và methods
2. **Inheritance** - Kế thừa từ class khác
3. **Polymorphism** - Một interface, nhiều implementations
4. **Abstraction** - Ẩn complexity, chỉ expose cần thiết

### 2. Classes vs Objects

#### **Class** = Blueprint (Bản thiết kế)
```cpp
class Car {
private:
    string brand;
    int year;
    
public:
    void start();
    void stop();
};
```

#### **Object** = Instance (Thể hiện cụ thể)
```cpp
Car my_car;        // Object 1
Car your_car;      // Object 2
```

### 3. Defining Classes

#### **Basic class syntax:**
```cpp
class ClassName {
private:
    // Private members (data and functions)
    
public:
    // Public members (interface)
    
protected:
    // Protected members (for inheritance)
};
```

#### **Game example: Player class**
```cpp
class Player {
private:
    string name;
    char symbol;     // 'X' or 'O'
    int score;
    bool is_human;
    
public:
    // Constructor
    Player(string player_name, char player_symbol);
    
    // Getters (accessor methods)
    string getName() const;
    char getSymbol() const;
    int getScore() const;
    bool isHuman() const;
    
    // Setters (mutator methods)
    void setName(string new_name);
    void setScore(int new_score);
    
    // Behaviors
    void increaseScore();
    void displayStats();
    void makeMove(int row, int col);
};
```

### 4. Access Specifiers

#### **Private** - Chỉ class đó access được
```cpp
class BankAccount {
private:
    double balance;    // Chỉ class này access được
    string pin;        // Sensitive data
    
public:
    void deposit(double amount) {
        balance += amount;  // ✅ OK - same class
    }
};

// Outside class:
BankAccount account;
account.balance = 1000;  // ❌ Error! Private member
```

#### **Public** - Ai cũng access được
```cpp
class Calculator {
public:
    int add(int a, int b) {
        return a + b;       // Anyone can call this
    }
    
    int last_result;        // Anyone can access this
};

Calculator calc;
int result = calc.add(5, 3);  // ✅ OK
calc.last_result = 100;       // ✅ OK
```

#### **Protected** - Class và derived classes access được
```cpp
class Vehicle {
protected:
    int max_speed;     // Derived classes can access
    
public:
    void setMaxSpeed(int speed) {
        max_speed = speed;
    }
};

class Car : public Vehicle {
public:
    void showSpeed() {
        cout << max_speed;  // ✅ OK - derived class
    }
};
```

---

## 💡 Ví dụ thực tế: Game Classes

### **1. Player Class Implementation**

**Header file (Player.h):**
```cpp
#ifndef PLAYER_H
#define PLAYER_H

#include <string>
#include <iostream>

class Player {
private:
    std::string name;
    char symbol;
    int score;
    int games_played;
    bool is_human;

public:
    // Constructors
    Player();  // Default constructor
    Player(const std::string& player_name, char player_symbol, bool human = true);
    
    // Getters
    std::string getName() const;
    char getSymbol() const;
    int getScore() const;
    int getGamesPlayed() const;
    bool isHuman() const;
    double getWinRate() const;
    
    // Setters
    void setName(const std::string& new_name);
    void setSymbol(char new_symbol);
    
    // Game actions
    void increaseScore();
    void incrementGamesPlayed();
    void resetStats();
    
    // Display
    void displayStats() const;
    void displayBrief() const;
    
    // Operators
    bool operator==(const Player& other) const;
};

#endif
```

**Implementation file (Player.cpp):**
```cpp
#include "Player.h"
#include <iomanip>

// Default constructor
Player::Player() : name("Unknown"), symbol(' '), score(0), games_played(0), is_human(true) {}

// Parameterized constructor
Player::Player(const std::string& player_name, char player_symbol, bool human)
    : name(player_name), symbol(player_symbol), score(0), games_played(0), is_human(human) {}

// Getters
std::string Player::getName() const {
    return name;
}

char Player::getSymbol() const {
    return symbol;
}

int Player::getScore() const {
    return score;
}

int Player::getGamesPlayed() const {
    return games_played;
}

bool Player::isHuman() const {
    return is_human;
}

double Player::getWinRate() const {
    if (games_played == 0) return 0.0;
    return (double)score / games_played * 100.0;
}

// Setters
void Player::setName(const std::string& new_name) {
    if (!new_name.empty()) {
        name = new_name;
    }
}

void Player::setSymbol(char new_symbol) {
    if (new_symbol == 'X' || new_symbol == 'O') {
        symbol = new_symbol;
    }
}

// Game actions
void Player::increaseScore() {
    score++;
}

void Player::incrementGamesPlayed() {
    games_played++;
}

void Player::resetStats() {
    score = 0;
    games_played = 0;
}

// Display methods
void Player::displayStats() const {
    std::cout << "=== PLAYER STATISTICS ===" << std::endl;
    std::cout << "Name: " << name << std::endl;
    std::cout << "Symbol: " << symbol << std::endl;
    std::cout << "Type: " << (is_human ? "Human" : "AI") << std::endl;
    std::cout << "Score: " << score << std::endl;
    std::cout << "Games Played: " << games_played << std::endl;
    std::cout << "Win Rate: " << std::fixed << std::setprecision(1) 
              << getWinRate() << "%" << std::endl;
}

void Player::displayBrief() const {
    std::cout << name << " (" << symbol << ") - " 
              << score << " wins";
}

// Operators
bool Player::operator==(const Player& other) const {
    return name == other.name && symbol == other.symbol;
}
```

### **2. GameBoard Class**

**Header file (GameBoard.h):**
```cpp
#ifndef GAMEBOARD_H
#define GAMEBOARD_H

#include <iostream>
#include <vector>

class GameBoard {
private:
    static const int SIZE = 3;
    char board[SIZE][SIZE];
    int move_count;
    
public:
    // Constructor
    GameBoard();
    
    // Board management
    void initialize();
    void clear();
    bool isEmpty(int row, int col) const;
    bool isValidPosition(int row, int col) const;
    
    // Game operations
    bool makeMove(int row, int col, char symbol);
    char getCell(int row, int col) const;
    int getMoveCount() const;
    bool isFull() const;
    
    // Win checking
    bool checkWin(char symbol) const;
    bool checkRow(int row, char symbol) const;
    bool checkColumn(int col, char symbol) const;
    bool checkDiagonals(char symbol) const;
    
    // Display
    void display() const;
    void displayWithCoordinates() const;
    
    // Utility
    std::vector<std::pair<int, int>> getAvailableMoves() const;
    void getGameState(char state[3][3]) const;
};

#endif
```

### **3. Usage Example**

```cpp
#include <iostream>
#include "Player.h"
#include "GameBoard.h"

int main() {
    std::cout << "=== OOP DEMO: TIC TAC TOE CLASSES ===" << std::endl;
    
    // Create players
    Player player1("Alice", 'X', true);
    Player player2("Bob", 'O', true);
    
    // Create game board
    GameBoard board;
    
    std::cout << "\n=== PLAYERS ===" << std::endl;
    player1.displayBrief();
    std::cout << std::endl;
    player2.displayBrief();
    std::cout << std::endl;
    
    std::cout << "\n=== INITIAL BOARD ===" << std::endl;
    board.displayWithCoordinates();
    
    // Simulate some moves
    std::cout << "\n=== GAME SIMULATION ===" << std::endl;
    
    // Player 1 moves
    if (board.makeMove(0, 0, player1.getSymbol())) {
        std::cout << player1.getName() << " places " << player1.getSymbol() 
                  << " at (1,1)" << std::endl;
    }
    
    // Player 2 moves
    if (board.makeMove(1, 1, player2.getSymbol())) {
        std::cout << player2.getName() << " places " << player2.getSymbol() 
                  << " at (2,2)" << std::endl;
    }
    
    // More moves...
    board.makeMove(0, 1, player1.getSymbol());
    board.makeMove(2, 0, player2.getSymbol());
    board.makeMove(0, 2, player1.getSymbol());
    
    std::cout << "\n=== FINAL BOARD ===" << std::endl;
    board.display();
    
    // Check win condition
    if (board.checkWin(player1.getSymbol())) {
        std::cout << "\n🎉 " << player1.getName() << " wins!" << std::endl;
        player1.increaseScore();
    } else if (board.checkWin(player2.getSymbol())) {
        std::cout << "\n🎉 " << player2.getName() << " wins!" << std::endl;
        player2.increaseScore();
    } else if (board.isFull()) {
        std::cout << "\n🤝 It's a tie!" << std::endl;
    }
    
    // Update game stats
    player1.incrementGamesPlayed();
    player2.incrementGamesPlayed();
    
    std::cout << "\n=== UPDATED STATS ===" << std::endl;
    player1.displayStats();
    std::cout << std::endl;
    player2.displayStats();
    
    return 0;
}
```

---

## 🔍 Kiến thức nâng cao

### 1. Const Methods
```cpp
class Player {
private:
    string name;
    
public:
    // Const method - không thay đổi object
    string getName() const {
        return name;  // ✅ OK - chỉ đọc
        // name = "new";  // ❌ Error! Const method
    }
    
    // Non-const method
    void setName(string new_name) {
        name = new_name;  // ✅ OK - có thể modify
    }
};
```

### 2. Static Members
```cpp
class GameConfig {
private:
    static int total_games;  // Shared by all instances
    
public:
    static const int BOARD_SIZE = 3;  // Class constant
    
    static int getTotalGames() {
        return total_games;
    }
    
    static void incrementGames() {
        total_games++;
    }
};

// Initialize static member
int GameConfig::total_games = 0;

// Usage
cout << GameConfig::BOARD_SIZE;    // No object needed
GameConfig::incrementGames();      // No object needed
```

### 3. Friend Functions
```cpp
class Player {
private:
    int score;
    
public:
    Player(int s) : score(s) {}
    
    // Friend function can access private members
    friend void displayComparison(const Player& p1, const Player& p2);
};

void displayComparison(const Player& p1, const Player& p2) {
    cout << "Player 1 score: " << p1.score << endl;  // ✅ Can access private
    cout << "Player 2 score: " << p2.score << endl;
}
```

---

## 📝 Bài tập về nhà

### **Bài 1: Student Class**
Tạo Student class với:
- Private: name, id, grades (array), subject_count
- Public: constructors, getters/setters, calculateGPA(), displayReport()
- Implement trong .h và .cpp files

### **Bài 2: Rectangle Class**
Tạo Rectangle class với:
- Private: width, height
- Public: getArea(), getPerimeter(), isSquare(), displayInfo()
- Validation trong setters (width/height > 0)

### **Bài 3: Game Inventory**
Tạo Inventory class cho RPG game:
- Private: items (array), max_capacity, current_count
- Public: addItem(), removeItem(), isFull(), displayItems()
- Implement item searching và sorting

### **Bài 4: Enhanced Player Class**
Mở rộng Player class với:
- Additional stats: level, experience, health
- Methods: levelUp(), gainExperience(), takeDamage()
- Save/load player data to file

---

## 🎯 Checklist hoàn thành

- [ ] Hiểu 4 nguyên lý OOP
- [ ] Biết tạo classes với proper encapsulation
- [ ] Nắm vững access specifiers
- [ ] Phân biệt được .h và .cpp files
- [ ] Implement được basic game classes
- [ ] Làm được 4 bài tập
- [ ] Code compile và chạy thành công

---

## ⚠️ Common Mistakes

### **1. Forgetting semicolon after class**
```cpp
class Player {
    // ...
}  // ❌ Missing semicolon!

class Player {
    // ...
};  // ✅ Correct
```

### **2. Accessing private members**
```cpp
class Player {
private:
    string name;
};

Player p;
cout << p.name;  // ❌ Error! Private member

// ✅ Use getter:
cout << p.getName();
```

### **3. Not implementing declared methods**
```cpp
// Player.h
class Player {
public:
    void displayStats();  // Declared but...
};

// Player.cpp - forgot to implement!
// ❌ Linker error!
```

---

## 💡 Best Practices

1. **Use meaningful class names** (PascalCase)
2. **Keep data private** - provide getters/setters
3. **Make methods const** when they don't modify object
4. **Initialize all members** in constructor
5. **Use header guards** (#ifndef, #define, #endif)
6. **Separate declaration and implementation** (.h và .cpp)

---

## 📚 Tài liệu tham khảo
- [cppreference - Classes](https://en.cppreference.com/w/cpp/language/classes)
- [learncpp.com - Object-oriented programming](https://www.learncpp.com/cpp-tutorial/welcome-to-object-oriented-programming/)
- [C++ Core Guidelines - Classes](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-class)

---

**Trước đó**: [Tuần 2 - Git & GitHub](../week02_git_github/01_git_basics.md)  
**Tiếp theo**: [Bài 2 - Constructors và Destructors](02_constructors_destructors.md)
