# Week 4 - Lesson 3: Hash Tables and Sets for Game Data Management

## Mục tiêu học tập
- Hiểu hash tables và hash functions
- Sử dụng std::unordered_map và std::unordered_set hiệu quả
- Implement custom hash tables cho game-specific needs
- Collision resolution và performance optimization

## 1. Hash Tables Fundamentals

### 1.1 Custom Hash Table Implementation
```cpp
#include <vector>
#include <functional>
#include <iostream>

template<typename Key, typename Value>
class GameHashTable {
private:
    struct HashNode {
        Key key;
        Value value;
        bool isOccupied;
        bool isDeleted;
        
        HashNode() : isOccupied(false), isDeleted(false) {}
        HashNode(const Key& k, const Value& v) 
            : key(k), value(v), isOccupied(true), isDeleted(false) {}
    };
    
    vector<HashNode> table;
    size_t tableSize;
    size_t numElements;
    size_t numDeleted;
    double maxLoadFactor;
    hash<Key> hasher;
    
public:
    GameHashTable(size_t initialSize = 16, double loadFactor = 0.75) 
        : tableSize(initialSize), numElements(0), numDeleted(0), 
          maxLoadFactor(loadFactor) {
        table.resize(tableSize);
    }
    
    void insert(const Key& key, const Value& value) {
        if (needsResize()) {
            resize();
        }
        
        size_t index = findSlot(key);
        
        if (!table[index].isOccupied || table[index].isDeleted) {
            if (table[index].isDeleted) {
                numDeleted--;
            } else {
                numElements++;
            }
        }
        
        table[index] = HashNode(key, value);
    }
    
    bool find(const Key& key, Value& value) const {
        size_t index = hash(key);
        size_t originalIndex = index;
        
        do {
            if (!table[index].isOccupied) {
                return false;  // Key not found
            }
            
            if (!table[index].isDeleted && table[index].key == key) {
                value = table[index].value;
                return true;
            }
            
            index = (index + 1) % tableSize;  // Linear probing
        } while (index != originalIndex);
        
        return false;
    }
    
    bool contains(const Key& key) const {
        Value dummy;
        return find(key, dummy);
    }
    
    bool remove(const Key& key) {
        size_t index = hash(key);
        size_t originalIndex = index;
        
        do {
            if (!table[index].isOccupied) {
                return false;  // Key not found
            }
            
            if (!table[index].isDeleted && table[index].key == key) {
                table[index].isDeleted = true;
                numDeleted++;
                return true;
            }
            
            index = (index + 1) % tableSize;
        } while (index != originalIndex);
        
        return false;
    }
    
    size_t size() const { return numElements; }
    bool empty() const { return numElements == 0; }
    double loadFactor() const { return static_cast<double>(numElements) / tableSize; }
    
    void clear() {
        table.clear();
        table.resize(tableSize);
        numElements = 0;
        numDeleted = 0;
    }
    
    // Statistics for performance analysis
    void printStats() const {
        cout << "Hash Table Statistics:" << endl;
        cout << "  Size: " << numElements << endl;
        cout << "  Capacity: " << tableSize << endl;
        cout << "  Load Factor: " << loadFactor() << endl;
        cout << "  Deleted Entries: " << numDeleted << endl;
        cout << "  Average Probe Length: " << getAverageProbeLength() << endl;
    }
    
private:
    size_t hash(const Key& key) const {
        return hasher(key) % tableSize;
    }
    
    size_t findSlot(const Key& key) {
        size_t index = hash(key);
        
        while (table[index].isOccupied && !table[index].isDeleted && 
               table[index].key != key) {
            index = (index + 1) % tableSize;
        }
        
        return index;
    }
    
    bool needsResize() const {
        return loadFactor() > maxLoadFactor || 
               static_cast<double>(numDeleted) / tableSize > 0.25;
    }
    
    void resize() {
        vector<HashNode> oldTable = move(table);
        size_t oldSize = tableSize;
        
        tableSize *= 2;
        table.clear();
        table.resize(tableSize);
        numElements = 0;
        numDeleted = 0;
        
        // Rehash all elements
        for (const auto& node : oldTable) {
            if (node.isOccupied && !node.isDeleted) {
                insert(node.key, node.value);
            }
        }
    }
    
    double getAverageProbeLength() const {
        if (numElements == 0) return 0.0;
        
        size_t totalProbes = 0;
        size_t count = 0;
        
        for (size_t i = 0; i < tableSize; i++) {
            if (table[i].isOccupied && !table[i].isDeleted) {
                size_t probes = 1;
                size_t index = hash(table[i].key);
                
                while (index != i) {
                    probes++;
                    index = (index + 1) % tableSize;
                }
                
                totalProbes += probes;
                count++;
            }
        }
        
        return static_cast<double>(totalProbes) / count;
    }
};
```

