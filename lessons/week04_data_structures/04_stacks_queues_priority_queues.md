# Week 4 - Lesson 4: Stacks, Queues, and Priority Queues for Game Development

## Mục tiêu học tập
- Hiểu và implement stacks, queues, và priority queues
- Áp dụng vào game state management và event handling
- Sử dụng STL containers hiệu quả (stack, queue, priority_queue)
- Implement custom containers khi cần thiết

## 1. Stacks for Game State Management

### 1.1 Custom Stack Implementation
```cpp
#include <vector>
#include <stdexcept>

template<typename T>
class GameStack {
private:
    vector<T> data;
    size_t maxSize;
    
public:
    GameStack(size_t max = 1000) : maxSize(max) {
        data.reserve(maxSize);
    }
    
    void push(const T& item) {
        if (data.size() >= maxSize) {
            throw overflow_error("Stack overflow - maximum size reached");
        }
        data.push_back(item);
    }
    
    void push(T&& item) {
        if (data.size() >= maxSize) {
            throw overflow_error("Stack overflow - maximum size reached");
        }
        data.push_back(move(item));
    }
    
    void pop() {
        if (empty()) {
            throw underflow_error("Stack underflow - cannot pop from empty stack");
        }
        data.pop_back();
    }
    
    T& top() {
        if (empty()) {
            throw underflow_error("Stack is empty - no top element");
        }
        return data.back();
    }
    
    const T& top() const {
        if (empty()) {
            throw underflow_error("Stack is empty - no top element");
        }
        return data.back();
    }
    
    bool empty() const {
        return data.empty();
    }
    
    size_t size() const {
        return data.size();
    }
    
    size_t capacity() const {
        return maxSize;
    }
    
    void clear() {
        data.clear();
    }
    
    // Iterator support
    typename vector<T>::iterator begin() { return data.begin(); }
    typename vector<T>::iterator end() { return data.end(); }
    typename vector<T>::const_iterator begin() const { return data.begin(); }
    typename vector<T>::const_iterator end() const { return data.end(); }
    
    // Debug utilities
    void printStack() const {
        cout << "Stack contents (top to bottom): ";
        for (auto it = data.rbegin(); it != data.rend(); ++it) {
            cout << *it << " ";
        }
        cout << endl;
    }
};
```

