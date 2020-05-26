CMAKE_MINIMUM_REQUIRED(VERSION 3.0.2)

LIST(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR})

PROJECT(libzip C)

set(ZLIB_INCLUDE_DIR ${COCOS2DX_ROOT_PATH}/external/zlib/include)
set(OPENSSL_INCLUDE_DIR ${COCOS2DX_ROOT_PATH}/external/openssl/include/win32)
# set(OPENSSL_LIBRARIES "")

OPTION(ENABLE_COMMONCRYPTO "Enable use of CommonCrypto" OFF)
OPTION(ENABLE_GNUTLS "Enable use of GnuTLS" OFF)
OPTION(ENABLE_MBEDTLS "Enable use of mbed TLS" OFF)
OPTION(ENABLE_OPENSSL "Enable use of OpenSSL" ON)
OPTION(ENABLE_WINDOWS_CRYPTO "Enable use of Windows cryptography libraries" OFF)

# OPTION(ENABLE_BZIP2 "Enable use of BZip2" OFF)
# OPTION(ENABLE_LZMA "Enable use of LZMA" OFF)

# OPTION(BUILD_TOOLS "Build tools in the src directory (zipcmp, zipmerge, ziptool)" OFF)
# OPTION(BUILD_REGRESS "Build regression tests" OFF)
# OPTION(BUILD_EXAMPLES "Build examples" OFF)
# OPTION(BUILD_DOC "Build documentation" OFF)

INCLUDE(CheckFunctionExists)
INCLUDE(CheckIncludeFiles)
INCLUDE(CheckLibraryExists)
INCLUDE(CheckSymbolExists)
INCLUDE(CheckTypeSize)
INCLUDE(CheckCSourceRuns)
INCLUDE(CheckCSourceCompiles)
INCLUDE(CheckStructHasMember)
INCLUDE(TestBigEndian)
INCLUDE(GNUInstallDirs)

SET(COMMONCRYPTO_FOUND FALSE)
SET(GNUTLS_FOUND FALSE)
SET(MBEDTLS_LIBRARIES FALSE)
SET(WINDOWS_CRYPTO_FOUND FALSE)

OPTION(BUILD_SHARED_LIBS "Build shared libraries" OFF)
# OPTION(SHARED_LIB_VERSIONNING "Add SO version in .so build" OFF)

SET(DOCUMENTATION_FORMAT "html" CACHE STRING "Documentation format")

SET(PACKAGE "libzip")
SET(PACKAGE_NAME ${PACKAGE})
SET(PACKAGE_VERSION_MAJOR "1")
SET(PACKAGE_VERSION_MINOR "6")
SET(PACKAGE_VERSION_MICRO "1")
#SET(VERSION "${PACKAGE_VERSION_MAJOR}.${PACKAGE_VERSION_MINOR}")
SET(VERSION "${PACKAGE_VERSION_MAJOR}.${PACKAGE_VERSION_MINOR}.${PACKAGE_VERSION_MICRO}")
SET(PACKAGE_VERSION ${VERSION})
SET(LIBZIP_VERSION ${PACKAGE_VERSION})
SET(LIBZIP_VERSION_MAJOR ${PACKAGE_VERSION_MAJOR})
SET(LIBZIP_VERSION_MINOR ${PACKAGE_VERSION_MINOR})
SET(LIBZIP_VERSION_MICRO ${PACKAGE_VERSION_MICRO})
SET(PACKAGE_STRING "${PACKAGE_NAME} ${PACKAGE_VERSION}")
SET(ARCHIVE_NAME ${PACKAGE_NAME}-${PACKAGE_VERSION})
SET(ZIP_STATIC TRUE)

# Checks

