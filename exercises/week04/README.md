# Week 4 Exercises: Data Structures & Algorithms

## 🎯 Mục tiêu
- Áp dụng arrays, vectors, strings vào game development
- Thực hành linked lists và trees cho game state management
- Sử dụng hash tables và sets cho game data
- Hiểu và áp dụng stacks, queues, priority queues
- Phân tích algorithm complexity cho game optimization

---

## 📋 Danh sách bài tập

### **Exercise 4.1: Array & Vector Game Board**
**Mô tả**: Tạo game board sử dụng 2D array/vector với các operations cơ bản.

**Yêu cầu**:
```cpp
class GameBoard {
private:
    vector<vector<char>> board;
    int size;
    
public:
    GameBoard(int n = 3);
    void displayBoard();
    bool makeMove(int row, int col, char player);
    bool checkWin(char player);
    bool isFull();
    void reset();
    vector<pair<int,int>> getAvailableMoves();
};
```

**Test cases**:
- Khởi tạo board 3x3, 5x5
- Đặt quân X, O vào các vị trí hợp lệ/không hợp lệ
- Kiểm tra thắng thua (ngang, dọc, chéo)
- Reset board về trạng thái ban đầu

**Tiêu chí đánh giá**:
- ✅ Code compile và chạy không lỗi
- ✅ Tất cả test cases pass
- ✅ Handle edge cases (out of bounds, occupied cells)
- ✅ Efficient memory usage
- ✅ Clean, readable code

---

### **Exercise 4.2: String Processing for Game Data**
**Mô tả**: Xây dựng hệ thống xử lý string cho game save/load.

**Yêu cầu**:
```cpp
class GameDataProcessor {
public:
    string serializeBoard(const vector<vector<char>>& board);
    vector<vector<char>> deserializeBoard(const string& data);
    bool validatePlayerName(const string& name);
    string formatScore(const map<string, int>& scores);
    vector<string> parseCommands(const string& input);
};
```

**Test cases**:
- Serialize/deserialize board states
- Validate player names (length, characters)
- Format leaderboard display
- Parse user commands ("move 1 2", "save game1", "load game2")

**Bonus**:
- Implement string compression for large boards
- Add error handling for corrupted save data

---

### **Exercise 4.3: Linked List Move History**
**Mô tả**: Sử dụng linked list để lưu trữ lịch sử nước đi.

**Yêu cầu**:
```cpp
struct Move {
    int row, col;
    char player;
    time_t timestamp;
    Move* next;
    
    Move(int r, int c, char p) : row(r), col(c), player(p), 
                                timestamp(time(nullptr)), next(nullptr) {}
};

class MoveHistory {
private:
    Move* head;
    Move* tail;
    int count;
    
public:
    void addMove(int row, int col, char player);
    void undoLastMove();
    void displayHistory();
    Move* getLastMove();
    int getMoveCount();
    ~MoveHistory(); // Proper cleanup
};
```

**Test cases**:
- Add 10 moves, verify count and order
- Undo last 3 moves, check remaining moves
- Display history in chronological order
- Memory leak check (no memory leaks after destruction)

---

### **Exercise 4.4: Binary Tree Game State Storage**
**Mô tả**: Sử dụng binary tree để lưu trữ và tìm kiếm game states.

**Yêu cầu**:
```cpp
struct GameState {
    vector<vector<char>> board;
    char currentPlayer;
    int moveCount;
    string stateID; // Hash of the board
    
    GameState* left;
    GameState* right;
};

class GameStateTree {
private:
    GameState* root;
    
public:
    void insertState(const vector<vector<char>>& board, char player);
    GameState* findState(const string& stateID);
    void inorderTraversal(); // Display all states
    int getTreeHeight();
    int getNodeCount();
};
```

**Test cases**:
- Insert 20 different game states
- Search for existing and non-existing states
- Verify BST property (states ordered by stateID)
- Calculate tree height and node count

---

### **Exercise 4.5: Hash Table Player Statistics**
**Mô tả**: Sử dụng hash table (unordered_map) để tracking player stats.

