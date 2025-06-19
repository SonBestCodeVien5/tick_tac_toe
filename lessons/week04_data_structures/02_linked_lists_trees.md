# Week 4 - Lesson 2: Linked Lists and Trees for Game Development

## Mục tiêu học tập
- Hiểu cấu trúc và operations của linked lists
- Áp dụng trees trong game AI và decision making
- Implement custom data structures cho game scenarios
- Performance analysis và use cases comparison

## 1. Linked Lists in Game Development

### 1.1 Basic Linked List Implementation
```cpp
template<typename T>
class GameLinkedList {
private:
    struct Node {
        T data;
        Node* next;
        
        Node(const T& value) : data(value), next(nullptr) {}
        Node(T&& value) : data(move(value)), next(nullptr) {}
    };
    
    Node* head;
    Node* tail;
    size_t size;
    
public:
    GameLinkedList() : head(nullptr), tail(nullptr), size(0) {}
    
    ~GameLinkedList() {
        clear();
    }
    
    // Copy constructor
    GameLinkedList(const GameLinkedList& other) : head(nullptr), tail(nullptr), size(0) {
        for (const T& item : other) {
            push_back(item);
        }
    }
    
    // Move constructor
    GameLinkedList(GameLinkedList&& other) noexcept
        : head(other.head), tail(other.tail), size(other.size) {
        other.head = other.tail = nullptr;
        other.size = 0;
    }
    
    // Assignment operators
    GameLinkedList& operator=(const GameLinkedList& other) {
        if (this != &other) {
            clear();
            for (const T& item : other) {
                push_back(item);
            }
        }
        return *this;
    }
    
    GameLinkedList& operator=(GameLinkedList&& other) noexcept {
        if (this != &other) {
            clear();
            head = other.head;
            tail = other.tail;
            size = other.size;
            other.head = other.tail = nullptr;
            other.size = 0;
        }
        return *this;
    }
    
    void push_front(const T& value) {
        Node* newNode = new Node(value);
        newNode->next = head;
        head = newNode;
        if (!tail) tail = head;
        size++;
    }
    
    void push_back(const T& value) {
        Node* newNode = new Node(value);
        if (tail) {
            tail->next = newNode;
            tail = newNode;
        } else {
            head = tail = newNode;
        }
        size++;
    }
    
    bool pop_front() {
        if (!head) return false;
        
        Node* temp = head;
        head = head->next;
        if (!head) tail = nullptr;
        delete temp;
        size--;
        return true;
    }
    
    bool pop_back() {
        if (!head) return false;
        
        if (head == tail) {
            delete head;
            head = tail = nullptr;
        } else {
            Node* current = head;
            while (current->next != tail) {
                current = current->next;
            }
            delete tail;
            tail = current;
            tail->next = nullptr;
        }
        size--;
        return true;
    }
    
    T& front() {
        if (!head) throw runtime_error("List is empty");
        return head->data;
    }
    
    const T& front() const {
        if (!head) throw runtime_error("List is empty");
        return head->data;
    }
    
    T& back() {
        if (!tail) throw runtime_error("List is empty");
        return tail->data;
    }
    
    const T& back() const {
        if (!tail) throw runtime_error("List is empty");
        return tail->data;
    }
    
    bool empty() const { return size == 0; }
    size_t getSize() const { return size; }
    
    void clear() {
        while (head) {
            Node* temp = head;
            head = head->next;
            delete temp;
        }
        tail = nullptr;
        size = 0;
    }
    
    // Iterator implementation
    class Iterator {
    private:
        Node* current;
        
    public:
        Iterator(Node* node) : current(node) {}
        
        T& operator*() { return current->data; }
        const T& operator*() const { return current->data; }
        
        Iterator& operator++() {
            if (current) current = current->next;
            return *this;
        }
        
        Iterator operator++(int) {
            Iterator temp = *this;
            ++(*this);
            return temp;
        }
        
        bool operator==(const Iterator& other) const {
            return current == other.current;
        }
        
        bool operator!=(const Iterator& other) const {
            return !(*this == other);
        }
    };
    
    Iterator begin() { return Iterator(head); }
    Iterator end() { return Iterator(nullptr); }
    
    const Iterator begin() const { return Iterator(head); }
    const Iterator end() const { return Iterator(nullptr); }
};
```

