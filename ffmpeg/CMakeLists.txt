cmake_minimum_required(VERSION 3.6)

set(lib_name ffmpeg)
set(target_name ext_${lib_name})

project(${lib_name})
set(PREBUILT_ROOT "")

if(WINDOWS OR ANDROID)
  add_library(${target_name} SHARED IMPORTED GLOBAL)
elseif(MACOSX OR IOS)
  add_library(${target_name} STATIC IMPORTED GLOBAL)
else()
  message(FATAL_ERROR "not supported yet")
endif()

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
)

if(WINDOWS)
  if(WIN64)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win64)
  else()
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win32)
  endif()
  
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avcodec-58.dll"
    IMPORTED_IMPLIB "${PREBUILT_ROOT}/avcodec.lib"
  )
  
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avdevice-58.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avfilter-7.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avformat-58.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avutil-56.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/swresample-3.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/swscale-5.dll")

  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avdevice.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avfilter.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avformat.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avutil.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/swresample.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/swscale.lib")
elseif(ANDROID)
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI})
  if(NOT EXISTS ${PREBUILT_ROOT})
    message(FATAL_ERROR "no prebuilt library for ${ANDROID_ABI} at ${PREBUILT_ROOT}")
  endif()
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libffmpeg.so"
  )
elseif(MACOSX OR IOS)
  if(MACOSX)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac)
  elseif(IOS)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_ios)
  endif()
  
  #if(NOT EXISTS PREBUILT_ROOT)
  #  message(FATAL_ERROR "no prebuilt library at ${PREBUILT_ROOT}")
  #endif()
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavcodec.a"
  )
  # multi libs
  add_library(libavdevice STATIC IMPORTED)
  set_target_properties(libavdevice PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavdevice.a"
  )
  add_library(libavfilter STATIC IMPORTED)
  set_target_properties(libavfilter PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavfilter.a"
  )
  add_library(libavformat STATIC IMPORTED)
  set_target_properties(libavformat PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavformat.a"
  )
  add_library(libavutil STATIC IMPORTED)
  set_target_properties(libavutil PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavutil.a"
  )
  add_library(libswresample STATIC IMPORTED)
  set_target_properties(libswresample PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libswresample.a"
  )
  add_library(libswscale STATIC IMPORTED)
  set_target_properties(libswscale PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libswscale.a"
  )
  add_library(libavcodec STATIC IMPORTED)
  set_target_properties(libavcodec PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavcodec.a"
  )
  # mark multi libs link into single one: "${target_name}"
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libavcodec"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libavdevice"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libavfilter"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libavformat"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libavutil"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libswresample"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libswscale"
  )
endif()
