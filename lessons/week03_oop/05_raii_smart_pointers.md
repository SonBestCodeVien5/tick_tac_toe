# Week 3 - Lesson 5: RAII and Smart Pointers

## Mục tiêu học tập
- Hiểu về RAII (Resource Acquisition Is Initialization)
- Nắm vững cách sử dụng Smart Pointers trong C++11/14/17
- Tránh memory leaks và dangling pointers
- Áp dụng modern C++ best practices trong game development

## 1. RAII (Resource Acquisition Is Initialization)

### 1.1 Khái niệm cơ bản
```cpp
// Bad practice - manual memory management
class BadGameManager {
private:
    int* scores;
    char** playerNames;
    GameBoard* board;
    
public:
    BadGameManager(int numPlayers) {
        scores = new int[numPlayers];
        playerNames = new char*[numPlayers];
        for (int i = 0; i < numPlayers; i++) {
            playerNames[i] = new char[50];
        }
        board = new GameBoard();
        
        // Khởi tạo values
        for (int i = 0; i < numPlayers; i++) {
            scores[i] = 0;
            strcpy(playerNames[i], "Player");
        }
    }
    
    ~BadGameManager() {
        // Phải nhớ cleanup manually - dễ quên!
        delete[] scores;
        for (int i = 0; i < numPlayers; i++) {
            delete[] playerNames[i];
        }
        delete[] playerNames;
        delete board;
    }
    
    // Nếu copy constructor/assignment không được implement properly
    // sẽ có double-delete problems!
};

// Good practice - RAII với smart pointers và containers
class GoodGameManager {
private:
    vector<int> scores;
    vector<string> playerNames;
    unique_ptr<GameBoard> board;
    
public:
    GoodGameManager(int numPlayers) 
        : scores(numPlayers, 0),
          playerNames(numPlayers, "Player"),
          board(make_unique<GameBoard>()) {
        
        // Tự động cleanup khi destructor được gọi
        // Không cần manual memory management
    }
    
    // Compiler tự generate proper destructor, copy constructor, etc.
    // hoặc ta có thể explicitly delete chúng nếu không muốn copying
    
    GoodGameManager(const GoodGameManager&) = delete;
    GoodGameManager& operator=(const GoodGameManager&) = delete;
    
    // Move semantics
    GoodGameManager(GoodGameManager&&) = default;
    GoodGameManager& operator=(GoodGameManager&&) = default;
};
```

### 1.2 RAII với Custom Resources
```cpp
class FileLogger {
private:
    FILE* logFile;
    string filename;
    
public:
    FileLogger(const string& fname) : filename(fname) {
        logFile = fopen(fname.c_str(), "a");
        if (!logFile) {
            throw runtime_error("Cannot open log file: " + fname);
        }
        
        // Log session start
        time_t now = time(0);
        fprintf(logFile, "\n=== Session started at %s", ctime(&now));
        fflush(logFile);
    }
    
    ~FileLogger() {
        if (logFile) {
            // Log session end
            time_t now = time(0);
            fprintf(logFile, "=== Session ended at %s\n", ctime(&now));
            fclose(logFile);
        }
    }
    
    // Disable copy, enable move
    FileLogger(const FileLogger&) = delete;
    FileLogger& operator=(const FileLogger&) = delete;
    
    FileLogger(FileLogger&& other) noexcept 
        : logFile(other.logFile), filename(move(other.filename)) {
        other.logFile = nullptr;
    }
    
    FileLogger& operator=(FileLogger&& other) noexcept {
        if (this != &other) {
            // Cleanup current resource
            if (logFile) {
                fclose(logFile);
            }
            
            // Move from other
            logFile = other.logFile;
            filename = move(other.filename);
            other.logFile = nullptr;
        }
        return *this;
    }
    
    void log(const string& message) {
        if (logFile) {
            time_t now = time(0);
            char* timeStr = ctime(&now);
            timeStr[strlen(timeStr) - 1] = '\0';  // Remove newline
            
            fprintf(logFile, "[%s] %s\n", timeStr, message.c_str());
            fflush(logFile);
        }
    }
    
    bool isOpen() const {
        return logFile != nullptr;
    }
};

// RAII wrapper cho game resources
class TextureResource {
private:
    string texturePath;
    int* textureData;  // Giả sử là texture data
    int width, height;
    
public:
    TextureResource(const string& path, int w, int h) 
        : texturePath(path), width(w), height(h) {
        
        textureData = new int[width * height];
        
        // Simulate loading texture
        cout << "Loading texture: " << path << endl;
        for (int i = 0; i < width * height; i++) {
            textureData[i] = rand() % 256;
        }
    }
    
    ~TextureResource() {
        cout << "Unloading texture: " << texturePath << endl;
        delete[] textureData;
    }
    
    // Move-only semantics
    TextureResource(const TextureResource&) = delete;
    TextureResource& operator=(const TextureResource&) = delete;
    
    TextureResource(TextureResource&& other) noexcept
        : texturePath(move(other.texturePath)),
          textureData(other.textureData),
          width(other.width),
          height(other.height) {
        other.textureData = nullptr;
    }
    
    TextureResource& operator=(TextureResource&& other) noexcept {
        if (this != &other) {
            delete[] textureData;
            
            texturePath = move(other.texturePath);
            textureData = other.textureData;
            width = other.width;
            height = other.height;
            
            other.textureData = nullptr;
        }
        return *this;
    }
    
    int getPixel(int x, int y) const {
        if (textureData && x >= 0 && x < width && y >= 0 && y < height) {
            return textureData[y * width + x];
        }
        return 0;
    }
    
    const string& getPath() const { return texturePath; }
    int getWidth() const { return width; }
    int getHeight() const { return height; }
};
```

