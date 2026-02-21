# 1. Minimum version of CMake required for the features used in this script
cmake_minimum_required(VERSION 3.20)

# 2. Project name and language (CXX for C++)
project(SDL3_Render3D LANGUAGES CXX)

# 3. Configure C++20 standards
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 4. Set compiler warnings based on the compiler being used
# MSVC is the Windows compiler; others (GCC/Clang) use standard flags
if (MSVC)
    add_compile_options(/W4)
else()
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# 5. CONFIGURE SDL3 DEPENDENCY
# We disable heavy extras (tests/examples) to keep the build time fast.
# 'add_subdirectory' tells CMake to look inside the /SDL folder for a CMakeLists.txt
# and treat it as part of this build tree.
set(SDL_TESTS OFF CACHE BOOL "" FORCE)
set(SDL_EXAMPLES OFF CACHE BOOL "" FORCE)
add_subdirectory(SDL) 

# 6. DEFINE THE EXECUTABLE
# List all source files (.cpp) and header files (.hpp).
# Including headers here allows IDEs like VS Code or CLion to track them properly.
add_executable(sdl3render
    src/main.cpp
    include/sdl3_renderer/Camera.hpp
    include/sdl3_renderer/IMeshLoader.hpp
    include/sdl3_renderer/IProjector.hpp
    include/sdl3_renderer/Mat4.hpp
    include/sdl3_renderer/Mesh.hpp
    include/sdl3_renderer/OBJMeshLoader.hpp
    include/sdl3_renderer/Renderer3D.hpp
    include/sdl3_renderer/Vec3.hpp
    include/sdl3_renderer/Projector.hpp
    include/sdl3_renderer/CameraController.hpp
    include/sdl3_renderer/GridRenderer.hpp
    include/sdl3_renderer/ModelTransformer.hpp
)

# 7. INCLUDE PATHS
# Tells the compiler to look in the /include folder when it sees #include "..."
target_include_directories(sdl3render PRIVATE
    ${CMAKE_SOURCE_DIR}/include
)

# 8. LINKING
# Links the compiled SDL3 library to your executable.
# The 'SDL3::SDL3' alias is created automatically by the SDL subdirectory build.
target_link_libraries(sdl3render PRIVATE SDL3::SDL3)

# 9. ASSET MANAGEMENT
# This command runs EVERY time you build. It copies your 3D models from the 
# source directory to the build directory so the executable can find them at runtime.
add_custom_command(TARGET sdl3render POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    ${CMAKE_SOURCE_DIR}/assets ${CMAKE_BINARY_DIR}/assets
)