# Week 3 Exercises: Object-Oriented Programming

## Mục tiêu
Thực hành các khái niệm OOP đã học: classes, objects, inheritance, polymorphism, encapsulation, và RAII với smart pointers.

## Bài tập 1: Basic Class Design (Cơ bản)

### Yêu cầu
Tạo class `TicTacToePlayer` với đầy đủ tính năng OOP cơ bản.

### Chi tiết
```cpp
// Tạo file: src/player_basic.cpp

class TicTacToePlayer {
private:
    // TODO: Implement private members
    // - string name
    // - char symbol ('X' hoặc 'O')
    // - int score
    // - vector<pair<int,int>> moveHistory
    
public:
    // TODO: Implement constructors
    // - Default constructor
    // - Parameterized constructor(name, symbol)
    
    // TODO: Implement getters/setters
    // - getName(), getSymbol(), getScore()
    // - setName() với validation
    
    // TODO: Implement game methods
    // - makeMove(row, col) - thêm vào history
    // - incrementScore()
    // - resetScore()
    // - getMoveHistory()
    // - clearHistory()
    
    // TODO: Implement utility methods
    // - displayInfo() - hiển thị thông tin player
    // - getTotalMoves() - số lượng moves đã thực hiện
    
    // TODO: Destructor (nếu cần)
};
```

### Test Cases
```cpp
void testPlayerBasic() {
    // Test constructor
    TicTacToePlayer player1("Alice", 'X');
    assert(player1.getName() == "Alice");
    assert(player1.getSymbol() == 'X');
    assert(player1.getScore() == 0);
    
    // Test moves
    player1.makeMove(1, 1);
    player1.makeMove(0, 2);
    assert(player1.getTotalMoves() == 2);
    
    // Test score
    player1.incrementScore();
    assert(player1.getScore() == 1);
    
    cout << "Player Basic tests passed!" << endl;
}
```

## Bài tập 2: Inheritance & Polymorphism (Trung bình)

### Yêu cầu
Tạo class hierarchy cho different types of players với inheritance và polymorphism.

### Chi tiết
```cpp
// Tạo file: src/player_hierarchy.cpp

// Base class
class Player {
protected:
    // TODO: Implement protected members
    string name;
    char symbol;
    int score;
    
public:
    // TODO: Implement base class
    Player(const string& name, char symbol);
    virtual ~Player() = default;
    
    // Pure virtual functions
    virtual bool makeMove(GameBoard& board) = 0;
    virtual string getPlayerType() const = 0;
    
    // Virtual functions với default implementation
    virtual void onGameStart() { cout << name << " is ready!" << endl; }
    virtual void onGameEnd(bool won) { 
        if (won) score++; 
        cout << name << (won ? " wins!" : " loses!") << endl;
    }
    
    // Non-virtual functions
    const string& getName() const { return name; }
    char getSymbol() const { return symbol; }
    int getScore() const { return score; }
};

// Derived classes
class HumanPlayer : public Player {
public:
    // TODO: Implement HumanPlayer
    HumanPlayer(const string& name, char symbol);
    
    bool makeMove(GameBoard& board) override;
    string getPlayerType() const override { return "Human"; }
    
    void onGameStart() override {
        Player::onGameStart();
        cout << "Enter row and column (0-2): ";
    }
};

class AIPlayer : public Player {
private:
    string difficulty;
    
public:
    // TODO: Implement AIPlayer
    AIPlayer(const string& name, char symbol, const string& diff);
    
    bool makeMove(GameBoard& board) override;
    string getPlayerType() const override { return "AI (" + difficulty + ")"; }
    
    void onGameStart() override {
        Player::onGameStart();
        cout << "AI " << name << " is calculating..." << endl;
    }
    
private:
    pair<int, int> getRandomMove(const GameBoard& board);
    pair<int, int> getBestMove(const GameBoard& board);
};

class NetworkPlayer : public Player {
private:
    string ipAddress;
    bool connected;
    
public:
    // TODO: Implement NetworkPlayer
    NetworkPlayer(const string& name, char symbol, const string& ip);
    
    bool makeMove(GameBoard& board) override;
    string getPlayerType() const override { return "Network"; }
    
    bool connect();
    void disconnect();
    bool isConnected() const { return connected; }
};
```

