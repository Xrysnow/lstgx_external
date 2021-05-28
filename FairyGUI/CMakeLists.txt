cmake_minimum_required(VERSION 3.6)

set(lib_name FairyGUI)
set(target_name ext_${lib_name})

project(${lib_name})

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src/libfairygui/Classes)

file(GLOB_RECURSE ${target_name}_src
  "${src_dir}/*.cpp"
  "${src_dir}/*.cc"
  "${src_dir}/*.c"
  "${src_dir}/*.hpp"
  "${src_dir}/*.h"
)

add_library(${target_name} STATIC
  ${${target_name}_src}
)
add_dependencies(${target_name} cocos2d)
use_cocos2dx_compile_define(${target_name})
use_cocos2dx_compile_options(${target_name})

target_include_directories(${target_name} PRIVATE ${src_dir})
target_include_directories(${target_name} INTERFACE ${src_dir})

include_directories(
  ${COCOS2DX_ROOT_PATH}/cocos
  ${COCOS2DX_ROOT_PATH}/cocos/audio/include
  ${COCOS2DX_ROOT_PATH}/cocos/base
  ${COCOS2DX_ROOT_PATH}/cocos/editor-support
  ${COCOS2DX_ROOT_PATH}/cocos/platform
  ${COCOS2DX_ROOT_PATH}/external
  ${COCOS2DX_ROOT_PATH}/external/glfw3/include/win32
)

if(WINDOWS)
  include_directories(${COCOS2DX_ROOT_PATH}/external/win32-specific/gles/include/OGLES)
endif()

set_target_properties(${target_name}
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)

if(XCODE OR VS)
  cocos_mark_code_files(${target_name})
endif()
