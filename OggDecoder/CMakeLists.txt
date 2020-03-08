cmake_minimum_required(VERSION 3.6)

set(lib_name ogg)
set(target_name ext_${lib_name})

project(${lib_name})

if(ANDROID)
  add_library(${target_name} SHARED IMPORTED GLOBAL)
elseif(MACOSX OR IOS)
  add_library(${target_name} STATIC IMPORTED GLOBAL)
else()
  message(FATAL_ERROR "not supported yet")
endif()

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
)

if(ANDROID)
  set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI})
  
  if(NOT EXISTS ${PREBUILT_ROOT})
    message(FATAL_ERROR "no prebuilt library for ${ANDROID_ABI} at ${PREBUILT_ROOT}")
  endif()
  
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libogg.so"
  )

  add_library(${target_name}_vorbis SHARED IMPORTED GLOBAL)
  set_target_properties(${target_name}_vorbis PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbis.so"
  )
  add_library(${target_name}_vorbisfile SHARED IMPORTED GLOBAL)
  set_target_properties(${target_name}_vorbisfile PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbisfile.so"
  )
  add_library(${target_name}_vorbisenc SHARED IMPORTED GLOBAL)
  set_target_properties(${target_name}_vorbisenc PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbisenc.so"
  )

  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES ${target_name}_vorbis
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES ${target_name}_vorbisfile
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES ${target_name}_vorbisenc
  )
elseif(MACOSX OR IOS)
  if(MACOSX)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac)
  elseif(IOS)
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_ios)
  endif()
  
  #if(NOT EXISTS PREBUILT_ROOT)
  #  message(FATAL_ERROR "no prebuilt library at ${PREBUILT_ROOT}")
  #endif()
  
  set_target_properties(${target_name} PROPERTIES
  IMPORTED_LOCATION "${PREBUILT_ROOT}/libogg.a"
  )

  # multi libs
  add_library(libogg STATIC IMPORTED)
  set_target_properties(libogg PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libogg.a"
  )
  add_library(libvorbis STATIC IMPORTED)
  set_target_properties(libvorbis PROPERTIES
    IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbis.a"
  )
  if(MACOSX)
    add_library(libvorbisfile STATIC IMPORTED)
    set_target_properties(libvorbisfile PROPERTIES
      IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbisfile.a"
    )
    add_library(libvorbisenc STATIC IMPORTED)
    set_target_properties(libvorbisenc PROPERTIES
      IMPORTED_LOCATION "${PREBUILT_ROOT}/libvorbisenc.a"
    )
  endif()

  # internal link depend
  set_property(TARGET libvorbis APPEND PROPERTY INTERFACE_LINK_LIBRARIES "libogg")
  if(MACOSX)
    set_property(TARGET libvorbisfile APPEND PROPERTY INTERFACE_LINK_LIBRARIES "libogg")
    set_property(TARGET libvorbisenc APPEND PROPERTY INTERFACE_LINK_LIBRARIES "libogg")
  endif()

  # mark multi libs link into single one: "${target_name}"
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libogg"
  )
  set_property(TARGET ${target_name} APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES "libvorbis"
  )
  if(MACOSX)
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_LINK_LIBRARIES "libvorbisfile"
    )
    set_property(TARGET ${target_name} APPEND PROPERTY
      INTERFACE_LINK_LIBRARIES "libvorbisenc"
    )
  endif()

endif()