### Test Cases
```cpp
void testPolymorphism() {
    vector<unique_ptr<Player>> players;
    
    players.push_back(make_unique<HumanPlayer>("Alice", 'X'));
    players.push_back(make_unique<AIPlayer>("Bot1", 'O', "Easy"));
    players.push_back(make_unique<NetworkPlayer>("Bob", 'X', "192.168.1.1"));
    
    // Test polymorphic behavior
    for (auto& player : players) {
        cout << "Player: " << player->getName() 
             << " Type: " << player->getPlayerType() << endl;
        player->onGameStart();
    }
    
    cout << "Polymorphism tests passed!" << endl;
}
```

## Bài tập 3: Encapsulation & Data Validation (Trung bình-Khó)

### Yêu cầu
Tạo fully encapsulated `GameBoard` class với comprehensive data validation.

### Chi tiết
```cpp
// Tạo file: src/safe_gameboard.cpp

class SafeGameBoard {
public:
    enum class CellState { EMPTY, PLAYER_X, PLAYER_O };
    enum class MoveResult { 
        SUCCESS, 
        INVALID_POSITION, 
        CELL_OCCUPIED, 
        GAME_FINISHED,
        INVALID_PLAYER
    };
    
    enum class GameState { 
        IN_PROGRESS, 
        PLAYER_X_WINS, 
        PLAYER_O_WINS, 
        DRAW 
    };
    
private:
    // TODO: Implement private members
    static const int BOARD_SIZE = 3;
    CellState board[BOARD_SIZE][BOARD_SIZE];
    GameState gameState;
    int moveCount;
    CellState currentPlayer;
    vector<pair<int, int>> moveHistory;
    
public:
    // TODO: Implement constructor
    SafeGameBoard();
    
    // TODO: Implement public interface
    MoveResult makeMove(int row, int col, char playerSymbol);
    MoveResult makeMove(int row, int col, CellState player);
    
    // Getters với validation
    CellState getCell(int row, int col) const;
    optional<CellState> getCellSafe(int row, int col) const;
    
    GameState getGameState() const { return gameState; }
    int getMoveCount() const { return moveCount; }
    CellState getCurrentPlayer() const { return currentPlayer; }
    
    // Board state queries
    bool isValidPosition(int row, int col) const;
    bool isEmpty(int row, int col) const;
    bool isGameFinished() const;
    
    // Game control
    void resetGame();
    void undoLastMove();  // Bonus feature
    
    // Display
    void displayBoard() const;
    void displayGameState() const;
    
    // Move history
    const vector<pair<int, int>>& getMoveHistory() const;
    void clearHistory();
    
private:
    // TODO: Implement private helper methods
    bool checkWin(CellState player) const;
    bool checkDraw() const;
    void updateGameState();
    void switchPlayer();
    CellState charToState(char c) const;
    char stateToChar(CellState state) const;
    
    // Validation helpers
    bool isValidPlayer(char playerSymbol) const;
    bool isValidPlayer(CellState player) const;
};

// TODO: Implement all methods với proper error handling và validation
```

### Test Cases
```cpp
void testSafeGameBoard() {
    SafeGameBoard board;
    
    // Test valid moves
    assert(board.makeMove(1, 1, 'X') == SafeGameBoard::MoveResult::SUCCESS);
    assert(board.getCell(1, 1) == SafeGameBoard::CellState::PLAYER_X);
    
    // Test invalid moves
    assert(board.makeMove(1, 1, 'O') == SafeGameBoard::MoveResult::CELL_OCCUPIED);
    assert(board.makeMove(-1, 0, 'O') == SafeGameBoard::MoveResult::INVALID_POSITION);
    assert(board.makeMove(0, 0, 'Z') == SafeGameBoard::MoveResult::INVALID_PLAYER);
    
    // Test game progression
    board.makeMove(0, 0, 'O');
    board.makeMove(1, 0, 'X');
    board.makeMove(0, 1, 'O');
    board.makeMove(1, 2, 'X');
    
    auto result = board.makeMove(0, 2, 'O');
    assert(result == SafeGameBoard::MoveResult::SUCCESS);
    assert(board.getGameState() == SafeGameBoard::GameState::PLAYER_O_WINS);
    
    // Test moves after game ends
    assert(board.makeMove(2, 2, 'X') == SafeGameBoard::MoveResult::GAME_FINISHED);
    
    cout << "Safe GameBoard tests passed!" << endl;
}
```

