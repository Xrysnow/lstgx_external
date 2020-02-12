cmake_minimum_required(VERSION 3.6)

set(lib_name cLaTeXMath)
set(target_name ext_${lib_name})

project(${lib_name})

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src/src)

file(GLOB_RECURSE ${target_name}_src
  "${src_dir}/*.cpp"
  "${src_dir}/*.h")

add_library(${target_name} STATIC
  ${${target_name}_src}
)

target_include_directories(${target_name} PRIVATE ${src_dir})
target_include_directories(${target_name} INTERFACE ${src_dir})

set_target_properties(${target_name}
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)
