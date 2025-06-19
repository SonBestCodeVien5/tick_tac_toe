# Week 3 - Lesson 3: Inheritance and Polymorphism

## Mục tiêu học tập
- Hiểu về tính kế thừa (Inheritance) trong OOP
- Nắm vững khái niệm đa hình (Polymorphism)
- Phân biệt virtual functions và pure virtual functions
- Áp dụng abstract classes trong thiết kế game

## 1. Inheritance (Tính kế thừa)

### 1.1 Khái niệm cơ bản
```cpp
// Base class
class GameObject {
protected:
    int x, y;
    char symbol;
    
public:
    GameObject(int x, int y, char symbol) : x(x), y(y), symbol(symbol) {}
    
    virtual void display() {
        cout << "GameObject at (" << x << ", " << y << "): " << symbol << endl;
    }
    
    virtual ~GameObject() {} // Virtual destructor
};

// Derived class
class Player : public GameObject {
private:
    string name;
    int score;
    
public:
    Player(int x, int y, char symbol, string name) 
        : GameObject(x, y, symbol), name(name), score(0) {}
    
    void display() override {
        cout << "Player " << name << " at (" << x << ", " << y << "): " << symbol;
        cout << " (Score: " << score << ")" << endl;
    }
    
    void increaseScore() { score++; }
    int getScore() const { return score; }
    string getName() const { return name; }
};
```

### 1.2 Types of Inheritance
```cpp
// Public inheritance
class HumanPlayer : public Player {
public:
    HumanPlayer(int x, int y, char symbol, string name) 
        : Player(x, y, symbol, name) {}
    
    void makeMove() {
        cout << "Human player " << getName() << " makes a move" << endl;
        // Logic for human input
    }
};

// Multiple inheritance (cẩn thận với diamond problem)
class Drawable {
public:
    virtual void draw() = 0;
};

class Movable {
public:
    virtual void move(int dx, int dy) = 0;
};

class GamePiece : public Drawable, public Movable {
private:
    int x, y;
    char symbol;
    
public:
    GamePiece(int x, int y, char symbol) : x(x), y(y), symbol(symbol) {}
    
    void draw() override {
        cout << symbol;
    }
    
    void move(int dx, int dy) override {
        x += dx;
        y += dy;
    }
};
```

## 2. Polymorphism (Tính đa hình)

### 2.1 Runtime Polymorphism
```cpp
// Virtual functions cho polymorphism
class GameStrategy {
public:
    virtual int evaluateMove(const vector<vector<char>>& board, int row, int col) = 0;
    virtual string getStrategyName() = 0;
    virtual ~GameStrategy() {}
};

class EasyStrategy : public GameStrategy {
public:
    int evaluateMove(const vector<vector<char>>& board, int row, int col) override {
        // Random strategy
        return rand() % 100;
    }
    
    string getStrategyName() override {
        return "Easy AI";
    }
};

class HardStrategy : public GameStrategy {
public:
    int evaluateMove(const vector<vector<char>>& board, int row, int col) override {
        // Minimax algorithm implementation
        return minimax(board, row, col, true, 0);
    }
    
    string getStrategyName() override {
        return "Hard AI (Minimax)";
    }
    
private:
    int minimax(const vector<vector<char>>& board, int row, int col, bool isMax, int depth) {
        // Minimax implementation (sẽ học ở week 7)
        return 0;
    }
};

// Sử dụng polymorphism
class AIPlayer : public Player {
private:
    unique_ptr<GameStrategy> strategy;
    
public:
    AIPlayer(int x, int y, char symbol, string name, unique_ptr<GameStrategy> strat)
        : Player(x, y, symbol, name), strategy(move(strat)) {}
    
    void makeMove(vector<vector<char>>& board) {
        cout << "AI Player " << getName() << " (" << strategy->getStrategyName() 
             << ") is thinking..." << endl;
        
        int bestRow = -1, bestCol = -1, bestScore = -1000;
        
        for (int i = 0; i < board.size(); i++) {
            for (int j = 0; j < board[i].size(); j++) {
                if (board[i][j] == ' ') {
                    int score = strategy->evaluateMove(board, i, j);
                    if (score > bestScore) {
                        bestScore = score;
                        bestRow = i;
                        bestCol = j;
                    }
                }
            }
        }
        
        if (bestRow != -1 && bestCol != -1) {
            board[bestRow][bestCol] = symbol;
            cout << "AI places " << symbol << " at (" << bestRow << ", " << bestCol << ")" << endl;
        }
    }
};
```

