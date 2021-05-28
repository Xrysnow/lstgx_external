cmake_minimum_required(VERSION 3.6)

set(lib_name yasio)
set(target_name ext_${lib_name})

project(${lib_name})

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src)

set(YASIO_CORE
  ${src_dir}/yasio/detail/obstream.hpp
  ${src_dir}/yasio/detail/ibstream.hpp
  ${src_dir}/yasio/xxsocket.hpp
  ${src_dir}/yasio/yasio.hpp
  ${src_dir}/yasio/xxsocket.cpp
  ${src_dir}/yasio/yasio.cpp
  ${src_dir}/yasio/bindings/lyasio.cpp
  ${src_dir}/yasio/bindings/yasio_cclua.cpp
  ${src_dir}/external/kcp/ikcp.c
)

add_library(${target_name} STATIC
  ${YASIO_CORE}
)
add_dependencies(${target_name} cocos2d)
use_cocos2dx_compile_define(${target_name})
use_cocos2dx_compile_options(${target_name})

target_include_directories(${target_name} PRIVATE
  ${COCOS2DX_ROOT_PATH}/external/lua/luajit/include
  ${src_dir}
  ${src_dir}/external
)
target_include_directories(${target_name} INTERFACE ${src_dir} ${src_dir}/external)

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

# yasio config

set(YASIO_HAVE_KCP 1)
set(YASIO_SSL_BACKEND 1)
target_link_libraries(${target_name} PRIVATE ext_ssl ext_crypto)

macro(yasio_config_pred pred)
  if(${pred})
    target_compile_definitions(${target_name} PUBLIC ${pred}=1)
  endif()
endmacro()

macro(yasio_config_option optname optval)
  if(${optname})
    target_compile_definitions(${target_name} PUBLIC ${optname}=${optval})
  endif()
endmacro()

yasio_config_pred(YASIO_VERBOSE_LOG)
yasio_config_pred(YASIO_USE_SPSC_QUEUE)
yasio_config_pred(YASIO_USE_SHARED_PACKET)
yasio_config_pred(YASIO_DISABLE_OBJECT_POOL)
yasio_config_pred(YASIO_ENABLE_ARES_PROFILER)
yasio_config_pred(YASIO_HAVE_CARES)
yasio_config_pred(YASIO_HAVE_KCP)
yasio_config_option(YASIO_SSL_BACKEND "${YASIO_SSL_BACKEND}")
yasio_config_pred(YASIO_ENABLE_UDS)
yasio_config_pred(YASIO_NT_COMPAT_GAI)
yasio_config_pred(YASIO_MINIFY_EVENT)
yasio_config_pred(YASIO_HAVE_HALF_FLOAT)
yasio_config_pred(YASIO_ENABLE_PASSIVE_EVENT)
yasio_config_pred(YASIO_NO_JNI_ONLOAD)