### 1.2 Undo/Redo System với Stacks
```cpp
struct GameMove {
    int row, col;
    char player;
    chrono::steady_clock::time_point timestamp;
    
    GameMove(int r, int c, char p) : row(r), col(c), player(p) {
        timestamp = chrono::steady_clock::now();
    }
    
    friend ostream& operator<<(ostream& os, const GameMove& move) {
        os << "(" << move.row << "," << move.col << "," << move.player << ")";
        return os;
    }
};

class UndoRedoManager {
private:
    GameStack<GameMove> undoStack;
    GameStack<GameMove> redoStack;
    GameBoard_Vector* board;
    size_t maxHistorySize;
    
public:
    UndoRedoManager(GameBoard_Vector* gameBoard, size_t maxHistory = 100) 
        : undoStack(maxHistory), redoStack(maxHistory), 
          board(gameBoard), maxHistorySize(maxHistory) {}
    
    void recordMove(int row, int col, char player) {
        // Clear redo stack when new move is made
        redoStack.clear();
        
        // Add move to undo stack
        try {
            undoStack.push(GameMove(row, col, player));
        } catch (const overflow_error&) {
            // If stack is full, remove oldest move
            vector<GameMove> temp;
            while (undoStack.size() > maxHistorySize - 1) {
                temp.push_back(undoStack.top());
                undoStack.pop();
            }
            
            undoStack.clear();
            for (auto it = temp.rbegin(); it != temp.rend(); ++it) {
                undoStack.push(*it);
            }
            undoStack.push(GameMove(row, col, player));
        }
    }
    
    bool canUndo() const {
        return !undoStack.empty();
    }
    
    bool canRedo() const {
        return !redoStack.empty();
    }
    
    bool undo() {
        if (!canUndo()) return false;
        
        GameMove lastMove = undoStack.top();
        undoStack.pop();
        
        // Remove move from board
        board->setCell(lastMove.row, lastMove.col, ' ');
        
        // Add to redo stack
        redoStack.push(lastMove);
        
        cout << "Undoing move: " << lastMove << endl;
        return true;
    }
    
    bool redo() {
        if (!canRedo()) return false;
        
        GameMove moveToRedo = redoStack.top();
        redoStack.pop();
        
        // Apply move to board
        board->setCell(moveToRedo.row, moveToRedo.col, moveToRedo.player);
        
        // Add back to undo stack
        undoStack.push(moveToRedo);
        
        cout << "Redoing move: " << moveToRedo << endl;
        return true;
    }
    
    void printHistory() const {
        cout << "\n=== MOVE HISTORY ===" << endl;
        cout << "Undo stack (" << undoStack.size() << " moves):" << endl;
        undoStack.printStack();
        
        cout << "Redo stack (" << redoStack.size() << " moves):" << endl;
        redoStack.printStack();
    }
    
    size_t getUndoCount() const { return undoStack.size(); }
    size_t getRedoCount() const { return redoStack.size(); }
    
    void clear() {
        undoStack.clear();
        redoStack.clear();
    }
    
    // Get move at specific position in history (0 = most recent)
    optional<GameMove> getMoveFromHistory(size_t index) const {
        if (index >= undoStack.size()) return nullopt;
        
        vector<GameMove> temp;
        size_t count = 0;
        
        // This is inefficient for large stacks, but demonstrates the concept
        GameStack<GameMove> tempStack = undoStack;
        while (!tempStack.empty() && count <= index) {
            temp.push_back(tempStack.top());
            tempStack.pop();
            count++;
        }
        
        if (index < temp.size()) {
            return temp[index];
        }
        
        return nullopt;
    }
};
```

## 2. Queues for Event Processing

### 2.1 Custom Queue Implementation
```cpp
template<typename T>
class GameQueue {
private:
    vector<T> data;
    size_t frontIndex;
    size_t rearIndex;
    size_t maxSize;
    size_t currentSize;
    
public:
    GameQueue(size_t max = 1000) 
        : maxSize(max), frontIndex(0), rearIndex(0), currentSize(0) {
        data.resize(maxSize);
    }
    
    void enqueue(const T& item) {
        if (currentSize >= maxSize) {
            throw overflow_error("Queue overflow - maximum size reached");
        }
        
        data[rearIndex] = item;
        rearIndex = (rearIndex + 1) % maxSize;
        currentSize++;
    }
    
    void enqueue(T&& item) {
        if (currentSize >= maxSize) {
            throw overflow_error("Queue overflow - maximum size reached");
        }
        
        data[rearIndex] = move(item);
        rearIndex = (rearIndex + 1) % maxSize;
        currentSize++;
    }
    
    void dequeue() {
        if (empty()) {
            throw underflow_error("Queue underflow - cannot dequeue from empty queue");
        }
        
        frontIndex = (frontIndex + 1) % maxSize;
        currentSize--;
    }
    
    T& front() {
        if (empty()) {
            throw underflow_error("Queue is empty - no front element");
        }
        return data[frontIndex];
    }
    
    const T& front() const {
        if (empty()) {
            throw underflow_error("Queue is empty - no front element");
        }
        return data[frontIndex];
    }
    
    T& back() {
        if (empty()) {
            throw underflow_error("Queue is empty - no back element");
        }
        size_t backIndex = (rearIndex - 1 + maxSize) % maxSize;
        return data[backIndex];
    }
    
    const T& back() const {
        if (empty()) {
            throw underflow_error("Queue is empty - no back element");
        }
        size_t backIndex = (rearIndex - 1 + maxSize) % maxSize;
        return data[backIndex];
    }
    
    bool empty() const {
        return currentSize == 0;
    }
    
    size_t size() const {
        return currentSize;
    }
    
    size_t capacity() const {
        return maxSize;
    }
    
    void clear() {
        frontIndex = rearIndex = currentSize = 0;
    }
    
    void printQueue() const {
        cout << "Queue contents (front to back): ";
        for (size_t i = 0; i < currentSize; i++) {
            size_t index = (frontIndex + i) % maxSize;
            cout << data[index] << " ";
        }
        cout << endl;
    }
};
```