### 1.2 Game Event Queue với Linked List
```cpp
enum class GameEventType {
    PLAYER_MOVE,
    PLAYER_JOIN,
    PLAYER_LEAVE,
    GAME_START,
    GAME_END,
    ANIMATION_START,
    ANIMATION_END,
    SOUND_PLAY,
    SYSTEM_MESSAGE
};

struct GameEvent {
    GameEventType type;
    string playerId;
    unordered_map<string, string> parameters;
    chrono::steady_clock::time_point timestamp;
    int priority;  // Higher number = higher priority
    
    GameEvent(GameEventType t, const string& player = "", int prio = 0)
        : type(t), playerId(player), priority(prio) {
        timestamp = chrono::steady_clock::now();
    }
    
    bool operator<(const GameEvent& other) const {
        if (priority != other.priority) {
            return priority > other.priority;  // Higher priority first
        }
        return timestamp < other.timestamp;  // Earlier timestamp if same priority
    }
};

class GameEventQueue {
private:
    GameLinkedList<GameEvent> events;
    mutable mutex eventMutex;
    
public:
    void enqueue(const GameEvent& event) {
        lock_guard<mutex> lock(eventMutex);
        
        // Insert in priority order
        if (events.empty() || events.back() < event) {
            events.push_back(event);
            return;
        }
        
        // Find correct position for insertion
        GameLinkedList<GameEvent> tempList;
        bool inserted = false;
        
        for (const auto& existingEvent : events) {
            if (!inserted && event < existingEvent) {
                tempList.push_back(event);
                inserted = true;
            }
            tempList.push_back(existingEvent);
        }
        
        if (!inserted) {
            tempList.push_back(event);
        }
        
        events = move(tempList);
    }
    
    bool dequeue(GameEvent& event) {
        lock_guard<mutex> lock(eventMutex);
        if (events.empty()) return false;
        
        event = events.front();
        events.pop_front();
        return true;
    }
    
    bool peek(GameEvent& event) const {
        lock_guard<mutex> lock(eventMutex);
        if (events.empty()) return false;
        
        event = events.front();
        return true;
    }
    
    size_t size() const {
        lock_guard<mutex> lock(eventMutex);
        return events.getSize();
    }
    
    bool empty() const {
        lock_guard<mutex> lock(eventMutex);
        return events.empty();
    }
    
    void clear() {
        lock_guard<mutex> lock(eventMutex);
        events.clear();
    }
    
    void processEvents(function<void(const GameEvent&)> processor) {
        GameEvent event;
        while (dequeue(event)) {
            processor(event);
        }
    }
    
    void filterEvents(GameEventType type) {
        lock_guard<mutex> lock(eventMutex);
        GameLinkedList<GameEvent> filtered;
        
        for (const auto& event : events) {
            if (event.type != type) {
                filtered.push_back(event);
            }
        }
        
        events = move(filtered);
    }
};

// Usage example
class GameEventProcessor {
public:
    static void handleEvent(const GameEvent& event) {
        switch (event.type) {
            case GameEventType::PLAYER_MOVE:
                cout << "Processing player move from " << event.playerId << endl;
                break;
            case GameEventType::PLAYER_JOIN:
                cout << "Player " << event.playerId << " joined the game" << endl;
                break;
            case GameEventType::GAME_START:
                cout << "Game started!" << endl;
                break;
            case GameEventType::GAME_END:
                cout << "Game ended!" << endl;
                break;
            default:
                cout << "Unknown event type" << endl;
        }
    }
};
```

## 2. Trees for Game AI

