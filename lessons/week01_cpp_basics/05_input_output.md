# 📝 Bài 5: Input/Output trong C++

## 🎯 Mục tiêu học
- Nắm vững iostream library và basic I/O operations
- Hiểu về input validation và error handling
- Biết cách format output đẹp mắt
- Thực hành với file I/O basics cho game save/load

---

## 📖 Lý thuyết

### 1. Standard I/O Streams

#### **Input Stream (std::cin)**
```cpp
#include <iostream>

int number;
std::string name;
char character;
double price;

// Basic input
std::cout << "Enter a number: ";
std::cin >> number;

std::cout << "Enter your name: ";
std::cin >> name;  // Chỉ đọc 1 từ (dừng ở space)

// Multiple inputs
std::cout << "Enter age and grade: ";
std::cin >> age >> grade;
```

#### **Output Stream (std::cout)**
```cpp
#include <iostream>

// Basic output
std::cout << "Hello World!" << std::endl;
std::cout << "Number: " << 42 << std::endl;

// Chaining outputs
std::cout << "Name: " << name << ", Age: " << age << std::endl;

// Without newline
std::cout << "Enter choice: ";  // Cursor stays on same line
```

### 2. String Input with Spaces

#### **getline() function**
```cpp
#include <string>

std::string full_name;
std::string address;

// Read entire line including spaces
std::cout << "Enter full name: ";
std::cin.ignore();  // Clear input buffer first
std::getline(std::cin, full_name);

std::cout << "Enter address: ";
std::getline(std::cin, address);
```

#### **Mixing cin and getline**
```cpp
int age;
std::string name;

std::cout << "Enter age: ";
std::cin >> age;

std::cout << "Enter full name: ";
std::cin.ignore();  // QUAN TRỌNG: Clear buffer
std::getline(std::cin, name);
```

### 3. Input Validation

#### **Basic validation**
```cpp
#include <iostream>
#include <limits>

// Function to clear input buffer
void clearInputBuffer() {
    std::cin.clear();
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
}

// Safe integer input
int getValidInteger() {
    int value;
    while (true) {
        std::cout << "Enter an integer: ";
        if (std::cin >> value) {
            return value;  // Successful input
        } else {
            std::cout << "Invalid input! Please enter a number." << std::endl;
            clearInputBuffer();
        }
    }
}

// Integer with range validation
int getIntegerInRange(int min, int max) {
    int value;
    do {
        value = getValidInteger();
        if (value < min || value > max) {
            std::cout << "Please enter a number between " << min << " and " << max << std::endl;
        }
    } while (value < min || value > max);
    
    return value;
}
```

### 4. Output Formatting

#### **iomanip library**
```cpp
#include <iostream>
#include <iomanip>

double price = 19.99;
int number = 42;

// Set decimal precision
std::cout << std::fixed << std::setprecision(2);
std::cout << "Price: $" << price << std::endl;  // Price: $19.99

// Set field width
std::cout << std::setw(10) << "Name" << std::setw(10) << "Score" << std::endl;
std::cout << std::setw(10) << "Alice" << std::setw(10) << 95 << std::endl;
std::cout << std::setw(10) << "Bob" << std::setw(10) << 87 << std::endl;

// Fill character
std::cout << std::setfill('*') << std::setw(20) << "TITLE" << std::endl;
// Output: ***************TITLE

// Number formatting
std::cout << "Hex: " << std::hex << number << std::endl;  // Hex: 2a
std::cout << "Dec: " << std::dec << number << std::endl;  // Dec: 42
std::cout << "Oct: " << std::oct << number << std::endl;  // Oct: 52
```

### 5. File I/O Basics