## 2. Smart Pointers

### 2.1 unique_ptr
```cpp
#include <memory>

class GameState {
private:
    unique_ptr<GameBoard> board;
    unique_ptr<FileLogger> logger;
    vector<unique_ptr<Player>> players;
    
public:
    GameState() {
        // Create unique resources
        board = make_unique<GameBoard>(3);
        logger = make_unique<FileLogger>("game.log");
    }
    
    void addPlayer(const string& name, char symbol) {
        auto player = make_unique<HumanPlayer>(name, symbol);
        players.push_back(move(player));
        
        if (logger) {
            logger->log("Player added: " + name);
        }
    }
    
    void addAIPlayer(const string& name, char symbol, const string& difficulty) {
        auto strategy = createStrategy(difficulty);
        auto aiPlayer = make_unique<AIPlayer>(name, symbol, move(strategy));
        players.push_back(move(aiPlayer));
        
        if (logger) {
            logger->log("AI Player added: " + name + " (" + difficulty + ")");
        }
    }
    
    // Transfer ownership
    unique_ptr<Player> removePlayer(size_t index) {
        if (index < players.size()) {
            auto player = move(players[index]);
            players.erase(players.begin() + index);
            
            if (logger) {
                logger->log("Player removed: " + player->getName());
            }
            
            return player;  // Transfer ownership to caller
        }
        return nullptr;
    }
    
    GameBoard* getBoard() const {
        return board.get();  // Raw pointer for access, but ownership remains
    }
    
    Player* getPlayer(size_t index) const {
        if (index < players.size()) {
            return players[index].get();
        }
        return nullptr;
    }
    
    size_t getPlayerCount() const {
        return players.size();
    }
    
private:
    unique_ptr<GameStrategy> createStrategy(const string& difficulty) {
        if (difficulty == "Easy") {
            return make_unique<RandomStrategy>();
        } else if (difficulty == "Medium") {
            return make_unique<BasicStrategy>();
        } else if (difficulty == "Hard") {
            return make_unique<MinimaxStrategy>();
        }
        return make_unique<RandomStrategy>();
    }
};
```

### 2.2 shared_ptr
```cpp
class GameAssetManager {
private:
    unordered_map<string, shared_ptr<TextureResource>> textures;
    unordered_map<string, shared_ptr<SoundResource>> sounds;
    
public:
    shared_ptr<TextureResource> loadTexture(const string& name, const string& path) {
        // Check if already loaded
        auto it = textures.find(name);
        if (it != textures.end()) {
            cout << "Texture '" << name << "' already loaded, returning shared reference" << endl;
            return it->second;
        }
        
        // Load new texture
        auto texture = make_shared<TextureResource>(path, 64, 64);
        textures[name] = texture;
        
        cout << "Loaded new texture: " << name << " (ref count: " 
             << texture.use_count() << ")" << endl;
        
        return texture;
    }
    
    void unloadTexture(const string& name) {
        auto it = textures.find(name);
        if (it != textures.end()) {
            cout << "Unloading texture: " << name << " (ref count before: " 
                 << it->second.use_count() << ")" << endl;
            textures.erase(it);
        }
    }
    
    void printAssetStats() const {
        cout << "\n=== Asset Manager Stats ===" << endl;
        cout << "Loaded textures: " << textures.size() << endl;
        
        for (const auto& pair : textures) {
            cout << "  " << pair.first << " - ref count: " 
                 << pair.second.use_count() << endl;
        }
    }
};

class GameObject {
private:
    shared_ptr<TextureResource> texture;
    shared_ptr<GameAssetManager> assetManager;
    int x, y;
    
public:
    GameObject(shared_ptr<GameAssetManager> manager, const string& textureName, int posX, int posY)
        : assetManager(manager), x(posX), y(posY) {
        
        texture = assetManager->loadTexture(textureName, "assets/" + textureName + ".png");
    }
    
    void render() const {
        if (texture) {
            cout << "Rendering object at (" << x << ", " << y 
                 << ") with texture: " << texture->getPath() << endl;
        }
    }
    
    void moveTo(int newX, int newY) {
        x = newX;
        y = newY;
    }
    
    // Getter returns shared_ptr cho shared ownership
    shared_ptr<TextureResource> getTexture() const {
        return texture;
    }
};

// Sử dụng shared_ptr
void demonstrateSharedPtr() {
    auto assetManager = make_shared<GameAssetManager>();
    
    {
        GameObject obj1(assetManager, "player_x", 0, 0);
        GameObject obj2(assetManager, "player_x", 1, 1);  // Same texture
        GameObject obj3(assetManager, "player_o", 2, 2);  // Different texture
        
        assetManager->printAssetStats();
        
        obj1.render();
        obj2.render();
        obj3.render();
    }  // Objects destroyed here, but textures may still be referenced
    
    cout << "\nAfter objects destroyed:" << endl;
    assetManager->printAssetStats();
}
```

