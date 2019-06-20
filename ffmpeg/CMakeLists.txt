cmake_minimum_required(VERSION 3.6)

set(lib_name ffmpeg)
set(target_name ext_${lib_name})

project(${lib_name})

if(WINDOWS OR ANDROID)
  add_library(${target_name} SHARED IMPORTED GLOBAL)
else()
  message(FATAL_ERROR "not supported yet")
endif()

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
)

if(WINDOWS)
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win32)

  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avcodec-57.dll"
    IMPORTED_IMPLIB "${PREBUILT_ROOT}/avcodec.lib"
  )
  
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avdevice-57.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avfilter-6.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avformat-57.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/avutil-55.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/postproc-54.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/swresample-2.dll")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/swscale-4.dll")

  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avdevice.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avfilter.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avformat.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/avutil.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/postproc.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/swresample.lib")
  set_property(TARGET ${target_name} APPEND PROPERTY 
    INTERFACE_LINK_LIBRARIES "${PREBUILT_ROOT}/swscale.lib")
elseif(ANDROID)
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI})
  if(NOT EXISTS PREBUILT_ROOT)
    message(FATAL_ERROR "no prebuilt library for ${ANDROID_ABI}")
  endif()
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libffmpeg.so"
  )
endif()
