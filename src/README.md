# Source Code Directory

Thư mục này chứa source code chính của game Tic Tac Toe.

## 📁 Cấu trúc dự kiến

```
src/
├── main.cpp              # Entry point
├── game_board.cpp        # Game board implementation
├── game_board.h          # Game board header
├── player.cpp            # Player class
├── player.h              # Player header
├── ai_player.cpp         # AI player implementation
├── ai_player.h           # AI player header
├── game_manager.cpp      # Game flow management
├── game_manager.h        # Game manager header
├── utils.cpp             # Utility functions
└── utils.h               # Utility headers
```

## 🚀 Development Timeline

- **Week 1-2**: Setup và basic C++ structure
- **Week 3**: OOP classes (Player, GameBoard)
- **Week 4**: Data structures integration
- **Week 5**: Core game implementation
- **Week 6**: Advanced features
- **Week 7**: AI implementation
- **Week 8**: Optimization và polish

## 🔧 Build Instructions

```bash
# Simple compilation (Week 1-4)
g++ -std=c++17 -o tic_tac_toe src/*.cpp

# With makefile (Week 5+)
make build

# With CMake (Advanced)
mkdir build && cd build
cmake ..
make
```

## 📝 Coding Standards

- **Style**: Follow Google C++ Style Guide
- **Naming**: camelCase for variables, PascalCase for classes
- **Comments**: Document all public methods
- **Headers**: Use header guards (#pragma once)
- **Memory**: Use smart pointers, avoid memory leaks

---

**Current Status**: Template ready, start development in Week 3!
