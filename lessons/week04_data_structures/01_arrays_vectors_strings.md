# Week 4 - Lesson 1: Arrays, Vectors, and Strings in Game Development

## Mục tiêu học tập
- Nắm vững việc sử dụng arrays, vectors, và strings trong C++
- Hiểu performance characteristics của các data structures
- Áp dụng vào game development scenarios
- Memory management và optimization techniques

## 1. Arrays vs Vectors

### 1.1 C-style Arrays
```cpp
// Basic game board representation
class GameBoard_Array {
private:
    static const int SIZE = 3;
    char board[SIZE][SIZE];  // Stack-allocated, fixed size
    
public:
    GameBoard_Array() {
        clear();
    }
    
    void clear() {
        for (int i = 0; i < SIZE; i++) {
            for (int j = 0; j < SIZE; j++) {
                board[i][j] = ' ';
            }
        }
    }
    
    bool setCell(int row, int col, char value) {
        if (row >= 0 && row < SIZE && col >= 0 && col < SIZE) {
            board[row][col] = value;
            return true;
        }
        return false;
    }
    
    char getCell(int row, int col) const {
        if (row >= 0 && row < SIZE && col >= 0 && col < SIZE) {
            return board[row][col];
        }
        return '\0';
    }
    
    void display() const {
        for (int i = 0; i < SIZE; i++) {
            for (int j = 0; j < SIZE; j++) {
                cout << board[i][j];
                if (j < SIZE - 1) cout << "|";
            }
            cout << endl;
            if (i < SIZE - 1) {
                for (int k = 0; k < SIZE * 2 - 1; k++) {
                    cout << "-";
                }
                cout << endl;
            }
        }
    }
    
    // Efficient memcpy operations
    void copyFrom(const GameBoard_Array& other) {
        memcpy(board, other.board, sizeof(board));
    }
    
    bool equals(const GameBoard_Array& other) const {
        return memcmp(board, other.board, sizeof(board)) == 0;
    }
};
```

### 1.2 Vector-based Implementation
```cpp
#include <vector>
#include <algorithm>

class GameBoard_Vector {
private:
    vector<vector<char>> board;
    int size;
    
public:
    GameBoard_Vector(int boardSize = 3) : size(boardSize) {
        board.resize(size, vector<char>(size, ' '));
    }
    
    // Dynamic resizing capability
    void resize(int newSize) {
        size = newSize;
        board.clear();
        board.resize(size, vector<char>(size, ' '));
    }
    
    void clear() {
        for (auto& row : board) {
            fill(row.begin(), row.end(), ' ');
        }
    }
    
    bool setCell(int row, int col, char value) {
        if (row >= 0 && row < size && col >= 0 && col < size) {
            board[row][col] = value;
            return true;
        }
        return false;
    }
    
    char getCell(int row, int col) const {
        if (row >= 0 && row < size && col >= 0 && col < size) {
            return board[row][col];
        }
        return '\0';
    }
    
    int getSize() const { return size; }
    
    // Vector-specific operations
    vector<char> getRow(int row) const {
        if (row >= 0 && row < size) {
            return board[row];
        }
        return vector<char>();
    }
    
    vector<char> getColumn(int col) const {
        vector<char> column;
        if (col >= 0 && col < size) {
            for (int i = 0; i < size; i++) {
                column.push_back(board[i][col]);
            }
        }
        return column;
    }
    
    vector<char> getDiagonal(bool main = true) const {
        vector<char> diagonal;
        for (int i = 0; i < size; i++) {
            if (main) {
                diagonal.push_back(board[i][i]);
            } else {
                diagonal.push_back(board[i][size - 1 - i]);
            }
        }
        return diagonal;
    }
    
    // STL algorithm usage
    bool hasEmptyCells() const {
        for (const auto& row : board) {
            if (find(row.begin(), row.end(), ' ') != row.end()) {
                return true;
            }
        }
        return false;
    }
    
    int countCells(char value) const {
        int count = 0;
        for (const auto& row : board) {
            count += count_if(row.begin(), row.end(), 
                            [value](char c) { return c == value; });
        }
        return count;
    }
    
    void display() const {
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                cout << board[i][j];
                if (j < size - 1) cout << "|";
            }
            cout << endl;
            if (i < size - 1) {
                for (int k = 0; k < size * 2 - 1; k++) {
                    cout << "-";
                }
                cout << endl;
            }
        }
    }
};
```

