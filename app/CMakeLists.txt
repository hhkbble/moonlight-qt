# Moonlight application
project(Moonlight LANGUAGES C CXX OBJCXX)

# Find Qt modules
if(QT_VERSION_MAJOR EQUAL 6)
    find_package(Qt6 REQUIRED COMPONENTS Core Quick Network QuickControls2 Svg LinguistTools)
    set(QT_LIBS Qt6::Core Qt6::Quick Qt6::Network Qt6::QuickControls2 Qt6::Svg)
else()
    find_package(Qt5 REQUIRED COMPONENTS Core Quick Network QuickControls2 Svg LinguistTools)
    set(QT_LIBS Qt5::Core Qt5::Quick Qt5::Network Qt5::QuickControls2 Qt5::Svg)
endif()

# Read version from version.txt
file(READ "${CMAKE_CURRENT_SOURCE_DIR}/version.txt" APP_VERSION)
string(STRIP "${APP_VERSION}" APP_VERSION)

# Define compile definitions
add_compile_definitions(
    VERSION_STR="${APP_VERSION}"
    HAVE_FFMPEG
    HAVE_DISCORD
)

# Source files
set(SOURCES
    backend/nvaddress.cpp
    backend/nvapp.cpp
    cli/pair.cpp
    main.cpp
    backend/computerseeker.cpp
    backend/identitymanager.cpp
    backend/nvcomputer.cpp
    backend/nvhttp.cpp
    backend/nvpairingmanager.cpp
    backend/computermanager.cpp
    backend/boxartmanager.cpp
    backend/richpresencemanager.cpp
    cli/commandlineparser.cpp
    cli/listapps.cpp
    cli/quitstream.cpp
    cli/startstream.cpp
    settings/compatfetcher.cpp
    settings/mappingfetcher.cpp
    settings/streamingpreferences.cpp
    streaming/input/abstouch.cpp
    streaming/input/gamepad.cpp
    streaming/input/input.cpp
    streaming/input/keyboard.cpp
    streaming/input/mouse.cpp
    streaming/input/reltouch.cpp
    streaming/session.cpp
    streaming/audio/audio.cpp
    streaming/audio/renderers/sdlaud.cpp
    gui/computermodel.cpp
    gui/appmodel.cpp
    streaming/streamutils.cpp
    backend/autoupdatechecker.cpp
    path.cpp
    settings/mappingmanager.cpp
    gui/sdlgamepadkeynavigation.cpp
    streaming/video/overlaymanager.cpp
    backend/systemproperties.cpp
    wm.cpp
    # FFmpeg decoder sources
    streaming/video/ffmpeg.cpp
    streaming/video/ffmpeg-renderers/genhwaccel.cpp
    streaming/video/ffmpeg-renderers/sdlvid.cpp
    streaming/video/ffmpeg-renderers/swframemapper.cpp
    streaming/video/ffmpeg-renderers/pacer/pacer.cpp
    # VideoToolbox renderer (macOS)
    streaming/video/ffmpeg-renderers/vt_base.mm
    streaming/video/ffmpeg-renderers/vt_avsamplelayer.mm
    streaming/video/ffmpeg-renderers/vt_metal.mm
)

# Header files
set(HEADERS
    SDL_compat.h
    backend/nvaddress.h
    backend/nvapp.h
    cli/pair.h
    settings/compatfetcher.h
    settings/mappingfetcher.h
    utils.h
    backend/computerseeker.h
    backend/identitymanager.h
    backend/nvcomputer.h
    backend/nvhttp.h
    backend/nvpairingmanager.h
    backend/computermanager.h
    backend/boxartmanager.h
    backend/richpresencemanager.h
    cli/commandlineparser.h
    cli/listapps.h
    cli/quitstream.h
    cli/startstream.h
    settings/streamingpreferences.h
    streaming/input/input.h
    streaming/session.h
    streaming/audio/renderers/renderer.h
    streaming/audio/renderers/sdl.h
    gui/computermodel.h
    gui/appmodel.h
    streaming/video/decoder.h
    streaming/streamutils.h
    backend/autoupdatechecker.h
    path.h
    settings/mappingmanager.h
    gui/sdlgamepadkeynavigation.h
    streaming/video/overlaymanager.h
    backend/systemproperties.h
    # FFmpeg headers
    streaming/video/ffmpeg.h
    streaming/video/ffmpeg-renderers/renderer.h
    streaming/video/ffmpeg-renderers/genhwaccel.h
    streaming/video/ffmpeg-renderers/sdlvid.h
    streaming/video/ffmpeg-renderers/swframemapper.h
    streaming/video/ffmpeg-renderers/pacer/pacer.h
    streaming/video/ffmpeg-renderers/vt.h
)