### 2.1 Game Tree Implementation
```cpp
template<typename T>
class GameTree {
public:
    struct Node {
        T data;
        vector<unique_ptr<Node>> children;
        Node* parent;
        int depth;
        double score;  // For AI evaluation
        
        Node(const T& value, Node* p = nullptr) 
            : data(value), parent(p), depth(p ? p->depth + 1 : 0), score(0.0) {}
        
        void addChild(const T& value) {
            auto child = make_unique<Node>(value, this);
            children.push_back(move(child));
        }
        
        Node* getChild(size_t index) {
            return index < children.size() ? children[index].get() : nullptr;
        }
        
        const Node* getChild(size_t index) const {
            return index < children.size() ? children[index].get() : nullptr;
        }
        
        size_t getChildCount() const {
            return children.size();
        }
        
        bool isLeaf() const {
            return children.empty();
        }
        
        bool isRoot() const {
            return parent == nullptr;
        }
        
        vector<const Node*> getPath() const {
            vector<const Node*> path;
            const Node* current = this;
            while (current) {
                path.push_back(current);
                current = current->parent;
            }
            reverse(path.begin(), path.end());
            return path;
        }
    };
    
private:
    unique_ptr<Node> root;
    
public:
    GameTree() = default;
    
    GameTree(const T& rootValue) {
        root = make_unique<Node>(rootValue);
    }
    
    Node* getRoot() { return root.get(); }
    const Node* getRoot() const { return root.get(); }
    
    void setRoot(const T& value) {
        root = make_unique<Node>(value);
    }
    
    // Breadth-first traversal
    void traverseBFS(function<void(const Node&)> visitor) const {
        if (!root) return;
        
        queue<const Node*> q;
        q.push(root.get());
        
        while (!q.empty()) {
            const Node* current = q.front();
            q.pop();
            
            visitor(*current);
            
            for (const auto& child : current->children) {
                q.push(child.get());
            }
        }
    }
    
    // Depth-first traversal
    void traverseDFS(function<void(const Node&)> visitor) const {
        if (root) {
            traverseDFSHelper(root.get(), visitor);
        }
    }
    
    // Find path to node with specific value
    vector<const Node*> findPath(const T& value) const {
        vector<const Node*> path;
        if (root && findPathHelper(root.get(), value, path)) {
            return path;
        }
        return {};
    }
    
    // Get all leaf nodes
    vector<const Node*> getLeaves() const {
        vector<const Node*> leaves;
        if (root) {
            getLeavesHelper(root.get(), leaves);
        }
        return leaves;
    }
    
    // Calculate tree height
    int getHeight() const {
        return root ? getHeightHelper(root.get()) : 0;
    }
    
    // Count total nodes
    size_t getNodeCount() const {
        return root ? getNodeCountHelper(root.get()) : 0;
    }
    
private:
    void traverseDFSHelper(const Node* node, function<void(const Node&)> visitor) const {
        visitor(*node);
        for (const auto& child : node->children) {
            traverseDFSHelper(child.get(), visitor);
        }
    }
    
    bool findPathHelper(const Node* node, const T& value, vector<const Node*>& path) const {
        path.push_back(node);
        
        if (node->data == value) {
            return true;
        }
        
        for (const auto& child : node->children) {
            if (findPathHelper(child.get(), value, path)) {
                return true;
            }
        }
        
        path.pop_back();
        return false;
    }
    
    void getLeavesHelper(const Node* node, vector<const Node*>& leaves) const {
        if (node->isLeaf()) {
            leaves.push_back(node);
        } else {
            for (const auto& child : node->children) {
                getLeavesHelper(child.get(), leaves);
            }
        }
    }
    
    int getHeightHelper(const Node* node) const {
        if (node->isLeaf()) {
            return 1;
        }
        
        int maxChildHeight = 0;
        for (const auto& child : node->children) {
            maxChildHeight = max(maxChildHeight, getHeightHelper(child.get()));
        }
        
        return 1 + maxChildHeight;
    }
    
    size_t getNodeCountHelper(const Node* node) const {
        size_t count = 1;  // Count current node
        for (const auto& child : node->children) {
            count += getNodeCountHelper(child.get());
        }
        return count;
    }
};
```