### 1.2 Custom Hash Functions for Game Objects
```cpp
// Hash function for game coordinates
struct CoordinateHash {
    size_t operator()(const pair<int, int>& coord) const {
        // Combine x and y coordinates into a single hash
        size_t h1 = hash<int>{}(coord.first);
        size_t h2 = hash<int>{}(coord.second);
        return h1 ^ (h2 << 1);  // XOR with shifted second hash
    }
};

// Hash function for game state
struct GameStateHash {
    size_t operator()(const vector<vector<char>>& board) const {
        size_t result = 0;
        size_t multiplier = 1;
        
        for (const auto& row : board) {
            for (char cell : row) {
                result += static_cast<size_t>(cell) * multiplier;
                multiplier *= 31;  // Prime number for better distribution
            }
        }
        
        return result;
    }
};

// Hash function for player information
struct PlayerHash {
    size_t operator()(const Player& player) const {
        size_t h1 = hash<string>{}(player.getName());
        size_t h2 = hash<char>{}(player.getSymbol());
        size_t h3 = hash<int>{}(player.getScore());
        
        return h1 ^ (h2 << 1) ^ (h3 << 2);
    }
};

// Example usage with custom hash functions
class GameDatabase {
private:
    unordered_map<pair<int, int>, char, CoordinateHash> boardCells;
    unordered_map<vector<vector<char>>, double, GameStateHash> stateEvaluations;
    unordered_set<Player, PlayerHash> registeredPlayers;
    
public:
    void setCellValue(int row, int col, char value) {
        boardCells[{row, col}] = value;
    }
    
    char getCellValue(int row, int col) const {
        auto it = boardCells.find({row, col});
        return (it != boardCells.end()) ? it->second : ' ';
    }
    
    void cacheStateEvaluation(const vector<vector<char>>& state, double evaluation) {
        stateEvaluations[state] = evaluation;
    }
    
    bool getCachedEvaluation(const vector<vector<char>>& state, double& evaluation) const {
        auto it = stateEvaluations.find(state);
        if (it != stateEvaluations.end()) {
            evaluation = it->second;
            return true;
        }
        return false;
    }
    
    void registerPlayer(const Player& player) {
        registeredPlayers.insert(player);
    }
    
    bool isPlayerRegistered(const Player& player) const {
        return registeredPlayers.find(player) != registeredPlayers.end();
    }
    
    void printStatistics() const {
        cout << "Game Database Statistics:" << endl;
        cout << "  Board cells cached: " << boardCells.size() << endl;
        cout << "  State evaluations cached: " << stateEvaluations.size() << endl;
        cout << "  Registered players: " << registeredPlayers.size() << endl;
    }
};
```

## 2. Game-Specific Hash Applications