### 2.2 Compile-time Polymorphism (Templates)
```cpp
template<typename T>
class GameBoard {
private:
    vector<vector<T>> board;
    int size;
    
public:
    GameBoard(int size) : size(size) {
        board.resize(size, vector<T>(size));
        clear();
    }
    
    void clear() {
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                board[i][j] = T{};
            }
        }
    }
    
    bool place(int row, int col, const T& piece) {
        if (row >= 0 && row < size && col >= 0 && col < size) {
            board[row][col] = piece;
            return true;
        }
        return false;
    }
    
    T get(int row, int col) const {
        if (row >= 0 && row < size && col >= 0 && col < size) {
            return board[row][col];
        }
        return T{};
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

// Sử dụng template
GameBoard<char> charBoard(3);  // Board với ký tự
GameBoard<int> intBoard(3);    // Board với số nguyên
```

## 3. Abstract Classes và Pure Virtual Functions

### 3.1 Abstract Base Class
```cpp
class GameMode {
public:
    // Pure virtual functions
    virtual void startGame() = 0;
    virtual bool checkWin() = 0;
    virtual bool checkDraw() = 0;
    virtual void endGame() = 0;
    
    // Virtual function with default implementation
    virtual void displayRules() {
        cout << "General game rules:" << endl;
        cout << "1. Players take turns" << endl;
        cout << "2. First to achieve winning condition wins" << endl;
    }
    
    // Non-virtual functions
    void initializeGame() {
        cout << "Initializing game..." << endl;
        startGame();
    }
    
    virtual ~GameMode() {}
};

class TicTacToeMode : public GameMode {
private:
    GameBoard<char> board;
    vector<unique_ptr<Player>> players;
    int currentPlayer;
    
public:
    TicTacToeMode() : board(3), currentPlayer(0) {
        players.push_back(make_unique<HumanPlayer>(0, 0, 'X', "Player 1"));
        players.push_back(make_unique<HumanPlayer>(0, 0, 'O', "Player 2"));
    }
    
    void startGame() override {
        cout << "Starting Tic Tac Toe game!" << endl;
        board.clear();
        displayRules();
    }
    
    bool checkWin() override {
        // Check rows, columns, diagonals
        for (int i = 0; i < 3; i++) {
            // Check rows
            if (board.get(i, 0) != ' ' && 
                board.get(i, 0) == board.get(i, 1) && 
                board.get(i, 1) == board.get(i, 2)) {
                return true;
            }
            
            // Check columns
            if (board.get(0, i) != ' ' && 
                board.get(0, i) == board.get(1, i) && 
                board.get(1, i) == board.get(2, i)) {
                return true;
            }
        }
        
        // Check diagonals
        if (board.get(0, 0) != ' ' && 
            board.get(0, 0) == board.get(1, 1) && 
            board.get(1, 1) == board.get(2, 2)) {
            return true;
        }
        
        if (board.get(0, 2) != ' ' && 
            board.get(0, 2) == board.get(1, 1) && 
            board.get(1, 1) == board.get(2, 0)) {
            return true;
        }
        
        return false;
    }
    
    bool checkDraw() override {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (board.get(i, j) == ' ') {
                    return false;
                }
            }
        }
        return !checkWin();
    }
    
    void endGame() override {
        if (checkWin()) {
            cout << "Player " << players[currentPlayer]->getName() << " wins!" << endl;
            players[currentPlayer]->increaseScore();
        } else if (checkDraw()) {
            cout << "It's a draw!" << endl;
        }
        
        cout << "Final scores:" << endl;
        for (auto& player : players) {
            cout << player->getName() << ": " << player->getScore() << endl;
        }
    }
    
    void displayRules() override {
        GameMode::displayRules(); // Call base class implementation
        cout << "Tic Tac Toe specific rules:" << endl;
        cout << "3. Get 3 in a row (horizontal, vertical, or diagonal) to win" << endl;
        cout << "4. Game is played on a 3x3 grid" << endl;
    }
};
```

