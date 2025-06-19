# Week 4 - Lesson 5: Algorithm Analysis and Optimization Techniques

## Mục tiêu học tập
- Hiểu về Big O notation và algorithm complexity analysis
- Áp dụng optimization techniques trong game development
- Benchmark và profile code performance
- Optimize algorithms cho real-time game scenarios

## 1. Big O Notation và Time Complexity

### 1.1 Basic Complexity Analysis
```cpp
#include <chrono>
#include <random>
#include <algorithm>

class ComplexityAnalyzer {
public:
    // O(1) - Constant time
    static bool isValidPosition(int row, int col, int boardSize) {
        return row >= 0 && row < boardSize && col >= 0 && col < boardSize;
    }
    
    // O(n) - Linear time
    static int countEmptyCells(const GameBoard_Vector& board) {
        int count = 0;
        int size = board.getSize();
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) == ' ') {
                    count++;
                }
            }
        }
        
        return count;  // Actually O(n²) for 2D board, but O(n) for total cells
    }
    
    // O(log n) - Logarithmic time
    static bool binarySearchSortedMoves(const vector<AIMove>& sortedMoves, 
                                       double targetScore) {
        int left = 0;
        int right = sortedMoves.size() - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            if (abs(sortedMoves[mid].score - targetScore) < 1e-9) {
                return true;
            } else if (sortedMoves[mid].score < targetScore) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return false;
    }
    
    // O(n log n) - Linearithmic time
    static vector<AIMove> sortMovesByScore(vector<AIMove> moves) {
        sort(moves.begin(), moves.end(), [](const AIMove& a, const AIMove& b) {
            return a.score > b.score;
        });
        return moves;
    }
    
    // O(n²) - Quadratic time
    static vector<pair<int, int>> findAllPairs(const vector<int>& positions) {
        vector<pair<int, int>> pairs;
        
        for (size_t i = 0; i < positions.size(); i++) {
            for (size_t j = i + 1; j < positions.size(); j++) {
                pairs.push_back({positions[i], positions[j]});
            }
        }
        
        return pairs;
    }
    
    // O(2^n) - Exponential time (inefficient)
    static int fibonacciRecursive(int n) {
        if (n <= 1) return n;
        return fibonacciRecursive(n - 1) + fibonacciRecursive(n - 2);
    }
    
    // O(n) - Optimized fibonacci using memoization
    static int fibonacciOptimized(int n, unordered_map<int, int>& memo) {
        if (n <= 1) return n;
        
        if (memo.find(n) != memo.end()) {
            return memo[n];
        }
        
        memo[n] = fibonacciOptimized(n - 1, memo) + fibonacciOptimized(n - 2, memo);
        return memo[n];
    }
    
    // Benchmark function to measure actual performance
    static void benchmarkComplexities() {
        cout << "=== Algorithm Complexity Benchmarks ===" << endl;
        
        vector<int> sizes = {10, 100, 1000, 10000};
        
        for (int size : sizes) {
            cout << "\n--- Size: " << size << " ---" << endl;
            
            // Generate test data
            vector<int> data(size);
            iota(data.begin(), data.end(), 0);
            
            // O(1) operation
            auto start = chrono::high_resolution_clock::now();
            bool valid = isValidPosition(5, 5, 10);
            auto end = chrono::high_resolution_clock::now();
            auto time1 = chrono::duration_cast<chrono::nanoseconds>(end - start);
            cout << "O(1) operation: " << time1.count() << " ns" << endl;
            
            // O(log n) operation
            start = chrono::high_resolution_clock::now();
            bool found = binary_search(data.begin(), data.end(), size / 2);
            end = chrono::high_resolution_clock::now();
            auto timeLogN = chrono::duration_cast<chrono::nanoseconds>(end - start);
            cout << "O(log n) operation: " << timeLogN.count() << " ns" << endl;
            
            // O(n) operation
            start = chrono::high_resolution_clock::now();
            int sum = accumulate(data.begin(), data.end(), 0);
            end = chrono::high_resolution_clock::now();
            auto timeN = chrono::duration_cast<chrono::microseconds>(end - start);
            cout << "O(n) operation: " << timeN.count() << " μs" << endl;
            
            // O(n log n) operation
            vector<int> dataCopy = data;
            start = chrono::high_resolution_clock::now();
            sort(dataCopy.begin(), dataCopy.end());
            end = chrono::high_resolution_clock::now();
            auto timeNLogN = chrono::duration_cast<chrono::microseconds>(end - start);
            cout << "O(n log n) operation: " << timeNLogN.count() << " μs" << endl;
            
            // O(n²) operation (limited size to prevent long runtime)
            if (size <= 1000) {
                start = chrono::high_resolution_clock::now();
                auto pairs = findAllPairs(data);
                end = chrono::high_resolution_clock::now();
                auto timeN2 = chrono::duration_cast<chrono::milliseconds>(end - start);
                cout << "O(n²) operation: " << timeN2.count() << " ms" << endl;
            }
        }
    }
};
```

