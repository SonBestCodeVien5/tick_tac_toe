# 📝 Bài 4: Loops và Conditions trong C++

## 🎯 Mục tiêu học
- Nắm vững các loại loops (for, while, do-while)
- Hiểu về conditional statements (if, else, switch)
- Biết cách sử dụng logical operators
- Thực hành với game logic và input validation

---

## 📖 Lý thuyết

### 1. Conditional Statements (Câu lệnh điều kiện)

#### **If-else statement**
```cpp
if (condition) {
    // Code thực thi khi condition = true
} else if (another_condition) {
    // Code thực thi khi another_condition = true
} else {
    // Code thực thi khi tất cả conditions = false
}
```

#### **Ví dụ thực tế:**
```cpp
int score = 85;

if (score >= 90) {
    std::cout << "Grade: A" << std::endl;
} else if (score >= 80) {
    std::cout << "Grade: B" << std::endl;
} else if (score >= 70) {
    std::cout << "Grade: C" << std::endl;
} else if (score >= 60) {
    std::cout << "Grade: D" << std::endl;
} else {
    std::cout << "Grade: F" << std::endl;
}
```

#### **Logical Operators**
```cpp
// AND (&&)
if (age >= 18 && hasLicense) {
    std::cout << "Can drive" << std::endl;
}

// OR (||)
if (isWeekend || isHoliday) {
    std::cout << "No work today!" << std::endl;
}

// NOT (!)
if (!isGameOver) {
    std::cout << "Game continues..." << std::endl;
}

// Combination
if ((score > 80 && attendance > 90) || hasExtraCredit) {
    std::cout << "Passed the course!" << std::endl;
}
```

#### **Switch statement**
```cpp
int choice;
std::cout << "Enter menu choice (1-4): ";
std::cin >> choice;

switch (choice) {
    case 1:
        std::cout << "New Game selected" << std::endl;
        break;
    case 2:
        std::cout << "Load Game selected" << std::endl;
        break;
    case 3:
        std::cout << "Settings selected" << std::endl;
        break;
    case 4:
        std::cout << "Exit selected" << std::endl;
        break;
    default:
        std::cout << "Invalid choice!" << std::endl;
        break;
}
```

### 2. Loops (Vòng lặp)

#### **For Loop**
```cpp
// Cú pháp cơ bản
for (initialization; condition; increment/decrement) {
    // Code block
}

// Ví dụ
for (int i = 0; i < 10; i++) {
    std::cout << i << " ";
}

// Range-based for loop (C++11)
int numbers[] = {1, 2, 3, 4, 5};
for (int num : numbers) {
    std::cout << num << " ";
}
```

#### **While Loop**
```cpp
// Kiểm tra condition trước khi thực thi
while (condition) {
    // Code block
    // Nhớ update condition để tránh infinite loop
}

// Ví dụ
int count = 0;
while (count < 5) {
    std::cout << "Count: " << count << std::endl;
    count++;  // Increment để tránh infinite loop
}
```

#### **Do-While Loop**
```cpp
// Thực thi ít nhất 1 lần, rồi mới kiểm tra condition
do {
    // Code block
} while (condition);

// Ví dụ - Menu loop
int choice;
do {
    std::cout << "1. Play\n2. Exit\nChoice: ";
    std::cin >> choice;
    
    if (choice == 1) {
        std::cout << "Starting game..." << std::endl;
    }
} while (choice != 2);
```

### 3. Loop Control Statements

#### **Break statement**
```cpp
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;  // Thoát khỏi loop ngay lập tức
    }
    std::cout << i << " ";
}
// Output: 0 1 2 3 4
```

#### **Continue statement**
```cpp
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) {
        continue;  // Skip phần còn lại, tiếp tục iteration tiếp theo
    }
    std::cout << i << " ";
}
// Output: 1 3 5 7 9 (chỉ số lẻ)
```

---

## 💡 Ví dụ thực tế: Game Loop và Input Validation

