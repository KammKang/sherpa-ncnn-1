cmake_minimum_required(VERSION 3.13 FATAL_ERROR)
project(sherpa-ncnn)

set(SHERPA_NCNN_VERSION "1.3.2")

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin")

set(CMAKE_SKIP_BUILD_RPATH FALSE)
set(BUILD_RPATH_USE_ORIGIN TRUE)
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

if(NOT APPLE)
  set(SHERPA_NCNN_RPATH_ORIGIN "$ORIGIN")
else()
  set(CMAKE_MACOSX_RPATH ON)
  set(SHERPA_NCNN_RPATH_ORIGIN "@loader_path")
endif()

set(CMAKE_INSTALL_RPATH ${SHERPA_NCNN_RPATH_ORIGIN})
set(CMAKE_BUILD_RPATH ${SHERPA_NCNN_RPATH_ORIGIN})

option(BUILD_SHARED_LIBS "Whether to build shared libraries" OFF)
option(SHERPA_NCNN_ENABLE_PYTHON "Whether to build Python" OFF)
option(SHERPA_NCNN_ENABLE_PORTAUDIO "Whether to build with portaudio" ON)
option(SHERPA_NCNN_ENABLE_JNI "Whether to build JNI internface" OFF)
option(SHERPA_NCNN_ENABLE_BINARY "Whether to build the binary sherpa-ncnn" ON)
option(SHERPA_NCNN_ENABLE_TEST "Whether to build tests" OFF)

if(DEFINED ANDROID_ABI)
  message(STATUS "Set SHERPA_NCNN_ENABLE_JNI to ON for Android")
  set(SHERPA_NCNN_ENABLE_JNI ON CACHE BOOL "" FORCE)
endif()

message(STATUS "BUILD_SHARED_LIBS ${BUILD_SHARED_LIBS}")
message(STATUS "SHERPA_NCNN_ENABLE_PYTHON ${SHERPA_NCNN_ENABLE_PYTHON}")
message(STATUS "SHERPA_NCNN_ENABLE_PORTAUDIO ${SHERPA_NCNN_ENABLE_PORTAUDIO}")
message(STATUS "SHERPA_NCNN_ENABLE_JNI ${SHERPA_NCNN_ENABLE_JNI}")
message(STATUS "SHERPA_NCNN_ENABLE_BINARY ${SHERPA_NCNN_ENABLE_BINARY}")
message(STATUS "SHERPA_NCNN_ENABLE_TEST ${SHERPA_NCNN_ENABLE_TEST}")


if(NOT CMAKE_BUILD_TYPE)
  message(STATUS "No CMAKE_BUILD_TYPE given, default to Release")
  set(CMAKE_BUILD_TYPE Release)
endif()
message(STATUS "CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")

set(CMAKE_CXX_STANDARD 14 CACHE STRING "The C++ version to be used.")
set(CMAKE_CXX_EXTENSIONS OFF)

include(CheckIncludeFileCXX)
check_include_file_cxx(alsa/asoundlib.h SHERPA_NCNN_HAS_ALSA)
if(SHERPA_NCNN_HAS_ALSA)
  add_definitions(-DSHERPA_NCNN_ENABLE_ALSA=1)
endif()

list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

include(kaldi-native-fbank)
include(ncnn)

if(SHERPA_NCNN_ENABLE_PORTAUDIO)
  include(portaudio)
endif()

if(SHERPA_NCNN_ENABLE_PYTHON)
  include(pybind11)
endif()

add_subdirectory(sherpa-ncnn)