**Yêu cầu**:
```cpp
struct PlayerStats {
    string name;
    int wins;
    int losses;
    int draws;
    double winRate;
    vector<time_t> gameHistory;
    
    void updateStats(char result); // 'W', 'L', 'D'
    void calculateWinRate();
};

class PlayerDatabase {
private:
    unordered_map<string, PlayerStats> players;
    
public:
    void addPlayer(const string& name);
    void updatePlayerResult(const string& name, char result);
    PlayerStats getPlayerStats(const string& name);
    vector<pair<string, double>> getLeaderboard();
    void displayTopPlayers(int count = 10);
    bool playerExists(const string& name);
};
```

**Test cases**:
- Add 100 players với random results
- Update stats và verify calculations
- Get leaderboard sorted by win rate
- Handle non-existent players gracefully

---

### **Exercise 4.6: Stack-based Undo System**
**Mô tả**: Implement undo/redo system sử dụng stacks.

**Yêu cầu**:
```cpp
struct GameSnapshot {
    vector<vector<char>> boardState;
    char currentPlayer;
    int moveNumber;
};

class UndoRedoSystem {
private:
    stack<GameSnapshot> undoStack;
    stack<GameSnapshot> redoStack;
    int maxUndoLevels;
    
public:
    UndoRedoSystem(int maxLevels = 50);
    void saveState(const vector<vector<char>>& board, char player, int move);
    GameSnapshot undo();
    GameSnapshot redo();
    bool canUndo();
    bool canRedo();
    void clearHistory();
};
```

**Test cases**:
- Save 10 states, undo 5, redo 3
- Verify stack limits (max 50 undo levels)
- Clear history and verify empty stacks
- Handle undo/redo when stacks are empty

---

### **Exercise 4.7: Priority Queue AI Move Scoring**
**Mô tả**: Sử dụng priority queue để sort AI moves theo score.

**Yêu cầu**:
```cpp
struct AIMove {
    int row, col;
    int score;
    string strategy; // "win", "block", "center", "corner"
    
    // For priority queue (higher score = higher priority)
    bool operator<(const AIMove& other) const {
        return score < other.score;
    }
};

class AIMoveEvaluator {
private:
    priority_queue<AIMove> moveQueue;
    
public:
    void evaluateAllMoves(const vector<vector<char>>& board, char aiPlayer);
    AIMove getBestMove();
    vector<AIMove> getTopNMoves(int n);
    void clearQueue();
    int getQueueSize();
    
private:
    int calculateMoveScore(int row, int col, const vector<vector<char>>& board, char player);
    string determineMoveStrategy(int row, int col, const vector<vector<char>>& board);
};
```

**Test cases**:
- Evaluate moves trên board có winning opportunities
- Verify highest scored moves được return đầu tiên
- Test với empty board (tất cả moves score bằng nhau)
- Performance test với large boards (5x5, 7x7)

---

### **Exercise 4.8: Algorithm Complexity Analysis**
**Mô tả**: Implement và benchmark các algorithms với different complexities.

**Yêu cầu**:
```cpp
class AlgorithmBenchmark {
public:
    // O(n²) - Brute force win checking
    bool checkWinBruteForce(const vector<vector<char>>& board, char player);
    
    // O(1) - Optimized win checking (chỉ check around last move)
    bool checkWinOptimized(const vector<vector<char>>& board, char player, int lastRow, int lastCol);
    
    // O(n log n) - Sort moves by some criteria
    vector<pair<int,int>> sortMovesByDistance(const vector<pair<int,int>>& moves, int centerRow, int centerCol);
    
    // O(n) - Linear search for best move
    pair<int,int> findBestMoveLinear(const vector<vector<int>>& scoreBoard);
    
    // Benchmark functions
    double measureExecutionTime(function<void()> func);
    void compareAlgorithmPerformance();
};
```

**Test cases**:
- Benchmark với board sizes: 3x3, 5x5, 10x10, 20x20
- Compare execution times của brute force vs optimized
- Verify tất cả algorithms produce correct results
- Create performance report

**Expected Results**:
- O(1) algorithm should be fastest
- O(n²) algorithm should scale poorly với large boards
- Performance differences should be measurable and documented

---

## 🧪 Mini Project: Complete Data Structure Integration

