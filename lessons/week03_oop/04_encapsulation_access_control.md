# Week 3 - Lesson 4: Encapsulation and Access Control

## Mục tiêu học tập
- Hiểu về tính đóng gói (Encapsulation) trong OOP
- Nắm vững các access specifiers (public, private, protected)
- Thiết kế interface và implementation separation
- Áp dụng data hiding trong game development

## 1. Encapsulation (Tính đóng gói)

### 1.1 Khái niệm cơ bản
```cpp
// Bad design - không có encapsulation
class BadGameBoard {
public:
    char board[3][3];  // Direct access - nguy hiểm!
    int currentPlayer;
    bool gameOver;
};

// Good design - có encapsulation
class GameBoard {
private:
    char board[3][3];
    int currentPlayer;
    bool gameOver;
    int moveCount;
    
public:
    // Constructor
    GameBoard() {
        initializeBoard();
    }
    
    // Public interface
    bool makeMove(int row, int col, char player);
    char getCell(int row, int col) const;
    bool isGameOver() const { return gameOver; }
    int getCurrentPlayer() const { return currentPlayer; }
    int getMoveCount() const { return moveCount; }
    void displayBoard() const;
    void resetGame();
    
private:
    // Private helper methods
    void initializeBoard();
    bool isValidMove(int row, int col) const;
    bool checkWin() const;
    bool checkDraw() const;
    void switchPlayer();
};
```

### 1.2 Implementation của encapsulated class
```cpp
void GameBoard::initializeBoard() {
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            board[i][j] = ' ';
        }
    }
    currentPlayer = 0;  // 0 = X, 1 = O
    gameOver = false;
    moveCount = 0;
}

bool GameBoard::makeMove(int row, int col, char player) {
    if (!isValidMove(row, col)) {
        return false;
    }
    
    board[row][col] = player;
    moveCount++;
    
    if (checkWin()) {
        gameOver = true;
        cout << "Player " << player << " wins!" << endl;
    } else if (checkDraw()) {
        gameOver = true;
        cout << "It's a draw!" << endl;
    } else {
        switchPlayer();
    }
    
    return true;
}

char GameBoard::getCell(int row, int col) const {
    if (row >= 0 && row < 3 && col >= 0 && col < 3) {
        return board[row][col];
    }
    return '\0';  // Invalid position
}

bool GameBoard::isValidMove(int row, int col) const {
    if (gameOver) return false;
    if (row < 0 || row >= 3 || col < 0 || col >= 3) return false;
    return board[row][col] == ' ';
}

void GameBoard::displayBoard() const {
    cout << "  0   1   2" << endl;
    for (int i = 0; i < 3; i++) {
        cout << i << " ";
        for (int j = 0; j < 3; j++) {
            cout << board[i][j];
            if (j < 2) cout << " | ";
        }
        cout << endl;
        if (i < 2) {
            cout << "  ---------" << endl;
        }
    }
    cout << endl;
}
```

## 2. Access Specifiers

### 2.1 Private Members
```cpp
class Player {
private:
    string name;           // Chỉ class này access được
    int score;
    char symbol;
    vector<pair<int, int>> moveHistory;  // Track moves
    
public:
    Player(const string& playerName, char sym) 
        : name(playerName), symbol(sym), score(0) {}
    
    // Controlled access through methods
    const string& getName() const { return name; }
    int getScore() const { return score; }
    char getSymbol() const { return symbol; }
    
    void incrementScore() { score++; }
    void addMove(int row, int col) {
        moveHistory.push_back({row, col});
    }
    
    // Read-only access to move history
    const vector<pair<int, int>>& getMoveHistory() const {
        return moveHistory;
    }
    
    void clearHistory() { moveHistory.clear(); }
    
private:
    // Private helper methods
    bool isValidName(const string& name) const {
        return !name.empty() && name.length() <= 20;
    }
};
```