### 2.2 Game Event Processing System
```cpp
enum class EventType {
    PLAYER_INPUT,
    AI_MOVE,
    ANIMATION,
    SOUND_EFFECT,
    UI_UPDATE,
    GAME_STATE_CHANGE,
    NETWORK_MESSAGE,
    TIMER_EXPIRED
};

struct GameEvent {
    EventType type;
    string data;
    int priority;
    chrono::steady_clock::time_point timestamp;
    function<void()> handler;
    
    GameEvent(EventType t, const string& d, int p = 0) 
        : type(t), data(d), priority(p) {
        timestamp = chrono::steady_clock::now();
    }
    
    GameEvent(EventType t, const string& d, function<void()> h, int p = 0)
        : type(t), data(d), priority(p), handler(h) {
        timestamp = chrono::steady_clock::now();
    }
    
    friend ostream& operator<<(ostream& os, const GameEvent& event) {
        os << "Event[" << static_cast<int>(event.type) << "]: " << event.data;
        return os;
    }
};

class EventProcessor {
private:
    GameQueue<GameEvent> eventQueue;
    unordered_map<EventType, function<void(const GameEvent&)>> eventHandlers;
    bool processing;
    mutex queueMutex;
    
public:
    EventProcessor(size_t maxEvents = 1000) : eventQueue(maxEvents), processing(false) {
        setupDefaultHandlers();
    }
    
    void enqueueEvent(const GameEvent& event) {
        lock_guard<mutex> lock(queueMutex);
        try {
            eventQueue.enqueue(event);
            cout << "Enqueued: " << event << endl;
        } catch (const overflow_error& e) {
            cout << "Event queue overflow: " << e.what() << endl;
        }
    }
    
    void processEvents() {
        lock_guard<mutex> lock(queueMutex);
        processing = true;
        
        while (!eventQueue.empty()) {
            GameEvent event = eventQueue.front();
            eventQueue.dequeue();
            
            // Execute custom handler if available
            if (event.handler) {
                event.handler();
            }
            
            // Execute registered handler
            auto it = eventHandlers.find(event.type);
            if (it != eventHandlers.end()) {
                it->second(event);
            } else {
                cout << "No handler for event type: " << static_cast<int>(event.type) << endl;
            }
        }
        
        processing = false;
    }
    
    void registerHandler(EventType type, function<void(const GameEvent&)> handler) {
        eventHandlers[type] = handler;
    }
    
    size_t getQueueSize() const {
        lock_guard<mutex> lock(queueMutex);
        return eventQueue.size();
    }
    
    bool isProcessing() const {
        return processing;
    }
    
    void clear() {
        lock_guard<mutex> lock(queueMutex);
        eventQueue.clear();
    }
    
private:
    void setupDefaultHandlers() {
        eventHandlers[EventType::PLAYER_INPUT] = [](const GameEvent& event) {
            cout << "Handling player input: " << event.data << endl;
        };
        
        eventHandlers[EventType::AI_MOVE] = [](const GameEvent& event) {
            cout << "Processing AI move: " << event.data << endl;
        };
        
        eventHandlers[EventType::ANIMATION] = [](const GameEvent& event) {
            cout << "Playing animation: " << event.data << endl;
        };
        
        eventHandlers[EventType::SOUND_EFFECT] = [](const GameEvent& event) {
            cout << "Playing sound: " << event.data << endl;
        };
        
        eventHandlers[EventType::UI_UPDATE] = [](const GameEvent& event) {
            cout << "Updating UI: " << event.data << endl;
        };
        
        eventHandlers[EventType::GAME_STATE_CHANGE] = [](const GameEvent& event) {
            cout << "Game state changed: " << event.data << endl;
        };
    }
};
```