# Resource files
set(RESOURCES
    resources.qrc
    qml.qrc
)

# Create macOS application bundle
add_executable(Moonlight MACOSX_BUNDLE
    ${SOURCES}
    ${HEADERS}
    ${RESOURCES}
)

# Configure Info.plist with version (replace VERSION placeholder)
file(READ "${CMAKE_CURRENT_SOURCE_DIR}/Info.plist" INFO_PLIST_CONTENT)
string(REPLACE "VERSION" "${APP_VERSION}" INFO_PLIST_CONTENT "${INFO_PLIST_CONTENT}")
file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/Info.plist" "${INFO_PLIST_CONTENT}")

# Set bundle properties
set_target_properties(Moonlight PROPERTIES
    MACOSX_BUNDLE_INFO_PLIST "${CMAKE_CURRENT_BINARY_DIR}/Info.plist"
    MACOSX_BUNDLE_BUNDLE_NAME "Moonlight"
    MACOSX_BUNDLE_GUI_IDENTIFIER "com.moonlight-stream.Moonlight"
    MACOSX_BUNDLE_BUNDLE_VERSION "${APP_VERSION}"
    MACOSX_BUNDLE_SHORT_VERSION_STRING "${APP_VERSION}"
    MACOSX_BUNDLE_ICON_FILE "moonlight"
    OUTPUT_NAME "Moonlight"
)

# Copy icon to Resources
set_source_files_properties(moonlight.icns PROPERTIES
    MACOSX_PACKAGE_LOCATION "Resources"
)
target_sources(Moonlight PRIVATE moonlight.icns)

# Include directories
target_include_directories(Moonlight PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_SOURCE_DIR}/moonlight-common-c/moonlight-common-c/src
    ${CMAKE_SOURCE_DIR}/qmdnsengine/qmdnsengine/src/include
    ${CMAKE_SOURCE_DIR}/qmdnsengine
    ${CMAKE_SOURCE_DIR}/h264bitstream/h264bitstream
    ${CMAKE_SOURCE_DIR}/libs/mac/include
    ${CMAKE_SOURCE_DIR}/libs/mac/Frameworks/SDL2.framework/Versions/A/Headers
    ${CMAKE_SOURCE_DIR}/libs/mac/Frameworks/SDL2_ttf.framework/Versions/A/Headers
)

# Link internal libraries
target_link_libraries(Moonlight PRIVATE
    moonlight-common-c
    qmdnsengine
    h264bitstream
    ${QT_LIBS}
)

# ============================================================================
# Import prebuilt libraries from libs/mac/
# ============================================================================

# SDL2 Framework
add_library(SDL2_Framework INTERFACE IMPORTED)
set_target_properties(SDL2_Framework PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_SOURCE_DIR}/libs/mac/Frameworks/SDL2.framework/Versions/A/Headers"
    INTERFACE_LINK_LIBRARIES "-F${CMAKE_SOURCE_DIR}/libs/mac/Frameworks -framework SDL2"
)

# SDL2_ttf Framework
add_library(SDL2_ttf_Framework INTERFACE IMPORTED)
set_target_properties(SDL2_ttf_Framework PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_SOURCE_DIR}/libs/mac/Frameworks/SDL2_ttf.framework/Versions/A/Headers"
    INTERFACE_LINK_LIBRARIES "-F${CMAKE_SOURCE_DIR}/libs/mac/Frameworks -framework SDL2_ttf"
)

