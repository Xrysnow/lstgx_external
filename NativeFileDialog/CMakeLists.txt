cmake_minimum_required(VERSION 3.6)

set(lib_name nfd)
set(target_name ext_${lib_name})

project(${lib_name})

if(WINDOWS OR MACOSX OR LINUX)
  add_library(${target_name} STATIC IMPORTED GLOBAL)
else()
  message(FATAL_ERROR "not supported")
endif()

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
)

if(WINDOWS)
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win32)
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/nfd.lib")
elseif(MACOSX)
  #
elseif(LINUX)
  #
endif()