#### **Writing to file**
```cpp
#include <fstream>
#include <string>

// Write to file
void saveGameData(const std::string& filename) {
    std::ofstream file(filename);
    
    if (file.is_open()) {
        file << "Player1,100,5\n";  // name,score,level
        file << "Player2,85,3\n";
        file << "GameSettings,3,3\n";  // board_size
        file.close();
        std::cout << "Game saved successfully!" << std::endl;
    } else {
        std::cout << "Error: Could not open file for writing!" << std::endl;
    }
}
```

#### **Reading from file**
```cpp
#include <fstream>
#include <sstream>

// Read from file
void loadGameData(const std::string& filename) {
    std::ifstream file(filename);
    std::string line;
    
    if (file.is_open()) {
        while (std::getline(file, line)) {
            std::cout << "Data: " << line << std::endl;
            
            // Parse CSV data
            std::stringstream ss(line);
            std::string item;
            std::vector<std::string> tokens;
            
            while (std::getline(ss, item, ',')) {
                tokens.push_back(item);
            }
            
            // Process tokens...
        }
        file.close();
    } else {
        std::cout << "Error: Could not open file for reading!" << std::endl;
    }
}
```

---

## 💡 Ví dụ thực tế: Game I/O System

```cpp
#include <iostream>
#include <iomanip>
#include <fstream>
#include <string>
#include <vector>
#include <limits>
#include <sstream>

// Player structure
struct Player {
    std::string name;
    int score;
    int games_played;
    int games_won;
};

// Clear input buffer
void clearInputBuffer() {
    std::cin.clear();
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
}

// Get valid string input
std::string getValidString(const std::string& prompt) {
    std::string input;
    do {
        std::cout << prompt;
        std::getline(std::cin, input);
        if (input.empty()) {
            std::cout << "Input cannot be empty! Please try again." << std::endl;
        }
    } while (input.empty());
    return input;
}

// Get valid integer input
int getValidInteger(const std::string& prompt) {
    int value;
    while (true) {
        std::cout << prompt;
        if (std::cin >> value) {
            clearInputBuffer();  // Clear remaining input
            return value;
        } else {
            std::cout << "Invalid input! Please enter a number." << std::endl;
            clearInputBuffer();
        }
    }
}

// Get integer within range
int getIntegerInRange(const std::string& prompt, int min, int max) {
    int value;
    do {
        value = getValidInteger(prompt);
        if (value < min || value > max) {
            std::cout << "Please enter a number between " << min << " and " << max << std::endl;
        }
    } while (value < min || value > max);
    return value;
}

// Display formatted player stats
void displayPlayerStats(const Player& player) {
    std::cout << std::left << std::setw(15) << player.name
              << std::right << std::setw(8) << player.score
              << std::setw(8) << player.games_played
              << std::setw(8) << player.games_won;
    
    if (player.games_played > 0) {
        double win_rate = (double)player.games_won / player.games_played * 100;
        std::cout << std::setw(10) << std::fixed << std::setprecision(1) << win_rate << "%";
    } else {
        std::cout << std::setw(10) << "N/A";
    }
    std::cout << std::endl;
}

// Display all players in table format
void displayAllPlayers(const std::vector<Player>& players) {
    std::cout << "\n" << std::string(60, '=') << std::endl;
    std::cout << "                    PLAYER STATISTICS" << std::endl;
    std::cout << std::string(60, '=') << std::endl;
    
    std::cout << std::left << std::setw(15) << "Name"
              << std::right << std::setw(8) << "Score"
              << std::setw(8) << "Played"
              << std::setw(8) << "Won"
              << std::setw(10) << "Win Rate" << std::endl;
    std::cout << std::string(60, '-') << std::endl;
    
    for (const auto& player : players) {
        displayPlayerStats(player);
    }
    std::cout << std::string(60, '=') << std::endl;
}

// Save players to file
void savePlayersToFile(const std::vector<Player>& players, const std::string& filename) {
    std::ofstream file(filename);
    
    if (file.is_open()) {
        // Write header
        file << "# Tic Tac Toe Player Data\n";
        file << "# Format: name,score,games_played,games_won\n";
        
        // Write player data
        for (const auto& player : players) {
            file << player.name << "," << player.score << ","
                 << player.games_played << "," << player.games_won << "\n";
        }
        
        file.close();
        std::cout << "✅ Player data saved to " << filename << std::endl;
    } else {
        std::cout << "❌ Error: Could not save to file " << filename << std::endl;
    }
}

// Load players from file
std::vector<Player> loadPlayersFromFile(const std::string& filename) {
    std::vector<Player> players;
    std::ifstream file(filename);
    std::string line;
    
    if (file.is_open()) {
        std::cout << "📂 Loading player data from " << filename << "..." << std::endl;
        
        while (std::getline(file, line)) {
            // Skip comments and empty lines
            if (line.empty() || line[0] == '#') {
                continue;
            }
            
            // Parse CSV line
            std::stringstream ss(line);
            std::string token;
            std::vector<std::string> tokens;
            
            while (std::getline(ss, token, ',')) {
                tokens.push_back(token);
            }
            
            // Create player if we have enough data
            if (tokens.size() >= 4) {
                Player player;
                player.name = tokens[0];
                player.score = std::stoi(tokens[1]);
                player.games_played = std::stoi(tokens[2]);
                player.games_won = std::stoi(tokens[3]);
                players.push_back(player);
            }
        }
        
        file.close();
        std::cout << "✅ Loaded " << players.size() << " players" << std::endl;
    } else {
        std::cout << "📄 No existing save file found. Starting fresh." << std::endl;
    }
    
    return players;
}

// Get player input
Player createNewPlayer() {
    Player player;
    
    std::cout << "\n=== NEW PLAYER REGISTRATION ===" << std::endl;
    player.name = getValidString("Enter player name: ");
    player.score = getIntegerInRange("Enter initial score (0-1000): ", 0, 1000);
    player.games_played = 0;
    player.games_won = 0;
    
    std::cout << "✅ Player " << player.name << " created successfully!" << std::endl;
    return player;
}

// Main demonstration
int main() {
    std::cout << "=== I/O SYSTEM DEMO: TIC TAC TOE ===" << std::endl;
    
    const std::string save_file = "players.txt";
    std::vector<Player> players;
    
    // Load existing data
    players = loadPlayersFromFile(save_file);
    
    bool running = true;
    while (running) {
        std::cout << "\n=== GAME MANAGEMENT MENU ===" << std::endl;
        std::cout << "1. View All Players" << std::endl;
        std::cout << "2. Add New Player" << std::endl;
        std::cout << "3. Save Data to File" << std::endl;
        std::cout << "4. Reload Data from File" << std::endl;
        std::cout << "5. Exit" << std::endl;
        
        int choice = getIntegerInRange("Choose option (1-5): ", 1, 5);
        
        switch (choice) {
            case 1:
                if (players.empty()) {
                    std::cout << "📭 No players found. Add some players first!" << std::endl;
                } else {
                    displayAllPlayers(players);
                }
                break;
                
            case 2: {
                Player new_player = createNewPlayer();
                players.push_back(new_player);
                break;
            }
            
            case 3:
                savePlayersToFile(players, save_file);
                break;
                
            case 4:
                players = loadPlayersFromFile(save_file);
                break;
                
            case 5:
                std::cout << "👋 Goodbye!" << std::endl;
                running = false;
                break;
        }
        
        if (running) {
            std::cout << "\nPress Enter to continue...";
            std::cin.ignore();
        }
    }
    
    return 0;
}
```