### 1.3 Performance Comparison
```cpp
#include <chrono>

class PerformanceTester {
public:
    static void compareArrayVsVector() {
        const int ITERATIONS = 1000000;
        
        // Test 1: Memory allocation
        cout << "=== Memory Allocation Test ===" << endl;
        
        auto start = chrono::high_resolution_clock::now();
        for (int i = 0; i < ITERATIONS; i++) {
            GameBoard_Array arrayBoard;  // Stack allocation
        }
        auto end = chrono::high_resolution_clock::now();
        auto arrayTime = chrono::duration_cast<chrono::microseconds>(end - start);
        
        start = chrono::high_resolution_clock::now();
        for (int i = 0; i < ITERATIONS; i++) {
            GameBoard_Vector vectorBoard(3);  // Heap allocation
        }
        end = chrono::high_resolution_clock::now();
        auto vectorTime = chrono::duration_cast<chrono::microseconds>(end - start);
        
        cout << "Array allocation time: " << arrayTime.count() << " microseconds" << endl;
        cout << "Vector allocation time: " << vectorTime.count() << " microseconds" << endl;
        
        // Test 2: Access patterns
        cout << "\n=== Access Pattern Test ===" << endl;
        GameBoard_Array arrayBoard;
        GameBoard_Vector vectorBoard(3);
        
        start = chrono::high_resolution_clock::now();
        for (int i = 0; i < ITERATIONS; i++) {
            arrayBoard.setCell(i % 3, (i * 7) % 3, 'X');
            char c = arrayBoard.getCell((i * 3) % 3, (i * 5) % 3);
        }
        end = chrono::high_resolution_clock::now();
        auto arrayAccessTime = chrono::duration_cast<chrono::microseconds>(end - start);
        
        start = chrono::high_resolution_clock::now();
        for (int i = 0; i < ITERATIONS; i++) {
            vectorBoard.setCell(i % 3, (i * 7) % 3, 'X');
            char c = vectorBoard.getCell((i * 3) % 3, (i * 5) % 3);
        }
        end = chrono::high_resolution_clock::now();
        auto vectorAccessTime = chrono::duration_cast<chrono::microseconds>(end - start);
        
        cout << "Array access time: " << arrayAccessTime.count() << " microseconds" << endl;
        cout << "Vector access time: " << vectorAccessTime.count() << " microseconds" << endl;
    }
};
```

## 2. Advanced Vector Operations

### 2.1 Game State Management
```cpp
class GameStateManager {
private:
    vector<GameBoard_Vector> gameHistory;
    vector<pair<int, int>> moveHistory;
    vector<char> playerHistory;
    int currentStateIndex;
    
public:
    GameStateManager() : currentStateIndex(-1) {}
    
    void saveState(const GameBoard_Vector& board, int row, int col, char player) {
        // Remove any future states if we're not at the end
        if (currentStateIndex < static_cast<int>(gameHistory.size()) - 1) {
            gameHistory.erase(gameHistory.begin() + currentStateIndex + 1, gameHistory.end());
            moveHistory.erase(moveHistory.begin() + currentStateIndex + 1, moveHistory.end());
            playerHistory.erase(playerHistory.begin() + currentStateIndex + 1, playerHistory.end());
        }
        
        gameHistory.push_back(board);
        moveHistory.push_back({row, col});
        playerHistory.push_back(player);
        currentStateIndex++;
        
        // Limit history size for memory management
        const int MAX_HISTORY = 100;
        if (gameHistory.size() > MAX_HISTORY) {
            gameHistory.erase(gameHistory.begin());
            moveHistory.erase(moveHistory.begin());
            playerHistory.erase(playerHistory.begin());
            currentStateIndex--;
        }
    }
    
    bool undo(GameBoard_Vector& board) {
        if (currentStateIndex > 0) {
            currentStateIndex--;
            board = gameHistory[currentStateIndex];
            return true;
        }
        return false;
    }
    
    bool redo(GameBoard_Vector& board) {
        if (currentStateIndex < static_cast<int>(gameHistory.size()) - 1) {
            currentStateIndex++;
            board = gameHistory[currentStateIndex];
            return true;
        }
        return false;
    }
    
    void getLastMove(int& row, int& col, char& player) const {
        if (currentStateIndex >= 0) {
            row = moveHistory[currentStateIndex].first;
            col = moveHistory[currentStateIndex].second;
            player = playerHistory[currentStateIndex];
        } else {
            row = col = -1;
            player = ' ';
        }
    }
    
    void clear() {
        gameHistory.clear();
        moveHistory.clear();
        playerHistory.clear();
        currentStateIndex = -1;
    }
    
    int getHistorySize() const {
        return gameHistory.size();
    }
    
    bool canUndo() const {
        return currentStateIndex > 0;
    }
    
    bool canRedo() const {
        return currentStateIndex < static_cast<int>(gameHistory.size()) - 1;
    }
};
```

