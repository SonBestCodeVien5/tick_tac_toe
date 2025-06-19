# 📝 Bài 1: Variables và Data Types trong C++

## 🎯 Mục tiêu học
- Hiểu về variables và cách khai báo
- Nắm vững các kiểu dữ liệu cơ bản
- Biết cách sử dụng const và auto
- Thực hành với các ví dụ thực tế

---

## 📖 Lý thuyết

### 1. Variables (Biến)
Variables là nơi lưu trữ dữ liệu trong bộ nhớ. Mỗi variable có:
- **Tên** (identifier)
- **Kiểu dữ liệu** (data type)
- **Giá trị** (value)

### 2. Cú pháp khai báo
```cpp
data_type variable_name = initial_value;
```

### 3. Các kiểu dữ liệu cơ bản

#### **Integer Types (Số nguyên)**
```cpp
int age = 25;              // 4 bytes, -2^31 to 2^31-1
short year = 2025;         // 2 bytes, -32,768 to 32,767
long population = 8000000; // 4 hoặc 8 bytes
long long big_number = 123456789LL;  // 8 bytes

// Unsigned variants
unsigned int positive_age = 25;
unsigned short port = 8080;
```

#### **Floating Point Types (Số thực)**
```cpp
float price = 19.99f;      // 4 bytes, ~7 decimal digits
double precise_pi = 3.14159265359;  // 8 bytes, ~15 decimal digits
long double extra_precise = 3.141592653589793238L;  // 12-16 bytes
```

#### **Character Types**
```cpp
char letter = 'A';         // 1 byte, single character
char grade = 65;           // ASCII value of 'A'
```

#### **Boolean Type**
```cpp
bool is_student = true;
bool is_working = false;
```

#### **String Type**
```cpp
#include <string>
std::string name = "Nguyen Van A";
std::string message = "Hello World!";
```

### 4. Naming Conventions (Quy tắc đặt tên)

#### **✅ Good practices:**
```cpp
int player_score;          // snake_case
int playerAge;             // camelCase
const int MAX_PLAYERS = 4; // UPPER_CASE for constants
```

#### **❌ Avoid:**
```cpp
int 2players;              // Không bắt đầu bằng số
int player-score;          // Không dùng dấu gạch ngang
int int;                   // Không dùng keywords
```

### 5. Constants (Hằng số)
```cpp
const int MAX_SCORE = 100;        // Không thể thay đổi
const double PI = 3.14159;
const std::string GAME_NAME = "Tic Tac Toe";
```

### 6. Auto keyword (C++11+)
```cpp
auto age = 25;                    // Compiler tự suy ra int
auto price = 19.99;               // Compiler tự suy ra double
auto name = std::string("John");  // Compiler tự suy ra string
```

---

## 💡 Ví dụ thực tế: Game Player Info

```cpp
#include <iostream>
#include <string>

int main() {
    // Player information
    std::string player_name = "Player1";
    int player_age = 20;
    double player_score = 85.5;
    bool is_active = true;
    char player_grade = 'A';
    
    // Game constants
    const int MAX_PLAYERS = 2;
    const int BOARD_SIZE = 3;
    
    // Display information
    std::cout << "=== PLAYER INFO ===" << std::endl;
    std::cout << "Name: " << player_name << std::endl;
    std::cout << "Age: " << player_age << std::endl;
    std::cout << "Score: " << player_score << std::endl;
    std::cout << "Active: " << (is_active ? "Yes" : "No") << std::endl;
    std::cout << "Grade: " << player_grade << std::endl;
    
    std::cout << "\n=== GAME CONFIG ===" << std::endl;
    std::cout << "Max Players: " << MAX_PLAYERS << std::endl;
    std::cout << "Board Size: " << BOARD_SIZE << "x" << BOARD_SIZE << std::endl;
    
    return 0;
}
```

---

## 🔍 Kiến thức bổ sung

### 1. Type Conversion (Chuyển đổi kiểu)
```cpp
// Implicit conversion (tự động)
int num = 10;
double result = num;  // int -> double

// Explicit conversion (thủ công)
double pi = 3.14159;
int rounded = (int)pi;        // C-style cast
int rounded2 = int(pi);       // Function-style cast
int rounded3 = static_cast<int>(pi);  // C++ style (recommended)
```

### 2. Sizeof operator
```cpp
std::cout << "Size of int: " << sizeof(int) << " bytes" << std::endl;
std::cout << "Size of double: " << sizeof(double) << " bytes" << std::endl;
std::cout << "Size of string: " << sizeof(std::string) << " bytes" << std::endl;
```

### 3. Variable scope (Phạm vi)
```cpp
{
    int local_var = 10;    // Chỉ tồn tại trong block này
    std::cout << local_var << std::endl;
}
// local_var không tồn tại ở đây
```

---

## 📝 Bài tập về nhà

### **Bài 1: Player Statistics**
Tạo chương trình lưu trữ và hiển thị thông tin của 2 người chơi:
- Tên (string)
- Tuổi (int)
- Điểm số (double)
- Trạng thái online (bool)

### **Bài 2: Game Constants**
Định nghĩa các constants cho game cờ caro:
- Kích thước board (3x3)
- Số người chơi tối đa
- Điểm thắng
- Tên game

### **Bài 3: Type Conversion Practice**
Viết chương trình:
1. Nhập một số thực
2. Chuyển thành số nguyên
3. Hiển thị cả hai giá trị

---

## 🎯 Checklist hoàn thành

- [ ] Hiểu cách khai báo variables
- [ ] Biết các kiểu dữ liệu cơ bản
- [ ] Sử dụng được const và auto
- [ ] Làm được 3 bài tập
- [ ] Chạy thành công ví dụ thực tế

---

## 📚 Tài liệu tham khảo
- [cppreference - Fundamental types](https://en.cppreference.com/w/cpp/language/types)
- [learncpp.com - Variables](https://www.learncpp.com/cpp-tutorial/variable-assignment-and-initialization/)
- [C++ Core Guidelines - Variables](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-naming)

---

**Tiếp theo**: [Bài 2 - Arrays và Strings](02_arrays_strings.md)