## 3. Priority Queues for AI and Game Logic

### 3.1 Custom Priority Queue Implementation
```cpp
template<typename T, typename Compare = less<T>>
class GamePriorityQueue {
private:
    vector<T> heap;
    Compare comp;
    
    void heapifyUp(size_t index) {
        while (index > 0) {
            size_t parent = (index - 1) / 2;
            if (!comp(heap[index], heap[parent])) {
                break;
            }
            swap(heap[index], heap[parent]);
            index = parent;
        }
    }
    
    void heapifyDown(size_t index) {
        while (true) {
            size_t leftChild = 2 * index + 1;
            size_t rightChild = 2 * index + 2;
            size_t largest = index;
            
            if (leftChild < heap.size() && comp(heap[leftChild], heap[largest])) {
                largest = leftChild;
            }
            
            if (rightChild < heap.size() && comp(heap[rightChild], heap[largest])) {
                largest = rightChild;
            }
            
            if (largest == index) {
                break;
            }
            
            swap(heap[index], heap[largest]);
            index = largest;
        }
    }
    
public:
    GamePriorityQueue(const Compare& c = Compare()) : comp(c) {}
    
    void push(const T& item) {
        heap.push_back(item);
        heapifyUp(heap.size() - 1);
    }
    
    void push(T&& item) {
        heap.push_back(move(item));
        heapifyUp(heap.size() - 1);
    }
    
    void pop() {
        if (empty()) {
            throw underflow_error("Priority queue is empty");
        }
        
        heap[0] = heap.back();
        heap.pop_back();
        
        if (!heap.empty()) {
            heapifyDown(0);
        }
    }
    
    const T& top() const {
        if (empty()) {
            throw underflow_error("Priority queue is empty");
        }
        return heap[0];
    }
    
    bool empty() const {
        return heap.empty();
    }
    
    size_t size() const {
        return heap.size();
    }
    
    void clear() {
        heap.clear();
    }
    
    // Debug utilities
    void printHeap() const {
        cout << "Priority Queue contents: ";
        for (const auto& item : heap) {
            cout << item << " ";
        }
        cout << endl;
    }
    
    // Verify heap property (for debugging)
    bool isValidHeap() const {
        for (size_t i = 0; i < heap.size(); i++) {
            size_t leftChild = 2 * i + 1;
            size_t rightChild = 2 * i + 2;
            
            if (leftChild < heap.size() && comp(heap[leftChild], heap[i])) {
                return false;
            }
            
            if (rightChild < heap.size() && comp(heap[rightChild], heap[i])) {
                return false;
            }
        }
        return true;
    }
};
```