```cpp
#include <iostream>
#include <string>
#include <limits>

// Function to clear input buffer
void clearInputBuffer() {
    std::cin.clear();
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
}

// Function to get valid integer input
int getValidInput(int min, int max) {
    int input;
    while (true) {
        std::cout << "Enter number (" << min << "-" << max << "): ";
        
        if (std::cin >> input) {
            if (input >= min && input <= max) {
                return input;
            } else {
                std::cout << "Invalid range! Please try again." << std::endl;
            }
        } else {
            std::cout << "Invalid input! Please enter a number." << std::endl;
            clearInputBuffer();
        }
    }
}

// Game menu function
void displayGameMenu() {
    std::cout << "\n=== TIC TAC TOE MENU ===" << std::endl;
    std::cout << "1. Start New Game" << std::endl;
    std::cout << "2. Game Rules" << std::endl;
    std::cout << "3. View Statistics" << std::endl;
    std::cout << "4. Exit" << std::endl;
}

// Game rules function
void displayGameRules() {
    std::cout << "\n=== GAME RULES ===" << std::endl;
    std::cout << "1. Players take turns placing X and O" << std::endl;
    std::cout << "2. First to get 3 in a row wins" << std::endl;
    std::cout << "3. Rows, columns, or diagonals count" << std::endl;
    std::cout << "4. If board is full, it's a tie" << std::endl;
}

// Simple statistics display
void displayStatistics(int wins, int losses, int ties) {
    std::cout << "\n=== GAME STATISTICS ===" << std::endl;
    std::cout << "Wins: " << wins << std::endl;
    std::cout << "Losses: " << losses << std::endl;
    std::cout << "Ties: " << ties << std::endl;
    
    int total_games = wins + losses + ties;
    if (total_games > 0) {
        double win_rate = (double)wins / total_games * 100;
        std::cout << "Win Rate: " << win_rate << "%" << std::endl;
    }
}

// Input validation for coordinates
bool getPlayerMove(int& row, int& col) {
    std::cout << "Enter your move (row col): ";
    
    if (std::cin >> row >> col) {
        // Convert to 0-based indexing if user enters 1-based
        if (row >= 1 && row <= 3 && col >= 1 && col <= 3) {
            row--;  // Convert to 0-based
            col--;  // Convert to 0-based
            return true;
        }
    }
    
    std::cout << "Invalid input! Use format: row(1-3) col(1-3)" << std::endl;
    clearInputBuffer();
    return false;
}

// Main game loop demonstration
int main() {
    std::cout << "=== LOOPS & CONDITIONS DEMO ===" << std::endl;
    
    // Game statistics
    int wins = 0, losses = 0, ties = 0;
    bool game_running = true;
    
    // Main menu loop
    while (game_running) {
        displayGameMenu();
        int choice = getValidInput(1, 4);
        
        switch (choice) {
            case 1: {
                std::cout << "\n=== STARTING NEW GAME ===" << std::endl;
                
                // Simple game simulation
                bool game_active = true;
                int turn = 1;
                
                while (game_active && turn <= 9) {  // Max 9 turns
                    std::cout << "\nTurn " << turn << std::endl;
                    
                    // Determine current player
                    char current_player = (turn % 2 == 1) ? 'X' : 'O';
                    std::cout << "Player " << current_player << "'s turn" << std::endl;
                    
                    // Get player move (simplified)
                    int row, col;
                    bool valid_move = false;
                    
                    do {
                        valid_move = getPlayerMove(row, col);
                        if (valid_move) {
                            std::cout << "Move placed at (" << (row+1) << ", " << (col+1) << ")" << std::endl;
                        }
                    } while (!valid_move);
                    
                    // Simulate random game end for demo
                    if (turn >= 5) {  // Game can end after turn 5
                        int random_end = rand() % 3;
                        if (random_end == 0) {
                            std::cout << "Player " << current_player << " wins!" << std::endl;
                            wins++;
                            game_active = false;
                        }
                    }
                    
                    turn++;
                }
                
                if (game_active) {
                    std::cout << "Game ended in a tie!" << std::endl;
                    ties++;
                }
                break;
            }
            
            case 2:
                displayGameRules();
                break;
                
            case 3:
                displayStatistics(wins, losses, ties);
                break;
                
            case 4:
                std::cout << "Thanks for playing! Goodbye!" << std::endl;
                game_running = false;
                break;
        }
        
        // Pause before showing menu again
        if (game_running) {
            std::cout << "\nPress Enter to continue...";
            std::cin.ignore();
            std::cin.get();
        }
    }
    
    return 0;
}
```

---

## 🔍 Kiến thức nâng cao