### 2.3 weak_ptr để tránh circular references
```cpp
class Player;
class GameMatch;

class Player {
private:
    string name;
    weak_ptr<GameMatch> currentMatch;  // weak_ptr để tránh circular reference
    vector<shared_ptr<GameMatch>> matchHistory;
    
public:
    Player(const string& playerName) : name(playerName) {}
    
    void joinMatch(shared_ptr<GameMatch> match) {
        currentMatch = match;
        matchHistory.push_back(match);
    }
    
    void leaveMatch() {
        currentMatch.reset();
    }
    
    bool isInMatch() const {
        return !currentMatch.expired();
    }
    
    shared_ptr<GameMatch> getCurrentMatch() const {
        return currentMatch.lock();  // Convert weak_ptr to shared_ptr safely
    }
    
    const string& getName() const { return name; }
    
    void printMatchHistory() const {
        cout << "Match history for " << name << ":" << endl;
        for (size_t i = 0; i < matchHistory.size(); i++) {
            if (auto match = matchHistory[i].lock()) {  // Check if still valid
                cout << "  Match " << i + 1 << ": " << match->getMatchId() << endl;
            } else {
                cout << "  Match " << i + 1 << ": [expired]" << endl;
            }
        }
    }
};

class GameMatch {
private:
    string matchId;
    vector<shared_ptr<Player>> players;
    bool isActive;
    
public:
    GameMatch(const string& id) : matchId(id), isActive(true) {}
    
    void addPlayer(shared_ptr<Player> player) {
        players.push_back(player);
        player->joinMatch(shared_from_this());  // Get shared_ptr to this
    }
    
    void removePlayer(shared_ptr<Player> player) {
        auto it = find(players.begin(), players.end(), player);
        if (it != players.end()) {
            (*it)->leaveMatch();
            players.erase(it);
        }
    }
    
    void endMatch() {
        isActive = false;
        for (auto& player : players) {
            player->leaveMatch();
        }
    }
    
    const string& getMatchId() const { return matchId; }
    bool getIsActive() const { return isActive; }
    size_t getPlayerCount() const { return players.size(); }
    
    void printPlayers() const {
        cout << "Players in match " << matchId << ":" << endl;
        for (const auto& player : players) {
            cout << "  " << player->getName() << endl;
        }
    }
};

// Enable shared_from_this
class GameMatch : public enable_shared_from_this<GameMatch> {
    // ... same implementation as above
};
```

## 3. Custom Smart Pointer và Resource Management

### 3.1 Custom Deleter
```cpp
// Custom deleter cho arrays
template<typename T>
struct ArrayDeleter {
    void operator()(T* ptr) const {
        cout << "Deleting array" << endl;
        delete[] ptr;
    }
};

// Custom deleter cho file handles
struct FileDeleter {
    void operator()(FILE* file) const {
        if (file) {
            cout << "Closing file" << endl;
            fclose(file);
        }
    }
};

class GameSaveManager {
private:
    unique_ptr<FILE, FileDeleter> saveFile;
    unique_ptr<int[], ArrayDeleter<int>> gameData;
    
public:
    GameSaveManager(const string& filename) {
        // Open file with custom deleter
        FILE* file = fopen(filename.c_str(), "wb");
        if (!file) {
            throw runtime_error("Cannot open save file: " + filename);
        }
        saveFile.reset(file);
        
        // Allocate array with custom deleter
        gameData = unique_ptr<int[], ArrayDeleter<int>>(new int[1000]);
    }
    
    void saveGameState(const vector<int>& state) {
        if (saveFile && !state.empty()) {
            copy(state.begin(), state.end(), gameData.get());
            fwrite(gameData.get(), sizeof(int), state.size(), saveFile.get());
            fflush(saveFile.get());
        }
    }
    
    vector<int> loadGameState(size_t count) {
        vector<int> state(count);
        if (saveFile) {
            fread(gameData.get(), sizeof(int), count, saveFile.get());
            copy(gameData.get(), gameData.get() + count, state.begin());
        }
        return state;
    }
};

// Lambda deleter
auto createTempBuffer(size_t size) {
    return unique_ptr<char[], function<void(char*)>>(
        new char[size],
        [](char* ptr) {
            cout << "Lambda deleter: cleaning up buffer" << endl;
            delete[] ptr;
        }
    );
}
```

