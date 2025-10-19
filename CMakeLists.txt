cmake_minimum_required(VERSION 3.16)

# Read version from app/version.txt
file(READ "${CMAKE_CURRENT_SOURCE_DIR}/app/version.txt" VERSION_STRING)
string(STRIP "${VERSION_STRING}" VERSION_STRING)

project(moonlight-qt VERSION ${VERSION_STRING} LANGUAGES C CXX OBJCXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set C standard for C files
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# macOS-specific settings
set(CMAKE_OSX_ARCHITECTURES "arm64" CACHE STRING "Build architectures for macOS")
set(CMAKE_OSX_DEPLOYMENT_TARGET "11.0" CACHE STRING "Minimum macOS deployment version")

# Enable automatic MOC, UIC, and RCC for Qt
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# Find Qt6 (try Qt6 first, fall back to Qt5 if needed)
find_package(Qt6 QUIET COMPONENTS Core Quick Network QuickControls2 Svg LinguistTools)
if(Qt6_FOUND)
    set(QT_VERSION_MAJOR 6)
    message(STATUS "Using Qt6")
else()
    find_package(Qt5 REQUIRED COMPONENTS Core Quick Network QuickControls2 Svg LinguistTools)
    set(QT_VERSION_MAJOR 5)
    message(STATUS "Using Qt5")
endif()

# Global compile definitions
add_compile_definitions(
    QT_DEPRECATED_WARNINGS
    QT_DISABLE_DEPRECATED_BEFORE=0x060000
    _USE_MATH_DEFINES
)

# Release build optimizations
if(CMAKE_BUILD_TYPE STREQUAL "Release")
    add_compile_definitions(NDEBUG)
endif()

# Debug build settings
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_compile_definitions(LC_DEBUG)
endif()

# Add subdirectories for dependencies
add_subdirectory(moonlight-common-c)
add_subdirectory(qmdnsengine)
add_subdirectory(h264bitstream)
add_subdirectory(app)

