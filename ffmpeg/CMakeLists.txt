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
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavcodec.so"
  )
  set(FFMPEG_LIBS
    avdevice
    avfilter
    avformat
    avutil
    swresample
    swscale
  )
  foreach(ff_lib ${FFMPEG_LIBS})
    set(ff_lib_target ${target_name}_${ff_lib})
    add_library(${ff_lib_target} SHARED IMPORTED GLOBAL)
    set_target_properties(${ff_lib_target} PROPERTIES
      IMPORTED_LOCATION "${PREBUILT_ROOT}/lib${ff_lib}.so"
    )
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_LINK_LIBRARIES ${ff_lib_target}
    )
  endforeach()
elseif(MACOSX OR IOS)
  if(MACOSX)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac)
  elseif(IOS)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_ios)
  endif()

  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libavcodec.a"
  )
  set(FFMPEG_LIBS
    avdevice
    avfilter
    avformat
    avutil
    swresample
    swscale
  )
  foreach(ff_lib ${FFMPEG_LIBS})
    set(ff_lib_target ${target_name}_${ff_lib})
    add_library(${ff_lib_target} STATIC IMPORTED)
    set_target_properties(${ff_lib_target} PROPERTIES
      IMPORTED_LOCATION "${PREBUILT_ROOT}/lib${ff_lib}.a"
    )
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_LINK_LIBRARIES ${ff_lib_target}
    )
  endforeach()
endif()