CHECK_FUNCTION_EXISTS(_chmod HAVE__CHMOD)
CHECK_FUNCTION_EXISTS(_close HAVE__CLOSE)
CHECK_FUNCTION_EXISTS(_dup HAVE__DUP)
CHECK_FUNCTION_EXISTS(_fdopen HAVE__FDOPEN)
CHECK_FUNCTION_EXISTS(_fileno HAVE__FILENO)
CHECK_FUNCTION_EXISTS(_open HAVE__OPEN)
CHECK_FUNCTION_EXISTS(_setmode HAVE__SETMODE)
CHECK_FUNCTION_EXISTS(_snprintf HAVE__SNPRINTF)
CHECK_FUNCTION_EXISTS(_strdup HAVE__STRDUP)
CHECK_FUNCTION_EXISTS(_stricmp HAVE__STRICMP)
CHECK_FUNCTION_EXISTS(_strtoi64 HAVE__STRTOI64)
CHECK_FUNCTION_EXISTS(_strtoui64 HAVE__STRTOUI64)
CHECK_FUNCTION_EXISTS(_unlink HAVE__UNLINK)
CHECK_FUNCTION_EXISTS(arc4random HAVE_ARC4RANDOM)
CHECK_FUNCTION_EXISTS(clonefile HAVE_CLONEFILE)
CHECK_FUNCTION_EXISTS(explicit_bzero HAVE_EXPLICIT_BZERO)
CHECK_FUNCTION_EXISTS(explicit_memset HAVE_EXPLICIT_MEMSET)
CHECK_FUNCTION_EXISTS(fileno HAVE_FILENO)
CHECK_FUNCTION_EXISTS(fseeko HAVE_FSEEKO)
CHECK_FUNCTION_EXISTS(ftello HAVE_FTELLO)
CHECK_FUNCTION_EXISTS(getprogname HAVE_GETPROGNAME)
CHECK_FUNCTION_EXISTS(localtime_r HAVE_LOCALTIME_R)
CHECK_FUNCTION_EXISTS(open HAVE_OPEN)
CHECK_FUNCTION_EXISTS(setmode HAVE_SETMODE)
CHECK_FUNCTION_EXISTS(snprintf HAVE_SNPRINTF)
CHECK_FUNCTION_EXISTS(strcasecmp HAVE_STRCASECMP)
CHECK_FUNCTION_EXISTS(strdup HAVE_STRDUP)
CHECK_FUNCTION_EXISTS(stricmp HAVE_STRICMP)
CHECK_FUNCTION_EXISTS(strtoll HAVE_STRTOLL)
CHECK_FUNCTION_EXISTS(strtoull HAVE_STRTOULL)

CHECK_INCLUDE_FILES("sys/types.h;sys/stat.h;fts.h" HAVE_FTS_H)
# fts functions may be in external library
IF(HAVE_FTS_H)
  CHECK_FUNCTION_EXISTS(fts_open HAVE_FTS_OPEN)
  IF(NOT HAVE_FTS_OPEN)
    CHECK_LIBRARY_EXISTS(fts fts_open "" HAVE_LIB_FTS)
  ELSE(NOT HAVE_FTS_OPEN)
    SET(HAVE_LIB_FTS "" CACHE INTERNAL "")
  ENDIF(NOT HAVE_FTS_OPEN)
ELSE(HAVE_FTS_H)
  SET(HAVE_LIB_FTS "" CACHE INTERNAL "")
ENDIF(HAVE_FTS_H)

IF(HAVE_LIB_FTS)
  SET(FTS_LIB fts CACHE INTERNAL "")
ELSE()
  SET(FTS_LIB "" CACHE INTERNAL "")
ENDIF()

CHECK_INCLUDE_FILES(stdbool.h HAVE_STDBOOL_H)
CHECK_INCLUDE_FILES(strings.h HAVE_STRINGS_H)
CHECK_INCLUDE_FILES(unistd.h HAVE_UNISTD_H)

CHECK_INCLUDE_FILES(inttypes.h HAVE_INTTYPES_H_LIBZIP)
CHECK_INCLUDE_FILES(stdint.h HAVE_STDINT_H_LIBZIP)
CHECK_INCLUDE_FILES(sys/types.h HAVE_SYS_TYPES_H_LIBZIP)

# TODO: fix test
# this test does not find __progname even when it exists
#CHECK_SYMBOL_EXISTS(__progname stdlib.h HAVE___PROGNAME)

CHECK_TYPE_SIZE(__int8 __INT8_LIBZIP)
CHECK_TYPE_SIZE(int8_t INT8_T_LIBZIP)
CHECK_TYPE_SIZE(uint8_t UINT8_T_LIBZIP)
CHECK_TYPE_SIZE(__int16 __INT16_LIBZIP)
CHECK_TYPE_SIZE(int16_t INT16_T_LIBZIP)
CHECK_TYPE_SIZE(uint16_t UINT16_T_LIBZIP)
CHECK_TYPE_SIZE(__int32 __INT32_LIBZIP)
CHECK_TYPE_SIZE(int32_t INT32_T_LIBZIP)
CHECK_TYPE_SIZE(uint32_t UINT32_T_LIBZIP)
CHECK_TYPE_SIZE(__int64 __INT64_LIBZIP)
CHECK_TYPE_SIZE(int64_t INT64_T_LIBZIP)
CHECK_TYPE_SIZE(uint64_t UINT64_T_LIBZIP)
CHECK_TYPE_SIZE("short" SHORT_LIBZIP)
CHECK_TYPE_SIZE("int" INT_LIBZIP)
CHECK_TYPE_SIZE("long" LONG_LIBZIP)
CHECK_TYPE_SIZE("long long" LONG_LONG_LIBZIP)
CHECK_TYPE_SIZE("off_t" SIZEOF_OFF_T)
CHECK_TYPE_SIZE("size_t" SIZE_T_LIBZIP)
CHECK_TYPE_SIZE("ssize_t" SSIZE_T_LIBZIP)

