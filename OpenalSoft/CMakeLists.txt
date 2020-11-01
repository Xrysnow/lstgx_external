cmake_minimum_required(VERSION 3.6)

if(NOT APPLE)
  set(lib_name al)
  set(target_name ext_${lib_name})

  project(${lib_name})

  if(ANDROID)
    add_library(${target_name} SHARED IMPORTED GLOBAL)
    set_target_properties(${target_name} PROPERTIES
      INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
    )
    
    set(PREBUILT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_android/${ANDROID_ABI})
    
    if(NOT EXISTS ${PREBUILT_ROOT})
      message(FATAL_ERROR "no prebuilt library for ${ANDROID_ABI} at ${PREBUILT_ROOT}")
    endif()
    
    set_target_properties(${target_name} PROPERTIES
      IMPORTED_LOCATION "${PREBUILT_ROOT}/libopenal.so"
    )
  endif()
elseif(MACOSX)
  # set(PREBUILT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac/soft_oal.framework)

  # set_target_properties(${target_name} PROPERTIES
  #   IMPORTED_LOCATION "${PREBUILT_ROOT}/libopenal.so"
  # )

  # target_link_libraries(${APP_NAME} ${PREBUILT_PATH})
  # target_link_libraries(${APP_NAME} -Wl,-rpath @executable_path/dylib ${PREBUILT_PATH})

  # set_target_properties(${APP_NAME} PROPERTIES
  #   LINK_FLAGS "-Wl,-F${PREBUILT_PATH}"
  #   FRAMEWORK_SEARCH_PATHS {PREBUILT_PATH}
  #   )
  # target_link_libraries(${APP_NAME}
  #   PRIVATE soft_oal
  #   PRIVATE "-framework soft_oal"
  # )

  # target_link_libraries(${APP_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac/soft_oal.framework)
  # set_target_properties(${APP_NAME} PROPERTIES XCODE_ATTRIBUTE_FRAMEWORK_SEARCH_PATHS ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac)

  set(ALSOFT_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include" CACHE INTERNAL "OpenAL Soft header location" )
  set(ALSOFT_FRAMEWORK_PATH "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac" CACHE INTERNAL "OpenAL Soft Framework location" )
  set(ALSOFT_OPENAL_LIBRARY "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_mac/soft_oal.framework" CACHE INTERNAL "OpenAL Soft library" )

elseif(IOS)
  set(ALSOFT_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include" CACHE INTERNAL "OpenAL Soft header location" )
  set(ALSOFT_FRAMEWORK_PATH "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_ios" CACHE INTERNAL "OpenAL Soft Framework location" )
  set(ALSOFT_OPENAL_LIBRARY "${CMAKE_CURRENT_SOURCE_DIR}/prebuilt_ios/soft_oal.framework" CACHE INTERNAL "OpenAL Soft library" )
endif()