## Bài tập 4: RAII & Smart Pointers (Khó)

### Yêu cầu
Implement complete game engine core sử dụng modern C++ với RAII và smart pointers.

### Chi tiết
```cpp
// Tạo file: src/game_engine.cpp

// Forward declarations
class GameState;
class Player;
class GameRenderer;
class GameLogger;

// Resource management với RAII
class GameResource {
private:
    string resourceId;
    size_t size;
    unique_ptr<char[]> data;
    bool loaded;
    
public:
    // TODO: Implement RAII resource management
    GameResource(const string& id, size_t dataSize);
    ~GameResource();
    
    // Move semantics
    GameResource(GameResource&& other) noexcept;
    GameResource& operator=(GameResource&& other) noexcept;
    
    // Delete copy operations
    GameResource(const GameResource&) = delete;
    GameResource& operator=(const GameResource&) = delete;
    
    // Interface
    bool load();
    void unload();
    bool isLoaded() const { return loaded; }
    const string& getId() const { return resourceId; }
    size_t getSize() const { return size; }
};

// Asset manager với shared ownership
class AssetManager {
private:
    unordered_map<string, shared_ptr<GameResource>> resources;
    weak_ptr<GameLogger> logger;  // Weak reference để tránh circular dependency
    
public:
    // TODO: Implement asset management
    AssetManager(shared_ptr<GameLogger> gameLogger);
    
    shared_ptr<GameResource> loadResource(const string& id, size_t size);
    void unloadResource(const string& id);
    void unloadAllResources();
    
    shared_ptr<GameResource> getResource(const string& id) const;
    bool isResourceLoaded(const string& id) const;
    
    size_t getResourceCount() const;
    size_t getTotalMemoryUsage() const;
    
    void printAssetStats() const;
};

// Game engine core
class GameEngine {
private:
    unique_ptr<SafeGameBoard> gameBoard;
    unique_ptr<AssetManager> assetManager;
    shared_ptr<GameLogger> logger;
    vector<shared_ptr<Player>> players;
    unique_ptr<GameRenderer> renderer;
    
    bool initialized;
    bool running;
    
public:
    // TODO: Implement game engine
    GameEngine();
    ~GameEngine();
    
    // Engine lifecycle
    bool initialize();
    void shutdown();
    bool isInitialized() const { return initialized; }
    
    // Game management
    void startGame();
    void endGame();
    bool isRunning() const { return running; }
    
    // Player management
    void addPlayer(shared_ptr<Player> player);
    void removePlayer(const string& playerName);
    shared_ptr<Player> getPlayer(const string& playerName) const;
    
    // Game loop
    void update();
    void render();
    
    // Resource access
    AssetManager* getAssetManager() const { return assetManager.get(); }
    SafeGameBoard* getGameBoard() const { return gameBoard.get(); }
    
private:
    // TODO: Implement private methods
    bool initializeSubsystems();
    void cleanupSubsystems();
    void handleGameEvents();
};

// RAII Logger với file management
class GameLogger {
private:
    unique_ptr<FILE, function<void(FILE*)>> logFile;
    string logFilename;
    mutex logMutex;
    
public:
    // TODO: Implement RAII file logging
    GameLogger(const string& filename);
    ~GameLogger() = default;
    
    void log(const string& message);
    void logError(const string& error);
    void logWarning(const string& warning);
    void logInfo(const string& info);
    
    bool isOpen() const;
    const string& getFilename() const { return logFilename; }
};
```

