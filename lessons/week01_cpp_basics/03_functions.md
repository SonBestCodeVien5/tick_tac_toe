# 📝 Bài 3: Functions trong C++

## 🎯 Mục tiêu học
- Hiểu về functions và tại sao cần dùng
- Nắm vững cú pháp định nghĩa và gọi functions
- Biết về parameters, return values, và scope
- Thực hành với functions cho game logic

---

## 📖 Lý thuyết

### 1. Functions là gì?

Function là một khối code có thể tái sử dụng, thực hiện một nhiệm vụ cụ thể.

#### **Lợi ích của Functions:**
- **Code reusability** - Tái sử dụng code
- **Modularity** - Chia nhỏ chương trình
- **Maintainability** - Dễ bảo trì và debug
- **Readability** - Code dễ đọc hiểu

### 2. Cú pháp Function

```cpp
return_type function_name(parameter_list) {
    // Function body
    // Statements
    return value;  // (optional, nếu return_type không phải void)
}
```

#### **Ví dụ cơ bản:**
```cpp
// Function không trả về giá trị
void sayHello() {
    std::cout << "Hello World!" << std::endl;
}

// Function trả về giá trị
int add(int a, int b) {
    return a + b;
}

// Function với multiple parameters
double calculateAverage(double num1, double num2, double num3) {
    return (num1 + num2 + num3) / 3.0;
}
```

### 3. Function Declaration vs Definition

#### **Declaration (Prototype)**
```cpp
// Khai báo function trước main()
int multiply(int x, int y);
void displayMenu();
bool isValidMove(int row, int col);
```

#### **Definition**
```cpp
// Định nghĩa function sau main() hoặc trong file khác
int multiply(int x, int y) {
    return x * y;
}

void displayMenu() {
    std::cout << "1. New Game" << std::endl;
    std::cout << "2. Load Game" << std::endl;
    std::cout << "3. Exit" << std::endl;
}
```

### 4. Parameters và Arguments

#### **Pass by Value**
```cpp
void printNumber(int num) {  // num là copy của argument
    num = 100;               // Không ảnh hưởng đến original variable
    std::cout << num << std::endl;
}

int main() {
    int value = 50;
    printNumber(value);      // value vẫn là 50
    std::cout << value << std::endl;  // Output: 50
}
```

#### **Pass by Reference**
```cpp
void doubleNumber(int& num) {  // num là reference đến original variable
    num *= 2;                  // Thay đổi original variable
}

int main() {
    int value = 50;
    doubleNumber(value);       // value bây giờ là 100
    std::cout << value << std::endl;  // Output: 100
}
```

#### **Pass by Pointer**
```cpp
void tripleNumber(int* num) {
    *num *= 3;                 // Thay đổi value thông qua pointer
}

int main() {
    int value = 50;
    tripleNumber(&value);      // Pass address của value
    std::cout << value << std::endl;  // Output: 150
}
```

---

## 💡 Ví dụ thực tế: Game Functions

