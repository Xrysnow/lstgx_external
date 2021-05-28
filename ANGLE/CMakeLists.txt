cmake_minimum_required(VERSION 3.6)

set(lib_name ANGLE)
set(target_name ext_${lib_name})

project(${lib_name})

if(WINDOWS)
    add_library(${target_name} SHARED IMPORTED GLOBAL)
    if(WIN64)
        set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win64)
    else()
        set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_win32)
    endif()

    set_target_properties(${target_name} PROPERTIES
        IMPORTED_LOCATION "${PREBUILT_ROOT}/libEGL.dll"
        IMPORTED_IMPLIB "${PREBUILT_ROOT}/libEGL.lib"
    )

    add_library(${target_name}_es2 SHARED IMPORTED GLOBAL)
    set_target_properties(${target_name}_es2 PROPERTIES
        IMPORTED_LOCATION "${PREBUILT_ROOT}/libGLESv2.dll"
        IMPORTED_IMPLIB "${PREBUILT_ROOT}/libGLESv2.lib"
    )
else()
    message(FATAL_ERROR "ANGLE is only available on windows")
endif()