### 2.2 Protected Members
```cpp
class GameObject {
protected:
    int x, y;              // Derived classes có thể access
    bool isActive;
    string id;
    
private:
    static int nextId;     // Chỉ class này access được
    
public:
    GameObject(int posX, int posY) : x(posX), y(posY), isActive(true) {
        id = "obj_" + to_string(nextId++);
    }
    
    // Public interface
    bool getIsActive() const { return isActive; }
    string getId() const { return id; }
    virtual void update() = 0;
    virtual void render() = 0;
    
protected:
    // Protected methods cho derived classes
    void setPosition(int newX, int newY) {
        x = newX;
        y = newY;
    }
    
    void setActive(bool active) {
        isActive = active;
    }
};

int GameObject::nextId = 1;

class GamePiece : public GameObject {
private:
    char symbol;
    string color;
    
public:
    GamePiece(int x, int y, char sym, const string& col) 
        : GameObject(x, y), symbol(sym), color(col) {}
    
    void moveTo(int newX, int newY) {
        // Có thể access protected members từ base class
        if (isActive) {  // protected member
            setPosition(newX, newY);  // protected method
            cout << "Moved " << symbol << " to (" << x << ", " << y << ")" << endl;
        }
    }
    
    void update() override {
        if (isActive) {
            // Update logic
        }
    }
    
    void render() override {
        if (isActive) {
            cout << "Rendering " << color << " " << symbol 
                 << " at (" << x << ", " << y << ")" << endl;
        }
    }
};
```

### 2.3 Friend Classes và Functions
```cpp
class GameStatistics;  // Forward declaration

class Game {
private:
    GameBoard board;
    vector<Player> players;
    int currentRound;
    bool isRunning;
    
    // Declare friend để cho phép access private members
    friend class GameStatistics;
    friend void debugGame(const Game& game);
    
public:
    Game() : currentRound(0), isRunning(false) {}
    
    void startGame() {
        isRunning = true;
        currentRound = 1;
    }
    
    void endGame() {
        isRunning = false;
    }
    
    // Public interface
    bool isGameRunning() const { return isRunning; }
    int getCurrentRound() const { return currentRound; }
};

class GameStatistics {
public:
    static void printGameStats(const Game& game) {
        // Có thể access private members vì là friend
        cout << "Game Statistics:" << endl;
        cout << "Current Round: " << game.currentRound << endl;
        cout << "Is Running: " << (game.isRunning ? "Yes" : "No") << endl;
        cout << "Number of Players: " << game.players.size() << endl;
    }
    
    static double calculateWinRate(const Game& game, int playerId) {
        if (playerId < game.players.size()) {
            // Access private members
            return (double)game.players[playerId].getScore() / game.currentRound;
        }
        return 0.0;
    }
};

// Friend function
void debugGame(const Game& game) {
    cout << "DEBUG: Game state - Round " << game.currentRound 
         << ", Running: " << game.isRunning << endl;
}
```

## 3. Getters và Setters Design Patterns

### 3.1 Smart Getters/Setters
```cpp
class PlayerProfile {
private:
    string username;
    int level;
    int experience;
    int maxHealth;
    int currentHealth;
    bool isOnline;
    time_t lastLoginTime;
    
public:
    PlayerProfile(const string& name) 
        : username(name), level(1), experience(0), 
          maxHealth(100), currentHealth(100), isOnline(false) {
        lastLoginTime = time(nullptr);
    }
    
    // Getters
    const string& getUsername() const { return username; }
    int getLevel() const { return level; }
    int getExperience() const { return experience; }
    int getCurrentHealth() const { return currentHealth; }
    int getMaxHealth() const { return maxHealth; }
    bool getIsOnline() const { return isOnline; }
    
    // Calculated getters
    double getHealthPercentage() const {
        return (double)currentHealth / maxHealth * 100.0;
    }
    
    int getExperienceToNextLevel() const {
        return (level * 100) - experience;
    }
    
    string getLastLoginString() const {
        char buffer[100];
        strftime(buffer, sizeof(buffer), "%Y-%m-%d %H:%M:%S", 
                localtime(&lastLoginTime));
        return string(buffer);
    }
    
    // Smart setters với validation
    bool setUsername(const string& newName) {
        if (isValidUsername(newName)) {
            username = newName;
            return true;
        }
        return false;
    }
    
    void addExperience(int exp) {
        if (exp > 0) {
            experience += exp;
            checkLevelUp();
        }
    }
    
    void takeDamage(int damage) {
        if (damage > 0) {
            currentHealth = max(0, currentHealth - damage);
            if (currentHealth == 0) {
                onPlayerDeath();
            }
        }
    }
    
    void heal(int amount) {
        if (amount > 0) {
            currentHealth = min(maxHealth, currentHealth + amount);
        }
    }
    
    void login() {
        isOnline = true;
        lastLoginTime = time(nullptr);
    }
    
    void logout() {
        isOnline = false;
    }
    
private:
    bool isValidUsername(const string& name) const {
        if (name.empty() || name.length() > 20) return false;
        
        for (char c : name) {
            if (!isalnum(c) && c != '_') return false;
        }
        return true;
    }
    
    void checkLevelUp() {
        int requiredExp = level * 100;
        while (experience >= requiredExp) {
            level++;
            maxHealth += 10;  // Increase health with level
            currentHealth = maxHealth;  // Full heal on level up
            cout << "Level up! Now level " << level << endl;
            requiredExp = level * 100;
        }
    }
    
    void onPlayerDeath() {
        cout << username << " has been defeated!" << endl;
        currentHealth = 0;
        // Additional death logic
    }
};
```