### 2.2 Move Generation và Validation
```cpp
class MoveGenerator {
public:
    struct Move {
        int row, col;
        char player;
        int score;  // For AI evaluation
        
        Move(int r, int c, char p, int s = 0) : row(r), col(c), player(p), score(s) {}
        
        bool operator<(const Move& other) const {
            return score > other.score;  // Higher score = better move
        }
    };
    
    static vector<Move> generateAllMoves(const GameBoard_Vector& board, char player) {
        vector<Move> moves;
        int size = board.getSize();
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) == ' ') {
                    moves.emplace_back(i, j, player);
                }
            }
        }
        
        return moves;
    }
    
    static vector<Move> generateScoredMoves(const GameBoard_Vector& board, char player) {
        vector<Move> moves = generateAllMoves(board, player);
        
        // Score each move
        for (Move& move : moves) {
            move.score = evaluateMove(board, move);
        }
        
        // Sort by score (best moves first)
        sort(moves.begin(), moves.end());
        
        return moves;
    }
    
    static vector<Move> filterMoves(const vector<Move>& moves, 
                                   function<bool(const Move&)> predicate) {
        vector<Move> filtered;
        copy_if(moves.begin(), moves.end(), back_inserter(filtered), predicate);
        return filtered;
    }
    
    static Move getBestMove(const GameBoard_Vector& board, char player) {
        vector<Move> moves = generateScoredMoves(board, player);
        if (!moves.empty()) {
            return moves[0];  // Highest scored move
        }
        return Move(-1, -1, player);  // No valid moves
    }
    
    static Move getRandomMove(const GameBoard_Vector& board, char player) {
        vector<Move> moves = generateAllMoves(board, player);
        if (!moves.empty()) {
            int randomIndex = rand() % moves.size();
            return moves[randomIndex];
        }
        return Move(-1, -1, player);
    }
    
private:
    static int evaluateMove(const GameBoard_Vector& board, const Move& move) {
        // Simple heuristic evaluation
        int score = 0;
        int size = board.getSize();
        
        // Center positions are generally better
        int centerDist = abs(move.row - size/2) + abs(move.col - size/2);
        score += (size - centerDist) * 10;
        
        // Check if move creates threats or blocks opponent
        GameBoard_Vector tempBoard = board;
        tempBoard.setCell(move.row, move.col, move.player);
        
        // Check for winning move
        if (isWinningMove(tempBoard, move)) {
            score += 1000;
        }
        
        // Check if move blocks opponent win
        char opponent = (move.player == 'X') ? 'O' : 'X';
        tempBoard.setCell(move.row, move.col, opponent);
        if (isWinningMove(tempBoard, Move(move.row, move.col, opponent))) {
            score += 500;  // Blocking is important but not as good as winning
        }
        
        return score;
    }
    
    static bool isWinningMove(const GameBoard_Vector& board, const Move& move) {
        // Check row
        vector<char> row = board.getRow(move.row);
        if (all_of(row.begin(), row.end(), [move](char c) { return c == move.player; })) {
            return true;
        }
        
        // Check column
        vector<char> col = board.getColumn(move.col);
        if (all_of(col.begin(), col.end(), [move](char c) { return c == move.player; })) {
            return true;
        }
        
        // Check diagonals
        if (move.row == move.col) {
            vector<char> diag = board.getDiagonal(true);
            if (all_of(diag.begin(), diag.end(), [move](char c) { return c == move.player; })) {
                return true;
            }
        }
        
        if (move.row + move.col == board.getSize() - 1) {
            vector<char> diag = board.getDiagonal(false);
            if (all_of(diag.begin(), diag.end(), [move](char c) { return c == move.player; })) {
                return true;
            }
        }
        
        return false;
    }
};
```