### 2.1 Transposition Table for AI
```cpp
class TranspositionTable {
private:
    struct TTEntry {
        vector<vector<char>> position;
        double evaluation;
        int depth;
        enum class NodeType { EXACT, LOWER_BOUND, UPPER_BOUND } type;
        chrono::steady_clock::time_point timestamp;
        
        TTEntry() : evaluation(0.0), depth(0), type(NodeType::EXACT) {
            timestamp = chrono::steady_clock::now();
        }
        
        TTEntry(const vector<vector<char>>& pos, double eval, int d, NodeType t)
            : position(pos), evaluation(eval), depth(d), type(t) {
            timestamp = chrono::steady_clock::now();
        }
        
        bool isValid() const {
            auto now = chrono::steady_clock::now();
            auto age = chrono::duration_cast<chrono::seconds>(now - timestamp);
            return age.count() < 300;  // Valid for 5 minutes
        }
    };
    
    GameHashTable<vector<vector<char>>, TTEntry> table;
    size_t hits;
    size_t misses;
    size_t collisions;
    
public:
    TranspositionTable(size_t size = 1024) : table(size), hits(0), misses(0), collisions(0) {}
    
    bool lookup(const vector<vector<char>>& position, double& evaluation, 
               int depth, double alpha, double beta) {
        TTEntry entry;
        if (table.find(position, entry) && entry.isValid()) {
            if (entry.depth >= depth) {
                switch (entry.type) {
                    case TTEntry::NodeType::EXACT:
                        evaluation = entry.evaluation;
                        hits++;
                        return true;
                        
                    case TTEntry::NodeType::LOWER_BOUND:
                        if (entry.evaluation >= beta) {
                            evaluation = entry.evaluation;
                            hits++;
                            return true;
                        }
                        break;
                        
                    case TTEntry::NodeType::UPPER_BOUND:
                        if (entry.evaluation <= alpha) {
                            evaluation = entry.evaluation;
                            hits++;
                            return true;
                        }
                        break;
                }
            }
        }
        
        misses++;
        return false;
    }
    
    void store(const vector<vector<char>>& position, double evaluation, 
              int depth, double alpha, double beta) {
        TTEntry::NodeType type;
        
        if (evaluation <= alpha) {
            type = TTEntry::NodeType::UPPER_BOUND;
        } else if (evaluation >= beta) {
            type = TTEntry::NodeType::LOWER_BOUND;
        } else {
            type = TTEntry::NodeType::EXACT;
        }
        
        TTEntry existing;
        if (table.find(position, existing)) {
            if (existing.position != position) {
                collisions++;
            }
        }
        
        table.insert(position, TTEntry(position, evaluation, depth, type));
    }
    
    void clear() {
        table.clear();
        hits = misses = collisions = 0;
    }
    
    double getHitRate() const {
        size_t total = hits + misses;
        return total > 0 ? static_cast<double>(hits) / total : 0.0;
    }
    
    void printStatistics() const {
        cout << "Transposition Table Statistics:" << endl;
        cout << "  Entries: " << table.size() << endl;
        cout << "  Hits: " << hits << endl;
        cout << "  Misses: " << misses << endl;
        cout << "  Collisions: " << collisions << endl;
        cout << "  Hit Rate: " << (getHitRate() * 100) << "%" << endl;
    }
};

// Enhanced AI with transposition table
class TranspositionAI {
private:
    TranspositionTable ttable;
    int maxDepth;
    long long nodesEvaluated;
    
public:
    TranspositionAI(int depth = 9, size_t tableSize = 2048) 
        : ttable(tableSize), maxDepth(depth), nodesEvaluated(0) {}
    
    pair<int, int> getBestMove(const GameBoard_Vector& board, char player) {
        nodesEvaluated = 0;
        
        // Convert board to vector format for hashing
        vector<vector<char>> position = boardToVector(board);
        
        double bestScore = minimaxWithTT(position, maxDepth, true, player, -1.0, 1.0);
        
        // Find the move that leads to the best score
        int size = board.getSize();
        pair<int, int> bestMove(-1, -1);
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (board.getCell(i, j) == ' ') {
                    vector<vector<char>> newPosition = position;
                    newPosition[i][j] = player;
                    
                    char nextPlayer = (player == 'X') ? 'O' : 'X';
                    double score = minimaxWithTT(newPosition, maxDepth - 1, false, 
                                               nextPlayer, -1.0, 1.0);
                    
                    if (abs(score - bestScore) < 1e-9) {
                        bestMove = {i, j};
                        break;
                    }
                }
            }
            if (bestMove.first != -1) break;
        }
        
        cout << "Nodes evaluated: " << nodesEvaluated << endl;
        ttable.printStatistics();
        
        return bestMove;
    }
    
private:
    double minimaxWithTT(const vector<vector<char>>& position, int depth, 
                        bool isMaximizing, char player, double alpha, double beta) {
        nodesEvaluated++;
        
        // Try to get result from transposition table
        double ttResult;
        if (ttable.lookup(position, ttResult, depth, alpha, beta)) {
            return ttResult;
        }
        
        // Check for terminal states
        GameState state(vectorToBoard(position), player);
        if (depth == 0 || state.isTerminal) {
            double result = state.evaluation;
            ttable.store(position, result, depth, alpha, beta);
            return result;
        }
        
        // Generate and evaluate moves
        double bestValue = isMaximizing ? -1.0 : 1.0;
        
        int size = position.size();
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (position[i][j] == ' ') {
                    vector<vector<char>> newPosition = position;
                    newPosition[i][j] = player;
                    
                    char nextPlayer = (player == 'X') ? 'O' : 'X';
                    double value = minimaxWithTT(newPosition, depth - 1, 
                                               !isMaximizing, nextPlayer, alpha, beta);
                    
                    if (isMaximizing) {
                        bestValue = max(bestValue, value);
                        alpha = max(alpha, bestValue);
                    } else {
                        bestValue = min(bestValue, value);
                        beta = min(beta, bestValue);
                    }
                    
                    if (beta <= alpha) {
                        break;  // Alpha-beta pruning
                    }
                }
            }
            if (beta <= alpha) break;
        }
        
        // Store result in transposition table
        ttable.store(position, bestValue, depth, alpha, beta);
        
        return bestValue;
    }
    
    vector<vector<char>> boardToVector(const GameBoard_Vector& board) const {
        int size = board.getSize();
        vector<vector<char>> result(size, vector<char>(size));
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                result[i][j] = board.getCell(i, j);
            }
        }
        
        return result;
    }
    
    GameBoard_Vector vectorToBoard(const vector<vector<char>>& position) const {
        int size = position.size();
        GameBoard_Vector board(size);
        
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                board.setCell(i, j, position[i][j]);
            }
        }
        
        return board;
    }
};
```