### 2.2 Minimax Tree for Tic Tac Toe
```cpp
struct GameState {
    GameBoard_Vector board;
    char currentPlayer;
    bool isTerminal;
    double evaluation;
    
    GameState(const GameBoard_Vector& b, char player) 
        : board(b), currentPlayer(player), isTerminal(false), evaluation(0.0) {
        checkTerminal();
    }
    
    void checkTerminal() {
        // Check for win/loss/draw
        if (hasWinner() || board.hasEmptyCells() == false) {
            isTerminal = true;
            evaluation = evaluatePosition();
        }
    }
    
    bool hasWinner() const {
        // Check rows, columns, diagonals for win condition
        int size = board.getSize();
        
        // Check rows and columns
        for (int i = 0; i < size; i++) {
            if (checkLine(board.getRow(i)) || checkLine(board.getColumn(i))) {
                return true;
            }
        }
        
        // Check diagonals
        if (checkLine(board.getDiagonal(true)) || checkLine(board.getDiagonal(false))) {
            return true;
        }
        
        return false;
    }
    
    double evaluatePosition() const {
        if (!hasWinner()) return 0.0;  // Draw
        
        // Determine winner and return score
        char winner = getWinner();
        if (winner == 'X') return 1.0;
        if (winner == 'O') return -1.0;
        return 0.0;
    }
    
    char getWinner() const {
        int size = board.getSize();
        
        // Check rows and columns
        for (int i = 0; i < size; i++) {
            char rowWinner = getLineWinner(board.getRow(i));
            char colWinner = getLineWinner(board.getColumn(i));
            if (rowWinner != ' ') return rowWinner;
            if (colWinner != ' ') return colWinner;
        }
        
        // Check diagonals
        char diagWinner1 = getLineWinner(board.getDiagonal(true));
        char diagWinner2 = getLineWinner(board.getDiagonal(false));
        if (diagWinner1 != ' ') return diagWinner1;
        if (diagWinner2 != ' ') return diagWinner2;
        
        return ' ';
    }
    
    vector<GameState> generateSuccessors() const {
        vector<GameState> successors;
        if (isTerminal) return successors;
        
        int size = board.getSize();
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) == ' ') {
                    GameBoard_Vector newBoard = board;
                    newBoard.setCell(i, j, currentPlayer);
                    
                    char nextPlayer = (currentPlayer == 'X') ? 'O' : 'X';
                    successors.emplace_back(newBoard, nextPlayer);
                }
            }
        }
        
        return successors;
    }
    
    bool operator==(const GameState& other) const {
        if (currentPlayer != other.currentPlayer) return false;
        
        int size = board.getSize();
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) != other.board.getCell(i, j)) {
                    return false;
                }
            }
        }
        
        return true;
    }
    
private:
    bool checkLine(const vector<char>& line) const {
        if (line.empty()) return false;
        
        char first = line[0];
        if (first == ' ') return false;
        
        return all_of(line.begin(), line.end(), [first](char c) { return c == first; });
    }
    
    char getLineWinner(const vector<char>& line) const {
        if (checkLine(line)) {
            return line[0];
        }
        return ' ';
    }
};

class MinimaxAI {
private:
    int maxDepth;
    long long nodesEvaluated;
    GameTree<GameState> searchTree;
    
public:
    MinimaxAI(int depth = 9) : maxDepth(depth), nodesEvaluated(0) {}
    
    pair<int, int> getBestMove(const GameBoard_Vector& board, char player) {
        nodesEvaluated = 0;
        GameState initialState(board, player);
        
        // Build search tree
        searchTree.setRoot(initialState);
        double bestScore = minimax(searchTree.getRoot(), maxDepth, true, -1.0, 1.0);
        
        // Find the move that leads to best score
        auto successors = initialState.generateSuccessors();
        for (const auto& successor : successors) {
            // Find the move that created this successor
            pair<int, int> move = findDifference(board, successor.board);
            
            // Re-evaluate this specific move
            GameTree<GameState> tempTree(successor);
            double score = minimax(tempTree.getRoot(), maxDepth - 1, false, -1.0, 1.0);
            
            if (abs(score - bestScore) < 1e-9) {
                cout << "AI evaluated " << nodesEvaluated << " positions" << endl;
                cout << "Best move score: " << bestScore << endl;
                return move;
            }
        }
        
        // Fallback to first available move
        int size = board.getSize();
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) == ' ') {
                    return {i, j};
                }
            }
        }
        
        return {-1, -1};  // No valid moves
    }
    
    long long getNodesEvaluated() const {
        return nodesEvaluated;
    }
    
private:
    double minimax(GameTree<GameState>::Node* node, int depth, bool isMaximizing, 
                  double alpha, double beta) {
        nodesEvaluated++;
        
        const GameState& state = node->data;
        
        // Base cases
        if (depth == 0 || state.isTerminal) {
            return state.evaluation;
        }
        
        // Generate children if not already done
        if (node->getChildCount() == 0) {
            auto successors = state.generateSuccessors();
            for (const auto& successor : successors) {
                node->addChild(successor);
            }
        }
        
        if (isMaximizing) {
            double maxEval = -1.0;
            for (size_t i = 0; i < node->getChildCount(); i++) {
                auto child = node->getChild(i);
                double eval = minimax(child, depth - 1, false, alpha, beta);
                maxEval = max(maxEval, eval);
                alpha = max(alpha, eval);
                
                if (beta <= alpha) {
                    break;  // Alpha-beta pruning
                }
            }
            node->score = maxEval;
            return maxEval;
        } else {
            double minEval = 1.0;
            for (size_t i = 0; i < node->getChildCount(); i++) {
                auto child = node->getChild(i);
                double eval = minimax(child, depth - 1, true, alpha, beta);
                minEval = min(minEval, eval);
                beta = min(beta, eval);
                
                if (beta <= alpha) {
                    break;  // Alpha-beta pruning
                }
            }
            node->score = minEval;
            return minEval;
        }
    }
    
    pair<int, int> findDifference(const GameBoard_Vector& before, 
                                const GameBoard_Vector& after) const {
        int size = before.getSize();
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (before.getCell(i, j) != after.getCell(i, j)) {
                    return {i, j};
                }
            }
        }
        return {-1, -1};
    }
};
```

