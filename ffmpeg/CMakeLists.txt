cmake_minimum_required(VERSION 3.28)

set(lib_name ffmpeg)
set(target_name ext_${lib_name})
set(PREBUILT_ROOT "")

if(WINDOWS OR ANDROID OR LINUX)
  project(${lib_name})
endif()

if(WINDOWS OR ANDROID OR LINUX OR APPLE)
  add_library(${target_name} SHARED IMPORTED GLOBAL)
  
  set_target_properties(${target_name} PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
  )
endif()

if(WINDOWS)
  if(WIN64)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win64)
  else()
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win32)
  endif()

  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/ffmpeg-9.dll"
    IMPORTED_IMPLIB "${PREBUILT_ROOT}/ffmpeg.lib"
  )
elseif(ANDROID)
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI})
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libffmpeg.so"
  )
elseif(APPLE)
  set(PREBUILT_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_apple")
  if(NOT EXISTS "${PREBUILT_ROOT}/FFmpeg.xcframework/Info.plist"
      OR NOT EXISTS "${PREBUILT_ROOT}/include/libavcodec/avcodec.h")
    message(FATAL_ERROR "Incomplete FFmpeg XCFramework package: ${PREBUILT_ROOT}")
  endif()

  # avbuild ships common headers next to the XCFramework, not in each slice.
  set(FFMPEG_INCLUDE_DIR "${PREBUILT_ROOT}/include" CACHE INTERNAL "FFmpeg header location")
  set(FFMPEG_LIBRARY "${PREBUILT_ROOT}/FFmpeg.xcframework" CACHE INTERNAL "FFmpeg library")
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${FFMPEG_LIBRARY}"
    INTERFACE_INCLUDE_DIRECTORIES "${FFMPEG_INCLUDE_DIR}"
    FRAMEWORK TRUE
  )
elseif(LINUX)
  if(CMAKE_SYSTEM_PROCESSOR MATCHES "^(x86_64|amd64|AMD64)$")
    set(FFMPEG_LINUX_ARCH amd64)
  elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "^(aarch64|arm64)$")
    set(FFMPEG_LINUX_ARCH arm64)
  elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "^arm")
    set(FFMPEG_LINUX_ARCH armhf)
  else()
    message(FATAL_ERROR "No bundled FFmpeg 9 library for Linux ${CMAKE_SYSTEM_PROCESSOR}")
  endif()
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_linux/${FFMPEG_LINUX_ARCH})
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libffmpeg.so.9"
  )
endif()