### 1.2 Game-Specific Algorithm Analysis
```cpp
class GameAlgorithmAnalysis {
public:
    // Analyze win condition checking complexity
    static void analyzeWinConditions() {
        cout << "\n=== Win Condition Algorithm Analysis ===" << endl;
        
        vector<int> boardSizes = {3, 4, 5, 10, 15};
        
        for (int size : boardSizes) {
            GameBoard_Vector board(size);
            
            // Fill board with random data for testing
            random_device rd;
            mt19937 gen(rd());
            uniform_int_distribution<> dis(0, 2);
            
            for (int i = 0; i < size; i++) {
                for (int j = 0; j < size; j++) {
                    char symbols[] = {' ', 'X', 'O'};
                    board.setCell(i, j, symbols[dis(gen)]);
                }
            }
            
            // Naive approach - O(n³)
            auto start = chrono::high_resolution_clock::now();
            bool hasWinner1 = checkWinNaive(board);
            auto end = chrono::high_resolution_clock::now();
            auto timeNaive = chrono::duration_cast<chrono::microseconds>(end - start);
            
            // Optimized approach - O(n²)
            start = chrono::high_resolution_clock::now();
            bool hasWinner2 = checkWinOptimized(board);
            end = chrono::high_resolution_clock::now();
            auto timeOptimized = chrono::duration_cast<chrono::microseconds>(end - start);
            
            // Incremental approach - O(1) for single move check
            start = chrono::high_resolution_clock::now();
            bool hasWinner3 = checkWinIncremental(board, size/2, size/2, 'X');
            end = chrono::high_resolution_clock::now();
            auto timeIncremental = chrono::duration_cast<chrono::nanoseconds>(end - start);
            
            cout << "Board size " << size << "x" << size << ":" << endl;
            cout << "  Naive O(n³): " << timeNaive.count() << " μs" << endl;
            cout << "  Optimized O(n²): " << timeOptimized.count() << " μs" << endl;
            cout << "  Incremental O(1): " << timeIncremental.count() << " ns" << endl;
            cout << "  Speedup: " << (double)timeNaive.count() / timeOptimized.count() << "x" << endl;
        }
    }
    
private:
    // O(n³) - Check every position for every direction
    static bool checkWinNaive(const GameBoard_Vector& board) {
        int size = board.getSize();
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                char player = board.getCell(i, j);
                if (player == ' ') continue;
                
                // Check all 4 directions from this position
                vector<pair<int, int>> directions = {{0, 1}, {1, 0}, {1, 1}, {1, -1}};
                
                for (auto& dir : directions) {
                    int count = 1;
                    
                    // Check positive direction
                    for (int k = 1; k < size; k++) {
                        int newRow = i + dir.first * k;
                        int newCol = j + dir.second * k;
                        
                        if (newRow < 0 || newRow >= size || newCol < 0 || newCol >= size ||
                            board.getCell(newRow, newCol) != player) {
                            break;
                        }
                        count++;
                    }
                    
                    if (count >= size) return true;
                }
            }
        }
        
        return false;
    }
    
    // O(n²) - Check each line once
    static bool checkWinOptimized(const GameBoard_Vector& board) {
        int size = board.getSize();
        
        // Check rows
        for (int i = 0; i < size; i++) {
            if (checkLine(board.getRow(i))) return true;
        }
        
        // Check columns
        for (int j = 0; j < size; j++) {
            if (checkLine(board.getColumn(j))) return true;
        }
        
        // Check diagonals
        if (checkLine(board.getDiagonal(true)) || checkLine(board.getDiagonal(false))) {
            return true;
        }
        
        return false;
    }
    
    // O(1) - Check only lines affected by last move
    static bool checkWinIncremental(const GameBoard_Vector& board, int lastRow, int lastCol, char player) {
        int size = board.getSize();
        
        // Check row
        int count = 1;
        for (int c = lastCol - 1; c >= 0 && board.getCell(lastRow, c) == player; c--) count++;
        for (int c = lastCol + 1; c < size && board.getCell(lastRow, c) == player; c++) count++;
        if (count >= size) return true;
        
        // Check column
        count = 1;
        for (int r = lastRow - 1; r >= 0 && board.getCell(r, lastCol) == player; r--) count++;
        for (int r = lastRow + 1; r < size && board.getCell(r, lastCol) == player; r++) count++;
        if (count >= size) return true;
        
        // Check main diagonal (if on diagonal)
        if (lastRow == lastCol) {
            count = 1;
            for (int d = 1; lastRow - d >= 0 && lastCol - d >= 0 && 
                 board.getCell(lastRow - d, lastCol - d) == player; d++) count++;
            for (int d = 1; lastRow + d < size && lastCol + d < size && 
                 board.getCell(lastRow + d, lastCol + d) == player; d++) count++;
            if (count >= size) return true;
        }
        
        // Check anti-diagonal (if on anti-diagonal)
        if (lastRow + lastCol == size - 1) {
            count = 1;
            for (int d = 1; lastRow - d >= 0 && lastCol + d < size && 
                 board.getCell(lastRow - d, lastCol + d) == player; d++) count++;
            for (int d = 1; lastRow + d < size && lastCol - d >= 0 && 
                 board.getCell(lastRow + d, lastCol - d) == player; d++) count++;
            if (count >= size) return true;
        }
        
        return false;
    }
    
    static bool checkLine(const vector<char>& line) {
        if (line.empty()) return false;
        
        char first = line[0];
        if (first == ' ') return false;
        
        return all_of(line.begin(), line.end(), [first](char c) { return c == first; });
    }
};
```