### 3.2 AI Move Evaluation với Priority Queue
```cpp
struct AIMove {
    int row, col;
    double score;
    int depth;
    string description;
    
    AIMove(int r, int c, double s, int d = 0, const string& desc = "")
        : row(r), col(c), score(s), depth(d), description(desc) {}
    
    // For max-heap (best moves first)
    bool operator<(const AIMove& other) const {
        if (abs(score - other.score) < 1e-9) {
            return depth > other.depth;  // Prefer shallower wins/deeper losses
        }
        return score < other.score;  // Higher score = better move
    }
    
    friend ostream& operator<<(ostream& os, const AIMove& move) {
        os << "Move(" << move.row << "," << move.col << ") Score:" << move.score 
           << " Depth:" << move.depth;
        if (!move.description.empty()) {
            os << " [" << move.description << "]";
        }
        return os;
    }
};

class PriorityAI {
private:
    GamePriorityQueue<AIMove> moveQueue;
    int maxDepth;
    long long evaluations;
    
public:
    PriorityAI(int depth = 6) : maxDepth(depth), evaluations(0) {}
    
    pair<int, int> getBestMove(const GameBoard_Vector& board, char aiPlayer) {
        evaluations = 0;
        moveQueue.clear();
        
        // Generate and evaluate all possible moves
        generateMoves(board, aiPlayer);
        
        if (moveQueue.empty()) {
            return {-1, -1};  // No valid moves
        }
        
        // Get the best move
        AIMove bestMove = moveQueue.top();
        
        cout << "AI evaluated " << evaluations << " positions" << endl;
        cout << "Best move found: " << bestMove << endl;
        
        return {bestMove.row, bestMove.col};
    }
    
    void printTopMoves(int count = 5) const {
        cout << "\nTop " << count << " moves:" << endl;
        
        GamePriorityQueue<AIMove> temp = moveQueue;
        int printed = 0;
        
        while (!temp.empty() && printed < count) {
            AIMove move = temp.top();
            temp.pop();
            cout << (printed + 1) << ". " << move << endl;
            printed++;
        }
    }
    
private:
    void generateMoves(const GameBoard_Vector& board, char player) {
        int size = board.getSize();
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) == ' ') {
                    double score = evaluateMove(board, i, j, player);
                    string description = getMoveDescription(board, i, j, player, score);
                    moveQueue.push(AIMove(i, j, score, 0, description));
                }
            }
        }
    }
    
    double evaluateMove(const GameBoard_Vector& board, int row, int col, char player) {
        evaluations++;
        
        // Create temporary board with the move
        GameBoard_Vector tempBoard = board;
        tempBoard.setCell(row, col, player);
        
        // Check for immediate win
        if (isWinningMove(tempBoard, row, col, player)) {
            return 1000.0;  // Winning move
        }
        
        // Check for blocking opponent win
        char opponent = (player == 'X') ? 'O' : 'X';
        tempBoard.setCell(row, col, opponent);
        if (isWinningMove(tempBoard, row, col, opponent)) {
            return 500.0;  // Blocking move
        }
        
        // Positional evaluation
        double score = 0.0;
        
        // Center preference
        int size = board.getSize();
        int center = size / 2;
        double centerDistance = sqrt(pow(row - center, 2) + pow(col - center, 2));
        score += (size - centerDistance) * 10;
        
        // Line potential evaluation
        score += evaluateLinesPotential(board, row, col, player);
        
        // Add some randomness for variety
        score += (rand() % 10) - 5;
        
        return score;
    }
    
    bool isWinningMove(const GameBoard_Vector& board, int row, int col, char player) {
        int size = board.getSize();
        
        // Check row
        int count = 1;
        for (int c = col - 1; c >= 0 && board.getCell(row, c) == player; c--) count++;
        for (int c = col + 1; c < size && board.getCell(row, c) == player; c++) count++;
        if (count >= size) return true;
        
        // Check column
        count = 1;
        for (int r = row - 1; r >= 0 && board.getCell(r, col) == player; r--) count++;
        for (int r = row + 1; r < size && board.getCell(r, col) == player; r++) count++;
        if (count >= size) return true;
        
        // Check main diagonal
        count = 1;
        for (int d = 1; row - d >= 0 && col - d >= 0 && board.getCell(row - d, col - d) == player; d++) count++;
        for (int d = 1; row + d < size && col + d < size && board.getCell(row + d, col + d) == player; d++) count++;
        if (count >= size) return true;
        
        // Check anti-diagonal
        count = 1;
        for (int d = 1; row - d >= 0 && col + d < size && board.getCell(row - d, col + d) == player; d++) count++;
        for (int d = 1; row + d < size && col - d >= 0 && board.getCell(row + d, col - d) == player; d++) count++;
        if (count >= size) return true;
        
        return false;
    }
    
    double evaluateLinesPotential(const GameBoard_Vector& board, int row, int col, char player) {
        double score = 0.0;
        int size = board.getSize();
        
        // Directions: horizontal, vertical, main diagonal, anti-diagonal
        vector<pair<int, int>> directions = {{0, 1}, {1, 0}, {1, 1}, {1, -1}};
        
        for (const auto& dir : directions) {
            int consecutive = 1;
            int spaces = 0;
            
            // Check in positive direction
            for (int d = 1; d < size; d++) {
                int newRow = row + dir.first * d;
                int newCol = col + dir.second * d;
                
                if (newRow < 0 || newRow >= size || newCol < 0 || newCol >= size) break;
                
                char cell = board.getCell(newRow, newCol);
                if (cell == player) {
                    consecutive++;
                } else if (cell == ' ') {
                    spaces++;
                    if (spaces > 1) break;
                } else {
                    break;  // Opponent piece blocks this direction
                }
            }
            
            // Check in negative direction
            for (int d = 1; d < size; d++) {
                int newRow = row - dir.first * d;
                int newCol = col - dir.second * d;
                
                if (newRow < 0 || newRow >= size || newCol < 0 || newCol >= size) break;
                
                char cell = board.getCell(newRow, newCol);
                if (cell == player) {
                    consecutive++;
                } else if (cell == ' ') {
                    spaces++;
                    if (spaces > 1) break;
                } else {
                    break;  // Opponent piece blocks this direction
                }
            }
            
            // Score based on consecutive pieces and available spaces
            if (consecutive >= 2) {
                score += consecutive * consecutive * 10;
            }
        }
        
        return score;
    }
    
    string getMoveDescription(const GameBoard_Vector& board, int row, int col, 
                            char player, double score) const {
        if (score >= 1000) return "WINNING MOVE";
        if (score >= 500) return "BLOCKING MOVE";
        if (score >= 100) return "STRONG POSITION";
        if (score >= 50) return "GOOD MOVE";
        if (score >= 0) return "DECENT MOVE";
        return "WEAK MOVE";
    }
};
```