## 4. Virtual Destructors và RAII

### 4.1 Virtual Destructors
```cpp
class Resource {
public:
    Resource() {
        cout << "Resource acquired" << endl;
    }
    
    // Virtual destructor để đảm bảo proper cleanup
    virtual ~Resource() {
        cout << "Resource released" << endl;
    }
};

class GameResource : public Resource {
private:
    int* data;
    
public:
    GameResource() : data(new int[1000]) {
        cout << "GameResource allocated" << endl;
    }
    
    ~GameResource() override {
        delete[] data;
        cout << "GameResource deallocated" << endl;
    }
};

// Sử dụng với smart pointers
void demonstrateVirtualDestructor() {
    unique_ptr<Resource> resource = make_unique<GameResource>();
    // Tự động cleanup khi ra khỏi scope
}
```

## 5. Ứng dụng trong Game Tic Tac Toe

### 5.1 Game Architecture với OOP
```cpp
class Game {
private:
    unique_ptr<GameMode> gameMode;
    bool isRunning;
    
public:
    Game() : isRunning(false) {}
    
    void setGameMode(unique_ptr<GameMode> mode) {
        gameMode = move(mode);
    }
    
    void run() {
        if (!gameMode) {
            cout << "No game mode set!" << endl;
            return;
        }
        
        isRunning = true;
        gameMode->initializeGame();
        
        while (isRunning) {
            // Game loop logic
            if (gameMode->checkWin() || gameMode->checkDraw()) {
                gameMode->endGame();
                break;
            }
        }
    }
    
    void stop() {
        isRunning = false;
    }
};

// Sử dụng
int main() {
    Game game;
    game.setGameMode(make_unique<TicTacToeMode>());
    game.run();
    
    return 0;
}
```

## Bài tập thực hành

### Bài tập 1: Tạo hierarchy Player
Tạo các class sau:
- `Player` (base class) với virtual functions
- `HumanPlayer` và `AIPlayer` (derived classes)
- Implement polymorphic behavior cho `makeMove()`

### Bài tập 2: Game Strategy Pattern
Implement Strategy pattern với:
- `GameStrategy` abstract class
- `RandomStrategy`, `MinimaxStrategy` concrete classes
- Test với different AI difficulties

### Bài tập 3: Template GameBoard
Tạo template `GameBoard<T>` có thể work với:
- `char` cho Tic Tac Toe truyền thống
- `int` cho numbered pieces
- Custom objects

### Bài tập 4: Abstract Game Framework
Tạo abstract `GameMode` class và implement:
- `TicTacToeMode`
- `ConnectFourMode` (bonus)

## Tài liệu tham khảo
- C++ Inheritance: https://en.cppreference.com/w/cpp/language/inheritance
- Virtual Functions: https://en.cppreference.com/w/cpp/language/virtual
- Polymorphism in C++: https://www.geeksforgeeks.org/polymorphism-in-c/
- Abstract Classes: https://www.tutorialspoint.com/cplusplus/cpp_interfaces.htm

## Bài tiếp theo
Lesson 4: Encapsulation and Access Control - Tìm hiểu về data hiding và access specifiers.
