# Copyright (c) 2022 Jake Merdich
# SPDX-License-Identifier: GPL-2.0-or-later

cmake_minimum_required(VERSION 3.13)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

project(AEOLight VERSION 2.3.3)

add_executable(AEOLight
    aeoexception.h
    DPX.h
    DPXHeader.h
    DPXStream.h
    extractdialog.h
    FilmScan.h
    frame_view_gl.h
    mainwindow.h
    metadata.h
    openglwindow.h
    overlap.h
    preferencesdialog.h
    project.h
    readframedpx.h
    readframeexr.h
    readframetiff.h
    savesampledialog.h
    videoencoder.h
    wav.h
    writexml.h
    extractdialog.cpp
    FilmScan.cpp
    frame_view_gl.cpp
    main.cpp
    mainwindow.cpp
    metadata.cpp
    openglwindow.cpp
    preferencesdialog.cpp
    project.cpp
    readframedpx.cpp
    readframeexr.cpp
    readframetiff.cpp
    savesampledialog.cpp
    videoencoder.cpp
    wav.cpp
    writexml.cpp
)

set_target_properties(AEOLight PROPERTIES
    AUTOUIC ON
    AUTOMOC ON
)
target_compile_definitions(AEOLight PUBLIC
    APP_VERSION="${CMAKE_PROJECT_VERSION}"
    APP_VERSION_STR="${CMAKE_PROJECT_VERSION}"
)

# Platform specific compile options
if (CMAKE_CXX_COMPILER_ID MATCHES "Clang|GNU")
    # gcc or clang on all platforms


elseif (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    # Despite what MS says, strncpy is not deprecated.
    target_compile_definitions(AEOLight PUBLIC _CRT_SECURE_NO_WARNINGS)

    # MSVC does not have 'dirent'. Use a wrapper
    target_include_directories(AEOLight PUBLIC extern/dirent)
endif()

# Well-known dependencies
find_package(Qt5 COMPONENTS Core Widgets Multimedia OpenGL REQUIRED)
find_package(OpenGL REQUIRED)
find_package(TIFF REQUIRED)
find_package(FFMPEG COMPONENTS avcodec avutil avformat swresample swscale REQUIRED)
find_package(OpenEXR REQUIRED)
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
    OpenEXR::OpenEXR
)

## Uncommon dependencies that we'll build ourself.
add_subdirectory(extern/DSPFilters/shared/DSPFilters dspfilters)

aux_source_directory(extern/dpx/libdpx DPX_SOURCES)
add_library(dpx STATIC ${DPX_SOURCES})
target_include_directories(dpx PUBLIC extern/dpx/libdpx)

target_link_libraries(AEOLight PUBLIC DSPFilters dpx)