# OpenSSL libraries
add_library(ssl_prebuilt SHARED IMPORTED)
set_target_properties(ssl_prebuilt PROPERTIES
    IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/libs/mac/lib/libssl.3.dylib"
)

add_library(crypto_prebuilt SHARED IMPORTED)
set_target_properties(crypto_prebuilt PROPERTIES
    IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/libs/mac/lib/libcrypto.3.dylib"
)

# FFmpeg libraries
add_library(avcodec_prebuilt SHARED IMPORTED)
set_target_properties(avcodec_prebuilt PROPERTIES
    IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/libs/mac/lib/libavcodec.62.dylib"
)

add_library(avutil_prebuilt SHARED IMPORTED)
set_target_properties(avutil_prebuilt PROPERTIES
    IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/libs/mac/lib/libavutil.60.dylib"
)

add_library(swscale_prebuilt SHARED IMPORTED)
set_target_properties(swscale_prebuilt PROPERTIES
    IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/libs/mac/lib/libswscale.9.dylib"
)

# Opus library (static)
add_library(opus_prebuilt STATIC IMPORTED)
set_target_properties(opus_prebuilt PROPERTIES
    IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/libs/mac/lib/libopus.a"
)

# Discord RPC library (static)
add_library(discord_rpc_prebuilt STATIC IMPORTED)
set_target_properties(discord_rpc_prebuilt PROPERTIES
    IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/libs/mac/lib/libdiscord-rpc.a"
)

# Link prebuilt libraries
target_link_libraries(Moonlight PRIVATE
    SDL2_Framework
    SDL2_ttf_Framework
    ssl_prebuilt
    crypto_prebuilt
    avcodec_prebuilt
    avutil_prebuilt
    swscale_prebuilt
    opus_prebuilt
    discord_rpc_prebuilt
)

# Link macOS system frameworks
target_link_libraries(Moonlight PRIVATE
    "-framework VideoToolbox"
    "-framework AVFoundation"
    "-framework CoreVideo"
    "-framework CoreGraphics"
    "-framework CoreMedia"
    "-framework AppKit"
    "-framework Metal"
    "-framework QuartzCore"
    "-lobjc"
)

# Set RPATH for finding dylibs in the bundle
set_target_properties(Moonlight PROPERTIES
    BUILD_WITH_INSTALL_RPATH TRUE
    INSTALL_RPATH "@executable_path/../Frameworks"
)

# Copy frameworks and dylibs to the bundle
add_custom_command(TARGET Moonlight POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks"
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${CMAKE_SOURCE_DIR}/libs/mac/Frameworks/SDL2.framework"
        "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks/SDL2.framework"
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${CMAKE_SOURCE_DIR}/libs/mac/Frameworks/SDL2_ttf.framework"
        "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks/SDL2_ttf.framework"
    COMMAND ${CMAKE_COMMAND} -E copy
        "${CMAKE_SOURCE_DIR}/libs/mac/lib/libssl.3.dylib"
        "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks/"
    COMMAND ${CMAKE_COMMAND} -E copy
        "${CMAKE_SOURCE_DIR}/libs/mac/lib/libcrypto.3.dylib"
        "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks/"
    COMMAND ${CMAKE_COMMAND} -E copy
        "${CMAKE_SOURCE_DIR}/libs/mac/lib/libavcodec.62.dylib"
        "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks/"
    COMMAND ${CMAKE_COMMAND} -E copy
        "${CMAKE_SOURCE_DIR}/libs/mac/lib/libavutil.60.dylib"
        "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks/"
    COMMAND ${CMAKE_COMMAND} -E copy
        "${CMAKE_SOURCE_DIR}/libs/mac/lib/libswscale.9.dylib"
        "$<TARGET_BUNDLE_DIR:Moonlight>/Contents/Frameworks/"
    COMMENT "Copying frameworks and dylibs to bundle"
)

# Install rules (optional - the bundle is already in the build directory)
install(TARGETS Moonlight
    BUNDLE DESTINATION .
    RUNTIME DESTINATION bin
)