## 4. STL Container Integration

### 4.1 Using STL Efficiently
```cpp
#include <stack>
#include <queue>
#include <priority_queue>
#include <deque>

class STLGameManager {
private:
    // Game state history using STL stack
    stack<GameBoard_Vector> gameStateHistory;
    
    // Event queue using STL queue
    queue<GameEvent> eventQueue;
    
    // AI moves using STL priority_queue
    priority_queue<AIMove> aiMoveQueue;
    
    // Player actions using deque (double-ended queue)
    deque<string> playerActions;
    
public:
    // Game state management
    void saveGameState(const GameBoard_Vector& board) {
        gameStateHistory.push(board);
        
        // Limit history size to prevent memory issues
        if (gameStateHistory.size() > 50) {
            // STL stack doesn't provide direct access to bottom,
            // so we need to transfer to temporary storage
            stack<GameBoard_Vector> temp;
            for (int i = 0; i < 49; i++) {
                temp.push(gameStateHistory.top());
                gameStateHistory.pop();
            }
            
            // Clear original stack and rebuild without oldest entry
            while (!gameStateHistory.empty()) {
                gameStateHistory.pop();
            }
            
            while (!temp.empty()) {
                gameStateHistory.push(temp.top());
                temp.pop();
            }
        }
    }
    
    bool restorePreviousState(GameBoard_Vector& board) {
        if (gameStateHistory.empty()) {
            return false;
        }
        
        board = gameStateHistory.top();
        gameStateHistory.pop();
        return true;
    }
    
    // Event processing
    void addEvent(const GameEvent& event) {
        eventQueue.push(event);
    }
    
    void processAllEvents() {
        while (!eventQueue.empty()) {
            GameEvent event = eventQueue.front();
            eventQueue.pop();
            
            cout << "Processing: " << event << endl;
            // Process event...
        }
    }
    
    // AI move management
    void addAIMove(const AIMove& move) {
        aiMoveQueue.push(move);
    }
    
    optional<AIMove> getBestAIMove() {
        if (aiMoveQueue.empty()) {
            return nullopt;
        }
        
        AIMove best = aiMoveQueue.top();
        aiMoveQueue.pop();
        return best;
    }
    
    void clearAIMoves() {
        while (!aiMoveQueue.empty()) {
            aiMoveQueue.pop();
        }
    }
    
    // Player action logging (using deque for efficient front/back operations)
    void logPlayerAction(const string& action) {
        playerActions.push_back(action);
        
        // Keep only last 20 actions
        if (playerActions.size() > 20) {
            playerActions.pop_front();
        }
    }
    
    void undoLastAction() {
        if (!playerActions.empty()) {
            string lastAction = playerActions.back();
            playerActions.pop_back();
            cout << "Undoing action: " << lastAction << endl;
        }
    }
    
    void printRecentActions(int count = 5) const {
        cout << "Recent actions:" << endl;
        int start = max(0, static_cast<int>(playerActions.size()) - count);
        
        for (int i = start; i < static_cast<int>(playerActions.size()); i++) {
            cout << (i + 1) << ". " << playerActions[i] << endl;
        }
    }
    
    // Statistics
    void printStatistics() const {
        cout << "\n=== Game Manager Statistics ===" << endl;
        cout << "Game states in history: " << gameStateHistory.size() << endl;
        cout << "Events in queue: " << eventQueue.size() << endl;
        cout << "AI moves in queue: " << aiMoveQueue.size() << endl;
        cout << "Player actions logged: " << playerActions.size() << endl;
    }
};
```