### 3.2 Builder Pattern với Encapsulation
```cpp
class GameConfig {
private:
    int boardSize;
    int maxPlayers;
    int timeLimit;
    bool enableAI;
    string difficulty;
    bool soundEnabled;
    bool animationsEnabled;
    
    // Private constructor - chỉ Builder có thể tạo
    GameConfig() : boardSize(3), maxPlayers(2), timeLimit(0), 
                   enableAI(false), difficulty("Medium"), 
                   soundEnabled(true), animationsEnabled(true) {}
    
public:
    // Getters
    int getBoardSize() const { return boardSize; }
    int getMaxPlayers() const { return maxPlayers; }
    int getTimeLimit() const { return timeLimit; }
    bool isAIEnabled() const { return enableAI; }
    const string& getDifficulty() const { return difficulty; }
    bool isSoundEnabled() const { return soundEnabled; }
    bool areAnimationsEnabled() const { return animationsEnabled; }
    
    // Builder class
    class Builder {
    private:
        GameConfig config;
        
    public:
        Builder& setBoardSize(int size) {
            if (size >= 3 && size <= 10) {
                config.boardSize = size;
            }
            return *this;
        }
        
        Builder& setMaxPlayers(int players) {
            if (players >= 2 && players <= 4) {
                config.maxPlayers = players;
            }
            return *this;
        }
        
        Builder& setTimeLimit(int seconds) {
            if (seconds >= 0) {
                config.timeLimit = seconds;
            }
            return *this;
        }
        
        Builder& enableAI(bool enable = true) {
            config.enableAI = enable;
            return *this;
        }
        
        Builder& setDifficulty(const string& diff) {
            if (diff == "Easy" || diff == "Medium" || diff == "Hard") {
                config.difficulty = diff;
            }
            return *this;
        }
        
        Builder& enableSound(bool enable = true) {
            config.soundEnabled = enable;
            return *this;
        }
        
        Builder& enableAnimations(bool enable = true) {
            config.animationsEnabled = enable;
            return *this;
        }
        
        GameConfig build() {
            return config;
        }
    };
    
    friend class Builder;
};

// Usage
GameConfig config = GameConfig::Builder()
    .setBoardSize(5)
    .setMaxPlayers(2)
    .enableAI(true)
    .setDifficulty("Hard")
    .setTimeLimit(300)
    .build();
```

## 4. Data Validation và Error Handling

