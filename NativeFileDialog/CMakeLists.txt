cmake_minimum_required(VERSION 3.6)

set(lib_name NativeFileDialog)
set(target_name ext_${lib_name})

project(${lib_name})

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src/src)
set(inc_dir ${CMAKE_CURRENT_SOURCE_DIR}/src/src/include)

set(${target_name}_src
  ${src_dir}/common.h
  ${src_dir}/nfd_common.c
  ${src_dir}/nfd_common.h
)
if(WINDOWS)
    list(APPEND ${target_name}_src ${src_dir}/nfd_win.cpp)
elseif(LINUX)
    list(APPEND ${target_name}_src ${src_dir}/nfd_gtk.c)
elseif(MACOSX)
    list(APPEND ${target_name}_src ${src_dir}/nfd_cocoa.m)
endif()

add_library(${target_name} STATIC
  ${${target_name}_src}
)

if(LINUX)
  find_package (PkgConfig REQUIRED)
  pkg_check_modules(GTK3 REQUIRED gtk+-3.0)
  include_directories(${GTK3_INCLUDE_DIRS})
  link_directories(${GTK3_LIBRARY_DIRS})
  add_definitions(${GTK3_CFLAGS_OTHER})
  target_link_libraries(${target_name} ${GTK3_LIBRARIES})
endif()

target_include_directories(${target_name} PRIVATE ${src_dir} ${inc_dir})
target_include_directories(${target_name} INTERFACE ${inc_dir})

set_target_properties(${target_name}
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)