---

## 🔍 Kiến thức nâng cao

### 1. Stream States
```cpp
if (std::cin.fail()) {
    std::cout << "Input operation failed!" << std::endl;
    std::cin.clear();  // Clear error flags
}

if (std::cin.eof()) {
    std::cout << "End of file reached!" << std::endl;
}

if (std::cin.bad()) {
    std::cout << "Stream corrupted!" << std::endl;
}
```

### 2. Custom Input Validation
```cpp
// Validate email format
bool isValidEmail(const std::string& email) {
    return email.find('@') != std::string::npos && 
           email.find('.') != std::string::npos;
}

// Validate phone number
bool isValidPhone(const std::string& phone) {
    return phone.length() >= 10 && 
           std::all_of(phone.begin(), phone.end(), ::isdigit);
}
```

### 3. Binary File I/O
```cpp
// Write binary data
std::ofstream file("data.bin", std::ios::binary);
int numbers[] = {1, 2, 3, 4, 5};
file.write(reinterpret_cast<const char*>(numbers), sizeof(numbers));

// Read binary data
std::ifstream file("data.bin", std::ios::binary);
int loaded_numbers[5];
file.read(reinterpret_cast<char*>(loaded_numbers), sizeof(loaded_numbers));
```

---

## 📝 Bài tập về nhà

