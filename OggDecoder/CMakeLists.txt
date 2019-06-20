cmake_minimum_required(VERSION 3.6)

set(lib_name ogg)
set(target_name ext_${lib_name})

project(${lib_name})

if(ANDROID)
  add_library(${target_name} SHARED IMPORTED GLOBAL)
else()
  message(FATAL_ERROR "not supported yet")
endif()

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
)

if(ANDROID)
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI})
  
  if(NOT EXISTS PREBUILT_ROOT)
  message(FATAL_ERROR "no prebuilt library for ${ANDROID_ABI}")
  endif()
  
  set_target_properties(${target_name} PROPERTIES
  IMPORTED_LOCATION "${PREBUILT_ROOT}/libogg.so"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbis.so"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbisfile.so"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY 
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbisenc.so"
  )
endif()
