cmake_minimum_required(VERSION 3.15)

# Project configuration
project(SilkRoadAlice 
    VERSION 1.0.0
    LANGUAGES CXX
)

# Fetch SDL3
include(FetchContent)
FetchContent_Declare(
    SDL3
    GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
    GIT_TAG main
    GIT_SHALLOW TRUE
    GIT_PROGRESS TRUE
)
FetchContent_MakeAvailable(SDL3)

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# Include directories
include_directories(${PROJECT_SOURCE_DIR}/include)

# Automatically gather all source files
file(GLOB_RECURSE SOURCES 
    "${PROJECT_SOURCE_DIR}/src/*.cpp"
)

# Automatically gather all header files
file(GLOB_RECURSE HEADERS 
    "${PROJECT_SOURCE_DIR}/include/*.h"
)

# Create executable
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

# Link SDL3
target_link_libraries(${PROJECT_NAME} PRIVATE SDL3::SDL3)

# Copy SDL3 DLL to bin directory on Windows
if(WIN32)
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        $<TARGET_FILE:SDL3::SDL3>
        $<TARGET_FILE_DIR:${PROJECT_NAME}>
        COMMENT "Copying SDL3 DLL to output directory"
    )
endif()

# Compiler-specific options
if(MSVC)
    # MSVC specific flags
    target_compile_options(${PROJECT_NAME} PRIVATE
        /W4          # Warning level 4
    )
else()
    # GCC/Clang flags
    target_compile_options(${PROJECT_NAME} PRIVATE
        -Wall        # Enable most warnings
        -Wextra      # Enable extra warnings
        -Wpedantic   # Pedantic warnings
    )
endif()

# Platform-specific configurations
if(WIN32)
    message(STATUS "Configuring for Windows")
    # Windows-specific settings
elseif(UNIX)
    message(STATUS "Configuring for Linux/Unix")
    # Linux-specific settings
endif()

# Build type configuration
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()

message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "C++ Standard: ${CMAKE_CXX_STANDARD}")