### Test Cases
```cpp
void testGameEngine() {
    // Test RAII và resource management
    {
        auto logger = make_shared<GameLogger>("test_game.log");
        GameEngine engine;
        
        assert(engine.initialize());
        assert(engine.isInitialized());
        
        // Test player management
        auto player1 = make_shared<HumanPlayer>("Alice", 'X');
        auto player2 = make_shared<AIPlayer>("Bot", 'O', "Easy");
        
        engine.addPlayer(player1);
        engine.addPlayer(player2);
        
        assert(engine.getPlayer("Alice") != nullptr);
        assert(engine.getPlayer("Bot") != nullptr);
        
        // Test asset management
        auto assetMgr = engine.getAssetManager();
        auto resource = assetMgr->loadResource("test_asset", 1024);
        assert(resource != nullptr);
        assert(assetMgr->isResourceLoaded("test_asset"));
        
        engine.startGame();
        assert(engine.isRunning());
        
        // Test game board access
        auto board = engine.getGameBoard();
        assert(board != nullptr);
        
        engine.endGame();
        assert(!engine.isRunning());
        
    }  // Engine destructor should cleanup everything properly
    
    cout << "Game Engine tests passed!" << endl;
}
```

## Bài tập 5: Design Patterns Integration (Nâng cao)

### Yêu cầu
Integrate multiple design patterns vào game architecture.

### Patterns cần implement:
1. **Singleton**: GameConfig
2. **Factory**: PlayerFactory  
3. **Observer**: GameEvents
4. **Strategy**: AI Strategies
5. **Command**: Game Commands

### Chi tiết
```cpp
// Tạo file: src/design_patterns.cpp

// 1. Singleton Pattern
class GameConfig {
private:
    static unique_ptr<GameConfig> instance;
    static mutex instanceMutex;
    
    // Private constructor
    GameConfig() = default;
    
    unordered_map<string, string> settings;
    
public:
    // TODO: Implement thread-safe singleton
    static GameConfig& getInstance();
    
    void setSetting(const string& key, const string& value);
    string getSetting(const string& key, const string& defaultValue = "") const;
    
    void loadFromFile(const string& filename);
    void saveToFile(const string& filename) const;
};

// 2. Factory Pattern
class PlayerFactory {
public:
    enum class PlayerType { HUMAN, AI_EASY, AI_MEDIUM, AI_HARD, NETWORK };
    
    // TODO: Implement factory method
    static unique_ptr<Player> createPlayer(
        PlayerType type, 
        const string& name, 
        char symbol,
        const unordered_map<string, string>& params = {}
    );
    
private:
    static unique_ptr<AIStrategy> createAIStrategy(const string& difficulty);
};

// 3. Observer Pattern
class GameObserver {
public:
    virtual ~GameObserver() = default;
    virtual void onGameStart() = 0;
    virtual void onGameEnd(const string& winner) = 0;
    virtual void onPlayerMove(const string& player, int row, int col) = 0;
    virtual void onPlayerAdded(const string& player) = 0;
    virtual void onPlayerRemoved(const string& player) = 0;
};

class GameSubject {
private:
    vector<weak_ptr<GameObserver>> observers;
    
public:
    // TODO: Implement observer management
    void addObserver(shared_ptr<GameObserver> observer);
    void removeObserver(shared_ptr<GameObserver> observer);
    
protected:
    void notifyGameStart();
    void notifyGameEnd(const string& winner);
    void notifyPlayerMove(const string& player, int row, int col);
    void notifyPlayerAdded(const string& player);
    void notifyPlayerRemoved(const string& player);
};

// 4. Strategy Pattern for AI
class AIStrategy {
public:
    virtual ~AIStrategy() = default;
    virtual pair<int, int> getBestMove(const SafeGameBoard& board, char player) = 0;
    virtual string getStrategyName() const = 0;
};

class RandomStrategy : public AIStrategy {
    // TODO: Implement random move strategy
};

class MinimaxStrategy : public AIStrategy {
    // TODO: Implement minimax algorithm
private:
    int minimax(SafeGameBoard& board, int depth, bool isMaximizing, char player, char opponent);
    int evaluateBoard(const SafeGameBoard& board, char player, char opponent);
};

// 5. Command Pattern
class GameCommand {
public:
    virtual ~GameCommand() = default;
    virtual bool execute() = 0;
    virtual bool undo() = 0;
    virtual string getDescription() const = 0;
};

class MoveCommand : public GameCommand {
private:
    SafeGameBoard* board;
    int row, col;
    char player;
    bool executed;
    
public:
    // TODO: Implement move command với undo capability
    MoveCommand(SafeGameBoard* gameBoard, int r, int c, char p);
    
    bool execute() override;
    bool undo() override;
    string getDescription() const override;
};

class CommandManager {
private:
    stack<unique_ptr<GameCommand>> history;
    size_t maxHistorySize;
    
public:
    // TODO: Implement command management
    CommandManager(size_t maxSize = 100);
    
    bool executeCommand(unique_ptr<GameCommand> command);
    bool undoLastCommand();
    void clearHistory();
    
    size_t getHistorySize() const;
    bool canUndo() const;
};
```