### 2.3 Decision Trees for Game Logic
```cpp
enum class DecisionType {
    CONDITION,
    ACTION
};

struct DecisionNode {
    DecisionType type;
    string description;
    function<bool()> condition;  // For condition nodes
    function<void()> action;     // For action nodes
    
    DecisionNode(const string& desc, function<bool()> cond)
        : type(DecisionType::CONDITION), description(desc), condition(cond) {}
    
    DecisionNode(const string& desc, function<void()> act)
        : type(DecisionType::ACTION), description(desc), action(act) {}
};

class GameDecisionTree {
private:
    GameTree<DecisionNode> tree;
    
public:
    void buildAIDecisionTree(const GameBoard_Vector& board, char aiPlayer) {
        // Root: Analyze current situation
        tree.setRoot(DecisionNode("Analyze Game State", []() { return true; }));
        auto root = tree.getRoot();
        
        // Level 1: Check for immediate win
        root->addChild(DecisionNode("Can Win?", [&board, aiPlayer]() {
            return canWinInOneMove(board, aiPlayer);
        }));
        
        root->addChild(DecisionNode("Must Block?", [&board, aiPlayer]() {
            char opponent = (aiPlayer == 'X') ? 'O' : 'X';
            return canWinInOneMove(board, opponent);
        }));
        
        root->addChild(DecisionNode("Take Center?", [&board]() {
            int size = board.getSize();
            int center = size / 2;
            return board.getCell(center, center) == ' ';
        }));
        
        root->addChild(DecisionNode("Take Corner?", [&board]() {
            int size = board.getSize();
            return board.getCell(0, 0) == ' ' || 
                   board.getCell(0, size-1) == ' ' ||
                   board.getCell(size-1, 0) == ' ' || 
                   board.getCell(size-1, size-1) == ' ';
        }));
        
        root->addChild(DecisionNode("Take Random", []() { return true; }));
        
        // Add specific actions for each decision path
        buildActionNodes();
    }
    
    pair<int, int> executeDecision(const GameBoard_Vector& board, char aiPlayer) {
        pair<int, int> selectedMove(-1, -1);
        
        // Traverse the decision tree
        function<void(GameTree<DecisionNode>::Node*)> traverse = 
            [&](GameTree<DecisionNode>::Node* node) {
                if (!node) return;
                
                const DecisionNode& decision = node->data;
                
                if (decision.type == DecisionType::CONDITION) {
                    if (decision.condition()) {
                        cout << "Decision: " << decision.description << " -> YES" << endl;
                        // Take the first child (YES branch)
                        if (node->getChildCount() > 0) {
                            traverse(node->getChild(0));
                        }
                    } else {
                        cout << "Decision: " << decision.description << " -> NO" << endl;
                        // Continue to next sibling through parent
                    }
                } else if (decision.type == DecisionType::ACTION) {
                    cout << "Action: " << decision.description << endl;
                    decision.action();
                }
            };
        
        // Start traversal from root
        traverse(tree.getRoot());
        
        return selectedMove;
    }
    
    void visualizeTree() const {
        cout << "\n=== AI Decision Tree ===" << endl;
        tree.traverseDFS([](const DecisionNode& node) {
            string typeStr = (node.type == DecisionType::CONDITION) ? "[?]" : "[!]";
            cout << typeStr << " " << node.description << endl;
        });
    }
    
private:
    static bool canWinInOneMove(const GameBoard_Vector& board, char player) {
        int size = board.getSize();
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) == ' ') {
                    // Try the move
                    GameBoard_Vector tempBoard = board;
                    tempBoard.setCell(i, j, player);
                    
                    // Check if this creates a win
                    GameState tempState(tempBoard, player);
                    if (tempState.hasWinner() && tempState.getWinner() == player) {
                        return true;
                    }
                }
            }
        }
        
        return false;
    }
    
    void buildActionNodes() {
        // This would typically be more complex, building action nodes
        // for each decision branch with specific move selection logic
    }
};
```

