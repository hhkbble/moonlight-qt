# qmdnsengine static library
project(qmdnsengine LANGUAGES CXX)

# Enable Qt Network module
if(QT_VERSION_MAJOR EQUAL 6)
    find_package(Qt6 REQUIRED COMPONENTS Network)
    set(QT_LIBS Qt6::Network)
else()
    find_package(Qt5 REQUIRED COMPONENTS Network)
    set(QT_LIBS Qt5::Network)
endif()

# Define source directory
set(QMDNSE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/qmdnsengine/src")

# Header files (for MOC processing)
set(HEADERS
    ${QMDNSE_DIR}/include/qmdnsengine/abstractserver.h
    ${QMDNSE_DIR}/include/qmdnsengine/bitmap.h
    ${QMDNSE_DIR}/include/qmdnsengine/browser.h
    ${QMDNSE_DIR}/include/qmdnsengine/cache.h
    ${QMDNSE_DIR}/include/qmdnsengine/dns.h
    ${QMDNSE_DIR}/include/qmdnsengine/hostname.h
    ${QMDNSE_DIR}/include/qmdnsengine/mdns.h
    ${QMDNSE_DIR}/include/qmdnsengine/message.h
    ${QMDNSE_DIR}/include/qmdnsengine/prober.h
    ${QMDNSE_DIR}/include/qmdnsengine/provider.h
    ${QMDNSE_DIR}/include/qmdnsengine/query.h
    ${QMDNSE_DIR}/include/qmdnsengine/record.h
    ${QMDNSE_DIR}/include/qmdnsengine/resolver.h
    ${QMDNSE_DIR}/include/qmdnsengine/server.h
    ${QMDNSE_DIR}/include/qmdnsengine/service.h
    ${QMDNSE_DIR}/src/bitmap_p.h
    ${QMDNSE_DIR}/src/browser_p.h
    ${QMDNSE_DIR}/src/cache_p.h
    ${QMDNSE_DIR}/src/hostname_p.h
    ${QMDNSE_DIR}/src/message_p.h
    ${QMDNSE_DIR}/src/prober_p.h
    ${QMDNSE_DIR}/src/provider_p.h
    ${QMDNSE_DIR}/src/query_p.h
    ${QMDNSE_DIR}/src/record_p.h
    ${QMDNSE_DIR}/src/resolver_p.h
    ${QMDNSE_DIR}/src/server_p.h
    ${QMDNSE_DIR}/src/service_p.h
)

# Source files
set(SOURCES
    ${QMDNSE_DIR}/src/abstractserver.cpp
    ${QMDNSE_DIR}/src/bitmap.cpp
    ${QMDNSE_DIR}/src/browser.cpp
    ${QMDNSE_DIR}/src/cache.cpp
    ${QMDNSE_DIR}/src/dns.cpp
    ${QMDNSE_DIR}/src/hostname.cpp
    ${QMDNSE_DIR}/src/mdns.cpp
    ${QMDNSE_DIR}/src/message.cpp
    ${QMDNSE_DIR}/src/prober.cpp
    ${QMDNSE_DIR}/src/provider.cpp
    ${QMDNSE_DIR}/src/query.cpp
    ${QMDNSE_DIR}/src/record.cpp
    ${QMDNSE_DIR}/src/resolver.cpp
    ${QMDNSE_DIR}/src/server.cpp
    ${QMDNSE_DIR}/src/service.cpp
)

# Create static library
add_library(qmdnsengine STATIC ${SOURCES} ${HEADERS})

# Include directories
target_include_directories(qmdnsengine PUBLIC
    ${QMDNSE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}
)

# Link Qt Network
target_link_libraries(qmdnsengine PUBLIC ${QT_LIBS})

# Compile definitions
target_compile_definitions(qmdnsengine PRIVATE
    QT_NO_SIGNALS_SLOTS_KEYWORDS
)

# Disable warnings
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(qmdnsengine PRIVATE -w)
elseif(MSVC)
    target_compile_options(qmdnsengine PRIVATE /W0)
endif()