## Testing Framework

### Unit Test Structure
```cpp
// Tạo file: tests/week03_tests.cpp

#include <cassert>
#include <iostream>
#include <vector>
#include <memory>

// Test runner
class TestRunner {
private:
    vector<function<void()>> tests;
    string currentSuite;
    
public:
    void addTest(const string& name, function<void()> test) {
        tests.push_back([name, test]() {
            cout << "Running " << name << "... ";
            try {
                test();
                cout << "PASSED" << endl;
            } catch (const exception& e) {
                cout << "FAILED: " << e.what() << endl;
            }
        });
    }
    
    void runAllTests() {
        cout << "=== Week 3 OOP Tests ===" << endl;
        for (auto& test : tests) {
            test();
        }
        cout << "=== Tests Complete ===" << endl;
    }
};

int main() {
    TestRunner runner;
    
    // Add all tests
    runner.addTest("Player Basic", testPlayerBasic);
    runner.addTest("Polymorphism", testPolymorphism);
    runner.addTest("Safe GameBoard", testSafeGameBoard);
    runner.addTest("Game Engine", testGameEngine);
    
    // Run tests
    runner.runAllTests();
    
    return 0;
}
```

## Hướng dẫn thực hiện

### Bước 1: Setup
1. Tạo thư mục `src/week03/` trong project
2. Tạo thư mục `tests/week03/` 
3. Copy template code từ exercises

### Bước 2: Implementation Order
1. Bài tập 1: Basic Class (1-2 hours)
2. Bài tập 2: Inheritance (2-3 hours)  
3. Bài tập 3: Encapsulation (3-4 hours)
4. Bài tập 4: RAII & Smart Pointers (4-5 hours)
5. Bài tập 5: Design Patterns (5+ hours)

### Bước 3: Testing
1. Implement unit tests cho mỗi bài
2. Run tests thường xuyên
3. Fix bugs và improve design

### Bước 4: Documentation
1. Comment code thoroughly
2. Document design decisions
3. Create class diagrams (optional)

## Đánh giá

### Criteria (100 points total):
- **Correctness (40%)**: Code works correctly theo requirements
- **Design (25%)**: Good OOP design, proper use of concepts
- **Code Quality (20%)**: Clean, readable, well-structured code  
- **Testing (10%)**: Comprehensive unit tests
- **Documentation (5%)**: Clear comments và documentation

### Bonus Points:
- Advanced features implementation (+10%)
- Creative enhancements (+5%)
- Performance optimizations (+5%)

## Resources
- [C++ OOP Tutorial](https://www.learncpp.com/cpp-tutorial/welcome-to-object-oriented-programming/)
- [Design Patterns in C++](https://refactoring.guru/design-patterns/cpp)
- [Modern C++ Best Practices](https://github.com/cpp-best-practices/cppbestpractices)

---
*Expected completion time: 15-20 hours*
*Difficulty: Intermediate to Advanced*