## 3. String Processing for Game Development

### 3.1 Game Commands và Parsing
```cpp
#include <string>
#include <sstream>
#include <regex>

class GameCommandParser {
public:
    enum class CommandType {
        MOVE,
        UNDO,
        REDO,
        SAVE,
        LOAD,
        QUIT,
        HELP,
        INVALID
    };
    
    struct Command {
        CommandType type;
        vector<string> arguments;
        string originalInput;
        
        Command(CommandType t, const vector<string>& args, const string& input)
            : type(t), arguments(args), originalInput(input) {}
    };
    
    static Command parseCommand(const string& input) {
        string trimmed = trim(input);
        vector<string> tokens = tokenize(trimmed);
        
        if (tokens.empty()) {
            return Command(CommandType::INVALID, {}, input);
        }
        
        string command = toLowerCase(tokens[0]);
        vector<string> args(tokens.begin() + 1, tokens.end());
        
        if (command == "move" || command == "m") {
            return Command(CommandType::MOVE, args, input);
        } else if (command == "undo" || command == "u") {
            return Command(CommandType::UNDO, args, input);
        } else if (command == "redo" || command == "r") {
            return Command(CommandType::REDO, args, input);
        } else if (command == "save" || command == "s") {
            return Command(CommandType::SAVE, args, input);
        } else if (command == "load" || command == "l") {
            return Command(CommandType::LOAD, args, input);
        } else if (command == "quit" || command == "q" || command == "exit") {
            return Command(CommandType::QUIT, args, input);
        } else if (command == "help" || command == "h" || command == "?") {
            return Command(CommandType::HELP, args, input);
        }
        
        return Command(CommandType::INVALID, args, input);
    }
    
    static bool validateMoveCommand(const Command& cmd, int& row, int& col) {
        if (cmd.type != CommandType::MOVE || cmd.arguments.size() != 2) {
            return false;
        }
        
        try {
            row = stoi(cmd.arguments[0]);
            col = stoi(cmd.arguments[1]);
            return row >= 0 && row < 3 && col >= 0 && col < 3;
        } catch (const exception&) {
            return false;
        }
    }
    
    static string getCommandHelp() {
        stringstream ss;
        ss << "Available commands:\n";
        ss << "  move <row> <col> (or m <row> <col>) - Make a move\n";
        ss << "  undo (or u) - Undo last move\n";
        ss << "  redo (or r) - Redo move\n";
        ss << "  save <filename> (or s <filename>) - Save game\n";
        ss << "  load <filename> (or l <filename>) - Load game\n";
        ss << "  help (or h or ?) - Show this help\n";
        ss << "  quit (or q or exit) - Quit game\n";
        return ss.str();
    }
    
private:
    static string trim(const string& str) {
        size_t start = str.find_first_not_of(" \t\n\r");
        if (start == string::npos) return "";
        
        size_t end = str.find_last_not_of(" \t\n\r");
        return str.substr(start, end - start + 1);
    }
    
    static vector<string> tokenize(const string& str) {
        vector<string> tokens;
        stringstream ss(str);
        string token;
        
        while (ss >> token) {
            tokens.push_back(token);
        }
        
        return tokens;
    }
    
    static string toLowerCase(const string& str) {
        string result = str;
        transform(result.begin(), result.end(), result.begin(), ::tolower);
        return result;
    }
};
```