### 4.1 Robust Input Validation
```cpp
class SafeGameBoard {
private:
    vector<vector<char>> board;
    int size;
    bool gameActive;
    
public:
    enum class MoveResult {
        SUCCESS,
        INVALID_POSITION,
        POSITION_OCCUPIED,
        GAME_OVER,
        INVALID_PLAYER
    };
    
    SafeGameBoard(int boardSize) : size(boardSize), gameActive(true) {
        if (size < 3 || size > 10) {
            throw invalid_argument("Board size must be between 3 and 10");
        }
        
        board.resize(size, vector<char>(size, ' '));
    }
    
    MoveResult makeMove(int row, int col, char player) {
        // Validate game state
        if (!gameActive) {
            return MoveResult::GAME_OVER;
        }
        
        // Validate player symbol
        if (player != 'X' && player != 'O') {
            return MoveResult::INVALID_PLAYER;
        }
        
        // Validate position
        if (!isValidPosition(row, col)) {
            return MoveResult::INVALID_POSITION;
        }
        
        // Check if position is empty
        if (board[row][col] != ' ') {
            return MoveResult::POSITION_OCCUPIED;
        }
        
        // Make the move
        board[row][col] = player;
        return MoveResult::SUCCESS;
    }
    
    // Safe getter với bounds checking
    char getCellSafe(int row, int col) const {
        if (isValidPosition(row, col)) {
            return board[row][col];
        }
        throw out_of_range("Position (" + to_string(row) + ", " + 
                          to_string(col) + ") is out of bounds");
    }
    
    // Optional-based getter (C++17)
    optional<char> getCell(int row, int col) const {
        if (isValidPosition(row, col)) {
            return board[row][col];
        }
        return nullopt;
    }
    
    int getSize() const { return size; }
    bool isGameActive() const { return gameActive; }
    
    void endGame() { gameActive = false; }
    
private:
    bool isValidPosition(int row, int col) const {
        return row >= 0 && row < size && col >= 0 && col < size;
    }
};

// Usage với error handling
void playGameSafely() {
    try {
        SafeGameBoard board(3);
        
        auto result = board.makeMove(1, 1, 'X');
        switch (result) {
            case SafeGameBoard::MoveResult::SUCCESS:
                cout << "Move successful!" << endl;
                break;
            case SafeGameBoard::MoveResult::INVALID_POSITION:
                cout << "Invalid position!" << endl;
                break;
            case SafeGameBoard::MoveResult::POSITION_OCCUPIED:
                cout << "Position already occupied!" << endl;
                break;
            case SafeGameBoard::MoveResult::GAME_OVER:
                cout << "Game is over!" << endl;
                break;
            case SafeGameBoard::MoveResult::INVALID_PLAYER:
                cout << "Invalid player symbol!" << endl;
                break;
        }
        
        // Safe cell access
        auto cell = board.getCell(1, 1);
        if (cell.has_value()) {
            cout << "Cell contains: " << cell.value() << endl;
        }
        
    } catch (const exception& e) {
        cout << "Error: " << e.what() << endl;
    }
}
```

## Bài tập thực hành

### Bài tập 1: Encapsulated Player Class
Tạo class `Player` với proper encapsulation:
- Private: name, score, statistics
- Public: constructor, getters, game methods
- Protected: utility methods cho derived classes

### Bài tập 2: Safe GameBoard
Implement `GameBoard` class với:
- Input validation
- Error handling
- Proper access control
- Builder pattern cho configuration

### Bài tập 3: Game Statistics System
Tạo system theo dõi thống kê game:
- Private data members
- Friend classes cho analysis
- Smart getters cho calculated values

### Bài tập 4: Configuration Manager
Implement Singleton pattern với encapsulation:
- Private constructor
- Controlled access to settings
- Validation cho tất cả inputs

## Best Practices

1. **Data Hiding**: Always make data members private
2. **Interface Design**: Provide minimal, clean public interface
3. **Validation**: Validate all inputs trong setters
4. **Const Correctness**: Use const methods cho getters
5. **Error Handling**: Return status codes hoặc throw exceptions
6. **Documentation**: Document public interface clearly

## Tài liệu tham khảo
- Encapsulation in C++: https://www.geeksforgeeks.org/encapsulation-in-c/
- Access Specifiers: https://en.cppreference.com/w/cpp/language/access
- Friend Functions: https://en.cppreference.com/w/cpp/language/friend

## Bài tiếp theo
Lesson 5: RAII and Smart Pointers - Tìm hiểu về resource management và memory safety.