### 1. Nested Loops
```cpp
// In bảng cửu chương
for (int i = 1; i <= 9; i++) {
    for (int j = 1; j <= 9; j++) {
        std::cout << i << " x " << j << " = " << (i * j) << "\t";
    }
    std::cout << std::endl;
}

// 2D array traversal
int matrix[3][3] = {{1,2,3}, {4,5,6}, {7,8,9}};
for (int row = 0; row < 3; row++) {
    for (int col = 0; col < 3; col++) {
        std::cout << matrix[row][col] << " ";
    }
    std::cout << std::endl;
}
```

### 2. Ternary Operator (Conditional operator)
```cpp
// condition ? value_if_true : value_if_false
int max_value = (a > b) ? a : b;
std::string result = (score >= 60) ? "Pass" : "Fail";
char current_player = (turn % 2 == 0) ? 'X' : 'O';
```

### 3. Short-circuit Evaluation
```cpp
// && stops evaluating if first condition is false
if (ptr != nullptr && ptr->value > 0) {
    // Safe to access ptr->value
}

// || stops evaluating if first condition is true
if (isAdmin || (hasPermission && isLoggedIn)) {
    // Grant access
}
```

---

## 📝 Bài tập về nhà

### **Bài 1: Grade Calculator**
Viết chương trình:
1. Nhập điểm của nhiều môn học (dừng khi nhập -1)
2. Tính điểm trung bình
3. Xếp loại học tập (A: 90+, B: 80-89, C: 70-79, D: 60-69, F: <60)
4. Hiển thị kết quả chi tiết

### **Bài 2: Number Guessing Game**
Tạo game đoán số:
1. Computer random một số từ 1-100
2. Player đoán số
3. Hiển thị "Too high", "Too low", hoặc "Correct"
4. Đếm số lần đoán
5. Hỏi có muốn chơi lại không

### **Bài 3: Simple Calculator Menu**
Tạo calculator với menu:
```
1. Addition
2. Subtraction  
3. Multiplication
4. Division
5. Exit
```
- Validate input
- Handle division by zero
- Loop until user chooses exit

### **Bài 4: Pattern Printing**
Viết chương trình in các patterns:
```cpp
// Pattern 1: Right triangle
*
**
***
****
*****

// Pattern 2: Number pyramid
    1
   121
  12321
 1234321
123454321

// Pattern 3: Diamond
   *
  ***
 *****
*******
 *****
  ***
   *
```

---

## 🎯 Checklist hoàn thành

- [ ] Hiểu if-else và logical operators
- [ ] Nắm vững 3 loại loops
- [ ] Biết sử dụng break và continue
- [ ] Validate input đúng cách
- [ ] Làm được 4 bài tập
- [ ] Chạy thành công demo program

---

## ⚠️ Common Mistakes

### **1. Infinite loops**
```cpp
int i = 0;
while (i < 10) {
    std::cout << i;
    // ❌ Quên increment i, loop vô tận!
}

// ✅ Fix:
while (i < 10) {
    std::cout << i;
    i++;  // Đừng quên increment!
}
```

### **2. Assignment vs Comparison**
```cpp
int x = 5;
if (x = 10) {  // ❌ Assignment! Always true
    std::cout << "This always executes";
}

if (x == 10) {  // ✅ Comparison
    std::cout << "This is correct";
}
```

### **3. Switch without break**
```cpp
switch (choice) {
    case 1:
        std::cout << "One";
        // ❌ Missing break, falls through!
    case 2:
        std::cout << "Two";  // This also executes for case 1
        break;
}
```

---

## 💡 Best Practices

1. **Always validate user input**
2. **Use meaningful variable names** in loops
3. **Avoid deep nesting** - extract to functions
4. **Add comments** for complex conditions
5. **Initialize loop variables** properly
6. **Use const** for values that don't change

---

## 📚 Tài liệu tham khảo
- [cppreference - if statement](https://en.cppreference.com/w/cpp/language/if)
- [cppreference - for loop](https://en.cppreference.com/w/cpp/language/for)
- [learncpp.com - Control flow](https://www.learncpp.com/cpp-tutorial/introduction-to-control-flow/)

---

**Trước đó**: [Bài 3 - Functions](03_functions.md)  
**Tiếp theo**: [Bài 5 - Input/Output](05_input_output.md)
