cmake_minimum_required(VERSION 3.6)

set(lib_name Yuescript)
set(target_name ext_${lib_name})

project(${lib_name})

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src/yuescript)
set(inc_dir ${CMAKE_CURRENT_SOURCE_DIR}/src)

file(GLOB_RECURSE ${target_name}_src
  "${src_dir}/*.cpp"
  "${src_dir}/*.hpp"
  "${src_dir}/*.h"
)

add_library(${target_name} STATIC
  ${${target_name}_src}
)

target_include_directories(${target_name} PRIVATE
  ${COCOS2DX_ROOT_PATH}/external/lua/luajit/include
  ${inc_dir}
)
target_include_directories(${target_name} INTERFACE ${inc_dir})

set_target_properties(${target_name}
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)