### 2.2 Player Statistics and Leaderboards
```cpp
struct PlayerStats {
    string playerName;
    int gamesPlayed;
    int gamesWon;
    int gamesLost;
    int gamesDraw;
    double totalPlayTime;  // in seconds
    chrono::steady_clock::time_point lastPlayed;
    vector<string> recentOpponents;
    
    PlayerStats(const string& name = "") 
        : playerName(name), gamesPlayed(0), gamesWon(0), 
          gamesLost(0), gamesDraw(0), totalPlayTime(0.0) {
        lastPlayed = chrono::steady_clock::now();
    }
    
    double getWinRate() const {
        return gamesPlayed > 0 ? static_cast<double>(gamesWon) / gamesPlayed : 0.0;
    }
    
    double getAverageGameTime() const {
        return gamesPlayed > 0 ? totalPlayTime / gamesPlayed : 0.0;
    }
    
    void recordGame(bool won, bool draw, double gameTime, const string& opponent) {
        gamesPlayed++;
        if (won) gamesWon++;
        else if (draw) gamesDraw++;
        else gamesLost++;
        
        totalPlayTime += gameTime;
        lastPlayed = chrono::steady_clock::now();
        
        recentOpponents.push_back(opponent);
        if (recentOpponents.size() > 10) {
            recentOpponents.erase(recentOpponents.begin());
        }
    }
    
    bool operator<(const PlayerStats& other) const {
        // Sort by win rate, then by games played
        double winRate = getWinRate();
        double otherWinRate = other.getWinRate();
        
        if (abs(winRate - otherWinRate) < 1e-9) {
            return gamesPlayed > other.gamesPlayed;
        }
        
        return winRate > otherWinRate;
    }
};

// Custom hash for PlayerStats
struct PlayerStatsHash {
    size_t operator()(const PlayerStats& stats) const {
        return hash<string>{}(stats.playerName);
    }
};

struct PlayerStatsEqual {
    bool operator()(const PlayerStats& a, const PlayerStats& b) const {
        return a.playerName == b.playerName;
    }
};

class PlayerDatabase {
private:
    unordered_set<PlayerStats, PlayerStatsHash, PlayerStatsEqual> players;
    unordered_map<string, string> playerAliases;  // nickname -> real name mapping
    
public:
    void registerPlayer(const string& playerName) {
        PlayerStats newPlayer(playerName);
        players.insert(newPlayer);
    }
    
    void addAlias(const string& alias, const string& realName) {
        playerAliases[alias] = realName;
    }
    
    string resolvePlayerName(const string& name) const {
        auto it = playerAliases.find(name);
        return (it != playerAliases.end()) ? it->second : name;
    }
    
    bool recordGameResult(const string& player1, const string& player2, 
                         int winner, double gameTime) {
        string realName1 = resolvePlayerName(player1);
        string realName2 = resolvePlayerName(player2);
        
        auto it1 = players.find(PlayerStats(realName1));
        auto it2 = players.find(PlayerStats(realName2));
        
        if (it1 == players.end() || it2 == players.end()) {
            return false;  // Player not found
        }
        
        // Update statistics (need to remove and re-insert due to const nature)
        PlayerStats stats1 = *it1;
        PlayerStats stats2 = *it2;
        
        players.erase(it1);
        players.erase(it2);
        
        bool draw = (winner == 0);
        stats1.recordGame(winner == 1, draw, gameTime, realName2);
        stats2.recordGame(winner == 2, draw, gameTime, realName1);
        
        players.insert(stats1);
        players.insert(stats2);
        
        return true;
    }
    
    vector<PlayerStats> getLeaderboard(size_t maxCount = 10) const {
        vector<PlayerStats> leaderboard(players.begin(), players.end());
        
        sort(leaderboard.begin(), leaderboard.end());
        
        if (leaderboard.size() > maxCount) {
            leaderboard.resize(maxCount);
        }
        
        return leaderboard;
    }
    
    bool getPlayerStats(const string& playerName, PlayerStats& stats) const {
        string realName = resolvePlayerName(playerName);
        auto it = players.find(PlayerStats(realName));
        
        if (it != players.end()) {
            stats = *it;
            return true;
        }
        
        return false;
    }
    
    void printLeaderboard() const {
        auto leaderboard = getLeaderboard();
        
        cout << "\n=== LEADERBOARD ===" << endl;
        cout << "Rank | Player Name       | Games | W-L-D | Win Rate | Avg Time" << endl;
        cout << "-----|-------------------|-------|-------|----------|----------" << endl;
        
        for (size_t i = 0; i < leaderboard.size(); i++) {
            const auto& stats = leaderboard[i];
            
            cout << setw(4) << (i + 1) << " | "
                 << setw(17) << left << stats.playerName << " | "
                 << setw(5) << stats.gamesPlayed << " | "
                 << stats.gamesWon << "-" << stats.gamesLost << "-" << stats.gamesDraw << " | "
                 << setw(7) << fixed << setprecision(1) << (stats.getWinRate() * 100) << "% | "
                 << setw(7) << fixed << setprecision(1) << stats.getAverageGameTime() << "s" << endl;
        }
    }
    
    void exportToCSV(const string& filename) const {
        ofstream file(filename);
        if (!file) return;
        
        file << "PlayerName,GamesPlayed,GamesWon,GamesLost,GamesDraw,WinRate,AvgGameTime,LastPlayed\n";
        
        for (const auto& stats : players) {
            auto lastPlayed = chrono::system_clock::to_time_t(
                chrono::system_clock::now() + 
                chrono::duration_cast<chrono::system_clock::duration>(
                    stats.lastPlayed - chrono::steady_clock::now()
                )
            );
            
            file << stats.playerName << ","
                 << stats.gamesPlayed << ","
                 << stats.gamesWon << ","
                 << stats.gamesLost << ","
                 << stats.gamesDraw << ","
                 << stats.getWinRate() << ","
                 << stats.getAverageGameTime() << ","
                 << lastPlayed << "\n";
        }
    }
    
    size_t getPlayerCount() const {
        return players.size();
    }
    
    void clearStats() {
        players.clear();
        playerAliases.clear();
    }
};
```

