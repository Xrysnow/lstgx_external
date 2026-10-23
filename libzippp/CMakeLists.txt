cmake_minimum_required(VERSION 3.14)

set(lib_name libzippp)
set(target_name ext_${lib_name})
project(${lib_name} VERSION 3.0.0)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# Build the pinned libzip submodule with the application's toolchain and CRT.
# Keep these options local to this directory, without changing other dependencies.
set(BUILD_SHARED_LIBS OFF)
set(LIBZIP_DO_INSTALL OFF)
set(BUILD_TOOLS OFF)
set(BUILD_REGRESS OFF)
set(BUILD_OSSFUZZ OFF)
set(BUILD_EXAMPLES OFF)
set(BUILD_DOC OFF)

add_subdirectory("${CMAKE_CURRENT_SOURCE_DIR}/../libzip"
                 "${CMAKE_CURRENT_BINARY_DIR}/libzip")
add_library(ext_zip ALIAS zip)

set_target_properties(zip PROPERTIES
  POSITION_INDEPENDENT_CODE ON
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)

add_library(${target_name} STATIC
  "src/src/libzippp.cpp"
  "src/src/libzippp.h"
)
target_include_directories(${target_name} INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/src/src)
target_compile_definitions(${target_name} PUBLIC LIBZIPPP_WITH_ENCRYPTION)

set_target_properties(${target_name} PROPERTIES PREFIX "")
target_link_libraries(${target_name} PRIVATE ext_zip)

set_target_properties(${target_name}
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)
