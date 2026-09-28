cmake_minimum_required(VERSION 3.16)
project(pathtracer CXX)

# Prefer C++17 (adjust if you need another)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# --- Embree 3/4 ---
# Using CONFIG forces CMake to look for the package config file (embreeConfig.cmake).
find_package(embree 4 CONFIG REQUIRED)

# --- OpenMP (optional but cleaner) ---
# AppleClang doesn't ship OpenMP. If you're on macOS, use Homebrew llvm and configure with:
#   -DCMAKE_C_COMPILER=$(brew --prefix llvm)/bin/clang
#   -DCMAKE_CXX_COMPILER=$(brew --prefix llvm)/bin/clang++
find_package(OpenMP)
if(OpenMP_CXX_FOUND)
  set(USE_OPENMP TRUE)
endif()

# Find shaders
file(GLOB_RECURSE SHADERS
  "${CMAKE_CURRENT_SOURCE_DIR}/*.vert"
  "${CMAKE_CURRENT_SOURCE_DIR}/*.frag"
)
source_group("Shaders" FILES ${SHADERS})

add_executable(${PROJECT_NAME}
  main.cpp
  Pathtracer.h
  Pathtracer.cpp
  sampling.h
  sampling.cpp
  HDRImage.h
  HDRImage.cpp
  embree.h
  embree.cpp
  material.h
  material.cpp
  ${SHADERS}
)

# Link Embree (handle both common exported target names)
if(TARGET embree)
  target_link_libraries(${PROJECT_NAME} PRIVATE labhelper embree)
elseif(TARGET Embree::Embree)
  target_link_libraries(${PROJECT_NAME} PRIVATE labhelper Embree::Embree)
else()
  message(FATAL_ERROR "Embree imported target not found. Set embree_DIR to the directory containing embreeConfig.cmake.")
endif()

# Add OpenMP if available
if(USE_OPENMP)
  target_link_libraries(${PROJECT_NAME} PRIVATE OpenMP::OpenMP_CXX)
endif()

# If you had this macro defined elsewhere, keep it:
config_build_output()

