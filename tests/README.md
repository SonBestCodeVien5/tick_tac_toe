# Test Directory

Thư mục này chứa unit tests và integration tests cho dự án.

## 📁 Cấu trúc test

```
tests/
├── unit/                 # Unit tests cho từng class
│   ├── test_game_board.cpp
│   ├── test_player.cpp
│   ├── test_ai_player.cpp
│   └── test_utils.cpp
├── integration/          # Integration tests
│   ├── test_game_flow.cpp
│   └── test_save_load.cpp
├── performance/          # Performance benchmarks
│   └── benchmark.cpp
└── test_main.cpp        # Test runner
```

## 🧪 Testing Framework

Sẽ sử dụng một trong các framework sau:
- **catch2** (recommended - header-only)
- **Google Test** (gtest)
- **Simple custom framework** (for learning)

## 🚀 Development Timeline

- **Week 1-2**: Setup test framework
- **Week 3**: Unit tests cho OOP classes
- **Week 4**: Data structure tests
- **Week 5-6**: Game logic tests
- **Week 7**: AI algorithm tests
- **Week 8**: Performance tests

## 🔧 Running Tests

```bash
# Compile tests
g++ -std=c++17 -o test_runner tests/*.cpp src/*.cpp

# Run all tests
./test_runner

# Run specific test category
./test_runner --filter="GameBoard*"

# With coverage (advanced)
g++ -std=c++17 --coverage -o test_runner tests/*.cpp src/*.cpp
./test_runner
gcov *.gcda
```

## 📊 Test Categories

### **Unit Tests**
- Test individual classes và methods
- Mock dependencies nếu cần
- Fast execution (< 1 second total)

### **Integration Tests**
- Test interaction between components
- Game flow từ start to finish
- Save/Load functionality

### **Performance Tests**
- Algorithm complexity verification
- Memory usage monitoring
- Benchmark different implementations

### **Regression Tests**
- Prevent bugs từ việc re-appear
- Run automatically trước mỗi commit
- Critical game scenarios

## 📝 Test Guidelines

### **Test Naming**
```cpp
TEST(ClassNameTest, MethodName_Scenario_ExpectedBehavior) {
    // Arrange
    GameBoard board(3);
    
    // Act
    bool result = board.makeMove(0, 0, 'X');
    
    // Assert
    EXPECT_TRUE(result);
    EXPECT_EQ(board.getCell(0, 0), 'X');
}
```

### **Test Organization**
- One test file per source file
- Group related tests trong test suites
- Use descriptive test names
- Keep tests independent

### **Mock Objects**
```cpp
class MockPlayer : public Player {
public:
    MOCK_METHOD(pair<int,int>, getMove, (const GameBoard& board), (override));
    MOCK_METHOD(string, getName, (), (const, override));
};
```

## 🎯 Coverage Goals

- **Week 3-4**: 60%+ code coverage
- **Week 5-6**: 80%+ code coverage
- **Week 7-8**: 90%+ code coverage
- **Critical paths**: 100% coverage

---

**Current Status**: Template ready, start implementing tests in Week 3!
