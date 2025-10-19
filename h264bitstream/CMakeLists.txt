# h264bitstream static library
project(h264bitstream LANGUAGES C)

# Disable Qt for this library
set(CMAKE_AUTOMOC OFF)
set(CMAKE_AUTORCC OFF)
set(CMAKE_AUTOUIC OFF)

# Define source directory
set(SRC_DIR "${CMAKE_CURRENT_SOURCE_DIR}/h264bitstream")

# Source files
set(SOURCES
    ${SRC_DIR}/h264_nal.c
    ${SRC_DIR}/h264_sei.c
    ${SRC_DIR}/h264_stream.c
)

# Header files
set(HEADERS
    ${SRC_DIR}/bs.h
    ${SRC_DIR}/h264_sei.h
    ${SRC_DIR}/h264_stream.h
)

# Create static library
add_library(h264bitstream STATIC ${SOURCES} ${HEADERS})

# Include directories
target_include_directories(h264bitstream PUBLIC
    ${SRC_DIR}
)

# Compiler flags for C99
if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(h264bitstream PRIVATE -std=gnu99)
endif()

# Disable warnings
if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(h264bitstream PRIVATE -w)
elseif(MSVC)
    target_compile_options(h264bitstream PRIVATE /W0)
endif()