## Bài tập thực hành

### Bài tập 1: Advanced Linked List
Implement doubly linked list với:
- Bidirectional iteration
- Efficient insertion/deletion at arbitrary positions
- Memory pool optimization

### Bài tập 2: Game Tree Analysis
Create tree-based game analyzer:
- Build complete game trees (for small boards)
- Analyze optimal play paths
- Calculate game complexity metrics

### Bài tập 3: Decision Tree AI
Implement comprehensive decision tree AI:
- Multiple difficulty levels
- Learning from past games
- Strategy adaptation

### Bài tập 4: Tree Visualization
Create ASCII tree visualization:
- Display game trees graphically
- Show evaluation scores
- Interactive tree exploration

## Performance Analysis

### Linked List vs Vector Comparison
```cpp
void comparePerformance() {
    const int SIZE = 10000;
    
    // Test insertion performance
    auto start = chrono::high_resolution_clock::now();
    vector<int> vec;
    for (int i = 0; i < SIZE; i++) {
        vec.insert(vec.begin(), i);  // Insert at front
    }
    auto end = chrono::high_resolution_clock::now();
    auto vectorTime = chrono::duration_cast<chrono::milliseconds>(end - start);
    
    start = chrono::high_resolution_clock::now();
    GameLinkedList<int> list;
    for (int i = 0; i < SIZE; i++) {
        list.push_front(i);  // Insert at front
    }
    end = chrono::high_resolution_clock::now();
    auto listTime = chrono::duration_cast<chrono::milliseconds>(end - start);
    
    cout << "Vector front insertion: " << vectorTime.count() << "ms" << endl;
    cout << "Linked list front insertion: " << listTime.count() << "ms" << endl;
}
```

## Tài liệu tham khảo
- Linked Lists: https://en.cppreference.com/w/cpp/container/forward_list
- Tree Algorithms: https://www.geeksforgeeks.org/binary-tree-data-structure/
- Minimax Algorithm: https://en.wikipedia.org/wiki/Minimax

## Bài tiếp theo
Lesson 3: Hash Tables and Sets - Tìm hiểu về hash-based data structures.