## 2. Memory Optimization Techniques

### 2.1 Memory-Efficient Data Structures
```cpp
class MemoryOptimizedGameBoard {
private:
    // Use bitsets for memory efficiency in larger boards
    vector<bitset<2>> board;  // 2 bits per cell: 00=' ', 01='X', 10='O'
    int size;
    
public:
    MemoryOptimizedGameBoard(int boardSize) : size(boardSize) {
        board.resize(size * size);
    }
    
    void setCell(int row, int col, char player) {
        if (row < 0 || row >= size || col < 0 || col >= size) return;
        
        int index = row * size + col;
        
        switch (player) {
            case ' ': board[index] = 0b00; break;
            case 'X': board[index] = 0b01; break;
            case 'O': board[index] = 0b10; break;
        }
    }
    
    char getCell(int row, int col) const {
        if (row < 0 || row >= size || col < 0 || col >= size) return '\0';
        
        int index = row * size + col;
        unsigned int value = board[index].to_ulong();
        
        switch (value) {
            case 0b00: return ' ';
            case 0b01: return 'X';
            case 0b10: return 'O';
            default: return '?';
        }
    }
    
    // Memory usage comparison
    static void compareMemoryUsage() {
        cout << "\n=== Memory Usage Comparison ===" << endl;
        
        vector<int> sizes = {10, 100, 1000};
        
        for (int size : sizes) {
            // Standard approach: char array
            size_t standardMemory = size * size * sizeof(char);
            
            // Optimized approach: bitsets (2 bits per cell)
            size_t optimizedMemory = size * size * 2 / 8;  // bits to bytes
            
            // Vector of chars
            size_t vectorMemory = size * size * sizeof(char) + sizeof(vector<char>);
            
            cout << "Board size " << size << "x" << size << ":" << endl;
            cout << "  Standard char array: " << standardMemory << " bytes" << endl;
            cout << "  Bitset (2 bits/cell): " << optimizedMemory << " bytes" << endl;
            cout << "  Vector overhead: " << vectorMemory << " bytes" << endl;
            cout << "  Memory reduction: " << 
                    (1.0 - (double)optimizedMemory / standardMemory) * 100 << "%" << endl;
        }
    }
};

// Object pooling for frequently allocated objects
template<typename T>
class ObjectPool {
private:
    stack<unique_ptr<T>> available;
    vector<unique_ptr<T>> all;
    size_t maxSize;
    size_t totalAllocated;
    size_t totalReused;
    
public:
    ObjectPool(size_t max = 1000) : maxSize(max), totalAllocated(0), totalReused(0) {}
    
    template<typename... Args>
    shared_ptr<T> acquire(Args&&... args) {
        unique_ptr<T> obj;
        
        if (!available.empty()) {
            obj = move(available.top());
            available.pop();
            totalReused++;
            
            // Reinitialize the object
            *obj = T(forward<Args>(args)...);
        } else if (all.size() < maxSize) {
            obj = make_unique<T>(forward<Args>(args)...);
            all.push_back(obj.get());  // Keep track for cleanup
            totalAllocated++;
        } else {
            // Pool exhausted, allocate new (not pooled)
            return make_shared<T>(forward<Args>(args)...);
        }
        
        // Return shared_ptr with custom deleter
        return shared_ptr<T>(obj.release(), [this](T* ptr) {
            available.push(unique_ptr<T>(ptr));
        });
    }
    
    void printStatistics() const {
        cout << "Object Pool Statistics:" << endl;
        cout << "  Total allocated: " << totalAllocated << endl;
        cout << "  Total reused: " << totalReused << endl;
        cout << "  Available objects: " << available.size() << endl;
        cout << "  Reuse rate: " << (totalReused * 100.0 / (totalAllocated + totalReused)) << "%" << endl;
    }
    
    ~ObjectPool() {
        // Cleanup is automatic with unique_ptr
    }
};

// Cache-friendly data layout
struct GameStateSOA {  // Structure of Arrays
    vector<int> rows;
    vector<int> cols;
    vector<char> players;
    vector<double> scores;
    vector<chrono::steady_clock::time_point> timestamps;
    
    void addMove(int row, int col, char player, double score) {
        rows.push_back(row);
        cols.push_back(col);
        players.push_back(player);
        scores.push_back(score);
        timestamps.push_back(chrono::steady_clock::now());
    }
    
    size_t size() const { return rows.size(); }
    
    // Cache-friendly operations on specific data
    double averageScore() const {
        return accumulate(scores.begin(), scores.end(), 0.0) / scores.size();
    }
    
    void printMemoryLayout() const {
        cout << "Structure of Arrays layout:" << endl;
        cout << "  All rows stored contiguously: " << rows.size() * sizeof(int) << " bytes" << endl;
        cout << "  All cols stored contiguously: " << cols.size() * sizeof(int) << " bytes" << endl;
        cout << "  All players stored contiguously: " << players.size() * sizeof(char) << " bytes" << endl;
        cout << "  Total: " << (rows.size() * sizeof(int) * 2 + players.size() * sizeof(char) + 
                               scores.size() * sizeof(double) + timestamps.size() * sizeof(chrono::steady_clock::time_point)) << " bytes" << endl;
    }
};
```

