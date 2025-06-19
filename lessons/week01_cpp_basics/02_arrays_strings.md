# 📝 Bài 2: Arrays và Strings trong C++

## 🎯 Mục tiêu học
- Hiểu về arrays và cách sử dụng
- Nắm vững string manipulation
- Biết cách làm việc với 2D arrays cho game board
- Thực hành với ví dụ game cờ caro

---

## 📖 Lý thuyết

### 1. Arrays (Mảng)

#### **Array cơ bản**
```cpp
// Khai báo và khởi tạo
int scores[5];                    // Array 5 phần tử, chưa khởi tạo
int numbers[5] = {10, 20, 30, 40, 50};  // Khởi tạo với values
int grades[] = {85, 90, 78, 92};  // Compiler tự tính size

// Truy cập phần tử
std::cout << numbers[0];          // Output: 10 (index bắt đầu từ 0)
numbers[2] = 35;                  // Thay đổi phần tử thứ 3
```

#### **Array properties**
```cpp
int arr[5] = {1, 2, 3, 4, 5};
int size = sizeof(arr) / sizeof(arr[0]);  // Tính số phần tử
std::cout << "Array size: " << size << std::endl;  // Output: 5
```

### 2. Multidimensional Arrays (Mảng nhiều chiều)

#### **2D Arrays cho Game Board**
```cpp
// Khai báo board 3x3 cho cờ caro
char board[3][3];

// Khởi tạo board trống
char game_board[3][3] = {
    {' ', ' ', ' '},
    {' ', ' ', ' '},
    {' ', ' ', ' '}
};

// Truy cập phần tử
board[0][0] = 'X';    // Đặt X ở góc trên trái
board[1][1] = 'O';    // Đặt O ở giữa
board[2][2] = 'X';    // Đặt X ở góc dưới phải
```

### 3. Strings (Chuỗi)

#### **C-style strings**
```cpp
char name[50] = "Player1";
char message[] = "Hello World!";
```

#### **std::string (Recommended)**
```cpp
#include <string>

std::string player_name = "Nguyen Van A";
std::string greeting = "Welcome to Tic Tac Toe!";

// String operations
int length = player_name.length();           // Độ dài
std::string full_msg = greeting + " " + player_name;  // Nối chuỗi
```

### 4. String Methods

```cpp
std::string text = "Hello World";

// Basic operations
text.length();              // Độ dài: 11
text.empty();              // Kiểm tra rỗng: false
text.clear();              // Xóa nội dung

// Access characters
text[0];                   // 'H'
text.at(1);               // 'e' (with bounds checking)
text.front();             // 'H' (first character)
text.back();              // 'd' (last character)

// Modification
text.append("!");         // Thêm vào cuối
text.insert(5, " C++");    // Chèn tại vị trí 5
text.replace(0, 5, "Hi");  // Thay thế từ 0-5 với "Hi"

// Search
text.find("World");        // Tìm vị trí của "World"
text.substr(6, 5);         // Lấy substring từ vị trí 6, độ dài 5
```

---

## 💡 Ví dụ thực tế: Tic Tac Toe Board

```cpp
#include <iostream>
#include <string>

// Constants
const int BOARD_SIZE = 3;
const char EMPTY = ' ';
const char PLAYER_X = 'X';
const char PLAYER_O = 'O';

// Function để hiển thị board
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

// Function để khởi tạo board
void initializeBoard(char board[BOARD_SIZE][BOARD_SIZE]) {
    for (int row = 0; row < BOARD_SIZE; row++) {
        for (int col = 0; col < BOARD_SIZE; col++) {
            board[row][col] = EMPTY;
        }
    }
}

int main() {
    char game_board[BOARD_SIZE][BOARD_SIZE];
    std::string player_names[2] = {"Alice", "Bob"};
    char player_symbols[2] = {PLAYER_X, PLAYER_O};
    
    // Initialize game
    initializeBoard(game_board);
    
    std::cout << "=== TIC TAC TOE GAME ===" << std::endl;
    std::cout << "Players: " << player_names[0] << " (X) vs " 
              << player_names[1] << " (O)" << std::endl;
    
    // Sample moves
    game_board[0][0] = PLAYER_X;  // Alice's move
    game_board[1][1] = PLAYER_O;  // Bob's move
    game_board[0][1] = PLAYER_X;  // Alice's move
    
    displayBoard(game_board);
    
    return 0;
}
```

---

## 🔍 Kiến thức nâng cao

### 1. Array traversal (Duyệt mảng)
```cpp
int numbers[] = {10, 20, 30, 40, 50};
int size = 5;

// For loop truyền thống
for (int i = 0; i < size; i++) {
    std::cout << numbers[i] << " ";
}

// Range-based for loop (C++11)
for (int num : numbers) {
    std::cout << num << " ";
}
```

### 2. Array as function parameters
```cpp
// Passing array to function
void printArray(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        std::cout << arr[i] << " ";
    }
}

// 2D array parameter
void print2DArray(int arr[][3], int rows) {
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < 3; j++) {
            std::cout << arr[i][j] << " ";
        }
        std::cout << std::endl;
    }
}
```

### 3. String input/output
```cpp
std::string name;

// Input string with spaces
std::cout << "Enter your full name: ";
std::getline(std::cin, name);

// Input single word
std::cout << "Enter username: ";
std::cin >> name;
```

---

## 📝 Bài tập về nhà

### **Bài 1: Player Name Management**
Viết chương trình:
1. Lưu tên 2 người chơi trong array
2. Hiển thị danh sách người chơi
3. Cho phép thay đổi tên người chơi

### **Bài 2: Score Tracking**
Tạo array lưu điểm của 5 game:
1. Nhập điểm từ keyboard
2. Tính điểm trung bình
3. Tìm điểm cao nhất và thấp nhất

### **Bài 3: Simple Board Display**
Tạo chương trình:
1. Khởi tạo board 3x3 trống
2. Đặt một số ký hiệu X, O
3. Hiển thị board đẹp mắt

### **Bài 4: String Manipulation**
Viết chương trình:
1. Nhập tên người chơi
2. Chuyển thành chữ hoa
3. Hiển thị thông điệp chào mừng

---

## 🎯 Checklist hoàn thành

- [ ] Hiểu cách khai báo và sử dụng arrays
- [ ] Biết làm việc với 2D arrays
- [ ] Nắm vững string operations
- [ ] Làm được 4 bài tập
- [ ] Chạy thành công ví dụ game board

---

## ⚠️ Common Mistakes (Lỗi thường gặp)

### **1. Array bounds**
```cpp
int arr[5];
arr[5] = 10;  // ❌ Index out of bounds! (0-4 is valid)
arr[4] = 10;  // ✅ Correct
```

### **2. Array initialization**
```cpp
int arr[5];
std::cout << arr[0];  // ❌ Undefined value!

int arr[5] = {0};     // ✅ Initialize all to 0
```

### **3. String comparison**
```cpp
std::string s1 = "hello";
std::string s2 = "hello";

if (s1 == s2) { /* ✅ Correct */ }
// Don't use: if (s1 = s2) // ❌ Assignment, not comparison!
```

---

## 📚 Tài liệu tham khảo
- [cppreference - Arrays](https://en.cppreference.com/w/cpp/language/array)
- [cppreference - std::string](https://en.cppreference.com/w/cpp/string/basic_string)
- [learncpp.com - Arrays](https://www.learncpp.com/cpp-tutorial/introduction-to-arrays/)

---

**Trước đó**: [Bài 1 - Variables và Data Types](01_variables_datatypes.md)  
**Tiếp theo**: [Bài 3 - Functions](03_functions.md)
