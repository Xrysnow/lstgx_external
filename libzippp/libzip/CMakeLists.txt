cmake_minimum_required(VERSION 3.6)

# depends on zlib and openssl
set(lib_name zip)
set(target_name ext_${lib_name})

project(${lib_name})

add_library(${target_name} STATIC IMPORTED GLOBAL)

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include"
)
if(LINUX)
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_linux/libzip.a"
  )
elseif(WINDOWS)
  if(WIN64)
    set_target_properties(${target_name} PROPERTIES
      IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win64/zip.lib"
    )
  else()
    set_target_properties(${target_name} PROPERTIES
      IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win32/zip.lib"
    )
  endif()
elseif(MACOSX)
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac/libzip.a"
  )
elseif(IOS)
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_ios/libzip.a"
  )
elseif(ANDROID)
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI}/libzip.a"
  )
elseif(LINUX)
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_linux/libzip.a"
  )
endif()