## 3. Performance Optimization Strategies

### 3.1 Algorithmic Optimizations
```cpp
class OptimizedGameLogic {
public:
    // Memoization for expensive calculations
    static unordered_map<string, double> evaluationCache;
    
    static double evaluatePositionMemoized(const GameBoard_Vector& board, char player) {
        string boardKey = boardToString(board) + player;
        
        auto it = evaluationCache.find(boardKey);
        if (it != evaluationCache.end()) {
            return it->second;
        }
        
        double evaluation = expensiveEvaluation(board, player);
        evaluationCache[boardKey] = evaluation;
        
        return evaluation;
    }
    
    // Lazy evaluation - compute only when needed
    class LazyGameAnalysis {
    private:
        const GameBoard_Vector& board;
        mutable optional<vector<pair<int, int>>> cachedEmptyPositions;
        mutable optional<bool> cachedHasWinner;
        mutable optional<char> cachedWinner;
        
    public:
        LazyGameAnalysis(const GameBoard_Vector& b) : board(b) {}
        
        const vector<pair<int, int>>& getEmptyPositions() const {
            if (!cachedEmptyPositions.has_value()) {
                vector<pair<int, int>> empty;
                int size = board.getSize();
                
                for (int i = 0; i < size; i++) {
                    for (int j = 0; j < size; j++) {
                        if (board.getCell(i, j) == ' ') {
                            empty.push_back({i, j});
                        }
                    }
                }
                
                cachedEmptyPositions = empty;
            }
            
            return cachedEmptyPositions.value();
        }
        
        bool hasWinner() const {
            if (!cachedHasWinner.has_value()) {
                cachedHasWinner = GameAlgorithmAnalysis::checkWinOptimized(board);
            }
            
            return cachedHasWinner.value();
        }
        
        char getWinner() const {
            if (!cachedWinner.has_value()) {
                if (hasWinner()) {
                    cachedWinner = findWinner(board);
                } else {
                    cachedWinner = ' ';
                }
            }
            
            return cachedWinner.value();
        }
    };
    
    // Branch and bound optimization for minimax
    static double minimaxWithPruning(const GameBoard_Vector& board, int depth, 
                                   bool isMaximizing, char player, 
                                   double alpha, double beta,
                                   int& nodesEvaluated, int& nodesPruned) {
        nodesEvaluated++;
        
        // Terminal node check
        LazyGameAnalysis analysis(board);
        if (depth == 0 || analysis.hasWinner()) {
            return evaluatePositionMemoized(board, player);
        }
        
        // Move ordering - evaluate promising moves first
        vector<pair<int, int>> moves = analysis.getEmptyPositions();
        vector<pair<double, pair<int, int>>> scoredMoves;
        
        for (const auto& move : moves) {
            double quickScore = quickMoveEvaluation(board, move.first, move.second, player);
            scoredMoves.push_back({quickScore, move});
        }
        
        // Sort moves by potential (best first for maximizing, worst first for minimizing)
        sort(scoredMoves.begin(), scoredMoves.end(), [isMaximizing](const auto& a, const auto& b) {
            return isMaximizing ? a.first > b.first : a.first < b.first;
        });
        
        double bestValue = isMaximizing ? -INFINITY : INFINITY;
        
        for (const auto& scoredMove : scoredMoves) {
            auto move = scoredMove.second;
            
            GameBoard_Vector newBoard = board;
            newBoard.setCell(move.first, move.second, player);
            
            char nextPlayer = (player == 'X') ? 'O' : 'X';
            double value = minimaxWithPruning(newBoard, depth - 1, !isMaximizing, 
                                            nextPlayer, alpha, beta, 
                                            nodesEvaluated, nodesPruned);
            
            if (isMaximizing) {
                bestValue = max(bestValue, value);
                alpha = max(alpha, bestValue);
            } else {
                bestValue = min(bestValue, value);
                beta = min(beta, bestValue);
            }
            
            if (beta <= alpha) {
                nodesPruned++;
                break;  // Alpha-beta pruning
            }
        }
        
        return bestValue;
    }
    
    // Iterative deepening for better move ordering
    static pair<int, int> iterativeDeepeningSearch(const GameBoard_Vector& board, 
                                                  char player, int maxDepth) {
        pair<int, int> bestMove(-1, -1);
        vector<pair<double, pair<int, int>>> moveHistory;
        
        for (int depth = 1; depth <= maxDepth; depth++) {
            int nodesEvaluated = 0, nodesPruned = 0;
            
            auto start = chrono::high_resolution_clock::now();
            
            LazyGameAnalysis analysis(board);
            const auto& moves = analysis.getEmptyPositions();
            
            double bestScore = -INFINITY;
            pair<int, int> currentBestMove(-1, -1);
            
            // Use previous iteration's results for move ordering
            vector<pair<int, int>> orderedMoves;
            if (!moveHistory.empty()) {
                sort(moveHistory.begin(), moveHistory.end(), 
                     [](const auto& a, const auto& b) { return a.first > b.first; });
                
                for (const auto& scored : moveHistory) {
                    orderedMoves.push_back(scored.second);
                }
            } else {
                orderedMoves = moves;
            }
            
            moveHistory.clear();
            
            for (const auto& move : orderedMoves) {
                GameBoard_Vector newBoard = board;
                newBoard.setCell(move.first, move.second, player);
                
                double score = minimaxWithPruning(newBoard, depth - 1, false, 
                                                (player == 'X') ? 'O' : 'X',
                                                -INFINITY, INFINITY, 
                                                nodesEvaluated, nodesPruned);
                
                moveHistory.push_back({score, move});
                
                if (score > bestScore) {
                    bestScore = score;
                    currentBestMove = move;
                }
            }
            
            auto end = chrono::high_resolution_clock::now();
            auto duration = chrono::duration_cast<chrono::milliseconds>(end - start);
            
            bestMove = currentBestMove;
            
            cout << "Depth " << depth << ": Best move (" << bestMove.first << "," << bestMove.second 
                 << ") Score: " << bestScore << " Time: " << duration.count() << "ms "
                 << "Nodes: " << nodesEvaluated << " Pruned: " << nodesPruned << endl;
            
            // Early termination for obvious wins/losses
            if (abs(bestScore) > 900) {
                cout << "Early termination - decisive move found" << endl;
                break;
            }
        }
        
        return bestMove;
    }
    
private:
    static string boardToString(const GameBoard_Vector& board) {
        string result;
        int size = board.getSize();
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                result += board.getCell(i, j);
            }
        }
        
        return result;
    }
    
    static double expensiveEvaluation(const GameBoard_Vector& board, char player) {
        // Simulate expensive calculation
        double score = 0.0;
        int size = board.getSize();
        
        // Evaluate all positions
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                char cell = board.getCell(i, j);
                if (cell == player) {
                    score += evaluatePosition(board, i, j, player);
                }
            }
        }
        
        return score;
    }
    
    static double evaluatePosition(const GameBoard_Vector& board, int row, int col, char player) {
        // Complex position evaluation
        double score = 0.0;
        int size = board.getSize();
        
        // Center bonus
        double centerDistance = sqrt(pow(row - size/2.0, 2) + pow(col - size/2.0, 2));
        score += (size - centerDistance) * 10;
        
        // Line potential
        vector<pair<int, int>> directions = {{0, 1}, {1, 0}, {1, 1}, {1, -1}};
        
        for (const auto& dir : directions) {
            int consecutive = 1;
            
            // Check both directions
            for (int d = 1; d < size; d++) {
                int newRow = row + dir.first * d;
                int newCol = col + dir.second * d;
                
                if (newRow >= 0 && newRow < size && newCol >= 0 && newCol < size &&
                    board.getCell(newRow, newCol) == player) {
                    consecutive++;
                } else {
                    break;
                }
            }
            
            for (int d = 1; d < size; d++) {
                int newRow = row - dir.first * d;
                int newCol = col - dir.second * d;
                
                if (newRow >= 0 && newRow < size && newCol >= 0 && newCol < size &&
                    board.getCell(newRow, newCol) == player) {
                    consecutive++;
                } else {
                    break;
                }
            }
            
            score += consecutive * consecutive;
        }
        
        return score;
    }
    
    static double quickMoveEvaluation(const GameBoard_Vector& board, int row, int col, char player) {
        // Fast heuristic for move ordering
        int size = board.getSize();
        double score = 0.0;
        
        // Center preference
        double centerDistance = abs(row - size/2) + abs(col - size/2);
        score += (size - centerDistance) * 5;
        
        // Corner and edge detection
        bool isCorner = (row == 0 || row == size-1) && (col == 0 || col == size-1);
        bool isEdge = (row == 0 || row == size-1 || col == 0 || col == size-1);
        
        if (isCorner) score += 15;
        else if (isEdge) score += 5;
        
        return score;
    }
    
    static char findWinner(const GameBoard_Vector& board) {
        int size = board.getSize();
        
        // Check rows
        for (int i = 0; i < size; i++) {
            auto row = board.getRow(i);
            if (!row.empty() && row[0] != ' ') {
                bool allSame = all_of(row.begin(), row.end(), 
                                    [&row](char c) { return c == row[0]; });
                if (allSame) return row[0];
            }
        }
        
        // Check columns
        for (int j = 0; j < size; j++) {
            auto col = board.getColumn(j);
            if (!col.empty() && col[0] != ' ') {
                bool allSame = all_of(col.begin(), col.end(), 
                                    [&col](char c) { return c == col[0]; });
                if (allSame) return col[0];
            }
        }
        
        // Check diagonals
        auto diag1 = board.getDiagonal(true);
        if (!diag1.empty() && diag1[0] != ' ') {
            bool allSame = all_of(diag1.begin(), diag1.end(), 
                                [&diag1](char c) { return c == diag1[0]; });
            if (allSame) return diag1[0];
        }
        
        auto diag2 = board.getDiagonal(false);
        if (!diag2.empty() && diag2[0] != ' ') {
            bool allSame = all_of(diag2.begin(), diag2.end(), 
                                [&diag2](char c) { return c == diag2[0]; });
            if (allSame) return diag2[0];
        }
        
        return ' ';
    }
};

// Static member definition
unordered_map<string, double> OptimizedGameLogic::evaluationCache;
```

