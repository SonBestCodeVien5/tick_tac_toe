# 💪 Bài tập Tuần 1: C++ Cơ bản

## 🎯 Mục tiêu
Củng cố kiến thức C++ cơ bản thông qua các bài tập thực hành từ dễ đến khó.

---

## 📝 **BÀI TẬP 1: CALCULATOR ĐƠN GIẢN**

### **Yêu cầu:**
Tạo chương trình calculator với menu:
```
=== SIMPLE CALCULATOR ===
1. Addition (+)
2. Subtraction (-)
3. Multiplication (*)
4. Division (/)
5. Exit
Choose operation (1-5): 
```

### **Chức năng:**
- Input validation cho menu choice
- Input validation cho numbers  
- Handle division by zero
- Display formatted results
- Loop until user chooses exit

### **Template code:**
```cpp
#include <iostream>
#include <iomanip>
#include <limits>

using namespace std;

// Function prototypes
void displayMenu();
int getValidChoice();
double getValidNumber(const string& prompt);
void clearInputBuffer();
double add(double a, double b);
double subtract(double a, double b);
double multiply(double a, double b);
double divide(double a, double b);
void displayResult(double result);

int main() {
    // TODO: Implement main calculator loop
    return 0;
}

// TODO: Implement all functions
```

### **Expected Output:**
```
=== SIMPLE CALCULATOR ===
1. Addition (+)
2. Subtraction (-)
3. Multiplication (*)
4. Division (/)
5. Exit
Choose operation (1-5): 1

Enter first number: 15.5
Enter second number: 7.2
Result: 15.50 + 7.20 = 22.70

Press Enter to continue...
```

---

## 📝 **BÀI TẬP 2: GAME BOARD MANAGER**

### **Yêu cầu:**
Tạo system quản lý game board 3x3 với các chức năng:

### **Chức năng:**
1. **Initialize board** - Tạo board trống
2. **Display board** - Hiển thị board đẹp mắt
3. **Place symbol** - Đặt X hoặc O vào vị trí
4. **Check position** - Kiểm tra vị trí có trống không
5. **Count symbols** - Đếm số X và O trên board
6. **Clear board** - Xóa tất cả symbols

### **Template:**
```cpp
#include <iostream>
#include <iomanip>

using namespace std;

// Constants
const int BOARD_SIZE = 3;
const char EMPTY = ' ';
const char PLAYER_X = 'X';
const char PLAYER_O = 'O';

// Function prototypes
void initializeBoard(char board[BOARD_SIZE][BOARD_SIZE]);
void displayBoard(char board[BOARD_SIZE][BOARD_SIZE]);
void displayMenu();
bool isValidPosition(int row, int col);
bool isEmpty(char board[BOARD_SIZE][BOARD_SIZE], int row, int col);
void placeSymbol(char board[BOARD_SIZE][BOARD_SIZE], int row, int col, char symbol);
void countSymbols(char board[BOARD_SIZE][BOARD_SIZE], int& x_count, int& o_count);
void clearBoard(char board[BOARD_SIZE][BOARD_SIZE]);

int main() {
    char game_board[BOARD_SIZE][BOARD_SIZE];
    initializeBoard(game_board);
    
    // TODO: Implement menu system
    return 0;
}

// TODO: Implement all functions
```

### **Expected Output:**
```
=== GAME BOARD MANAGER ===

Current Board:
   0   1   2
0    |   |  
  -----------
1    |   |  
  -----------
2    |   |  

Menu:
1. Place X
2. Place O  
3. Clear Board
4. Show Statistics
5. Exit
Choice: 1

Enter position (row col): 0 0
X placed at (0,0)

Current Board:
   0   1   2
0  X |   |  
  -----------
1    |   |  
  -----------
2    |   |  

Statistics: X=1, O=0
```

---

## 📝 **BÀI TẬP 3: STUDENT GRADE PROCESSOR**

### **Yêu cầu:**
Tạo chương trình xử lý điểm sinh viên:

### **Chức năng:**
1. Input student info (name, student ID)
2. Input grades for multiple subjects
3. Calculate GPA và letter grade
4. Display formatted report
5. Save/load data to/from file

### **Grading Scale:**
- A: 90-100
- B: 80-89  
- C: 70-79
- D: 60-69
- F: Below 60

