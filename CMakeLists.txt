cmake_minimum_required(VERSION 3.10)
project(BareDMG C)

# C Standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Export compile commands for clangd
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Set default build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
    message(STATUS "Build type not specified, defaulting to Debug")
endif()

# Compiler flags
if (MSVC)
    # MSVC warning level
    add_compile_options(/W4)
    set(CMAKE_C_FLAGS_DEBUG "/Zi /Od")
    set(CMAKE_C_FLAGS_RELEASE "/O2 /DNDEBUG")
else()
    add_compile_options(-Wall -Wextra -pedantic)
    set(CMAKE_C_FLAGS_DEBUG "-g -O0")
    set(CMAKE_C_FLAGS_RELEASE "-O3 -DNDEBUG")
endif()

# Include directories
include_directories(${PROJECT_SOURCE_DIR}/include)

# Build core library
add_subdirectory(src/core)

# Build main executable
add_executable(baredmg src/main.c)
target_link_libraries(baredmg gbcore)

# NOTE: Build tests
include(CTest)

if(BUILD_TESTING)
    find_package(PkgConfig)

    if(PkgConfig_FOUND)
        add_subdirectory(tests)
    else()
        message(WARNING "PkgConfig not found, skipping tests")
    endif()
endif()

# Print build info
message(STATUS "========================================")
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "C Compiler: ${CMAKE_C_COMPILER}")
message(STATUS "C Flags: ${CMAKE_C_FLAGS}")
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    message(STATUS "Debug flags: ${CMAKE_C_FLAGS_DEBUG}")
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
    message(STATUS "Release flags: ${CMAKE_C_FLAGS_RELEASE}")
endif()
message(STATUS "Build tests: ${BUILD_TESTS}")
message(STATUS "========================================")