## 4. Profiling và Benchmarking

### 4.1 Performance Profiler
```cpp
class GameProfiler {
private:
    struct ProfileData {
        string functionName;
        chrono::high_resolution_clock::time_point startTime;
        chrono::duration<double> totalTime{0};
        int callCount = 0;
        
        ProfileData(const string& name) : functionName(name) {}
    };
    
    static unordered_map<string, ProfileData> profiles;
    static bool enabled;
    
public:
    class ScopedTimer {
    private:
        string functionName;
        chrono::high_resolution_clock::time_point startTime;
        
    public:
        ScopedTimer(const string& name) : functionName(name) {
            if (!GameProfiler::enabled) return;
            
            startTime = chrono::high_resolution_clock::now();
            
            auto& profile = GameProfiler::profiles[functionName];
            profile.functionName = functionName;
            profile.callCount++;
        }
        
        ~ScopedTimer() {
            if (!GameProfiler::enabled) return;
            
            auto endTime = chrono::high_resolution_clock::now();
            auto duration = endTime - startTime;
            
            GameProfiler::profiles[functionName].totalTime += duration;
        }
    };
    
    static void enable() { enabled = true; }
    static void disable() { enabled = false; }
    static void reset() { profiles.clear(); }
    
    static void printReport() {
        cout << "\n=== PERFORMANCE PROFILE REPORT ===" << endl;
        cout << left << setw(30) << "Function" << setw(12) << "Calls" 
             << setw(15) << "Total Time" << setw(15) << "Avg Time" << endl;
        cout << string(72, '-') << endl;
        
        vector<pair<string, ProfileData*>> sortedProfiles;
        for (auto& pair : profiles) {
            sortedProfiles.push_back({pair.first, &pair.second});
        }
        
        sort(sortedProfiles.begin(), sortedProfiles.end(), 
             [](const auto& a, const auto& b) {
                 return a.second->totalTime > b.second->totalTime;
             });
        
        for (const auto& pair : sortedProfiles) {
            const ProfileData& data = *pair.second;
            double avgTime = data.totalTime.count() / data.callCount;
            
            cout << left << setw(30) << data.functionName
                 << setw(12) << data.callCount
                 << setw(15) << fixed << setprecision(6) << data.totalTime.count()
                 << setw(15) << fixed << setprecision(6) << avgTime << endl;
        }
        
        cout << string(72, '-') << endl;
    }
};

// Static member definitions
unordered_map<string, GameProfiler::ProfileData> GameProfiler::profiles;
bool GameProfiler::enabled = false;

// Macro for easy profiling
#define PROFILE_FUNCTION() GameProfiler::ScopedTimer timer(__FUNCTION__)
#define PROFILE_SCOPE(name) GameProfiler::ScopedTimer timer(name)

// Example usage
class ProfiledGameAI {
public:
    static pair<int, int> getBestMove(const GameBoard_Vector& board, char player) {
        PROFILE_FUNCTION();
        
        {
            PROFILE_SCOPE("Move Generation");
            // Generate moves...
        }
        
        {
            PROFILE_SCOPE("Move Evaluation");
            // Evaluate moves...
        }
        
        {
            PROFILE_SCOPE("Best Move Selection");
            // Select best move...
        }
        
        return {0, 0};  // Placeholder
    }
};
```

