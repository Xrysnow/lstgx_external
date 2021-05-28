cmake_minimum_required(VERSION 3.8.0)

set(lib_name libzippp)
set(target_name ext_${lib_name})
project(${lib_name} VERSION 3.0.0)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

add_library(${target_name} STATIC
  "src/src/libzippp.cpp"
  "src/src/libzippp.h"
)
target_include_directories(${target_name} INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/src/src)
target_compile_definitions(${target_name} PUBLIC LIBZIPPP_WITH_ENCRYPTION)

set_target_properties(${target_name} PROPERTIES PREFIX "")
add_subdirectory(libzip)
target_link_libraries(${target_name} PRIVATE ext_zip)

set_target_properties(${target_name}
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)