```cpp
#include <iostream>
#include <string>

// Constants
const int BOARD_SIZE = 3;
const char EMPTY = ' ';
const char PLAYER_X = 'X';
const char PLAYER_O = 'O';

// Function prototypes
void initializeBoard(char board[BOARD_SIZE][BOARD_SIZE]);
void displayBoard(char board[BOARD_SIZE][BOARD_SIZE]);
bool isValidMove(char board[BOARD_SIZE][BOARD_SIZE], int row, int col);
void makeMove(char board[BOARD_SIZE][BOARD_SIZE], int row, int col, char player);
bool checkWin(char board[BOARD_SIZE][BOARD_SIZE], char player);
bool isBoardFull(char board[BOARD_SIZE][BOARD_SIZE]);
char switchPlayer(char current_player);
void displayMenu();
int getPlayerChoice();

// Initialize empty board
void initializeBoard(char board[BOARD_SIZE][BOARD_SIZE]) {
    for (int row = 0; row < BOARD_SIZE; row++) {
        for (int col = 0; col < BOARD_SIZE; col++) {
            board[row][col] = EMPTY;
        }
    }
}

// Display current board state
void displayBoard(char board[BOARD_SIZE][BOARD_SIZE]) {
    std::cout << "\n   0   1   2\n";
    for (int row = 0; row < BOARD_SIZE; row++) {
        std::cout << row << "  ";
        for (int col = 0; col < BOARD_SIZE; col++) {
            std::cout << board[row][col];
            if (col < BOARD_SIZE - 1) std::cout << " | ";
        }
        std::cout << std::endl;
        if (row < BOARD_SIZE - 1) {
            std::cout << "  -----------\n";
        }
    }
    std::cout << std::endl;
}

// Check if move is valid
bool isValidMove(char board[BOARD_SIZE][BOARD_SIZE], int row, int col) {
    // Check bounds
    if (row < 0 || row >= BOARD_SIZE || col < 0 || col >= BOARD_SIZE) {
        return false;
    }
    // Check if cell is empty
    return board[row][col] == EMPTY;
}

// Make a move on the board
void makeMove(char board[BOARD_SIZE][BOARD_SIZE], int row, int col, char player) {
    if (isValidMove(board, row, col)) {
        board[row][col] = player;
    }
}

// Check if player has won
bool checkWin(char board[BOARD_SIZE][BOARD_SIZE], char player) {
    // Check rows
    for (int row = 0; row < BOARD_SIZE; row++) {
        if (board[row][0] == player && board[row][1] == player && board[row][2] == player) {
            return true;
        }
    }
    
    // Check columns
    for (int col = 0; col < BOARD_SIZE; col++) {
        if (board[0][col] == player && board[1][col] == player && board[2][col] == player) {
            return true;
        }
    }
    
    // Check diagonals
    if (board[0][0] == player && board[1][1] == player && board[2][2] == player) {
        return true;
    }
    if (board[0][2] == player && board[1][1] == player && board[2][0] == player) {
        return true;
    }
    
    return false;
}

// Check if board is full
bool isBoardFull(char board[BOARD_SIZE][BOARD_SIZE]) {
    for (int row = 0; row < BOARD_SIZE; row++) {
        for (int col = 0; col < BOARD_SIZE; col++) {
            if (board[row][col] == EMPTY) {
                return false;
            }
        }
    }
    return true;
}

// Switch between players
char switchPlayer(char current_player) {
    return (current_player == PLAYER_X) ? PLAYER_O : PLAYER_X;
}

// Display game menu
void displayMenu() {
    std::cout << "\n=== TIC TAC TOE MENU ===" << std::endl;
    std::cout << "1. Start New Game" << std::endl;
    std::cout << "2. Game Rules" << std::endl;
    std::cout << "3. Exit" << std::endl;
    std::cout << "Choose option (1-3): ";
}

// Get player menu choice
int getPlayerChoice() {
    int choice;
    std::cin >> choice;
    return choice;
}

// Main game demonstration
int main() {
    char board[BOARD_SIZE][BOARD_SIZE];
    
    std::cout << "=== FUNCTION DEMO: TIC TAC TOE ===" << std::endl;
    
    // Initialize game
    initializeBoard(board);
    std::cout << "Empty board created:" << std::endl;
    displayBoard(board);
    
    // Simulate some moves
    std::cout << "Making some moves..." << std::endl;
    makeMove(board, 0, 0, PLAYER_X);  // X at (0,0)
    makeMove(board, 1, 1, PLAYER_O);  // O at (1,1)
    makeMove(board, 0, 1, PLAYER_X);  // X at (0,1)
    makeMove(board, 2, 0, PLAYER_O);  // O at (2,0)
    makeMove(board, 0, 2, PLAYER_X);  // X at (0,2)
    
    displayBoard(board);
    
    // Check win condition
    if (checkWin(board, PLAYER_X)) {
        std::cout << "Player X wins!" << std::endl;
    } else if (checkWin(board, PLAYER_O)) {
        std::cout << "Player O wins!" << std::endl;
    } else if (isBoardFull(board)) {
        std::cout << "It's a tie!" << std::endl;
    } else {
        std::cout << "Game continues..." << std::endl;
    }
    
    // Display menu
    displayMenu();
    
    return 0;
}
```