## Bài tập thực hành

### Bài tập 1: Algorithm Complexity Analysis
Analyze và optimize các algorithms trong game:
- Win condition checking
- Move generation
- Board evaluation
- State comparison

### Bài tập 2: Memory Optimization
Implement memory-efficient versions:
- Compact board representation
- Object pooling system
- Cache-friendly data structures
- Memory usage profiling

### Bài tập 3: AI Performance Optimization
Optimize minimax algorithm:
- Alpha-beta pruning
- Transposition tables
- Move ordering
- Iterative deepening

### Bài tập 4: Real-time Performance
Design for real-time constraints:
- Time-bounded search
- Anytime algorithms
- Progressive refinement
- Performance monitoring

## Best Practices Summary

1. **Algorithmic Complexity**: Always analyze time and space complexity
2. **Premature Optimization**: Profile before optimizing
3. **Cache Efficiency**: Design for cache-friendly access patterns
4. **Memory Management**: Use appropriate data structures and pooling
5. **Measurement**: Always measure performance improvements
6. **Trade-offs**: Balance between time, space, and code complexity

## Tài liệu tham khảo
- Big O Cheat Sheet: https://www.bigocheatsheet.com/
- Algorithm Analysis: https://en.wikipedia.org/wiki/Analysis_of_algorithms
- Performance Optimization: https://www.agner.org/optimize/
- Cache-Friendly Programming: https://mechanical-sympathy.blogspot.com/

## Kết thúc Week 4
Bạn đã hoàn thành Week 4 về Data Structures & Algorithms! Tuần tiếp theo chúng ta sẽ bắt đầu implement game thực tế.