### 3.2 Game State Serialization
```cpp
class GameSerializer {
public:
    static string serializeBoard(const GameBoard_Vector& board) {
        stringstream ss;
        int size = board.getSize();
        
        ss << size << "\n";
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                char cell = board.getCell(i, j);
                ss << (cell == ' ' ? '.' : cell);
            }
            ss << "\n";
        }
        
        return ss.str();
    }
    
    static bool deserializeBoard(const string& data, GameBoard_Vector& board) {
        stringstream ss(data);
        string line;
        
        // Read size
        if (!getline(ss, line)) return false;
        
        int size;
        try {
            size = stoi(line);
        } catch (const exception&) {
            return false;
        }
        
        board.resize(size);
        
        // Read board data
        for (int i = 0; i < size; i++) {
            if (!getline(ss, line)) return false;
            
            if (line.length() != size) return false;
            
            for (int j = 0; j < size; j++) {
                char cell = (line[j] == '.') ? ' ' : line[j];
                if (!board.setCell(i, j, cell)) return false;
            }
        }
        
        return true;
    }
    
    static string serializeGameState(const GameBoard_Vector& board,
                                   const vector<pair<int, int>>& moveHistory,
                                   const vector<char>& playerHistory,
                                   const string& player1Name,
                                   const string& player2Name) {
        stringstream ss;
        
        // Header
        ss << "TICTACTOE_SAVE_V1\n";
        ss << "PLAYER1=" << player1Name << "\n";
        ss << "PLAYER2=" << player2Name << "\n";
        ss << "MOVES=" << moveHistory.size() << "\n";
        
        // Move history
        for (size_t i = 0; i < moveHistory.size(); i++) {
            ss << moveHistory[i].first << "," << moveHistory[i].second 
               << "," << playerHistory[i] << "\n";
        }
        
        // Board state
        ss << "BOARD_START\n";
        ss << serializeBoard(board);
        ss << "BOARD_END\n";
        
        return ss.str();
    }
    
    static bool deserializeGameState(const string& data,
                                   GameBoard_Vector& board,
                                   vector<pair<int, int>>& moveHistory,
                                   vector<char>& playerHistory,
                                   string& player1Name,
                                   string& player2Name) {
        stringstream ss(data);
        string line;
        
        // Check header
        if (!getline(ss, line) || line != "TICTACTOE_SAVE_V1") {
            return false;
        }
        
        // Parse metadata
        while (getline(ss, line) && line != "BOARD_START") {
            if (line.substr(0, 8) == "PLAYER1=") {
                player1Name = line.substr(8);
            } else if (line.substr(0, 8) == "PLAYER2=") {
                player2Name = line.substr(8);
            } else if (line.substr(0, 6) == "MOVES=") {
                int moveCount = stoi(line.substr(6));
                moveHistory.clear();
                playerHistory.clear();
                
                // Read moves
                for (int i = 0; i < moveCount; i++) {
                    if (!getline(ss, line)) return false;
                    
                    // Parse "row,col,player"
                    size_t comma1 = line.find(',');
                    size_t comma2 = line.find(',', comma1 + 1);
                    
                    if (comma1 == string::npos || comma2 == string::npos) {
                        return false;
                    }
                    
                    int row = stoi(line.substr(0, comma1));
                    int col = stoi(line.substr(comma1 + 1, comma2 - comma1 - 1));
                    char player = line[comma2 + 1];
                    
                    moveHistory.push_back({row, col});
                    playerHistory.push_back(player);
                }
            }
        }
        
        // Read board
        stringstream boardData;
        while (getline(ss, line) && line != "BOARD_END") {
            boardData << line << "\n";
        }
        
        return deserializeBoard(boardData.str(), board);
    }
};
```