### **Project 4.9: Tic-Tac-Toe với Advanced Data Structures**
**Mô tả**: Tích hợp tất cả data structures đã học vào một complete tic-tac-toe system.

**Architecture**:
```cpp
class AdvancedTicTacToe {
private:
    // Core game
    GameBoard board;                    // Exercise 4.1
    GameDataProcessor dataProcessor;    // Exercise 4.2
    
    // History and states
    MoveHistory moveHistory;            // Exercise 4.3
    GameStateTree stateTree;           // Exercise 4.4
    UndoRedoSystem undoSystem;         // Exercise 4.6
    
    // Player management
    PlayerDatabase playerDB;            // Exercise 4.5
    
    // AI system
    AIMoveEvaluator aiEvaluator;       // Exercise 4.7
    
    // Performance monitoring
    AlgorithmBenchmark benchmark;       // Exercise 4.8
    
public:
    void playGame();
    void showMainMenu();
    void handlePlayerInput();
    void saveGameState();
    void loadGameState();
    void showStatistics();
    void runPerformanceTests();
};
```

**Features**:
1. **Complete game flow** với 2-player mode
2. **Save/Load system** với serialization
3. **Player statistics** tracking và leaderboard
4. **Undo/Redo** functionality
5. **Basic AI** với move scoring
6. **Performance monitoring** và benchmarking
7. **Full history tracking** của tất cả moves

**Test Requirements**:
- Chơi complete game từ start đến finish
- Test save/load functionality
- Verify player stats accuracy
- Test undo/redo system
- Performance benchmark report
- Memory usage analysis

**Bonus Features**:
- Multiple board sizes (3x3, 5x5)
- Different AI difficulty levels
- Game replay functionality
- Export statistics to file
- Multi-language support

---

## 📊 Grading Criteria

### **Individual Exercises (70%)**
- **Correctness (40%)**: Code chạy đúng và pass tất cả test cases
- **Code Quality (20%)**: Clean, readable, well-documented code
- **Efficiency (10%)**: Proper algorithm complexity và memory usage

### **Mini Project (30%)**
- **Integration (15%)**: Tất cả components work together
- **Features (10%)**: All required features implemented
- **Testing (5%)**: Comprehensive testing và error handling

### **Bonus Points (up to +20%)**
- Creative additional features
- Performance optimizations
- Excellent documentation
- Advanced error handling

---

## 🚀 Getting Started

1. **Setup Development Environment**:
   ```bash
   # Create week04 workspace
   mkdir week04_workspace
   cd week04_workspace
   
   # Setup basic project structure
   mkdir src include tests
   ```

2. **Recommended Development Order**:
   - Start với Exercise 4.1 (GameBoard)
   - Build incrementally: 4.2 → 4.3 → 4.4 → 4.5
   - Implement performance exercises: 4.6 → 4.7 → 4.8
   - Integrate everything trong Mini Project 4.9

3. **Testing Strategy**:
   - Write unit tests cho mỗi class/function
   - Use assertions để verify correctness
   - Create sample data cho testing
   - Benchmark performance regularly

4. **Resources**:
   - STL documentation: [cppreference.com](https://cppreference.com)
   - Algorithm complexity reference: [Big O Cheat Sheet](https://www.bigocheatsheet.com)
   - Data structures visualizations: [visualgo.net](https://visualgo.net)

---

## 📝 Submission Guidelines

### **Code Organization**:
```
week04_submission/
├── src/
│   ├── exercise_4_1.cpp
│   ├── exercise_4_2.cpp
│   ├── ...
│   └── mini_project.cpp
├── include/
│   ├── game_board.h
│   ├── data_processor.h
│   └── ...
├── tests/
│   ├── test_exercise_4_1.cpp
│   └── ...
├── docs/
│   ├── performance_report.md
│   └── design_decisions.md
└── README.md
```

### **Documentation Requirements**:
- Comments trong code explaining complex logic
- README.md với build instructions
- Performance report với benchmark results
- Design decisions documentation

### **Deadline**: End of Week 4
### **Submission Method**: Git commit với tag "week04-submission"

---

**🎯 Learning Outcomes**: Sau Week 4, bạn sẽ thành thạo fundamental data structures và có thể apply chúng hiệu quả trong game development và software engineering nói chung.
