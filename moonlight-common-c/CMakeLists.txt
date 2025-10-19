# moonlight-common-c static library
project(moonlight-common-c LANGUAGES C)

# Disable Qt for this library
set(CMAKE_AUTOMOC OFF)
set(CMAKE_AUTORCC OFF)
set(CMAKE_AUTOUIC OFF)

# Define source directories
set(COMMON_C_DIR "${CMAKE_CURRENT_SOURCE_DIR}/moonlight-common-c")
set(ENET_DIR "${COMMON_C_DIR}/enet")
set(RS_DIR "${COMMON_C_DIR}/reedsolomon")

# Source files
set(SOURCES
    ${RS_DIR}/rs.c
    ${ENET_DIR}/callbacks.c
    ${ENET_DIR}/compress.c
    ${ENET_DIR}/host.c
    ${ENET_DIR}/list.c
    ${ENET_DIR}/packet.c
    ${ENET_DIR}/peer.c
    ${ENET_DIR}/protocol.c
    ${ENET_DIR}/unix.c
    ${ENET_DIR}/win32.c
    ${COMMON_C_DIR}/src/AudioStream.c
    ${COMMON_C_DIR}/src/ByteBuffer.c
    ${COMMON_C_DIR}/src/Connection.c
    ${COMMON_C_DIR}/src/ConnectionTester.c
    ${COMMON_C_DIR}/src/ControlStream.c
    ${COMMON_C_DIR}/src/FakeCallbacks.c
    ${COMMON_C_DIR}/src/InputStream.c
    ${COMMON_C_DIR}/src/LinkedBlockingQueue.c
    ${COMMON_C_DIR}/src/Misc.c
    ${COMMON_C_DIR}/src/Platform.c
    ${COMMON_C_DIR}/src/PlatformCrypto.c
    ${COMMON_C_DIR}/src/PlatformSockets.c
    ${COMMON_C_DIR}/src/RtpAudioQueue.c
    ${COMMON_C_DIR}/src/RtpVideoQueue.c
    ${COMMON_C_DIR}/src/RtspConnection.c
    ${COMMON_C_DIR}/src/RtspParser.c
    ${COMMON_C_DIR}/src/SdpGenerator.c
    ${COMMON_C_DIR}/src/SimpleStun.c
    ${COMMON_C_DIR}/src/VideoDepacketizer.c
    ${COMMON_C_DIR}/src/VideoStream.c
)

# Header files
set(HEADERS
    ${COMMON_C_DIR}/src/Limelight.h
)

# Create static library
add_library(moonlight-common-c STATIC ${SOURCES} ${HEADERS})

# Include directories
target_include_directories(moonlight-common-c PUBLIC
    ${RS_DIR}
    ${ENET_DIR}/include
    ${COMMON_C_DIR}/src
)

# macOS-specific include directories
target_include_directories(moonlight-common-c PRIVATE
    ${CMAKE_SOURCE_DIR}/libs/mac/include
)

# Compile definitions
target_compile_definitions(moonlight-common-c PRIVATE
    HAS_SOCKLEN_T
    HAVE_CLOCK_GETTIME=1
)

# Compiler flags for C99
if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(moonlight-common-c PRIVATE
        -std=gnu99
        -Wno-unused-parameter
    )
endif()