### 2.3 Game Configuration Management
```cpp
class GameConfigManager {
private:
    unordered_map<string, string> stringSettings;
    unordered_map<string, int> intSettings;
    unordered_map<string, double> doubleSettings;
    unordered_map<string, bool> boolSettings;
    
    static GameConfigManager* instance;
    static mutex instanceMutex;
    
    GameConfigManager() {
        loadDefaults();
    }
    
public:
    static GameConfigManager& getInstance() {
        lock_guard<mutex> lock(instanceMutex);
        if (!instance) {
            instance = new GameConfigManager();
        }
        return *instance;
    }
    
    // String settings
    void setString(const string& key, const string& value) {
        stringSettings[key] = value;
    }
    
    string getString(const string& key, const string& defaultValue = "") const {
        auto it = stringSettings.find(key);
        return (it != stringSettings.end()) ? it->second : defaultValue;
    }
    
    // Integer settings
    void setInt(const string& key, int value) {
        intSettings[key] = value;
    }
    
    int getInt(const string& key, int defaultValue = 0) const {
        auto it = intSettings.find(key);
        return (it != intSettings.end()) ? it->second : defaultValue;
    }
    
    // Double settings
    void setDouble(const string& key, double value) {
        doubleSettings[key] = value;
    }
    
    double getDouble(const string& key, double defaultValue = 0.0) const {
        auto it = doubleSettings.find(key);
        return (it != doubleSettings.end()) ? it->second : defaultValue;
    }
    
    // Boolean settings
    void setBool(const string& key, bool value) {
        boolSettings[key] = value;
    }
    
    bool getBool(const string& key, bool defaultValue = false) const {
        auto it = boolSettings.find(key);
        return (it != boolSettings.end()) ? it->second : defaultValue;
    }
    
    // File I/O
    bool loadFromFile(const string& filename) {
        ifstream file(filename);
        if (!file) return false;
        
        string line;
        while (getline(file, line)) {
            if (line.empty() || line[0] == '#') continue;  // Skip comments
            
            size_t equals = line.find('=');
            if (equals == string::npos) continue;
            
            string key = line.substr(0, equals);
            string value = line.substr(equals + 1);
            
            // Trim whitespace
            key.erase(0, key.find_first_not_of(" \t"));
            key.erase(key.find_last_not_of(" \t") + 1);
            value.erase(0, value.find_first_not_of(" \t"));
            value.erase(value.find_last_not_of(" \t") + 1);
            
            // Determine type and store
            if (value == "true" || value == "false") {
                setBool(key, value == "true");
            } else if (value.find('.') != string::npos) {
                try {
                    setDouble(key, stod(value));
                } catch (...) {
                    setString(key, value);
                }
            } else {
                try {
                    setInt(key, stoi(value));
                } catch (...) {
                    setString(key, value);
                }
            }
        }
        
        return true;
    }
    
    bool saveToFile(const string& filename) const {
        ofstream file(filename);
        if (!file) return false;
        
        file << "# Game Configuration File\n";
        file << "# Generated on " << getCurrentTimeString() << "\n\n";
        
        // Write string settings
        if (!stringSettings.empty()) {
            file << "# String Settings\n";
            for (const auto& pair : stringSettings) {
                file << pair.first << " = " << pair.second << "\n";
            }
            file << "\n";
        }
        
        // Write integer settings
        if (!intSettings.empty()) {
            file << "# Integer Settings\n";
            for (const auto& pair : intSettings) {
                file << pair.first << " = " << pair.second << "\n";
            }
            file << "\n";
        }
        
        // Write double settings
        if (!doubleSettings.empty()) {
            file << "# Double Settings\n";
            for (const auto& pair : doubleSettings) {
                file << pair.first << " = " << pair.second << "\n";
            }
            file << "\n";
        }
        
        // Write boolean settings
        if (!boolSettings.empty()) {
            file << "# Boolean Settings\n";
            for (const auto& pair : boolSettings) {
                file << pair.first << " = " << (pair.second ? "true" : "false") << "\n";
            }
            file << "\n";
        }
        
        return true;
    }
    
    void printAllSettings() const {
        cout << "=== Game Configuration ===" << endl;
        
        if (!stringSettings.empty()) {
            cout << "String Settings:" << endl;
            for (const auto& pair : stringSettings) {
                cout << "  " << pair.first << " = " << pair.second << endl;
            }
        }
        
        if (!intSettings.empty()) {
            cout << "Integer Settings:" << endl;
            for (const auto& pair : intSettings) {
                cout << "  " << pair.first << " = " << pair.second << endl;
            }
        }
        
        if (!doubleSettings.empty()) {
            cout << "Double Settings:" << endl;
            for (const auto& pair : doubleSettings) {
                cout << "  " << pair.first << " = " << pair.second << endl;
            }
        }
        
        if (!boolSettings.empty()) {
            cout << "Boolean Settings:" << endl;
            for (const auto& pair : boolSettings) {
                cout << "  " << pair.first << " = " << (pair.second ? "true" : "false") << endl;
            }
        }
    }
    
    void reset() {
        stringSettings.clear();
        intSettings.clear();
        doubleSettings.clear();
        boolSettings.clear();
        loadDefaults();
    }
    
private:
    void loadDefaults() {
        // Game settings
        setInt("board_size", 3);
        setInt("max_players", 2);
        setBool("enable_ai", true);
        setString("ai_difficulty", "Medium");
        setBool("show_coordinates", true);
        setBool("enable_sound", true);
        setBool("enable_animations", true);
        setDouble("animation_speed", 1.0);
        setInt("time_limit", 0);  // 0 = no limit
        
        // UI settings
        setString("theme", "Classic");
        setBool("fullscreen", false);
        setInt("window_width", 800);
        setInt("window_height", 600);
        
        // Performance settings
        setInt("transposition_table_size", 1024);
        setInt("max_search_depth", 9);
        setBool("enable_alpha_beta", true);
        setBool("enable_iterative_deepening", true);
    }
    
    string getCurrentTimeString() const {
        auto now = chrono::system_clock::now();
        auto time_t = chrono::system_clock::to_time_t(now);
        stringstream ss;
        ss << put_time(localtime(&time_t), "%Y-%m-%d %H:%M:%S");
        return ss.str();
    }
};

// Static member definitions
GameConfigManager* GameConfigManager::instance = nullptr;
mutex GameConfigManager::instanceMutex;
```