---

## 🔍 Kiến thức nâng cao

### 1. Function Overloading
```cpp
// Có thể có nhiều functions cùng tên nhưng khác parameters
int add(int a, int b) {
    return a + b;
}

double add(double a, double b) {
    return a + b;
}

int add(int a, int b, int c) {
    return a + b + c;
}
```

### 2. Default Parameters
```cpp
void greetPlayer(std::string name, std::string greeting = "Hello") {
    std::cout << greeting << ", " << name << "!" << std::endl;
}

// Usage
greetPlayer("Alice");              // "Hello, Alice!"
greetPlayer("Bob", "Welcome");     // "Welcome, Bob!"
```

### 3. Inline Functions
```cpp
inline int square(int x) {
    return x * x;
}
// Compiler có thể thay thế function call bằng code trực tiếp
```

### 4. Recursive Functions
```cpp
int factorial(int n) {
    if (n <= 1) {
        return 1;
    }
    return n * factorial(n - 1);
}
```

---

## 📝 Bài tập về nhà

### **Bài 1: Calculator Functions**
Tạo calculator với các functions:
```cpp
int add(int a, int b);
int subtract(int a, int b);
int multiply(int a, int b);
double divide(int a, int b);
void displayResult(int result);
```

### **Bài 2: Game Utility Functions**
Viết các functions cho game:
```cpp
void clearScreen();                    // Xóa màn hình
void pauseGame();                     // Tạm dừng game
std::string getPlayerName();          // Nhập tên người chơi
void displayWelcome(std::string name); // Chào mừng
bool confirmExit();                   // Xác nhận thoát
```

### **Bài 3: Array Functions**
Tạo functions để xử lý arrays:
```cpp
void printArray(int arr[], int size);
int findMax(int arr[], int size);
int findMin(int arr[], int size);
double calculateAverage(int arr[], int size);
void sortArray(int arr[], int size);
```

### **Bài 4: String Functions**
Viết functions xử lý strings:
```cpp
bool isPalindrome(std::string text);
std::string reverseString(std::string text);
int countVowels(std::string text);
std::string toUpperCase(std::string text);
```

---

## 🎯 Checklist hoàn thành

- [ ] Hiểu cú pháp function declaration/definition
- [ ] Biết phân biệt pass by value vs reference
- [ ] Nắm vững return values và parameters
- [ ] Làm được 4 bài tập
- [ ] Chạy thành công ví dụ game functions

---

## ⚠️ Common Mistakes

### **1. Missing return statement**
```cpp
int add(int a, int b) {
    int sum = a + b;
    // ❌ Thiếu return statement!
}

int add(int a, int b) {
    return a + b;  // ✅ Correct
}
```

### **2. Function không được declare**
```cpp
int main() {
    printHello();  // ❌ Error: 'printHello' was not declared
}

void printHello() {
    std::cout << "Hello!";
}
```

### **3. Parameter type mismatch**
```cpp
void processScore(int score);

int main() {
    processScore("100");  // ❌ Passing string to int parameter
    processScore(100);    // ✅ Correct
}
```

---

## 💡 Best Practices

1. **Function names** should be descriptive verbs
2. **Keep functions small** - One function, one purpose
3. **Use const** for parameters that shouldn't change
4. **Validate input** parameters when necessary
5. **Document complex functions** with comments

---

## 📚 Tài liệu tham khảo
- [cppreference - Functions](https://en.cppreference.com/w/cpp/language/functions)
- [learncpp.com - Functions](https://www.learncpp.com/cpp-tutorial/introduction-to-functions/)
- [C++ Core Guidelines - Functions](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-functions)

---

**Trước đó**: [Bài 2 - Arrays và Strings](02_arrays_strings.md)  
**Tiếp theo**: [Bài 4 - Loops và Conditions](04_loops_conditions.md)