### **Bài 1: Student Grade Manager**
Tạo chương trình quản lý điểm sinh viên:
- Input: tên, tuổi, và 5 điểm môn học
- Validate tất cả input
- Tính GPA và xếp loại
- Save/load data từ file

### **Bài 2: Game Settings Manager**
Tạo system quản lý settings:
- Volume (0-100)
- Difficulty (Easy/Medium/Hard)
- Player name
- Screen resolution
- Save to config file với format đẹp

### **Bài 3: Simple Quiz System**
Viết chương trình quiz:
- Load câu hỏi từ file text
- Display formatted questions
- Validate answers
- Calculate và display score
- Save results với timestamp

### **Bài 4: Game Statistics Tracker**
Tạo system tracking:
- Player scores over time
- Win/loss ratios
- Best scores
- Export to CSV format
- Import existing data

---

## 🎯 Checklist hoàn thành

- [ ] Hiểu basic I/O với cin/cout
- [ ] Biết validate input đúng cách
- [ ] Nắm vững string input với getline
- [ ] Biết format output với iomanip
- [ ] Làm được file I/O cơ bản
- [ ] Làm được 4 bài tập
- [ ] Chạy thành công demo program

---

## ⚠️ Common Mistakes

### **1. Mixing cin >> and getline**
```cpp
int age;
std::string name;

std::cin >> age;        // Leaves newline in buffer
std::getline(std::cin, name);  // ❌ Gets empty string!

// ✅ Fix:
std::cin >> age;
std::cin.ignore();      // Clear the newline
std::getline(std::cin, name);
```

### **2. Not validating input**
```cpp
int age;
std::cout << "Enter age: ";
std::cin >> age;  // ❌ What if user enters "abc"?

// ✅ Better:
if (std::cin >> age) {
    // Valid input
} else {
    std::cout << "Invalid input!";
    std::cin.clear();
    // Clear buffer...
}
```

### **3. File not checked**
```cpp
std::ofstream file("data.txt");
file << "data";  // ❌ What if file couldn't be opened?

// ✅ Better:
std::ofstream file("data.txt");
if (file.is_open()) {
    file << "data";
} else {
    std::cout << "Error opening file!";
}
```

---

## 💡 Best Practices

1. **Always validate user input**
2. **Check file operations** for success
3. **Use meaningful prompts** and error messages
4. **Clear input buffer** when needed
5. **Format output** for better readability
6. **Handle edge cases** gracefully

---

## 📚 Tài liệu tham khảo
- [cppreference - iostream](https://en.cppreference.com/w/cpp/io)
- [cppreference - iomanip](https://en.cppreference.com/w/cpp/io/manip)
- [learncpp.com - Input and output](https://www.learncpp.com/cpp-tutorial/introduction-to-iostream-cout-cin-and-endl/)

---

**Trước đó**: [Bài 4 - Loops và Conditions](04_loops_conditions.md)  
**Tiếp theo**: [Tuần 2 - Git & GitHub](../week02_git_github/01_git_basics.md)
