cmake_minimum_required(VERSION 2.6)

set(lib_name cjson)
set(target_name ext_${lib_name})
project(${lib_name} C)

# option(USE_INTERNAL_FPCONV "Use internal strtod() / g_fmt() code for performance" ON)
# option(MULTIPLE_THREADS "Support multi-threaded apps with internal fpconv - recommended" OFF)

# Use libc number conversion routines (strtod(), sprintf())
set(FPCONV_SOURCES fpconv.c)

# Use internal number conversion routines
# add_definitions(-DUSE_INTERNAL_FPCONV)
# set(FPCONV_SOURCES g_fmt.c dtoa.c)
# include(TestBigEndian)
# TEST_BIG_ENDIAN(IEEE_BIG_ENDIAN)
# if(IEEE_BIG_ENDIAN)
#     add_definitions(-DIEEE_BIG_ENDIAN)
# endif()

# Handle platforms missing isinf() macro (Eg, some Solaris systems).
# include(CheckSymbolExists)
# CHECK_SYMBOL_EXISTS(isinf math.h HAVE_ISINF)
# if(NOT HAVE_ISINF)
#     add_definitions(-DUSE_INTERNAL_ISINF)
# endif()

# set(_MODULE_LINK "${CMAKE_THREAD_LIBS_INIT}")

if(APPLE)
    set(CMAKE_SHARED_MODULE_CREATE_C_FLAGS
        "${CMAKE_SHARED_MODULE_CREATE_C_FLAGS} -undefined dynamic_lookup")
endif()

if(WIN32)
    # Windows sprintf()/strtod() handle NaN/inf differently. Not supported.
    add_definitions(-DDISABLE_INVALID_NUMBERS)
endif()

if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    add_definitions(-Dinline=__inline)
    #add_definitions(-Dsnprintf=_snprintf)
    add_definitions(-Dstrncasecmp=_strnicmp)
endif()

add_library(${target_name} STATIC lua_cjson.c strbuf.c ${FPCONV_SOURCES})
# set_target_properties(${target_name} PROPERTIES PREFIX "")
# target_link_libraries(${target_name} ${_MODULE_LINK})

target_include_directories(${target_name} PRIVATE ${COCOS2DX_ROOT_PATH}/external/lua/luajit/include)
target_include_directories(${target_name} INTERFACE .)

set_target_properties(${target_name}
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    FOLDER "External"
)