## Bài tập thực hành

### Bài tập 1: Custom Hash Table Optimization
Implement advanced hash table với:
- Robin Hood hashing
- Cuckoo hashing
- Performance comparison với std::unordered_map

### Bài tập 2: Game State Caching System
Tạo comprehensive caching system:
- Multi-level cache (L1, L2, L3)
- LRU eviction policy
- Cache statistics và monitoring

### Bài tập 3: Player Matchmaking System
Implement matchmaking dựa trên:
- Skill rating (ELO system)
- Play preferences
- Connection quality

### Bài tập 4: Configuration and Mod Support
Tạo flexible configuration system:
- Hot-reloading configs
- Plugin/mod support
- Configuration validation

## Performance Analysis

### Hash Table Performance Comparison
```cpp
void benchmarkHashTables() {
    const int OPERATIONS = 100000;
    
    // Test custom hash table
    auto start = chrono::high_resolution_clock::now();
    GameHashTable<int, string> customTable;
    for (int i = 0; i < OPERATIONS; i++) {
        customTable.insert(i, "value" + to_string(i));
    }
    auto end = chrono::high_resolution_clock::now();
    auto customTime = chrono::duration_cast<chrono::milliseconds>(end - start);
    
    // Test std::unordered_map
    start = chrono::high_resolution_clock::now();
    unordered_map<int, string> stdMap;
    for (int i = 0; i < OPERATIONS; i++) {
        stdMap[i] = "value" + to_string(i);
    }
    end = chrono::high_resolution_clock::now();
    auto stdTime = chrono::duration_cast<chrono::milliseconds>(end - start);
    
    cout << "Custom hash table: " << customTime.count() << "ms" << endl;
    cout << "std::unordered_map: " << stdTime.count() << "ms" << endl;
}
```

## Tài liệu tham khảo
- Hash Tables: https://en.cppreference.com/w/cpp/container/unordered_map
- Hash Functions: https://en.wikipedia.org/wiki/Hash_function
- Collision Resolution: https://en.wikipedia.org/wiki/Hash_table#Collision_resolution

## Bài tiếp theo
Lesson 4: Stacks, Queues, and Priority Queues - Tìm hiểu về linear data structures.
