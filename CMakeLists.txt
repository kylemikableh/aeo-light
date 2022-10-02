# Copyright (c) 2022 Jake Merdich
# SPDX-License-Identifier: GPL-2.0-or-later

cmake_minimum_required(VERSION 3.13)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

project(AEOLight VERSION 2.3.3)

add_executable(AEOLight WIN32 MACOSX_BUNDLE
    DPX.h
    DPXHeader.h
    DPXStream.h
    FilmScan.cpp
    FilmScan.h
    aeoexception.h
    extractdialog.cpp
    extractdialog.h
    frame_view_gl.cpp
    frame_view_gl.h
    main.cpp
    mainwindow.cpp
    mainwindow.h
    metadata.cpp
    metadata.h
    openglwindow.cpp
    openglwindow.h
    overlap.h
    preferencesdialog.cpp
    preferencesdialog.h
    project.cpp
    project.h
    readframedpx.cpp
    readframedpx.h
    readframeexr.cpp
    readframeexr.h
    readframetiff.cpp
    readframetiff.h
    savesampledialog.cpp
    savesampledialog.h
    videoencoder.cpp
    videoencoder.h
    wav.cpp
    wav.h
    writexml.cpp
    writexml.h
    # QT Resources
    images.qrc
    license.qrc
    shaders.qrc
    # QT forms
    mainwindow.ui
    savesampledialog.ui
    preferencesdialog.ui
    extractdialog.ui
    # MacOS icon
    aeolight.icns
)

# Well-known dependencies
find_package(Qt5 COMPONENTS Core Widgets Multimedia OpenGL REQUIRED)
find_package(OpenGL REQUIRED)
find_package(TIFF REQUIRED)
find_package(FFMPEG COMPONENTS avcodec avutil avformat swresample swscale REQUIRED)
find_package(OpenEXR REQUIRED)
if (TARGET OpenEXR::OpenEXR)
    # 3.x
    set(OpenEXR_TARGET OpenEXR::OpenEXR)
else()
    # 2.x
    set(OpenEXR_TARGET OpenEXR::IlmImf)
endif()

# Build dspfilters from source; it already uses cmake
add_subdirectory(extern/DSPFilters/shared/DSPFilters dspfilters)

# Build dpx from source; sub in a 'compile everything' here to keep it simple.
aux_source_directory(extern/dpx/libdpx DPX_SOURCES)
add_library(dpx STATIC ${DPX_SOURCES})
target_include_directories(dpx PUBLIC extern/dpx/libdpx)

target_link_libraries(AEOLight PUBLIC
    ${OPENGL_LIBRARIES}
    Qt5::Core
    Qt5::Widgets
    Qt5::Multimedia
    Qt5::OpenGL
    TIFF::TIFF
    FFMPEG::avcodec
    FFMPEG::avutil
    FFMPEG::avformat
    FFMPEG::swresample
    FFMPEG::swscale
    ${OpenEXR_TARGET}
    DSPFilters
    dpx
)

# Add the version number to the source
target_compile_definitions(AEOLight PUBLIC
    APP_VERSION="${CMAKE_PROJECT_VERSION}"
    APP_VERSION_STR="${CMAKE_PROJECT_VERSION}"
)

# Platform specific compile options
if (CMAKE_CXX_COMPILER_ID MATCHES "Clang|GNU")
    # gcc or clang on all platforms

    target_compile_options(AEOLight PUBLIC
        -Wno-unused-private-field
        -Wno-unused-variable
        -Wno-unused-parameter
        -Wno-ignored-qualifiers
        -Wno-unused-function
        -Wno-sign-compare
        -Wno-unused-local-typedef
        -Wno-reserved-user-defined-literal
    )
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    # Visual Studio only

    # Despite what MS says, strncpy is not deprecated.
    target_compile_definitions(AEOLight PUBLIC _CRT_SECURE_NO_WARNINGS)

    # MSVC does not have 'dirent'. Use a wrapper
    find_path(DIRENT_INCLUDE_DIRS "dirent.h")
    target_include_directories(AEOLight PRIVATE ${DIRENT_INCLUDE_DIRS})

    target_compile_options(dpx PUBLIC 
        # Unused variables
        /wd4101

        # sign mismatch
        /wd4018
    )

    # Same thing, but for other compiled libs
    target_compile_definitions(DSPFilters PUBLIC _SILENCE_TR1_NAMESPACE_DEPRECATION_WARNING)
    target_compile_definitions(dpx PUBLIC _CRT_SECURE_NO_WARNINGS)
    target_compile_options(dpx PUBLIC 
        # Various int-truncation warnings
        /wd4305 /wd4244 /wd4267
    )
endif()


# More QT stuff
set_target_properties(AEOLight PROPERTIES
    AUTOUIC ON
    AUTOMOC ON
    AUTORCC ON
)

# Handle app icons
if (WIN32)
    target_sources(AEOLight PRIVATE aeolight.rc)
elseif(APPLE)
    target_sources(AEOLight PRIVATE aeolight.icns)
    set_source_files_properties(aeolight.icns PROPERTIES
        MACOSX_PACKAGE_LOCATION "Resources"
    )
elseif (${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    # TODO: make a .desktop file for linux
endif()