CHECK_C_SOURCE_COMPILES("#include <sys/ioctl.h>
#include <linux/fs.h>
int main(int argc, char *argv[]) { unsigned long x = FICLONERANGE; }" HAVE_FICLONERANGE)

CHECK_C_SOURCE_COMPILES("
int foo(char * _Nullable bar);
int main(int argc, char *argv[]) { }" HAVE_NULLABLE)

TEST_BIG_ENDIAN(WORDS_BIGENDIAN)

INCLUDE_DIRECTORIES(${ZLIB_INCLUDE_DIR})
SET(CMAKE_REQUIRED_INCLUDES ${ZLIB_INCLUDE_DIR})

SET (HAVE_CRYPTO 1)
SET (HAVE_OPENSSL 1)
INCLUDE_DIRECTORIES(${OPENSSL_INCLUDE_DIR})
# SET (OPTIONAL_LIBRARY ${OPTIONAL_LIBRARY} ${OPENSSL_LIBRARIES})

IF (NOT HAVE_CRYPTO)
  MESSAGE(WARNING "-- neither Common Crypto, GnuTLS, mbed TLS, OpenSSL, nor Windows Cryptography found; AES support disabled")
ENDIF()

IF(MSVC)
ADD_DEFINITIONS("-D_CRT_SECURE_NO_WARNINGS")
ADD_DEFINITIONS("-D_CRT_NONSTDC_NO_DEPRECATE")
ENDIF(MSVC)

if(WIN32)
  if(CMAKE_SYSTEM_NAME MATCHES WindowsPhone OR CMAKE_SYSTEM_NAME MATCHES WindowsStore)
    ADD_DEFINITIONS(-DMS_UWP)
  else()
    SET (OPTIONAL_LIBRARY ${OPTIONAL_LIBRARY} advapi32)
  endif()
endif(WIN32)

ADD_DEFINITIONS("-DHAVE_CONFIG_H")

# rpath handling: use rpath in installed binaries
IF(NOT CMAKE_SYSTEM_NAME MATCHES Linux)
	SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
	SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
ENDIF()

# Testing
ENABLE_TESTING()

# fixed size integral types

IF(HAVE_INTTYPES_H_LIBZIP)
  SET(LIBZIP_TYPES_INCLUDE "#define __STDC_FORMAT_MACROS 1
#include <inttypes.h>")
ELSEIF(HAVE_STDINT_H_LIBZIP)
  SET(LIBZIP_TYPES_INCLUDE "#include <stdint.h>")
ELSEIF(HAVE_SYS_TYPES_H_LIBZIP)
  SET(LIBZIP_TYPES_INCLUDE "#include <sys/types.h>")
ENDIF()

SET(ZIP_INT8_T int8_t)
SET(ZIP_UINT8_T uint8_t)
SET(ZIP_INT16_T int16_t)
SET(ZIP_UINT16_T uint16_t)
SET(ZIP_INT32_T int32_t)
SET(ZIP_UINT32_T uint32_t)
SET(ZIP_INT64_T int64_t)
SET(ZIP_UINT64_T uint64_t)

IF(HAVE_NULLABLE)
  SET(ZIP_NULLABLE_DEFINES)
ELSE()
  SET(ZIP_NULLABLE_DEFINES "#define _Nullable
#define _Nonnull")
ENDIF()

# write out config file
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/src/cmake-config.h.in ${CMAKE_CURRENT_SOURCE_DIR}/src/lib/config.h)
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/src/cmake-zipconf.h.in ${CMAKE_CURRENT_SOURCE_DIR}/src/lib/zipconf.h)

# Targets

SET(CMAKE_C_VISIBILITY_PRESET hidden)

set(LIBZIP_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/lib)

SET(LIBZIP_SOURCES
  ${LIBZIP_SRC}/zip_add.c
  ${LIBZIP_SRC}/zip_add_dir.c
  ${LIBZIP_SRC}/zip_add_entry.c
  ${LIBZIP_SRC}/zip_algorithm_deflate.c
  ${LIBZIP_SRC}/zip_buffer.c
  ${LIBZIP_SRC}/zip_close.c
  ${LIBZIP_SRC}/zip_delete.c
  ${LIBZIP_SRC}/zip_dir_add.c
  ${LIBZIP_SRC}/zip_dirent.c
  ${LIBZIP_SRC}/zip_discard.c
  ${LIBZIP_SRC}/zip_entry.c
  ${LIBZIP_SRC}/zip_err_str.c
  ${LIBZIP_SRC}/zip_error.c
  ${LIBZIP_SRC}/zip_error_clear.c
  ${LIBZIP_SRC}/zip_error_get.c
  ${LIBZIP_SRC}/zip_error_get_sys_type.c
  ${LIBZIP_SRC}/zip_error_strerror.c
  ${LIBZIP_SRC}/zip_error_to_str.c
  ${LIBZIP_SRC}/zip_extra_field.c
  ${LIBZIP_SRC}/zip_extra_field_api.c
  ${LIBZIP_SRC}/zip_fclose.c
  ${LIBZIP_SRC}/zip_fdopen.c
  ${LIBZIP_SRC}/zip_file_add.c
  ${LIBZIP_SRC}/zip_file_error_clear.c
  ${LIBZIP_SRC}/zip_file_error_get.c
  ${LIBZIP_SRC}/zip_file_get_comment.c
  ${LIBZIP_SRC}/zip_file_get_external_attributes.c
  ${LIBZIP_SRC}/zip_file_get_offset.c
  ${LIBZIP_SRC}/zip_file_rename.c
  ${LIBZIP_SRC}/zip_file_replace.c
  ${LIBZIP_SRC}/zip_file_set_comment.c
  ${LIBZIP_SRC}/zip_file_set_encryption.c
  ${LIBZIP_SRC}/zip_file_set_external_attributes.c
  ${LIBZIP_SRC}/zip_file_set_mtime.c
  ${LIBZIP_SRC}/zip_file_strerror.c
  ${LIBZIP_SRC}/zip_filerange_crc.c
  ${LIBZIP_SRC}/zip_fopen.c
  ${LIBZIP_SRC}/zip_fopen_encrypted.c
  ${LIBZIP_SRC}/zip_fopen_index.c
  ${LIBZIP_SRC}/zip_fopen_index_encrypted.c
  ${LIBZIP_SRC}/zip_fread.c
  ${LIBZIP_SRC}/zip_fseek.c
  ${LIBZIP_SRC}/zip_ftell.c
  ${LIBZIP_SRC}/zip_get_archive_comment.c
  ${LIBZIP_SRC}/zip_get_archive_flag.c
  ${LIBZIP_SRC}/zip_get_encryption_implementation.c
  ${LIBZIP_SRC}/zip_get_file_comment.c
  ${LIBZIP_SRC}/zip_get_name.c
  ${LIBZIP_SRC}/zip_get_num_entries.c
  ${LIBZIP_SRC}/zip_get_num_files.c
  ${LIBZIP_SRC}/zip_hash.c
  ${LIBZIP_SRC}/zip_io_util.c
  ${LIBZIP_SRC}/zip_libzip_version.c
  ${LIBZIP_SRC}/zip_memdup.c
  ${LIBZIP_SRC}/zip_name_locate.c
  ${LIBZIP_SRC}/zip_new.c
  ${LIBZIP_SRC}/zip_open.c
  ${LIBZIP_SRC}/zip_progress.c
  ${LIBZIP_SRC}/zip_rename.c
  ${LIBZIP_SRC}/zip_replace.c
  ${LIBZIP_SRC}/zip_set_archive_comment.c
  ${LIBZIP_SRC}/zip_set_archive_flag.c
  ${LIBZIP_SRC}/zip_set_default_password.c
  ${LIBZIP_SRC}/zip_set_file_comment.c
  ${LIBZIP_SRC}/zip_set_file_compression.c
  ${LIBZIP_SRC}/zip_set_name.c
  ${LIBZIP_SRC}/zip_source_accept_empty.c
  ${LIBZIP_SRC}/zip_source_begin_write.c
  ${LIBZIP_SRC}/zip_source_begin_write_cloning.c
  ${LIBZIP_SRC}/zip_source_buffer.c
  ${LIBZIP_SRC}/zip_source_call.c
  ${LIBZIP_SRC}/zip_source_close.c
  ${LIBZIP_SRC}/zip_source_commit_write.c
  ${LIBZIP_SRC}/zip_source_compress.c
  ${LIBZIP_SRC}/zip_source_crc.c
  ${LIBZIP_SRC}/zip_source_error.c
  ${LIBZIP_SRC}/zip_source_filep.c
  ${LIBZIP_SRC}/zip_source_free.c
  ${LIBZIP_SRC}/zip_source_function.c
  ${LIBZIP_SRC}/zip_source_get_compression_flags.c
  ${LIBZIP_SRC}/zip_source_is_deleted.c
  ${LIBZIP_SRC}/zip_source_layered.c
  ${LIBZIP_SRC}/zip_source_open.c
  ${LIBZIP_SRC}/zip_source_pkware.c
  ${LIBZIP_SRC}/zip_source_read.c
  ${LIBZIP_SRC}/zip_source_remove.c
  ${LIBZIP_SRC}/zip_source_rollback_write.c
  ${LIBZIP_SRC}/zip_source_seek.c
  ${LIBZIP_SRC}/zip_source_seek_write.c
  ${LIBZIP_SRC}/zip_source_stat.c
  ${LIBZIP_SRC}/zip_source_supports.c
  ${LIBZIP_SRC}/zip_source_tell.c
  ${LIBZIP_SRC}/zip_source_tell_write.c
  ${LIBZIP_SRC}/zip_source_window.c
  ${LIBZIP_SRC}/zip_source_write.c
  ${LIBZIP_SRC}/zip_source_zip.c
  ${LIBZIP_SRC}/zip_source_zip_new.c
  ${LIBZIP_SRC}/zip_stat.c
  ${LIBZIP_SRC}/zip_stat_index.c
  ${LIBZIP_SRC}/zip_stat_init.c
  ${LIBZIP_SRC}/zip_strerror.c
  ${LIBZIP_SRC}/zip_string.c
  ${LIBZIP_SRC}/zip_unchange.c
  ${LIBZIP_SRC}/zip_unchange_all.c
  ${LIBZIP_SRC}/zip_unchange_archive.c
  ${LIBZIP_SRC}/zip_unchange_data.c
  ${LIBZIP_SRC}/zip_utf-8.c
)

IF(WIN32)
  SET(LIBZIP_OPSYS_FILES
    ${LIBZIP_SRC}/zip_source_win32handle.c
    ${LIBZIP_SRC}/zip_source_win32utf8.c
    ${LIBZIP_SRC}/zip_source_win32w.c
  )
  IF(CMAKE_SYSTEM_NAME MATCHES WindowsPhone OR CMAKE_SYSTEM_NAME MATCHES WindowsStore)
  ELSE()
    SET(LIBZIP_OPSYS_FILES "${LIBZIP_OPSYS_FILES}"
    ${LIBZIP_SRC}/zip_source_win32a.c
    )
  ENDIF()
ELSE()
  SET(LIBZIP_OPSYS_FILES
    ${LIBZIP_SRC}/zip_mkstempm.c
    ${LIBZIP_SRC}/zip_source_file.c
    ${LIBZIP_SRC}/zip_random_unix.c
  )
ENDIF()

IF(HAVE_OPENSSL)
  SET(LIBZIP_OPTIONAL_FILES
    ${LIBZIP_OPTIONAL_FILES}
    ${LIBZIP_SRC}/zip_crypto_openssl.c
  )
ENDIF()

IF(HAVE_CRYPTO)
  SET(LIBZIP_OPTIONAL_FILES
    ${LIBZIP_OPTIONAL_FILES}
    ${LIBZIP_SRC}/zip_winzip_aes.c
    ${LIBZIP_SRC}/zip_source_winzip_aes_decode.c
    ${LIBZIP_SRC}/zip_source_winzip_aes_encode.c
  )
ENDIF()

ADD_LIBRARY(zip ${LIBZIP_SOURCES} ${LIBZIP_EXTRA_FILES} ${LIBZIP_OPTIONAL_FILES} ${LIBZIP_OPSYS_FILES})
TARGET_LINK_LIBRARIES(zip PRIVATE ${ZLIB_LIBRARIES} ${OPTIONAL_LIBRARY})

target_include_directories(zip INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/src/lib)
target_include_directories(zip PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src/lib)
target_compile_definitions(zip PRIVATE Z_SOLO) # avoid bad zconf.h
set_target_properties(zip
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)
if(MSVC)
  target_compile_options(zip PUBLIC /MP)
endif()
