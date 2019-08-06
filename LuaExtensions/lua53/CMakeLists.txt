cmake_minimum_required(VERSION 3.6)

set(lib_name lua53)
set(target_name ext_${lib_name})

project(${lib_name})

set(${target_name}_src
  lstrlib.c
  lutf8lib.c
)

add_library(${target_name} STATIC
  ${${target_name}_src}
)

target_include_directories(${target_name} PRIVATE ${COCOS2DX_ROOT_PATH}/external/lua/luajit/include)
target_include_directories(${target_name} INTERFACE .)

set_target_properties(${target_name}
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    FOLDER "External"
)