## Bài tập thực hành

### Bài tập 1: Enhanced Undo/Redo System
Implement advanced undo/redo với:
- Branching history (multiple undo paths)
- Compression for old states
- Metadata for each state change

### Bài tập 2: Priority-based Event System
Create sophisticated event system:
- Multiple priority levels
- Event dependencies
- Delayed event execution

### Bài tập 3: AI Move Ordering
Implement move ordering optimization:
- Historical move success rates
- Position-based heuristics
- Dynamic priority adjustment

### Bài tập 4: Memory-Efficient State Management
Design system for large game states:
- State compression
- Incremental state storage
- Smart garbage collection

## Performance Considerations

### Container Choice Guidelines
```cpp
void demonstrateContainerChoice() {
    cout << "=== Container Performance Guidelines ===" << endl;
    
    // Stack operations
    cout << "\nStack Usage:" << endl;
    cout << "- Game state history: O(1) push/pop" << endl;
    cout << "- Function call tracking: O(1) operations" << endl;
    cout << "- Undo/redo systems: Perfect for LIFO behavior" << endl;
    
    // Queue operations
    cout << "\nQueue Usage:" << endl;
    cout << "- Event processing: O(1) enqueue/dequeue" << endl;
    cout << "- BFS algorithms: Natural FIFO behavior" << endl;
    cout << "- Producer-consumer patterns: Thread-safe versions available" << endl;
    
    // Priority Queue operations
    cout << "\nPriority Queue Usage:" << endl;
    cout << "- AI move selection: O(log n) insert, O(log n) extract-max" << endl;
    cout << "- Dijkstra's algorithm: Efficient priority-based processing" << endl;
    cout << "- Task scheduling: Natural priority ordering" << endl;
    
    // Deque operations
    cout << "\nDeque Usage:" << endl;
    cout << "- Sliding window operations: O(1) front/back operations" << endl;
    cout << "- Double-ended queues: When you need both stack and queue behavior" << endl;
    cout << "- Cache implementations: LRU with efficient insertion/deletion" << endl;
}
```

## Tài liệu tham khảo
- STL Containers: https://en.cppreference.com/w/cpp/container
- Stack: https://en.cppreference.com/w/cpp/container/stack
- Queue: https://en.cppreference.com/w/cpp/container/queue
- Priority Queue: https://en.cppreference.com/w/cpp/container/priority_queue

## Bài tiếp theo
Lesson 5: Algorithm Analysis and Optimization Techniques - Tìm hiểu về Big O notation và optimization strategies.
