# Dependency Plan

## Required Stack

- C++20
- CMake
- OpenGL
- GLFW
- GLAD-style OpenGL loader in `ThirdParty/glad`
- GLM
- Dear ImGui docking
- Assimp
- Bullet Physics
- OpenAL Soft
- nlohmann/json

## Strategy

The CMake project is set up to fetch most upstream libraries from their official archives when a normal Windows C++ toolchain is available. The OpenGL loader is provided locally so the repository stays self-contained even on machines without Python-based GLAD generation.

## Tradeoffs

- Bullet, Assimp, and OpenAL are optional at configure time so the editor shell can still build while heavier integrations are being stabilized.
- The local GL loader only covers the OpenGL 3.3 calls currently used by the engine. It is intentionally small and can be replaced with full generated GLAD sources later without changing engine code.
