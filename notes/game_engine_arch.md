# Game Engine Architecture

Personal notes while reading Jason Gregory's Game Engine Architecture.

# Runtime Engine Architecture
-  2 parts: Tool Suite and Runtime Component
- Build in layers to avoid circular dependencies where lower level dependent on higher level

### Third Party Sdks
- Middleware and other software in use like OpenGL, Havok, Boost, etc.

### Data Structures + Algorithms
- Boost: power library in C++
- C++ Standard Library: (STL) std::vector, std::list

### Graphics
-  OpenGL: widley used, open source
- DirectX: microsoft, most games

### Collision/Physics
- Havok, PhysX, ODE

### Platform Independence Layer
- per game/engine to be able to run on multiple platforms.