### 3.2 Resource Pool với Smart Pointers
```cpp
template<typename T>
class ObjectPool {
private:
    queue<unique_ptr<T>> available;
    vector<unique_ptr<T>> all;
    size_t maxSize;
    
public:
    ObjectPool(size_t max) : maxSize(max) {}
    
    template<typename... Args>
    shared_ptr<T> acquire(Args&&... args) {
        unique_ptr<T> obj;
        
        if (!available.empty()) {
            obj = move(available.front());
            available.pop();
        } else if (all.size() < maxSize) {
            obj = make_unique<T>(forward<Args>(args)...);
            all.push_back(obj.get());  // Keep reference for cleanup
        } else {
            return nullptr;  // Pool exhausted
        }
        
        // Return shared_ptr với custom deleter để return object to pool
        return shared_ptr<T>(obj.release(), [this](T* ptr) {
            unique_ptr<T> returned(ptr);
            available.push(move(returned));
        });
    }
    
    size_t getAvailableCount() const {
        return available.size();
    }
    
    size_t getTotalCount() const {
        return all.size();
    }
};

// Usage
class Projectile {
private:
    int x, y;
    int velocityX, velocityY;
    bool active;
    
public:
    Projectile() : x(0), y(0), velocityX(0), velocityY(0), active(false) {}
    
    void initialize(int startX, int startY, int velX, int velY) {
        x = startX;
        y = startY;
        velocityX = velX;
        velocityY = velY;
        active = true;
    }
    
    void update() {
        if (active) {
            x += velocityX;
            y += velocityY;
        }
    }
    
    void deactivate() { active = false; }
    bool isActive() const { return active; }
    int getX() const { return x; }
    int getY() const { return y; }
};

void demonstrateObjectPool() {
    ObjectPool<Projectile> projectilePool(10);
    vector<shared_ptr<Projectile>> activeProjectiles;
    
    // Fire projectiles
    for (int i = 0; i < 5; i++) {
        auto projectile = projectilePool.acquire();
        if (projectile) {
            projectile->initialize(0, 0, i * 2, i);
            activeProjectiles.push_back(projectile);
        }
    }
    
    cout << "Active projectiles: " << activeProjectiles.size() << endl;
    cout << "Available in pool: " << projectilePool.getAvailableCount() << endl;
    
    // Projectiles automatically return to pool when shared_ptr is destroyed
    activeProjectiles.clear();
    
    cout << "After clearing - Available in pool: " << projectilePool.getAvailableCount() << endl;
}
```

## Bài tập thực hành

### Bài tập 1: RAII Game Resource Manager
Tạo class `GameResourceManager` sử dụng RAII để manage:
- File handles
- Memory allocations
- Network connections (simulate)

### Bài tập 2: Smart Pointer Game Engine
Implement game engine core với:
- `unique_ptr` cho single-owner resources
- `shared_ptr` cho shared assets
- `weak_ptr` cho observer patterns

### Bài tập 3: Memory-Safe Game Board
Tạo `GameBoard` class hoàn toàn memory-safe:
- Không raw pointers
- Exception-safe operations
- Move semantics

### Bài tập 4: Custom Smart Pointer
Implement custom smart pointer `game_ptr<T>` với:
- Reference counting
- Custom deleters
- Debug tracking

## Best Practices

1. **Prefer stack allocation** over dynamic allocation
2. **Use smart pointers** thay vì raw pointers
3. **Follow Rule of Zero/Three/Five**
4. **Make resources non-copyable** nếu không cần
5. **Use move semantics** cho performance
6. **RAII cho tất cả resources** (memory, files, locks, etc.)

## Tài liệu tham khảo
- Smart Pointers: https://en.cppreference.com/w/cpp/memory
- RAII: https://en.cppreference.com/w/cpp/language/raii
- Move Semantics: https://en.cppreference.com/w/cpp/language/move_constructor

## Bài tiếp theo
Week 4: Data Structures & Algorithms - Tìm hiểu về các cấu trúc dữ liệu cần thiết cho game development.
