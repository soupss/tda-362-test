cmake_minimum_required(VERSION 3.16)
project(lab1-rasterization CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_FIND_FRAMEWORK NEVER)

# Find GLEW (Homebrew provides a config package: glew-config.cmake)
find_package(GLEW CONFIG REQUIRED)

# OpenGL (on macOS this maps to the OpenGL.framework)
find_package(OpenGL REQUIRED)

find_package(SDL2 CONFIG REQUIRED)

# Find *all* shaders.
file(GLOB_RECURSE SHADERS
    "${CMAKE_CURRENT_SOURCE_DIR}/*.vert"
    "${CMAKE_CURRENT_SOURCE_DIR}/*.frag"
)
source_group("Shaders" FILES ${SHADERS})

add_executable(${PROJECT_NAME}
    lab1_main.cpp
    ${SHADERS}
)

# if header is at <repo>/labhelper/labhelper.h
target_include_directories(lab1-rasterization PRIVATE
  ${CMAKE_SOURCE_DIR}/labhelper
)

# Link against labhelper, GLEW, and OpenGL
target_link_libraries(${PROJECT_NAME}
    PRIVATE
        labhelper
        GLEW::GLEW
        OpenGL::GL
        SDL2::SDL2
)

set_target_properties(lab1-rasterization PROPERTIES
  MACOSX_RPATH ON
  BUILD_RPATH "/opt/homebrew/lib"
  INSTALL_RPATH "/opt/homebrew/lib"
)