### 3.3 Text-based UI và Formatting
```cpp
class GameUI {
public:
    static void displayTitle() {
        cout << R"(
╔═══════════════════════════════════════╗
║            TIC TAC TOE GAME           ║
║          Advanced C++ Edition         ║
╚═══════════════════════════════════════╝
)" << endl;
    }
    
    static void displayBoard(const GameBoard_Vector& board) {
        int size = board.getSize();
        
        // Column headers
        cout << "   ";
        for (int j = 0; j < size; j++) {
            cout << " " << j << " ";
            if (j < size - 1) cout << " ";
        }
        cout << endl;
        
        // Top border
        cout << "  ┌";
        for (int j = 0; j < size; j++) {
            cout << "───";
            if (j < size - 1) cout << "┬";
        }
        cout << "┐" << endl;
        
        // Rows
        for (int i = 0; i < size; i++) {
            cout << i << " │";
            for (int j = 0; j < size; j++) {
                char cell = board.getCell(i, j);
                cout << " " << (cell == ' ' ? '·' : cell) << " ";
                if (j < size - 1) cout << "│";
            }
            cout << "│" << endl;
            
            // Row separator
            if (i < size - 1) {
                cout << "  ├";
                for (int j = 0; j < size; j++) {
                    cout << "───";
                    if (j < size - 1) cout << "┼";
                }
                cout << "┤" << endl;
            }
        }
        
        // Bottom border
        cout << "  └";
        for (int j = 0; j < size; j++) {
            cout << "───";
            if (j < size - 1) cout << "┴";
        }
        cout << "┘" << endl;
    }
    
    static void displayGameStats(const string& player1, const string& player2,
                               int score1, int score2, int games) {
        cout << "\n╔══════════ GAME STATISTICS ══════════╗" << endl;
        cout << "║ Player 1: " << setw(20) << left << player1 
             << " Score: " << setw(3) << score1 << " ║" << endl;
        cout << "║ Player 2: " << setw(20) << left << player2 
             << " Score: " << setw(3) << score2 << " ║" << endl;
        cout << "║ Total Games: " << setw(25) << games << " ║" << endl;
        cout << "╚══════════════════════════════════════╝" << endl;
    }
    
    static void displayMoveHistory(const vector<pair<int, int>>& moves,
                                 const vector<char>& players) {
        cout << "\n═══ MOVE HISTORY ═══" << endl;
        for (size_t i = 0; i < moves.size(); i++) {
            cout << setw(2) << (i + 1) << ". Player " << players[i] 
                 << " -> (" << moves[i].first << ", " << moves[i].second 
                 << ")" << endl;
        }
    }
    
    static string getPlayerInput(const string& prompt) {
        cout << prompt;
        string input;
        getline(cin, input);
        return input;
    }
    
    static void displayError(const string& message) {
        cout << "❌ Error: " << message << endl;
    }
    
    static void displaySuccess(const string& message) {
        cout << "✅ " << message << endl;
    }
    
    static void displayInfo(const string& message) {
        cout << "ℹ️  " << message << endl;
    }
    
    static void clearScreen() {
        #ifdef _WIN32
            system("cls");
        #else
            system("clear");
        #endif
    }
    
    static void pause() {
        cout << "\nPress Enter to continue...";
        cin.ignore();
    }
};
```

## Bài tập thực hành

### Bài tập 1: Custom Vector Implementation
Implement custom `MyVector<T>` class với:
- Dynamic memory management
- Iterators
- STL-compatible interface
- Performance benchmarking vs std::vector

### Bài tập 2: Game Board Optimization
Optimize game board representation:
- Compare different storage methods
- Implement memory pooling
- Measure performance impact

### Bài tập 3: Command System
Create comprehensive command system:
- Complex command parsing
- Command history
- Macro commands
- Undo/redo functionality

### Bài tập 4: Save/Load System
Implement robust save/load:
- Multiple file formats (text, binary, JSON)
- Error handling
- Backward compatibility
- Compression

## Tài liệu tham khảo
- STL Containers: https://en.cppreference.com/w/cpp/container
- String Processing: https://en.cppreference.com/w/cpp/string
- Algorithms: https://en.cppreference.com/w/cpp/algorithm

## Bài tiếp theo
Lesson 2: Linked Lists and Trees - Tìm hiểu về dynamic data structures.
