cmake_minimum_required(VERSION 3.6)

if(NOT APPLE)
  set(lib_name al)
  set(target_name ext_${lib_name})

  project(${lib_name})

  if(ANDROID)
    add_library(${target_name} SHARED IMPORTED GLOBAL)
    set_target_properties(${target_name} PROPERTIES
      INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
    )
    
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI})
    
    if(NOT EXISTS ${PREBUILT_ROOT})
      message(FATAL_ERROR "no prebuilt library for ${ANDROID_ABI} at ${PREBUILT_ROOT}")
    endif()
    
    set_target_properties(${target_name} PROPERTIES
      IMPORTED_LOCATION "${PREBUILT_ROOT}/libopenal.so"
    )
  endif()
elseif(MACOSX OR IOS)
  if(MACOSX)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac)
  elseif(IOS)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_ios)
  endif()

  set(ALSOFT_FRAMEWORK "soft_oal.framework" CACHE INTERNAL "FFmpeg Framework name")
  set(ALSOFT_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include" CACHE INTERNAL "OpenAL Soft header location")
  set(ALSOFT_FRAMEWORK_PATH "${PREBUILT_ROOT}" CACHE INTERNAL "OpenAL Soft Framework location")
  set(ALSOFT_OPENAL_LIBRARY "${PREBUILT_ROOT}/${ALSOFT_FRAMEWORK}" CACHE INTERNAL "OpenAL Soft library")
endif()