### **Template:**
```cpp
#include <iostream>
#include <string>
#include <fstream>
#include <iomanip>
#include <vector>

using namespace std;

// Student structure
struct Student {
    string name;
    string student_id;
    vector<double> grades;
    double gpa;
    char letter_grade;
};

// Function prototypes
void inputStudentInfo(Student& student);
void inputGrades(Student& student);
void calculateGPA(Student& student);
char getLetterGrade(double gpa);
void displayReport(const Student& student);
void saveToFile(const Student& student, const string& filename);
bool loadFromFile(Student& student, const string& filename);

int main() {
    Student student;
    
    cout << "=== STUDENT GRADE PROCESSOR ===" << endl;
    
    // TODO: Implement main program flow
    return 0;
}

// TODO: Implement all functions
```

---

## 📝 **BÀI TẬP 4: NUMBER GUESSING GAME**

### **Yêu cầu:**
Tạo game đoán số với AI smart:

### **Features:**
1. Computer generates random number (1-100)
2. Player guesses with hints (too high/low)
3. Track number of attempts
4. Difficulty levels (Easy: 1-50, Medium: 1-100, Hard: 1-200)
5. Statistics tracking (games played, best score, win rate)
6. AI hint system

### **Template:**
```cpp
#include <iostream>
#include <random>
#include <string>
#include <limits>

using namespace std;

// Game statistics
struct GameStats {
    int games_played;
    int total_attempts;
    int best_score;
    double average_attempts;
};

// Function prototypes
void displayMenu();
int getDifficultyLevel();
int generateRandomNumber(int min, int max);
int getPlayerGuess(int min, int max);
void provideHint(int guess, int target, int attempts);
void updateStats(GameStats& stats, int attempts);
void displayStats(const GameStats& stats);
bool playAgain();

int main() {
    GameStats stats = {0, 0, 999, 0.0};
    
    cout << "=== SMART NUMBER GUESSING GAME ===" << endl;
    
    // TODO: Implement game loop
    return 0;
}

// TODO: Implement all functions
```

### **Expected Gameplay:**
```
=== SMART NUMBER GUESSING GAME ===

Select Difficulty:
1. Easy (1-50)
2. Medium (1-100)  
3. Hard (1-200)
Choice: 2

I'm thinking of a number between 1 and 100.
You have unlimited attempts. Good luck!

Attempt 1: Enter your guess: 50
📈 Too low! Try a higher number.
Hint: The number is significantly higher.

Attempt 2: Enter your guess: 75
📉 Too high! Try a lower number.
Hint: You're getting warmer!

Attempt 3: Enter your guess: 65
🎉 Congratulations! You found it in 3 attempts!

=== GAME STATISTICS ===
Games Played: 1
Best Score: 3 attempts
Average Attempts: 3.0

Play again? (y/n):
```

---

## 🎯 **SUBMISSION CHECKLIST**

### **For each exercise:**
- [ ] Code compiles without errors
- [ ] All functions implemented
- [ ] Input validation works properly
- [ ] Output formatted nicely
- [ ] Comments explain complex logic
- [ ] Code follows C++ best practices

### **Bonus Points:**
- [ ] Add extra features beyond requirements
- [ ] Implement error handling
- [ ] Create user-friendly interface
- [ ] Add file I/O functionality
- [ ] Use appropriate data structures

---

## 💡 **HINTS & TIPS**

### **Input Validation:**
```cpp
// Safe integer input
int getValidInput(int min, int max) {
    int value;
    while (true) {
        if (cin >> value && value >= min && value <= max) {
            return value;
        }
        cout << "Invalid input! Try again: ";
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }
}
```

### **Random Numbers:**
```cpp
#include <random>

random_device rd;
mt19937 gen(rd());
uniform_int_distribution<> dis(1, 100);
int random_num = dis(gen);
```

### **File I/O:**
```cpp
// Save data
ofstream file("data.txt");
if (file.is_open()) {
    file << name << "," << score << endl;
    file.close();
}

// Load data  
ifstream file("data.txt");
string line;
while (getline(file, line)) {
    // Parse line...
}
```

---

## 📚 **RESOURCES**

- [C++ Reference](https://cppreference.com)
- [Input Validation Tutorial](https://www.learncpp.com/cpp-tutorial/stdcin-and-handling-invalid-input/)
- [Random Number Generation](https://en.cppreference.com/w/cpp/numeric/random)

---

**Next**: [Bài tập Tuần 2 - Git Practice](../week02/)
